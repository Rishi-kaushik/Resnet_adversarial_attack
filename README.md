## About Project

We tested different adversarial attack stratergies on resnet-34 and tested how well the attacks transfer to other models

## Observations & Insights

| Model / Set                    | Top‑1 | Top‑5 | Δ Top‑1 vs Orig |
|--------------------------------|-------|-------|-----------------|
| **ResNet‑34 (white‑box)**      |       |       |                 |
| Original                       | **76.0 %** | **94.2 %** | — |
| FGSM ‖ ε∞ = 0.02               | 6.2 % | 35.4 % | **−69.8 pp** |
| FGM‑L2 ‖ ε₂ ≈ 0.02             | 74.4 % | 93.4 % | −1.6 pp |
| FGM‑L2 ‖ ε₂ = ε∞·√d (≈ 24.5)    | 6.0 % | 35.0 % | **−70.0 pp** |
| PGD‑∞ × 10 ‖ ε∞ = 0.02         | **0.2 %** | 12.4 % | **−75.8 pp** |
| Patch‑PGD 32² ‖ ε∞ = 0.3       | 46.6 % | ~82 % | −30 pp |
| **DenseNet‑121 (transfer)**    |       |       |                 |
| Original                       | **74.8 %** | **93.6 %** | — |
| FGSM (∞) from ResNet           | 70.8 % | 92.2 % | −4.0 pp |
| FGM‑L2 ‖ ε₂ ≈ 0.02             | 74.4 % | 93.4 % | −0.4 pp |
| FGM‑L2 ‖ ε₂ = ε∞·√d            | **67.2 %** | 91.4 % | **−7.6 pp** |
| PGD‑∞ × 10 from ResNet         | 73.0 % | 93.2 % | −1.8 pp |
| Patch‑PGD 32²                  | 73.6 % | 92.4 % | −1.2 pp |

*pp = percentage‑point drop*

### Key take‑aways

* **PGD‑∞ is catastrophic in white‑box** – ResNet‑34 collapses to *0.2 %* top‑1 after only 10 steps.

* **L₂ vs L∞ budgets**  
  *Using the *same* ε (0.02), L₂‑FGM barely changes accuracy (−1.6 pp).  
  Scaling L₂ by √d to equalize energy (ε₂ ≈ 24.5) makes it as effective as FGSM‑∞.*

* **Patch attack trade‑off**  
  Limiting perturbation to a 32 × 32 square decreases the effectiveness of the attack but we still observe a **30 pp** drop in accuracy.  
  Heat‑maps show noise is confined to the patch; logits often flip only when the patch covers salient object pixels.

* **Transfer robustness**  
  DenseNet‑121 loses at most **7.6 pp** (scaled L₂); PGD‑∞ and patch attacks hardly hurt it.  
  Gradient alignment across architectures is weak without momentum / input diversity.

* **Visualization sanity‑check**  
  Difference panels confirm pixel changes respect the ε‑ball; top‑5 readouts show either (i) confidence collapse to a wrong class or (ii) rank‑order swap of visually similar classes.
