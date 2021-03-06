# Common Props

Not every component uses all of these props. These are all common to things like `VictoryBar`, `VictoryScatter`, but other components like `VictoryStack` use only some of them.

The props explanations given here are general. Each component docs page should be considered as the the source of truth for a component's props, and any caveats will be listed there.

### `animate`

The `animate` prop specifies props for [VictoryAnimation] and [VictoryTransition] to use. The animate prop may be used to specify the duration, delay, and easing of an animation as well as the behavior of `onEnter` and `onExit` and `onLoad` transitions. Each Victory component defines its own default transitions, be these may be modified, or overwritten with the `animate` prop.

*examples:* `animate={{duration: 2000, onLoad: {duration: 1000}, onEnter: {duration: 500, before: () => ({y: 0})})}`

```js
animate: PropTypes.object
```

### `categories`

The `categories` prop specifies how categorical data for a chart should be ordered. This prop should be given as an array of string values, or an object with these arrays of values specified for x and y. If this prop is not set, categorical data will be plotted in the order it was given in the data array.

*examples:* `categories={["dogs", "cats", "mice"]}`

```js
categories: PropTypes.oneOfType([
  PropTypes.arrayOf(PropTypes.string),
  PropTypes.shape({
    x: PropTypes.arrayOf(PropTypes.string), y: PropTypes.arrayOf(PropTypes.string)
  })
])
```

### `containerComponent`

The `containerComponent` prop takes a component instance which will be used to create a container element for standalone charts. The new element created from the passed `containerComponent` will be provided with the following props: `height`, `width`, `children` (the chart itself) and `style`. If a `containerComponent` is not provided, the default `VictoryContainer` component will be used. `VictoryContainer` supports `title` and `desc` props, which are intended to add accessibility to Victory components. The more descriptive these props are, the more accessible your data will be for people using screen readers. These props may be set by passing them directly to the supplied component. By default, `VictoryContainer` renders a responsive `svg` using the `viewBox` attribute. To render a static container, set `responsive={false}` directly on the instance of `VictoryContainer` supplied via the `containerComponent` prop. `VictoryContainer` also renders a `Portal` element that may be used in conjunction with [VictoryPortal] to force components to render above other children.

*examples:* `containerComponent={<VictoryContainer responsive={false} title="Chart of Q1 Profit/>}`

*default:* `containerComponent={<VictoryContainer/>}`

```js
containerComponent: PropTypes.element
```

### `data`

Specify data via the `data` prop. By default, Victory components expect data as an array of objects with `x` and `y` properties. Use the [x] and [y] data accessor props to define a custom data format. The `data` prop must be given as an array.

```js
data: PropTypes.array
```

### `dataComponent`

The `dataComponent` prop takes a component instance which will be responsible for rendering a data element. The new element created from the passed `dataComponent` will be provided with all the props it needs to render. These props will always include `data`, `events`, `scale` and `style`. Individual components will supply additional props expected by their default `dataComponents`. See individual api docs for complete props lists. Any of these props may be overridden by passing in props to the supplied component, or modified or ignored within the custom component itself. If a `dataComponent` is not provided, each component will use its default `dataComponent`.

*examples:* `dataComponent={<Area events={{onClick: () => console.log("wow")}}/>}`, `dataComponent={<MyCustomArea/>}`

```js
dataComponent: PropTypes.element
```

### `domain`

The `domain` prop describes the range of data the component will include. This prop can be given as a array of the minimum and maximum expected values of the data or as an object that specifies separate arrays for x and y. If this prop is not provided, a domain will be calculated from data, or other available information.

*examples:* `domain={[-1, 1]}` `domain={{x: [0, 100], y: [0, 1]}}`

```js
domain: PropTypes.oneOfType([
  CustomPropTypes.domain,
  PropTypes.shape({ x: CustomPropTypes.domain, y: CustomPropTypes.domain })
])
```

### `domainPadding`

The `domainPadding` prop specifies a number of pixels of padding to add the beginning or end of a domain. This prop is useful for explicitly spacing data elements farther from the beginning or end of a domain to prevent axis crowding. When given as a single number, `domainPadding` will be applied to the upper and lower bound of both the x and y domains. This prop may also be given as an object with numbers or two-element arrays specified for x and y. When specifying arrays for `domainPadding`, the first element of the array will specify the padding to be applied to domain minimum, and the second element will specify padding the be applied to domain maximum.

*examples:* `domainPadding={20}`, `domainPadding={{x: [20, 0]}}`

**note:** Values supplied for `domainPadding` will be coerced so that padding a domain will never result in charts including an additonal quadrant. For example, if an original domain included only positive values, `domainPadding` will be coerced so that the resulted padded domain will not include negative values.

```js
domainPadding: PropTypes.oneOfType([
  PropTypes.shape({
    x: PropTypes.oneOfType([ PropTypes.number, PropTypes.arrayOf(PropTypes.number) ]),
    y: PropTypes.oneOfType([ PropTypes.number, PropTypes.arrayOf(PropTypes.number) ])
  }),
  PropTypes.number,
  PropTypes.arrayOf(PropTypes.number)
])
```

### `eventKey`

The `eventKey` prop is used to assign eventKeys to data. This prop operates identically to the [x] and [y] data accessor props. By default, the eventKey of each datum will be equal to its index in the data array.

```js
eventKey: PropTypes.oneOfType([
  PropTypes.func,
  CustomPropTypes.allOfType([CustomPropTypes.integer, CustomPropTypes.nonNegative]),
  PropTypes.string
])
```

### `events`

The `events` prop takes an array of event objects. Event objects are composed of a `target`, an `eventKey`, and `eventHandlers`. Targets may be any valid style namespace for a given component, so "data" and "labels" are valid targets for this component. `eventKey` may be given as a single value, or as an array of values to specify individual targets. If `eventKey` is not specified, the given `eventHandlers` will be attached to all elements of the specified `target` type. The `eventHandlers` object should be given as an object whose keys are standard event names (_e.g.,_ `onClick`) and whose values are event callbacks. The return value of an event handler is used to modify elemnts. The return value should be given as an object or an array of objects with optional `target` and `eventKey` keys for specifying the element(s) to be modified, and a `mutation` key whose value is a function. The `target` and `eventKey` keys will default to those corresponding to the element the event handler was attached to. The `mutation` function will be called with the calculated props for each element that should be modified (_e.g.,_ a bar label), and the object returned from the mutation function will override the props of that element via object assignment.

*examples:*
```jsx
 events={[
  {
    target: "data",
    eventKey: [0, 2, 4],
    eventHandlers: {
      onClick: () => {
        return [
          {
            mutation: (props) => {
              return {
                style: Object.assign({}, props.style, {fill: "orange"})
              };
            }
          }, {
            target: "labels",
            mutation: () => {
              return {text: "hey"};
            },
            callback: () => {
              console.log("I happen after setState");
            }
          }
        ];
      }
    }
  }
 ]}
```

**note:** Elements that render only one element for a given dataset (_e.g._ `VictoryArea`) will use the special `eventKey` "all" rather than refering to data be index. Refer to individual API docs for additinal caveats

```js
events: PropTypes.arrayOf(PropTypes.shape({
  target: PropTypes.oneOf(["data", "labels", "parent"]),
  eventKey: PropTypes.oneOfType([
    PropTypes.array,
    CustomPropTypes.allOfType([CustomPropTypes.integer, CustomPropTypes.nonNegative]),
    PropTypes.string
  ]),
  eventHandlers: PropTypes.object
}))
```

### `groupComponent`

The `groupComponent` prop takes a component instance which will be used to create group elements for use within container elements. For most components, this prop defaults to a `<g>` tag. Continuous data components like `VictoryLine` and `VictoryArea` use [VictoryClipContainer] a component which renders a `<g>` tag or a `<g>` tag with a `clipPath` `def` depending on whether the component should animate. This allows continuous data components to transition smoothly when new data points enter and exit.

```js
groupComponent: PropTypes.element
```

### `height`

The `height` prop determines the height of the containing `<svg>`. By default Victory components render responsive containers with the `viewBox` attribute set to `viewBox="0, 0, width, height"` and `width="100%`, `height="auto`. In responsive containers, the `width` and `height` props affect the _aspect ratio_ of the rendered component, while the absolute width and height are determined by the container. To render a static container, pass `responsive={false}` to the `containerComponent` like `containerComponent={<VictoryContainer responsive={false}/>}`, or set `standalone={false}` and render the resulting `<g>` tag in your own `<svg>` container. When a component is nested within `VictoryChart`, `VictoryStack`, or `VictoryGroup` setting the `height` prop on the child component will have no effect.

*default (provided by default theme):* `height={300}`

```js
height: CustomPropTypes.nonNegative
```

### `labelComponent`

The `labelComponent` prop takes a component instance which will be used to render a labels for the component. The new element created from the passed `labelComponent` will be supplied with the following properties: x, y, index, data, datum, verticalAnchor, textAnchor, angle, style, text, and events. Any of these props may be overridden by passing in props to the supplied component, or modified or ignored within the custom component itself. If `labelComponent` is omitted, a new [VictoryLabel] will be created with the props described above.

*examples:* `labelComponent={<VictoryLabel dy={20}/>}`, `labelComponent={<MyCustomLabel/>}`

*default:* `<VictoryLabel/>`

```js
labelComponent: PropTypes.element
```

### `labels`

The `labels` prop defines the labels that will appear above each point. This prop should be given as an array or as a function of data.

*examples:* `labels="Series 1"` , `labels={(datum) => datum.y}`

```js
labels: PropTypes.oneOfType([ PropTypes.func, PropTypes.array ])
```

### `name`

The `name` prop is used to reference a component instance when defining shared events.

```js
name: PropTypes.string
```

### `padding`

The `padding` prop specifies the amount of padding in number of pixels between the edge of the chart and any rendered child components. This prop can be given as a number or as an object with padding specified for top, bottom, left and right. As with [width] and [height], the absolute padding will depend on whether the component is rendered in a responsive container. When a component is nested within `VictoryChart`, `VictoryStack`, or `VictoryGroup` setting `padding` on the child component will have no effect.

*examples:* `padding={{top: 20, bottom: 60}}` or `padding={40}`

*default (provided by default theme):* `padding={50}`

```js
padding: PropTypes.oneOfType([
  PropTypes.number,
  PropTypes.shape({
    top: PropTypes.number, bottom: PropTypes.number,
    left: PropTypes.number, right: PropTypes.number
  })
])
```

### `samples`

The `samples` prop specifies how many individual points to plot when plotting
y as a function of x. The `samples` prop is ignored if `data` is supplied in props.

*default:* `samples={50}`

```js
samples: CustomPropTypes.nonNegative
```

### `scale`

The `scale` prop determines which scales your chart should use. This prop can be given as a string specifying a supported scale ("linear", "time", "log", "sqrt"), or as an object with scales specified for x and y. For "time" scales, data points should be `Date` objects or `getTime()` ints.

*examples:* `scale="time"`, `scale={{x: "linear", y: "log"}}`

*default:* `scale="linear"`

```js
scale: PropTypes.oneOfType([
  CustomPropTypes.scale,
  PropTypes.shape({ x: CustomPropTypes.scale, y: CustomPropTypes.scale })
])
```

### `sharedEvents`

The `sharedEvents` prop is used to coordinate events between Victory components using `VictorySharedEvents`. This prop should not be set manually.

```js
sharedEvents: PropTypes.shape({
  events: PropTypes.array,
  getEventState: PropTypes.func
})
```

### `sortKey`

Use the `sortKey` prop to indicate how data should be sorted. This prop is
given directly to the lodash [sortBy] function to be executed on the final
dataset.

This prop can be provided as a string, function, or array of either.

*examples*: `sortKey="x"`, `sortKey={["age", "height"]}`

```js
sortKey: PropTypes.oneOfType([
  PropTypes.func,
  CustomPropTypes.allOfType([CustomPropTypes.integer, CustomPropTypes.nonNegative]),
  PropTypes.string,
  PropTypes.arrayOf(PropTypes.string)
])
```

### `standalone`

The `standalone` props specifies whether the component should be rendered in a independent `<svg>` element or in a `<g>` tag. This prop defaults to true, and renders an `svg`, however, wrapper components like `VictoryChart`, `VictoryStack`, and `VictoryGroup` force children to use `standalone={false}`.

*default:* `standalone={true}`

```js
standalone: PropTypes.bool
```

### `style`

The `style` prop defines the style of the component. The style prop should be given as an object with styles defined for `data`, `labels` and `parent`. Any valid svg styles are supported, but `width`, `height`, and `padding` should be specified via props as they determine relative layout for components in VictoryChart.

```jsx
style={{
  data: {fill: "tomato", opacity: 0.7},
  labels: {fontSize: 12},
  parent: {border: "1px solid #ccc"}
}}
```

**note:** The `style` prop used by `VictoryAxis` has a different format than the standard `style` prop.

**note:** When a component is rendered as a child of another Victory component, or within a custom `<svg>` element with `standalone={false}` parent styles will be applied to the enclosing `<g>` tag. Many styles that can be applied to a parent `<svg>` will not be expressed when applied to a `<g>`.

**note:** custom `angle` and `verticalAnchor` properties maybe included in labels styles.

*default (provided by default theme):* See [grayscale theme] for more detail

```js
style: PropTypes.shape({
  parent: PropTypes.object, data: PropTypes.object, labels: PropTypes.object
})
```

### `theme`

The `theme` prop specifies a theme to use for determining styles and layout properties for a component. Any styles or props defined in `theme` may be overwritten by props specified on the component instance. By default, components use a [grayscale theme]. [Read more about themes here].

*default:* `theme={VictoryTheme.grayscale}`

```js
theme: PropTypes.object
```

### `width`

The `width` prop determines the width of the containing `<svg>`. By default Victory components render responsive containers with the `viewBox` attribute set to `viewBox="0, 0, width, height"` and `width="100%`, `height="auto`. In responsive containers, the `width` and `height` props affect the _aspect ratio_ of the rendered component, while the absolute width and height are determined by the container. To render a static container, pass `responsive={false}` to the `containerComponent` like `containerComponent={<VictoryContainer responsive={false}/>}`, or set `standalone={false}` and render the resulting `<g>` tag in your own `<svg>` container. When a component is nested within `VictoryChart`, `VictoryStack`, or `VictoryGroup` setting `width` prop on the child component will have no effect.

*default (provided by default theme):* `width={450}`

```js
width: CustomPropTypes.nonNegative
```
### `x`

Use the `x` data accessor prop to determine how the component defines data in the x dimension. This prop may be given in a variety of formats:

**string:** specify which property in an array of data objects should be used as the x value

*examples:* `x="month"`

**function:** use a function to translate each element in a data array into an x value

*examples:* `x={(datum) => datum.xValue + datum.error}`

**array index:** specify which index of an array should be used as an x value when data is given as an array of arrays

*examples:* `x={0}`

**path string or path array:** specify which property in an array of nested data objects should be used as an x value

*examples:* `x="employees.name"`, `x={["employees", "name"]}`

```js
x: PropTypes.oneOfType([
  PropTypes.func,
  CustomPropTypes.allOfType([CustomPropTypes.integer, CustomPropTypes.nonNegative]),
  PropTypes.string,
  PropTypes.arrayOf(PropTypes.string)
])
```

### `y`

Use `y` data accessor prop to determine how the component defines data in the y dimension. This prop may be given in a variety of formats:

**string:** specify which property in an array of data objects should be used as the y value

*examples:* `y="profit"`

**function:** use a function to translate each element in a data array into a y value

*examples:* `y={(datum) => Math.sin(2 * Math.PI * datum.x)}`

**array index:** specify which index of an array should be used as a y value when data is given as an array of arrays

*examples:* `y={1}`

**path string or path array:** specify which property in an array of nested data objects should be used as a y value

*examples:* `y="employees.salary"`, `y={["employees", "salary"]}`

```js
y: PropTypes.oneOfType([
  PropTypes.func,
  CustomPropTypes.allOfType([CustomPropTypes.integer, CustomPropTypes.nonNegative]),
  PropTypes.string,
  PropTypes.arrayOf(PropTypes.string)
])
```

### `y0`

Use `y0` data accessor prop to determine how the component defines the baseline y0 data. This prop is useful for defining custom baselines for components like `VictoryBar` or `VictoryArea`. This prop may be given in a variety of formats.

**string:** specify which property in an array of data objects should be used as the y0 value

*examples:* `y0="last_quarter_profit"`

**function:** use a function to translate each element in a data array into a y0 value

*examples:* `y0={() => 10}`

**array index:** specify which index of an array should be used as a y0 value when data is given as an array of arrays

*examples:* `y0={1}`

**path string or path array:** specify which property in an array of nested data objects should be used as a y0 value

*examples:* `y0="employees.salary"`, `y0={["employees", "salary"]}`

```js
y0: PropTypes.oneOfType([
  PropTypes.func,
  CustomPropTypes.allOfType([CustomPropTypes.integer, CustomPropTypes.nonNegative]),
  PropTypes.string,
  PropTypes.arrayOf(PropTypes.string)
])
```

[x]: https://formidable.com/open-source/victory/docs/common-props#x
[y]: https://formidable.com/open-source/victory/docs/common-props#y
[grayscale theme]: https://github.com/FormidableLabs/victory-core/blob/master/src/victory-theme/grayscale.js
[Read more about themes here]: https://formidable.com/open-source/victory/guides/themes
[width]: https://formidable.com/open-source/victory/docs/common-props#width
[height]: https://formidable.com/open-source/victory/docs/common-props#height
[`Area` component]: https://formidable.com/open-source/victory/docs/victory-primitives#area
[VictoryLabel]: https://formidable.com/open-source/victory/docs/victory-label
[VictoryPortal]: https://formidable.com/open-source/victory/docs/victory-portal
[VictoryClipContainer]: https://github.com/FormidableLabs/victory-core/blob/master/src/victory-clip-container/victory-clip-container.js
[VictoryAnimation]: https://formidable.com/open-source/victory/docs/victory-animation
[VictoryTransition]: https://formidable.com/open-source/victory/docs/victory-transition
[sortBy]: https://lodash.com/docs/4.17.4#sortBy
