# Access Eash Part Individually:

```javascript
import {LA1} from 'relative/path/to/lambdatt-ui-layout-admin1'

// Components:
LA1.COMPONENTS
// Pages:
LA1.PAGES
```

# Auto-Wire:

To auto-wire the Layout Admin 1 module, add the following to your `/src/boot/la1-boot.js`:

```javascript
import {LA1} from 'relative/path/to/lambdatt-ui-layout-admin1'

export default boot(({ app }) => {
    LA1.autoWire(app);
})
```