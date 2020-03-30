When exporting one of these to HTML, execute the following lines in console:

```javascript
document.querySelectorAll(".CodeMirror-scroll").forEach(elem => elem.style.width="100vw")
document.querySelectorAll(".CodeMirror.cm-s-inner").forEach(elem => elem.style.overflowX="scroll");

```

It'll make the code blocks scroll, which is quite nice I think.