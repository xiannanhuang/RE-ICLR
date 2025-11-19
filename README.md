The novelty of ADAPT-Z seems limited as its design combines adapters and historical gradients, which are extensively studied in Parameter-Efficient Fine-Tuning (PEFT) and continual learning, respectively. The contribution is more of an engineering integration rather than a novel contribution. The authors should further emphasize their main contribution.

in Table 2, the reported performance for baselines FSNet and OneNet is highly questionable. Their MSEs are orders of magnitude worse than other methods and drastically inconsistent with their original papers. Such a stark discrepancy suggests potential issues with their implementation or the experimental setup, making a fair comparison questionable. The authors should clarify this significant discrepancy.

In Section 4.3.3, the authors claim that the performance of ADAPT-Z is correlated to the choice of feature layer and the optimal layers varying across datasets. However, the paper lacks a deeper analysis of the correlation between dataset characteristics and the best feature layer, leaving this critical design choice largely unexplained.

More recent prediction models should be compared to further validate the effectiveness of ADAPT-Z, e.g., TimeFilter [1] and TimeKAN [2].

## Q1: Novelty
Thank you for your suggestion.
Essentially, most online deployment or Parameter-Efficient Fine-Tuning (PEFT) work for time series prediction needs to address two issues: which parameters to update and how to update them. We propose that changes in data distribution stem from shifts in the underlying latent variables that determine the distribution. Therefore, we avoid the concept of updating parameters and propose a method to update feature representations instead. And we propose to use current features and historical gradients to update the features in order to alleviate the delayed feedback issue present in multi-step prediction.

## Q2: Performance for baselines FSNet and OneNet
Thank you for your valuable feedback.
In the original OneNet and FSNet papers, the reported results were based on experiments with data leakage. Specifically, when predicting the next k steps at time t, these methods use the model’s predictions for steps t+1 to t+k, as well as the true values for steps t+1 to t+k to update parameters. But the true values for steps t+1 to t+k are not available at step t. This issue is also mentioned in the DSOF paper. Additionally, that paper conducted experiments without data leakage. In their Table 2, the results for FSNet and OneNet differ by orders of magnitude compared to the original models. To ensure a fair comparison, our experiments on OneNet and FSNet are entirely based on the data leakage-free version of the code provided in the official DSOF repository. 

## Q3: Feature lacation
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

## Q4: More base model
We conducted additional experiments on the ETT datasets using TimeFilter and TimeKAN. The results are as follows:

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


**Reference**

Lau, Ying-yee Ava, Zhiwen Shao, and Dit-Yan Yeung. "Fast and Slow Streams for Online Time Series Forecasting Without Information Leakage." The Thirteenth International Conference on Learning Representations. 2025. (DSOF)


# Reviewer 2
The paper identifies concept drift as a key limitation of existing research schemes, but the method does not explain how ADAPT-Z addresses it, making it unclear why concept drift is mentioned.

For the main results, the metric used for evaluation is MSE. Could the performance of various methods also be examined using MAE?

In section 4.3.3, the paper explores the performance impact of extracting feature from different regions of iTransformer. Could this exploration be extended to feature locations in other models? For instance, the models used in this paper, such as SOFTS and TimesNet. Based on the best-performing feature locations across several models, could any commonalities be identified?

In section 4.3.6, the paper states: "our shorter prediction horizons (1-48 steps) experience more volatile distribution shifts. This instability makes it challenging for normalization method to consistently align input-output distributions across diverse time segments." Could the stable improvement of combining ADAPT-Z with normalization methods be explored under longer prediction horizons? Simultaneously, could ADAPT-Z's leading performance in forecasting tasks be examined under these longer prediction horizons as well?
The idea is quite simple. Can you clarify the novelty lies in the proposed method?
## Q1: concept drift
Thank you for your comment. ADAPT-Z implicitly handles concept drift through feature-space correction. Its core mechanism works as follows: The prediction model is divided into an encoder (f) and a prediction head (g). The encoder extracts feature representations z from observed data, while the prediction head uses z for forecasting. Due to model obsolescence, the prediction g(z) often differs from the true value y. Therefore, ADAPT-Z finds a correction term $\delta$ such that g(z+$\delta$) approximates y. This approach essentially adjusts feature representations to adapt to changes in the current data distribution, thereby mitigating concept drift. Although the term "concept drift" is not explicitly used in the method description, ADAPT-Z's entire design aims to handle distribution shifts, including concept drift. It keeps the model up-to-date through feature adjustment and online learning, enabling it to adapt to distribution changes.

## Q2 MAE
Thank you for your feedback.
Our method also outperforms existing approaches on the MAE metric. The reason we did not include MAE results in the main text was to keep the table manageable. We report the  MAE on the ETT datasets in the table below (The reported MAE is the average value for three prediction models).

| Dataset | H  | Ori    | fOGD   | OGD    | DSOF   | SOILD  | ADCSD  | Proceed | ADAPT-Z |
|---------|----|--------|--------|--------|--------|--------|--------|---------|---------|
| **ETTh1** | 1  | 0.2357 | 0.2336 | 0.2326 | 0.2458 | 0.2346 | 0.2346 | 0.2346  | **0.2262** |
|         | 24 | 0.3856 | 0.3835 | 0.3845 | 0.3968 | 0.3847 | 0.3853 | 0.3846  | **0.3725** |
|         | 48 | 0.4162 | 0.4141 | 0.4151 | 0.4274 | 0.4161 | 0.4151 | 0.4153  | **0.4037** |
| **ETTh2** | 1  | 0.1824 | 0.1805 | 0.1795 | 0.1887 | 0.1805 | 0.1824 | 0.1817  | **0.1735** |
|         | 24 | 0.2845 | 0.2825 | 0.2825 | 0.2917 | 0.2844 | 0.2837 | 0.2840  | **0.2785** |
|         | 48 | 0.3239 | 0.3223 | 0.3231 | 0.3315 | 0.3228 | 0.3223 | 0.3213  | **0.3133** |
| **ETTm1** | 1  | 0.1513 | 0.1499 | 0.1489 | 0.1581 | 0.1506 | 0.1503 | 0.1499  | **0.1430** |
|         | 24 | 0.3158 | 0.3142 | 0.3142 | 0.3244 | 0.3143 | 0.3142 | 0.3149  | **0.3055** |
|         | 48 | 0.3959 | 0.3937 | 0.3953 | 0.4039 | 0.3949 | 0.3937 | 0.3947  | **0.3698** |
| **ETTm2** | 1  | 0.1135 | 0.1122 | 0.1153 | 0.1204 | 0.1183 | 0.1163 | 0.1153  | **0.1079** |
|         | 24 | 0.2222 | 0.2203 | 0.2213 | 0.2295 | 0.2220 | 0.2203 | 0.2213  | **0.2169** |
|         | 48 | 0.2516 | 0.2480 | 0.2512 | 0.3000 | 0.2515 | 0.2508 | 0.2564  | **0.2473** |


## Q3: feature location
We also conducted experiments on the SOFTS model. Similar to the iTransformer experiments, we extracted features from various layers for feature correction. The final results are shown in the table below.

| Location | electricity_1 | electricity_24 | electricity_48 | PEMS03_1 | PEMS03_24 | PEMS03_48 | PEMS07_1 | PEMS07_24 | PEMS07_48 | solar_1 | solar_24 | solar_48 | weather_1 | weather_24 | weather_48 |
|--------|---------------|----------------|----------------|----------|-----------|-----------|----------|-----------|-----------|---------|----------|----------|-----------|------------|------------|
| ori    | 0.0485        | 0.1104         | 0.1338         | 0.0390   | 0.0912    | 0.1419    | 0.0365   | 0.0893    | 0.1375    | 0.0076  | 0.0969   | 0.1542   | 0.0591    | 0.1695     | 0.2426     |
| input  | 0.0611        | 0.1324         | 0.2649         | 0.0892   | 0.1000    | 0.1413    | 0.0412   | 0.0923    | 0.1435    | 0.0080  | 0.1104   | 0.1868   | 0.0641    | 0.1717     | 0.2512     |
| norm   | 0.0492        | 0.1061         | 0.1203         | 0.0371   | 0.0877    | 0.1419    | 0.0364   | 0.0884    | 0.1389    | 0.0080  | 0.1031   | 0.1463   | 0.0556    | 0.1680     | 0.2321     |
| emb    | 0.0464        | 0.1053         | 0.1286         | 0.0373   | 0.0865    | **0.1409**| **0.0359**| **0.0871**| **0.1300**| 0.0075  | 0.0885   | 0.1439   | **0.0525**| 0.1685     | 0.2230     |
| 1      | **0.0449**    | 0.1039         | 0.1317         | **0.0367**| **0.0847**| 0.1411    | 0.0360   | 0.0873    | 0.1344    | 0.0076  | 0.0940   | 0.1498   | **0.0525**| 0.1691     | 0.2231     |
| 2      | 0.0473        | 0.1090         | 0.1318         | 0.0382   | 0.0892    | 0.1411    | 0.0360   | 0.0880    | 0.1356    | **0.0069**| **0.0881**| 0.1423   | 0.0531    | **0.1675** | **0.2155** |
| 3      | 0.0476        | **0.0991**     | 0.1271         | 0.0369   | 0.0909    | 0.1453    | 0.0364   | 0.0881    | 0.1323    | 0.0070  | 0.0916   | **0.1415**| 0.0525    | 0.1694     | 0.2312     |
| proj   | 0.0475        | 0.1050         | **0.1200**     | 0.0372   | 0.0898    | 0.1418    | 0.0389   | 0.0886    | 0.1346    | 0.0076  | 0.0922   | 0.1496   | 0.0534    | 0.1690     | 0.2183     |
| denorm | 0.0474        | 0.1088         | 0.1276         | 0.0467   | 0.0834    | 0.1416    | 0.0386   | 0.0882    | 0.1336    | 0.0074  | 0.0958   | 0.1539   | 0.0571    | 0.1623     | 0.2306     |

It appears that the best prediction results can come from different layer. 
However, please refer to our response to Reviewer 1's Question 3. We aggregated the gradients of features during deployment and calculated the mean and Root Mean Square Difference (RMSD) of these gradients. We then examined their relationship with the final MSE. The results show a certain positive correlation between RMSD of the gradients and the final MSE. This implies that during online deployment, features with more stable gradients lead to better prediction performance. In other words, it is beneficial for online deployment if the feature estimates are consistently biased high or consistently biased low. However, if the feature estimates are sometimes too high and sometimes too low, this is detrimental to online deployment.

