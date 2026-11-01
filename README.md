# Fitness & Mortality Calculator

An open-source calculator for exploring the relationship between physical fitness and all-cause mortality. It supports VO₂ max and grip strength and expresses the modeled differences in annual mortality, remaining life expectancy, and equivalent risks from other activities.

**[Open the calculator](https://aeftimia.github.io/fitness-mortality/)** · **[Methodology and sources](https://aeftimia.github.io/fitness-mortality/methodology.html)**

## What it does

Enter your age, sex, and a fitness measurement. You can choose VO₂ max in mL/kg/min or grip strength in kg and optionally include health conditions and smoking status.

The calculator estimates your percentile among adults of the same age and sex, normalizes a continuous fitness hazard model against the reference fitness distribution, and applies that multiplier to US population mortality rates.

The results include annual mortality estimates, ranges derived from the published fitness hazard ratio confidence intervals, a plot of mortality across fitness levels, and comparisons with other fitness percentiles. It also estimates remaining life expectancy and expresses annual mortality differences as equivalent numbers of BASE jumps, skydives, and general anesthesias.

Each calculation uses one fitness metric. VO₂ max and grip strength have separate reference distributions and mortality associations.

## Fitness metrics and sources

| Metric | Reference distribution | Mortality association |
|---|---|---|
| VO₂ max | FRIEND 2022 age and sex norms | Kokkinos et al. 2022, HR 0.86 per additional MET, with 1 MET equal to 3.5 mL/kg/min |
| Grip strength | International iGRIPS norms from Tomkinson et al. 2024 | Celis-Morales et al. 2018, HR 1.16 for men and 1.20 for women per 5 kg lower grip strength |

The grip strength model uses sex-specific hazard ratios. Both models interpolate the published fitness percentiles across age and fitness level and normalize the resulting hazard multipliers separately for each age, sex, and hazard ratio estimate.

The embedded mortality data come from the CDC/NCHS *United States Life Tables, 2021*. The data file contains exact annual mortality probabilities for ages 0–99 and extrapolates ages 100–119. Optional risk factors include type 2 diabetes, hypertension, current or former smoking, and obesity class.

The [methodology](methodology.html) explains the interpolation, normalization, life expectancy calculation, assumptions, and sources. Citation metadata is stored in [js/data/citations.js](js/data/citations.js).

## Model in brief

For a fitness measurement `B`, the model uses a published hazard ratio `r` per unit `u`.

```text
rawHR(B) = r^(B / u)
fitnessHR(B) = rawHR(B) / E[rawHR | age, sex]
qUser = qPopulation(age, sex) × fitnessHR(B) × product(selected risk factor HRs)
```

The expectation is taken over the reference fitness distribution for the selected age and sex. This makes the average fitness multiplier equal to one and preserves the population mortality baseline before optional risk factor adjustments. Each confidence interval bound has its own normalization.

Remaining life expectancy is computed from survival across future ages while holding the current proportional mortality multiplier constant. Risk equivalents divide the difference in annual mortality probability between two fitness levels by the mortality probability per event.

## Run locally

The site uses HTML, CSS, and vanilla JavaScript. It has no application build step, and calculations run in the browser.

Clone the repository and open `index.html`, or serve the directory with a local static server.

```sh
git clone https://github.com/aeftimia/fitness-mortality.git
cd fitness-mortality
python3 -m http.server 8000
```

Open [localhost:8000](http://localhost:8000). Chart.js and the methodology page's KaTeX assets load from CDNs.

## Verification

Open the calculator and run the existing model suites in the browser's developer console.

```js
testContinuousModel()
testGripModel()
```

Each suite logs its results and returns `true` when all checks pass. The checks cover data loading, percentile lookup, monotonicity, population normalization, and integration with the mortality engine.

Also check both metric options in the interface and confirm that the units, reference percentiles, chart, risk equivalents, and life expectancy results update correctly.

## Limitations

- The mortality associations come from observational studies. Confounding and reverse causality can affect the estimates.
- Fitness measurements are proxies for broader health. A modeled difference between fitness levels does not establish the benefit of a particular exercise program.
- The source populations differ. VO₂ max mortality estimates come from clinical exercise testing, grip strength estimates come from UK Biobank, and the fitness norms and mortality baseline come from other populations.
- The model applies a constant hazard ratio per fitness unit and multiplies optional risk factor adjustments independently.
- Life expectancy estimates assume the current proportional mortality multiplier persists across future ages.
- The displayed ranges reflect uncertainty in the published fitness hazard ratios. They do not capture all uncertainty in the measurements, source populations, or modeling assumptions.
- Risk equivalents compare mortality probabilities. Activities also differ in injury risk, timing, and other consequences.

This calculator is for education and research. It is not medical advice.

## Contributing

Improvements to data sources, model validation, risk comparisons, and accessibility are welcome. Please open an issue before submitting large changes and provide a DOI or source URL for new data.

## License

MIT. US federal government mortality data are public domain. Other data are used per their respective licenses for educational purposes with attribution.
