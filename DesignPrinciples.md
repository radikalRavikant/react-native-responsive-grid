## Design Principles

### _Simple Calculations_

This grid fixes the mental model for grid based layouts by abandoning the format-based, columns-per-view approach (e.g. "12 column grid") and instead allowing the developer to specify the width of each grid column as a percentage of parent view's size, so 10% meams 10 column grid, and 8.333% means a 12 column grid etc. But let's not think in terms of columns per grid! That is a formatting model, not a layout system. There is no reason for a grid to be 11, 12, 13, 14, 15 or 16.6 columns. The number should be determined by actual layout needs, not by some fixed format. Plus, all other style measurements are done using percentages when making layouts that respond linearly to screen size changes, so why should we measure column width as n:12 (or n:11 etc) and yet measure everything else as n:100? It's time to fix this mental impedence mismatch and move beyond the fixed-column grid toward a free-form layout model, one that allows us to leverage the consistent, repeatable, and nestable pattern of Rows and Columns, but in a more fluid and mathematically simpler way. 

### _Simple Layout_

While most React Native developers use `flex: n` (which is based on Facebook's Yoga layout algorithm) rather than the confusing mess of `flexGrow`, `flexShrink` and `flexBasis` (lots has been written about the Flexbox spec and its steep learning curve, e.g. [flex-grow is weird. Or is it?](https://css-tricks.com/flex-grow-is-weird/)) there is still a fundamental problem with using `flex: n` since n is not a percentage of the parent view's computed or explicit width or height (as percentages are in CSS) but a comparative size factor! It's much easier to say the parent view's width or height is 100% and divide that however we like, e.g. 20%, 35% and 45% than to specify n as 0.2, 0.35 and 0.45 because the latter set of values do not correspond to percentages. You can see that by adding a fourth item with some value, e.g. 0.5, which will cause all four elements to be contained in the full width or full height of the parent (width or height depends on parent's flexDirection) so n=0.2 no longer means 20% and n=0.5 no longer means 50%. It just means that the fourth item we added is 2.5 (0.5 divided by 0.2) times wider or taller than the first item. We lose perspective on the item size relative to the size of its parent as Flexbox is concerned with the item sizes relative to each other rather that the size of each item relative to a single parent. It's like O(n) vs O(n^2) complexity for these two different sizing models in that instead of relating the size of each item to the size of its parent as a percentage (n steps), with `flex: n` we relate the size of each item to the size of each other (sibling) item (n^2 steps.) That's because we don't have a single scale (parent's width or height) to measure against. More importantly, we give up direct knowledge of each item's width as a percentage of the parent's width in favor of having comparative size factors for the sibling items. However, there are times when we'd like to have that, so this grid does not take that ability away from us. In fact, this grid relies heavily (under the hood) on Flexbox features like flexDirection, justifyContent, alignItems, and alignContent, but it uses them under the hood and combines them with a simple percentage-based layiut model. This results in a layout system that is simple and predictable, yet powerful.

### _RTL Support_

Sometimes, we lay things out from left to right (LTR.) Other times, we might find it easier to lay things out from right to left (RTL.) I've found that RTL support to be generally lacking in both React and React Native grids, so I've added support for it. React makes it really simple. This can be very useful for apps with right-to-left text, i.e. Arabic, Aramaic, Azeri, Dhivehi/Maldivian, Hebrew, Kurdish (Sorani), Persian/Farsi, and Urdu.

### _Consistency, Repeatability, Nestability_

To keep the grid's structure and design simple (as well as logical and consistent) Rows may not contain other Rows as children. They must be wrapped in a Column inside the row) and Columns may not contain other columns as children. They must be wrapped in a Row inside the column. The "grid" construct has been redued to its essence here, which is a composition of Rows and Columns. 

### _Predictable Dynamic Layout_

Being able to readt to layout changes, including changes due to device rotation (for apps that allow it), is a key aspect of responsive design. This grid is designed to enable dynamic response to layout changes (see the demos at the start of this Readme) 

Columns and Rows have `position: 'relative'` enforced by design to keep them within the layout flow. Each can be moved about within their parent Row and Column, respectively, using top and bottom margins and/or offsets. They can be made to overlap within the row using a negative offset, and overlap across rows using a negative top margin. The intent is to allow movement of rows and columns without taking them out of the layout flow. This is required to work around a Flexbox layout spec glitch/deviation in React Native and to react to make reaction to layout change more predictable.

Elements, including Column elements, must be wapped in a Row in order for the grid to react to layout changes in those elements, including their mounting, un-mounting and re-mounting. You can decide where the re-rendering happens in the component subtree by placing the `layoutEvent` prop at the desired Row node (see layoutEvent demo markup in Introduction.)