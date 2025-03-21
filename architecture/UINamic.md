```js
// core.props-filter.js
import displayValidator from '../utils/validators/display.validator'
import filterUtils from '../utils/filterUtils'

const corePropsFilter = (config) => {
    const { display, type, dynamicStyle: styleProps, props: spreadProps, dynamicType } = config

    const mergeProps = { ...spreadProps, ...styleProps }

    const { displayGroup, patchDisplay } = displayValidator(type, display)

    const result = filterUtils.splitPropsByType(mergeProps, displayGroup)

    const onEvent = mergeProps[dynamicType] || null

    const response = {
        style: {
            ...result.styleProps,
        },
        other: {
            ...result.onEventProps,
            ...result.normalProps,
        },
        dynamicClass: {
            dynamicType,
            onEvent,
        },
        patchDisplay,
    }

    return response
}

export default corePropsFilter
```

```js
// filterUtils.js
import { onEventList } from './constants'
import { displaySetMap } from './constants'

const keySet = new Set(['dynamic', 'keyframes', 'media', 'pseudo'])
const transitionType = new Set(['transition'])
const onEventSet = new Set(onEventList)

class FilterUtils {
    splitPropsByType(props, display) {
        const formatData = filterHelper.formatTransition(props)

        const { primitiveProps, referenceProps } = filterHelper.dataTypeSplit(formatData)

        const { cssStringProps, normalStringProps } = filterHelper.cssPropsSplit(primitiveProps, display)

        const { cssObjProps, onEventProps, normalObjProps } = filterHelper.splitPropsGroup(referenceProps)

        const result = {
            styleProps: {
                ...cssStringProps,
                ...cssObjProps,
            },
            onEventProps,
            normalProps: {
                ...normalStringProps,
                ...normalObjProps,
            },
        }

        return result
    }
}

class filterHelper {
    static formatTransition(object) {
        const traverse = (current) => {
            for (const [key, value] of Object.entries(current)) {
                if (transitionType.has(key)) {
                    if (Array.isArray(value)) {
                        current[key] = value.join(', ') // 배열 → 문자열로 덮어쓰기
                    }
                    // string이면 그대로 두니까 else는 필요 없음
                } else if (typeof value === 'object' && value !== null) {
                    traverse(value) // 재귀 탐색
                }
            }
        }
        traverse(object)

        return object
    }

    static dataTypeSplit(props) {
        const primitiveProps = {}
        const referenceProps = {}

        for (const [key, value] of Object.entries(props)) {
            if (typeof value === 'string' || transitionType.has(key) === true) {
                primitiveProps[key] = value
            } else {
                referenceProps[key] = value
            }
        }

        return { primitiveProps, referenceProps }
    }

    static cssPropsSplit(props, display) {
        const cssStringProps = {}
        const normalStringProps = {}
        const cssSet = displaySetMap[display]

        for (const [key, value] of Object.entries(props)) {
            if (cssSet.has(key)) {
                cssStringProps[key] = value
            } else {
                normalStringProps[key] = value
            }
        }

        return { cssStringProps, normalStringProps }
    }

    static splitPropsGroup(props) {
        const cssObjProps = {}
        const onEventProps = {}
        const normalObjProps = {}

        for (const [key, value] of Object.entries(props)) {
            if (keySet.has(key)) {
                cssObjProps[key] = value
            } else if (onEventSet.has(key)) {
                onEventProps[key] = value
            } else {
                normalObjProps[key] = value
            }
        }

        return {
            cssObjProps,
            onEventProps,
            normalObjProps,
        }
    }
}

export default new FilterUtils()
```

```js
// display.validator.js
import { tableTagList, displayGroupMap, displayList } from '../constants'
import { tableDisplayMap } from '../constants'

const tableTagSet = new Set(tableTagList)
const displaySet = new Set(displayList)

const validDisplay = (display, type) => {
    if (!displaySet.has(display)) {
        throw new Error(`Invalid display value "${display}" for type "${type}". Allowed values: ${[...displaySet].join(', ')}`)
    }
}

const patchDisplayGroup = (display) => {
    for (const group in displayGroupMap) {
        if (displayGroupMap[group].includes(display)) return group
    }
    return 'common' // 기본값
}

function displayValidator(type, display) {
    const getDisplay = tableTagSet.has(type) ? tableDisplayMap[type] : display

    validDisplay(getDisplay, type)

    const displayGroup = patchDisplayGroup(getDisplay)

    return { displayGroup, patchDisplay: getDisplay }
}

export default displayValidator
```
