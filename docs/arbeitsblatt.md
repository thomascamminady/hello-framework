---
title: Arbeitsblatt
theme: [wide]

---
# A title

some text some text some text some text some text some text  some text some text some text some text some text some text some text some text some text some text some text some text  some text some text some text some text some text some text some text some text some text some text some text some text  some text some text some text some text some text some text some text some text some text some text some text some text  some text some text some text some text some text some text some text some text some text some text some text some text  some text some text some text some text some text some text 


## Subheading

```js
const penguins = FileAttachment("penguins.csv").csv({typed: true});
let errors = []
```

```js
Inputs.table(penguins)
```
```js
penguins
```


## A cool plot

```js
const gerade = view(Inputs.form([
  Inputs.range([-0.05, 0.05], {step: 0.0001, label: "slope"}),
  Inputs.range([100.0, 300.0], {step: 1, label: "offset"}),
]));
```
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
```






```js
Plot.plot({
      title: "How big are penguins, anyway? 🐧",
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
    })
```





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