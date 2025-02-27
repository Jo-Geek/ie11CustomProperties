# CSS Variables for IE11
A real polyfill for Custom Properties in IE11


## It can:
- chaining `--bar:var(--foo)`
- fallback `var(--color, blue)`
- :focus, :target, :hover
- handle dynamic added html-content
- handle dynamic added `<style>`, `<link>`-elements
- js-integration:
    - `style.setProperty('--x','y')`
    - `style.getPropertyValue('--x')`
    - `getComputedStyle(el).getPropertyValue('--inherited')`
    - `CSS.registerProperty({name:'--red', inherit:false, initialValue:'#e33'})`
- style-attributes `<div ie-style="--foo:bar"...`
- cascade works
- inheritance works
- under 3k (min+gzip) and dependency-free

## Demo:
https://rawcdn.githack.com/nuxodin/ie11CustomProperties/14f6b0acba054d2c693da44bd7857761d289987e/test.html

## How it works
The script makes use of the fact that IE has minimal custom properties support where properties can be defined and read out with the cascade in mind. This is not possible with properties starting with double dashes.  
`.myEl {-ie-test:'aaa'} // only one dash allowed! "-"`  
then you can read it in IE with javascript:  
`getComputedStyle( querySelector('.myEl') )['-ie-test']`  
In the raw CSS, it replaces for example `--foo` with `-ie-foo`.
It searches for all rules containing variable getters and setter, remembers the affected selectors so future affected Elements can be found in a mutation observer.
Each affected Element gets a uniq class-attribute and its own style-sheet to draw the Element.  
These are the steps that the script does:  
1. given the CSS  
```css
header { --myColor:red; }  
main { --myColor:green; }  
li { color:var(--myColor); }
```
2. rewritten CSS  
```css
header { -ie-myColor:red; }  
main { -ie-myColor:green; }  
li { -ieHasVar-color:var(-ie-myColor); }
```
3. find all affected Element and get its property-values
```js
querySelectorAll('li').forEach(function(){
    var color = getComputedStyle(this).getPropertyValue('--myColor');
    // getPropertyValue is extended to handle custom properties
    // draw_the_Element()
})
```
3. draw Elements, this leads in separate rules for each Element
```css
li.iecp-u1 { color:red; }
li.iecp-u2 { color:red; }
li.iecp-u3 { color:green; }
li.iecp-u4 { color:green; }
```
[![custom-properties-ie11.png](https://i.postimg.cc/bryMt02N/custom-properties-ie11.png)](https://postimg.cc/gX7N9ZDf)


## Limitations
#### styles in element-attributes
There is no way to get the raw content of style-attributes in IE11.
Use `<div style="--color:blue" ie-style="--color:blue">` for it

#### specificity for properties containing "var()"
...is always little highter, cause each selector gets an additional class-selector
eg. `#header` results in `#header.iecp_u44`


## Help wanted!
- Add a ⭐️  
- Test, report bugs and send pull requests.  
- Tweet about if you like it.  
- Vote this solution at stackoverflow https://stackoverflow.com/a/57000437/4865307  
