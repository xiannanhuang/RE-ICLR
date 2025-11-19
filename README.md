The novelty of ADAPT-Z seems limited as its design combines adapters and historical gradients, which are extensively studied in Parameter-Efficient Fine-Tuning (PEFT) and continual learning, respectively. The contribution is more of an engineering integration rather than a novel contribution. The authors should further emphasize their main contribution.

in Table 2, the reported performance for baselines FSNet and OneNet is highly questionable. Their MSEs are orders of magnitude worse than other methods and drastically inconsistent with their original papers. Such a stark discrepancy suggests potential issues with their implementation or the experimental setup, making a fair comparison questionable. The authors should clarify this significant discrepancy.

In Section 4.3.3, the authors claim that the performance of ADAPT-Z is correlated to the choice of feature layer and the optimal layers varying across datasets. However, the paper lacks a deeper analysis of the correlation between dataset characteristics and the best feature layer, leaving this critical design choice largely unexplained.

More recent prediction models should be compared to further validate the effectiveness of ADAPT-Z, e.g., TimeFilter [1] and TimeKAN [2].

# Novelty
Thank you for your suggestion.
Essentially, most online deployment or Parameter-Efficient Fine-Tuning (PEFT) work for time series prediction needs to address two issues: which parameters to update and how to update them. We propose that changes in data distribution stem from shifts in the underlying latent variables that determine the distribution. Therefore, we avoid the concept of updating parameters and propose a method to update feature representations instead. And we propose to use current features and historical gradients to update the features in order to alleviate the delayed feedback issue present in multi-step prediction.

# Performance for baselines FSNet and OneNet
Thank you for your valuable feedback.
In the original OneNet and FSNet papers, the reported results were based on experiments with data leakage. Specifically, when predicting the next k steps at time t, these methods use the model’s predictions for steps t+1 to t+k, as well as the true values for steps t+1 to t+k to update parameters. But the true values for steps t+1 to t+k are not available at step t. This issue is also mentioned in the DSOF paper. Additionally, that paper conducted experiments without data leakage. In their Table 2, the results for FSNet and OneNet differ by orders of magnitude compared to the original models. To ensure a fair comparison, our experiments on OneNet and FSNet are entirely based on the data leakage-free version of the code provided in the official DSOF repository. 

**Reference**

Lau, Ying-yee Ava, Zhiwen Shao, and Dit-Yan Yeung. "Fast and Slow Streams for Online Time Series Forecasting Without Information Leakage." The Thirteenth International Conference on Learning Representations. 2025. (DSOF)

# Feature lacation
Thank you for raising this question.
To further investigate which feature layers are more suitable for our method, We computed the Root Mean Square Difference (RMSD) of the feature gradients. This metric is related to the stability of gradients across different time steps. Additionally, we calculated the Mean Absolute Value of the feature gradients, which, to some extent, reflects the severity of the distribution shift. We examined whether these statistics correlate with the performance of ADAPT-Z. The results are shown below. It can be observed that RMSD has a clear positive correlation with the final MSE. This suggests that during deployment, more stable gradients lead to better performance.
|      | H   | 1       | 24      | 48      |
|------|-----|---------|---------|---------|
| **electricity** | mean  | 0.1243  | 0.2482  | 0.1239  |
|      | RMSD | 0.5432  | 0.4119  | 0.4790  |
| **PEMS03**  | mean  | -0.3240 | 0.2934  | 0.1528  |
|      | RMSD | 0.5382  | 0.6253  | 0.4921  |
| **PEMS07**  | mean  | 0.1943  | -0.0835 | 0.0832  |
|      | RMSD | 0.6341  | 0.5612  | 0.6267  |
| **solar**   | mean  | 0.1432  | 0.2732  | 0.3854  |
|      | RMSD | 0.6390  | 0.5192  | 0.5715  |
| **weather** | mean  | 0.0211  | -0.0982 | 0.1693  |
|      | RMSD | 0.4836  | 0.4293  | 0.6923  |

# More base model
We conducted additional experiments on the ETT dataset using TimeFilter and TimeKAN. The results are as follows:
Results for TimeFilter:
| Dataset | H  | Ori    | fOGD   | OGD    | DSOF   | SOILD  | ADCSD  | Proceed | ADAPT-Z | IMP    |
|---------|----|--------|--------|--------|--------|--------|--------|---------|---------|--------|
| **ETTh1** | 1  | 0.1201 | 0.1172 | 0.1193 | 0.1205 | 0.1192 | 0.1186 | 0.1199  | **0.1107** | 7.78%  |
|         | 24 | 0.3159 | 0.3127 | 0.3154 | 0.3193 | 0.3158 | 0.3154 | 0.3118  | **0.2942** | 6.86%  |
|         | 48 | 0.3654 | 0.3528 | 0.3638 | 0.3633 | 0.3606 | 0.3636 | 0.3506  | **0.3431** | 6.10%  |
| **ETTh2** | 1  | 0.0666 | 0.0647 | 0.0681 | 0.0661 | 0.0670 | 0.0669 | 0.0660  | **0.0628** | 5.66%  |
|         | 24 | 0.1712 | 0.1698 | 0.1711 | 0.1743 | 0.1703 | 0.1699 | 0.1702  | **0.1607** | 6.11%  |
|         | 48 | 0.2246 | 0.2217 | 0.2217 | 0.2218 | 0.2191 | 0.2217 | 0.2191  | **0.2153** | 4.16%  |
| **ETTm1** | 1  | 0.0549 | 0.0527 | 0.0529 | 0.0738 | 0.0526 | 0.0528 | 0.0527  | **0.0509** | 7.22%  |
|         | 24 | 0.2482 | 0.2447 | 0.2495 | 0.2726 | 0.2382 | 0.2466 | 0.2376  | **0.2292** | 7.65%  |
|         | 48 | 0.3362 | 0.3266 | 0.3370 | 0.3573 | 0.3195 | 0.3276 | 0.3189  | **0.3073** | 8.60%  |
| **ETTm2** | 1  | 0.0317 | 0.0313 | 0.0314 | 0.0447 | 0.0313 | 0.0313 | 0.0311  | **0.0307** | 3.15%  |
|         | 24 | 0.1062 | 0.1051 | 0.1065 | 0.1352 | 0.1065 | 0.1058 | 0.1065  | **0.0997** | 6.12%  |
|         | 48 | 0.1388 | 0.1347 | 0.1369 | 0.1958 | 0.1350 | 0.1348 | 0.1347  | **0.1339** | 3.53%  |


Results for TimeKAN:


| Dataset | H  | Ori    | fOGD   | OGD    | DSOF   | SOILD  | ADCSD  | Proceed | ADAPT-Z | IMP    |
|---------|----|--------|--------|--------|--------|--------|--------|---------|---------|--------|
| **ETTh1** | 1  | 0.1277 | 0.1235 | 0.1238 | 0.1204 | 0.1194 | 0.1242 | 0.1198  | **0.1177** | 7.78%  |
|         | 24 | 0.3215 | 0.3151 | 0.3134 | 0.3170 | 0.3182 | 0.3179 | 0.3130  | **0.3042** | 5.40%  |
|         | 48 | 0.3652 | 0.3517 | 0.3537 | 0.3632 | 0.3482 | 0.3534 | 0.3483  | **0.3431** | 6.06%  |
| **ETTh2** | 1  | 0.0679 | 0.0673 | 0.0675 | 0.0643 | 0.0660 | 0.0670 | 0.0660  | **0.0628** | 7.55%  |
|         | 24 | 0.1779 | 0.1718 | 0.1725 | 0.1763 | 0.1691 | 0.1728 | 0.1689  | **0.1667** | 6.28%  |
|         | 48 | 0.2205 | 0.2182 | 0.2189 | 0.2447 | 0.2173 | 0.2189 | 0.2173  | **0.2103** | 4.63%  |
| **ETTm1** | 1  | 0.0581 | 0.0560 | 0.0568 | 0.0783 | 0.0569 | 0.0578 | 0.0564  | **0.0529** | 8.96%  |
|         | 24 | 0.2705 | 0.2618 | 0.2678 | 0.3055 | 0.2615 | 0.2661 | 0.2616  | **0.2458** | 9.14%  |
|         | 48 | 0.3393 | 0.3253 | 0.3299 | 0.3918 | 0.3177 | 0.3277 | 0.3175  | **0.3113** | 8.27%  |
| **ETTm2** | 1  | 0.0320 | 0.0316 | 0.0317 | 0.0453 | 0.0318 | 0.0317 | 0.0316  | **0.0309** | 3.44%  |
|         | 24 | 0.1100 | 0.1090 | 0.1084 | 0.1458 | 0.1082 | 0.1084 | 0.1087  | **0.1039** | 5.55%  |
|         | 48 | 0.1481 | 0.1422 | 0.1423 | 0.1824 | 0.1424 | 0.1436 | 0.1423  | **0.1419** | 4.22%  |


