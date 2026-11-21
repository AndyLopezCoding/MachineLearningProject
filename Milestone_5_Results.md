# Milestone 5 — Final Experiments and Analysis

**Household Appliance Energy Prediction**  
CSCI 3052U · Andy Lopez, Hasan Nawaz, and Eliseo Marinez

## Evaluation procedure

We used the data to estimate how much energy the appliances used during each 10-minute interval. The inputs came from that same interval. We kept the observations in date order and used the first 13,814 rows for training, the next 2,960 for validation, and the last 2,961 for testing. For tuning, we used five folds where each model trained on earlier data and was checked on the next block. We fit scaling and polynomial preprocessing inside each fold.

We picked the settings with the lowest average MAE across the folds, then trained the selected models again using the training and validation data together. We used that same combined data to calculate the mean and median benchmarks, and evaluated all six methods on the same test block.

For kNN we changed the number of neighbours, for quadratic Ridge we changed the regularization strength, and for the decision tree and random forest we changed the maximum depth. We also tried ordinary linear regression and linear Ridge as reference points. The table shows the setting that did best in cross-validation for each model family. [Experiment log](tuning_results/experiment_log.json) · [All configurations](tuning_results/all_configurations.csv) · [Fold metrics](tuning_results/fold_metrics.csv)

## Main comparison

**Table 1.** Results using all available inputs for the trained models. CV MAE is the average of the five fold MAEs, and SD shows how much the scores varied across those folds. The benchmark rows have a dash because we didn’t run them through the five folds for this comparison. Their test scores come from predicting one constant, the mean or median of the combined training and validation data.

| Model | Selected setting | CV MAE ± SD (Wh) | Test MAE (Wh) | Test RMSE (Wh) | Test R² |
|---|---|---:|---:|---:|---:|
| kNN | 200 neighbours | **57.77 ± 4.40** | 68.59 | 95.41 | −0.102 |
| Random Forest | Maximum depth 1 | 58.64 ± 5.59 | 48.19 | 87.30 | 0.077 |
| Quadratic Ridge | α = 10,000 | 58.82 ± 8.35 | 50.77 | **84.94** | **0.126** |
| Decision Tree | Maximum depth 1 | 59.29 ± 5.68 | 48.24 | 87.31 | 0.077 |
| Mean benchmark | Training + validation mean | — | 52.40 | 90.88 | −0.00010 |
| Median benchmark | Training + validation median | — | **43.26** | 98.09 | −0.165 |

We picked kNN because it had the lowest average CV MAE, but its test MAE ended up at 68.59 Wh, the highest in the table. The median benchmark had the lowest test MAE at 43.26 Wh. Random Forest was the best trained model by MAE at 48.19 Wh, it was only 0.05 Wh ahead of the decision tree. Quadratic Ridge had the lowest RMSE and highest R². RMSE puts more weight on big misses, so the model with the best MAE can still rank lower on RMSE. [Main results CSV](tuning_results/main_results.csv)

## Error analysis during high consumption

**Table 2.** Test errors for intervals at or above 340 Wh. We picked 340 Wh using the 95th percentile of the training data. A total of 107 test intervals met that cutoff (3.6% of the test set). Signed error is the prediction minus the actual reading.

| Model | Peak MAE (Wh) | Mean signed error (Wh) | Underestimated |
|---|---:|---:|---:|
| kNN | **327.30** | −327.30 | 100% |
| Quadratic Ridge | 338.01 | −338.01 | 100% |
| Decision Tree | 355.84 | −355.84 | 100% |
| Random Forest | 355.93 | −355.93 | 100% |
| Mean benchmark | 374.69 | −374.69 | 100% |
| Median benchmark | 412.52 | −412.52 | 100% |

kNN did best on the high-use intervals with a peak MAE of 327.30 Wh, which is still a pretty large miss. The median benchmark did best overall by MAE but had the highest peak error. All six methods predicted too low on all 107 of these intervals. One example was May 26, 2016 at 16:40, the actual reading was 850 Wh and kNN only predicted 115.70 Wh, so it was off by 734.30 Wh. [Peak metrics](tuning_results/test_peak_metrics.csv) · [Largest peak errors by model](tuning_results/test_peak_examples.csv)

## Feature ablation

To see which inputs mattered, we removed groups of features and trained the models again with the same settings and the same five folds. We compared each run to that model’s score with all 27 inputs. A negative change means the error went down after we removed the input.

**Table 3.** Effect of removing the lighting input.

| Model | All inputs CV MAE (Wh) | Without lighting CV MAE (Wh) | Change (Wh) |
|---|---:|---:|---:|
| kNN | 57.77 | 60.29 | +2.52 |
| Random Forest | 58.64 | 55.54 | −3.10 |
| Quadratic Ridge | 58.82 | 58.99 | +0.17 |
| Decision Tree | 59.29 | 55.49 | −3.79 |

Taking lighting out helped the decision tree and random forest in three folds and made no difference in the other two. The decision tree’s average error dropped by 3.79 Wh, the biggest change in the ablation. For kNN the error went up in four of the five folds when we removed lighting. We also tried kNN with just hour, day of week, and lighting, and that brought its average CV MAE down from 57.77 Wh to 55.04 Wh. These were all cross-validation results using the training data. [Ablation summary](tuning_results/ablation_summary.csv) · [Ablation fold metrics](tuning_results/ablation_fold_metrics.csv)

## Residual analysis
From the residual plots we made, the results obtained show that the models end up producing a wide range of errors across the test observations(Has large amount of underprediction at high energy values). We mostly got positive points because the actual test readings are often higher up than the predicted values(especially for the largest observations). The plots spread by quite a fair margin around zero versus having a more consistently accurate or an unbiased pattern.

This finding supports our previous peak-load results, that the overall MAE doesn't really capture how severely the models end up missing uncommon high-consumption periods. With this residual analysis concludes that the average performance hides systematic underprediction in the higher consumption range.



## Conclusions and limitations

Our main takeaway is that the median was hard to beat on overall test MAE, but the peak results show how much that score can hide. Even kNN, which did best during peaks, missed them by a lot. We also saw that a feature like lighting can help one model and hurt another.

The data came from one household between January and May 2016, so we don’t know how well these models would do in other homes or at other times of year. Our inputs were measured during the same 10-minute period we were estimating, and lighting might not always be available. We had already looked at this test period in earlier runs, so these test scores should be read as a follow-up comparison. The folds also shared some of their training data, so the SD gives an idea of how much the scores changed across time periods.