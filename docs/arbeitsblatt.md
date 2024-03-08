---
title: Arbeitsblatt
theme: [wide]

---
# Titel, einfach via Markdown

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aliquam laoreet aliquam fermentum. In hac habitasse platea dictumst. Integer a iaculis massa, eu venenatis arcu. Sed congue ultricies elit et mollis. Maecenas pretium, tellus ut venenatis consequat, lacus ante vulputate neque, ut imperdiet nunc magna eget velit. Suspendisse potenti. Morbi sed sodales erat. Proin ipsum nisi, facilisis in nibh ullamcorper, condimentum rutrum tortor. Fusce consectetur pellentesque turpis sit amet bibendum. Integer ut egestas massa. In eget iaculis orci, ut suscipit justo.

Donec finibus velit eget erat blandit, at auctor quam ornare. Phasellus et odio mi. Duis nibh sem, convallis vitae semper in, luctus a libero. Ut euismod ac nulla rhoncus commodo. Ut posuere egestas bibendum. Cras tristique, arcu id facilisis tincidunt, mi mi commodo leo, non vestibulum ipsum metus non lectus. Phasellus sit amet ullamcorper nunc. Orci varius natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Praesent lacus nulla, bibendum vitae tortor sit amet, placerat elementum lacus. Maecenas sodales odio eu bibendum varius. Fusce elementum venenatis ipsum, et malesuada elit hendrerit quis. Cras sit amet magna nec purus rutrum semper. Donec scelerisque pellentesque augue, in tempus est scelerisque ut. Suspendisse varius scelerisque nisl eget tincidunt. Fusce imperdiet tincidunt ante, malesuada lacinia lorem varius ac. Quisque ex velit, porttitor quis elit in, cursus ornare eros.



## Subtitle

Hier ist eine Tabelle. Die Daten wurden von einer CSV Datei gelesen.
```js
const penguins = FileAttachment("penguins.csv").csv({typed: true});
let errors = []
```

```js
Inputs.table(penguins)
```



## Interaktiv geht auch

Wir können die Steigung und den y-Abschnitt der Gerade hier variieren. 
```js
const gerade = view(Inputs.form([
  Inputs.range([-0.05, 0.05], {step: 0.0001, label: "slope"}),
  Inputs.range([100.0, 300.0], {step: 1, label: "offset"}),
]));
```

Und dann packen wir diese Gerade in ein Chart mit den jeweiligen Errors als vertikale Linien.
```js
const gerade_array = [
    {"body_mass_g":0,"flipper_length_mm":gerade[1]},
    {"body_mass_g":10000,"flipper_length_mm":gerade[1]+gerade[0]*10000.0}
]
let error = 0
for (let step = 0; step < penguins.length; step++){
    penguins[step]["intersection"] = gerade[1] + gerade[0]*penguins[step]["body_mass_g"]
    penguins[step]["error"] = (penguins[step]["intersection"] - penguins[step]["flipper_length_mm"])
    error = error + Math.abs(penguins[step]["error"])

}
errors.push({"slope":gerade[0],"offset":gerade[1],"error":Math.log(error)})


let min = Math.min(...errors.map(item => item.error))
let results = errors.filter(item => item.error === min)




view(

Plot.plot({
      title: "Süße kleine 🐧",
      width,
      grid: true,
      x: {label: "Body mass (g)",domain:[2000,7000]},
      y: {label: "Flipper length (mm)",domain:[150,250]},
      color: {legend: true},
      marks: [
        // Plot.linearRegressionY(penguins, {x: "body_mass_g", y: "flipper_length_mm", stroke: "species"}),
        Plot.ruleX(penguins, {x: "body_mass_g", y1: "flipper_length_mm",y2:"intersection", stroke: "species", tip: true}),
        Plot.dot(penguins, {x: "body_mass_g", y: "flipper_length_mm", stroke: "species", tip: true}),
        Plot.line(gerade_array,{x: "body_mass_g", y: "flipper_length_mm"})
        
      ]
    }))
```


Nur zum Spaß ist hier noch ein Chart. Das hier plotted den Fehler den man bekommt vs. die Parameter.


```js
Plot.plot({
      title: `gerade: (m,n) =(${gerade})`,
      grid: true,
     
      color: {legend: true,scheme:"inferno"},
      marks: [        
        Plot.dot(errors, {x: "slope", y: "offset", stroke: "error",fill:"error", tip: true}),
        Plot.dot(errors.slice(-1), {x: "slope", y: "offset", stroke: "error",fill:"error", "r":5,tip: true}),
        Plot.dot(results, {x: "slope", y: "offset", stroke: "error","r":15,tip: true}),
       
      ]
    })
```

## Was kann man alles mit Observable Framework machen?

```js
const colors = view(Inputs.radio(["red", "green", "blue"], {value:"red",label: "Was ist deine Lieblingsfarbe"}));
```

Und dann kann man die variable im Text nutzen: ${colors} ist eine tolle Farbe.


Oder eine Textbox.
```js
const bio = view(Inputs.textarea({label: "Biography", placeholder: "What’s your story?"}));
```


### Latex geht auch
```tex
\Delta E^*_{00} = \sqrt{
  \Big(\frac{\Delta L'}{k_LS_L}\Big)^2 +
  \Big(\frac{\Delta C'}{k_CS_C}\Big)^2 +
  \Big(\frac{\Delta H'}{k_HS_H}\Big)^2 +
  R_T
  \frac{\Delta C'}{k_CS_C}
  \frac{\Delta H'}{k_HS_H}}
  ```



  ### D3.js für plots ist kompliziert aber mächtig
```js
await visibility(); // wait until this node is visible

const width = 640;
const height = 640;
const color = d3.scaleOrdinal(d3.schemeCategory10);

// Copy the data to protect against mutation by d3.forceSimulation.
const links = data.links.map((d) => Object.create(d));
const nodes = data.nodes.map((d) => Object.create(d));

const simulation = d3.forceSimulation(nodes)
    .force("link", d3.forceLink(links).id((d) => d.id))
    .force("charge", d3.forceManyBody())
    .force("center", d3.forceCenter(width / 2, height / 2))
    .on("tick", ticked);

const svg = d3.create("svg")
    .attr("width", width)
    .attr("height", height)
    .attr("viewBox", [0, 0, width, height])
    .attr("style", "max-width: 100%; height: auto;");

const link = svg.append("g")
    .attr("stroke", "var(--theme-foreground-faint)")
    .attr("stroke-opacity", 0.6)
  .selectAll("line")
  .data(links)
  .join("line")
    .attr("stroke-width", (d) => Math.sqrt(d.value));

const node = svg.append("g")
    .attr("stroke", "var(--theme-background)")
    .attr("stroke-width", 1.5)
  .selectAll("circle")
  .data(nodes)
  .join("circle")
    .attr("r", 5)
    .attr("fill", (d) => color(d.group))
    .call(drag(simulation));

node.append("title")
    .text((d) => d.id);

function ticked() {
  link
      .attr("x1", (d) => d.source.x)
      .attr("y1", (d) => d.source.y)
      .attr("x2", (d) => d.target.x)
      .attr("y2", (d) => d.target.y);

  node
      .attr("cx", (d) => d.x)
      .attr("cy", (d) => d.y);
}

display(svg.node());
```

```js
function drag(simulation) {

  function dragstarted(event) {
    if (!event.active) simulation.alphaTarget(0.3).restart();
    event.subject.fx = event.subject.x;
    event.subject.fy = event.subject.y;
  }

  function dragged(event) {
    event.subject.fx = event.x;
    event.subject.fy = event.y;
  }

  function dragended(event) {
    if (!event.active) simulation.alphaTarget(0);
    event.subject.fx = null;
    event.subject.fy = null;
  }

  return d3.drag()
      .on("start", dragstarted)
      .on("drag", dragged)
      .on("end", dragended);
}
```

```js
const data = FileAttachment("miserables.json").json();
```
