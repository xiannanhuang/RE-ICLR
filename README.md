# Review1
# Questions
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
## Qusetions
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

## Q4: longer prediction horizon
We conducted additional experiments on the ETT datasets for prediction lengths of 96, 192, 336, and 720. The experiments included our proposed ADAPT-Z method, various online forecasting baseline methods, adaptive normalization methods (FAN, DIST-ST), and adaptive normalization methods combined with ADAPT-Z. The results are shown in the table below. All experiments were based on the iTransformer model.

Results of baseline methods

| Dataset | H   | Ori    | fOGD   | OGD    | DSOF   | SOILD  | ADCSD  | Proceed | ADAPT-Z | IMP    |
|---------|-----|--------|--------|--------|--------|--------|--------|---------|---------|--------|
| **ETTh1** | 96  | 0.4092 | 0.4011 | 0.4035 | 0.4018 | 0.4044 | 0.4019 | 0.4019  | **0.3930** | 3.96%  |
|         | 192 | 0.4857 | 0.4832 | 0.4828 | 0.4816 | 0.4844 | 0.4823 | 0.4829  | **0.4702** | 3.19%  |
|         | 336 | 0.5032 | 0.4953 | 0.5037 | 0.4985 | 0.5053 | 0.5008 | 0.4927  | **0.4710** | 6.40%  |
|         | 720 | 0.5389 | 0.5116 | 0.5139 | 0.5158 | 0.5187 | 0.5162 | 0.5114  | **0.4855** | 9.91%  |
| **ETTh2** | 96  | 0.2978 | 0.2773 | 0.2770 | 0.2778 | 0.2796 | 0.2773 | 0.2777  | **0.2741** | 7.96%  |
|         | 192 | 0.3924 | 0.3876 | 0.3887 | 0.3878 | 0.3951 | 0.3922 | 0.3869  | **0.3751** | 4.41%  |
|         | 336 | 0.4283 | 0.4245 | 0.4247 | 0.4275 | 0.4265 | 0.4288 | 0.4251  | **0.4050** | 5.44%  |
|         | 720 | 0.4382 | 0.4326 | 0.4415 | 0.4402 | 0.4446 | 0.4426 | 0.4298  | **0.4138** | 5.57%  |
| **ETTm1** | 96  | 0.3894 | 0.3899 | 0.3937 | 0.3899 | 0.3926 | 0.3920 | 0.3887  | **0.3769** | 3.21%  |
|         | 192 | 0.4082 | 0.4074 | 0.4088 | 0.4066 | 0.4120 | 0.4098 | 0.4058  | **0.3929** | 3.75%  |
|         | 336 | 0.4362 | 0.4392 | 0.4411 | 0.4413 | 0.4410 | 0.4410 | 0.4355  | **0.4222** | 3.21%  |
|         | 720 | 0.5123 | 0.5164 | 0.5172 | 0.5182 | 0.5174 | 0.5178 | 0.5116  | **0.4930** | 3.77%  |
| **ETTm2** | 96  | 0.1983 | 0.1924 | 0.1917 | 0.1927 | 0.1938 | 0.1931 | 0.1908  | **0.1879** | 5.24%  |
|         | 192 | 0.2582 | 0.2533 | 0.2536 | 0.2535 | 0.2534 | 0.2523 | 0.2520  | **0.2463** | 4.61%  |
|         | 336 | 0.3182 | 0.3193 | 0.3214 | 0.3186 | 0.3222 | 0.3187 | 0.3168  | **0.3062** | 3.77%  |
|         | 720 | 0.4231 | 0.4182 | 0.4219 | 0.4174 | 0.4255 | 0.4202 | 0.4174  | **0.4007** | 5.29%  |

Results of adaptive normalization methods

| Dataset | H   | Ori    | DISH-TS | DISH-TS+ | FAN    | FAN+   | ADAPT-Z |
|---------|-----|--------|---------|----------|--------|--------|---------|
| **ETTh1** | 96  | 0.4092 | 0.3969  | 0.3745   | 0.3955 | 0.3762 | 0.3930  |
|         | 192 | 0.4857 | 0.4742  | 0.4476   | 0.4712 | 0.4375 | 0.4702  |
|         | 336 | 0.5032 | 0.4766  | 0.4575   | 0.4744 | 0.4379 | 0.4710  |
|         | 720 | 0.5389 | 0.4930  | 0.4744   | 0.4917 | 0.4694 | 0.4855  |
| **ETTh2** | 96  | 0.2978 | 0.2781  | 0.2673   | 0.2792 | 0.2554 | 0.2741  |
|         | 192 | 0.3924 | 0.3792  | 0.3574   | 0.3788 | 0.3629 | 0.3751  |
|         | 336 | 0.4283 | 0.4111  | 0.3901   | 0.4089 | 0.3876 | 0.4050  |
|         | 720 | 0.4382 | 0.4191  | 0.3989   | 0.4191 | 0.3973 | 0.4138  |
| **ETTm1** | 96  | 0.3894 | 0.3789  | 0.3644   | 0.3810 | 0.3619 | 0.3769  |
|         | 192 | 0.4082 | 0.3945  | 0.3800   | 0.3966 | 0.3728 | 0.3929  |
|         | 336 | 0.4362 | 0.4269  | 0.4140   | 0.4242 | 0.3958 | 0.4222  |
|         | 720 | 0.5123 | 0.4964  | 0.4809   | 0.4962 | 0.4761 | 0.4930  |
| **ETTm2** | 96  | 0.1983 | 0.1908  | 0.1819   | 0.1879 | 0.1777 | 0.1879  |
|         | 192 | 0.2582 | 0.2484  | 0.2357   | 0.2463 | 0.2338 | 0.2463  |
|         | 336 | 0.3182 | 0.3088  | 0.2993   | 0.3096 | 0.2848 | 0.3062  |
|         | 720 | 0.4231 | 0.4047  | 0.3803   | 0.4062 | 0.3868 | 0.4007  |

## Q5: novelty
ADAPT-Z introduces a new paradigm. Its novelty lies in shifting from traditional parameter updates to feature space adjustment. Existing online time series forecasting methods focus on updating model parameters, such as final layer weights or adapter modules. However, ADAPT-Z challenges this convention. It proposes that distribution shifts stem from changes in latent factors. Therefore, directly adjusting feature representations is more effective. To achieve this, ADAPT-Z introduces an adapter network. This network integrates current features and historical gradient information to predict correction terms. This approach solves the delayed feedback problem in multi-step prediction and ensures stable updates.

# Reviewer 3
## Questions
While the experiments are extensive and demonstrate consistent gains, they lack statistical robustness, as no variance or confidence intervals are reported across different random seeds, making it difficult to assess the reliability of the improvements. 
 
 The evaluation could be broadened to include a wider range of model architectures, such as MLP-based and linear forecasting models, which are common baselines in the time-series literature. 
 
 The computational efficiency of the approach is also unclear, particularly the memory and runtime overhead introduced by storing and updating historical gradients, which may limit scalability in long-horizon or high-frequency settings. 
 
 Furthermore, assessing performance under stronger distribution shifts or longer forecasting horizons would provide stronger evidence of generalizability and robustness across real-world deployment scenarios.

 # Q1: Robustness across different random seeds
 
 We report the experimental results for iTransformer using three random seeds in the Appendix (A.6.1). This demonstrates that our method remains effective across different random seeds. Regarding your interest in statistical robustness, we have conducted additional experiments on the SOFTS and TimesNET models using the ETT datasets with three random seeds (2024, 2025, and 2026). The results, including both the MSE and the standard deviation of MSE, are reported below.

**SOFTS**

| Dataset | H  | Ori_mean | Ori_std | fOGD_mean | fOGD_std | OGD_mean | OGD_std | DSOF_mean | DSOF_std | SOLID_mean | SOLID_std | ADCSD_mean | ADCSD_std | Proceed_mean | Proceed_std | ADAPT-Z_mean | ADAPT-Z_std |
|---------|----|----------|---------|-----------|----------|----------|---------|-----------|----------|------------|-----------|------------|-----------|--------------|-------------|--------------|-------------|
| **ETTh1** | 1  | 0.1211 | 0.0021 | 0.1209 | 0.0028 | 0.1192 | 0.0020 | 0.1316 | 0.0004 | 0.1194 | 0.0024 | 0.1199 | 0.0026 | 0.1203 | 0.0025 | **0.1122** | 0.0026 |
|         | 24 | 0.3182 | 0.0012 | 0.3217 | 0.0020 | 0.3223 | 0.0010 | 0.3312 | 0.0010 | 0.3228 | 0.0031 | 0.3172 | 0.0011 | 0.3210 | 0.0031 | **0.3029** | 0.0013 |
|         | 48 | 0.3666 | 0.0063 | 0.3656 | 0.0054 | 0.3657 | 0.0064 | 0.3894 | 0.0020 | 0.3600 | 0.0054 | 0.3618 | 0.0066 | 0.3625 | 0.0053 | **0.3522** | 0.0029 |
| **ETTh2** | 1  | 0.0702 | 0.0023 | 0.0700 | 0.0024 | 0.0692 | 0.0024 | 0.0691 | 0.0018 | 0.0700 | 0.0019 | 0.0699 | 0.0026 | 0.0701 | 0.0020 | **0.0661** | 0.0017 |
|         | 24 | 0.1801 | 0.0009 | 0.1791 | 0.0009 | 0.1788 | 0.0008 | 0.1834 | 0.0008 | 0.1797 | 0.0020 | 0.1807 | 0.0008 | 0.1784 | 0.0021 | **0.1729** | 0.0016 |
|         | 48 | 0.2305 | 0.0031 | 0.2327 | 0.0031 | 0.2309 | 0.0033 | 0.2906 | 0.0079 | 0.2310 | 0.0035 | 0.2318 | 0.0032 | 0.2300 | 0.0035 | **0.2239** | 0.0016 |
| **ETTm1** | 1  | 0.0570 | 0.0008 | 0.0554 | 0.0008 | 0.0552 | 0.0003 | 0.0741 | 0.0006 | 0.0563 | 0.0004 | 0.0555 | 0.0009 | 0.0566 | 0.0005 | **0.0523** | 0.0002 |
|         | 24 | 0.2384 | 0.0038 | 0.2385 | 0.0050 | 0.2390 | 0.0042 | 0.2888 | 0.0051 | 0.2419 | 0.0032 | 0.2399 | 0.0040 | 0.2396 | 0.0033 | **0.2148** | 0.0012 |
|         | 48 | 0.3154 | 0.0140 | 0.3081 | 0.0103 | 0.3122 | 0.0138 | 0.3935 | 0.0061 | 0.3156 | 0.0107 | 0.3087 | 0.0094 | 0.3123 | 0.0110 | **0.2865** | 0.0030 |
| **ETTm2** | 1  | 0.0335 | 0.0005 | 0.0336 | 0.0005 | 0.0337 | 0.0005 | 0.0448 | 0.0001 | 0.0338 | 0.0006 | 0.0335 | 0.0005 | 0.0337 | 0.0005 | **0.0322** | 0.0002 |
|         | 24 | 0.1062 | 0.0012 | 0.1078 | 0.0012 | 0.1075 | 0.0012 | 0.1532 | 0.0015 | 0.1079 | 0.0026 | 0.1074 | 0.0011 | 0.1072 | 0.0026 | **0.1032** | 0.0001 |
|         | 48 | 0.1439 | 0.0017 | 0.1422 | 0.0016 | 0.1444 | 0.0016 | 0.2063 | 0.0086 | 0.1426 | 0.0017 | 0.1415 | 0.0016 | 0.1423 | 0.0018 | **0.1399** | 0.0007 |

**TimesNet**

| Dataset | H  | Ori_mean | Ori_std | fOGD_mean | fOGD_std | OGD_mean | OGD_std | DSOF_mean | DSOF_std | SOLID_mean | SOLID_std | ADCSD_mean | ADCSD_std | Proceed_mean | Proceed_std | ADAPT-Z_mean | ADAPT-Z_std |
|---------|----|----------|---------|-----------|----------|----------|---------|-----------|----------|------------|-----------|------------|-----------|--------------|-------------|--------------|-------------|
| **ETTh1** | 1  | 0.1371 | 0.0020 | 0.1362 | 0.0029 | 0.1370 | 0.0016 | 0.1620 | 0.0004 | 0.1366 | 0.0025 | 0.1366 | 0.0027 | 0.1368 | 0.0029 | **0.1290** | 0.0031 |
|         | 24 | 0.3481 | 0.0012 | 0.3420 | 0.0020 | 0.3397 | 0.0009 | 0.4604 | 0.0010 | 0.3438 | 0.0034 | 0.3474 | 0.0013 | 0.3459 | 0.0035 | **0.3360** | 0.0015 |
|         | 48 | 0.3954 | 0.0070 | 0.3854 | 0.0058 | 0.3836 | 0.0054 | 0.5793 | 0.0018 | 0.3904 | 0.0046 | 0.3950 | 0.0056 | 0.3929 | 0.0056 | **0.3763** | 0.0032 |
| **ETTh2** | 1  | 0.0775 | 0.0031 | 0.0775 | 0.0025 | 0.0763 | 0.0028 | 0.0781 | 0.0016 | 0.0787 | 0.0020 | 0.0765 | 0.0027 | 0.0776 | 0.0023 | **0.0750** | 0.0018 |
|         | 24 | 0.1984 | 0.0009 | 0.1956 | 0.0008 | 0.1965 | 0.0008 | 0.2623 | 0.0007 | 0.1983 | 0.0026 | 0.1982 | 0.0007 | 0.2025 | 0.0025 | **0.1909** | 0.0014 |
|         | 48 | 0.2428 | 0.0026 | 0.2340 | 0.0027 | 0.2424 | 0.0028 | 0.3461 | 0.0076 | 0.2410 | 0.0027 | 0.2398 | 0.0032 | 0.2362 | 0.0033 | **0.2402** | 0.0021 |
| **ETTm1** | 1  | 0.0585 | 0.0010 | 0.0594 | 0.0009 | 0.0572 | 0.0004 | 0.0766 | 0.0006 | 0.0576 | 0.0005 | 0.0582 | 0.0008 | 0.0581 | 0.0004 | **0.0548** | 0.0002 |
|         | 24 | 0.2675 | 0.0047 | 0.2715 | 0.0047 | 0.2595 | 0.0042 | 0.3494 | 0.0067 | 0.2761 | 0.0040 | 0.2631 | 0.0050 | 0.2668 | 0.0033 | **0.2425** | 0.0013 |
|         | 48 | 0.3625 | 0.0133 | 0.3619 | 0.0130 | 0.3554 | 0.0158 | 0.4351 | 0.0057 | 0.3580 | 0.0129 | 0.3492 | 0.0085 | 0.3614 | 0.0123 | **0.3073** | 0.0029 |
| **ETTm2** | 1  | 0.0329 | 0.0006 | 0.0328 | 0.0005 | 0.0328 | 0.0004 | 0.0439 | 0.0000 | 0.0333 | 0.0005 | 0.0335 | 0.0006 | 0.0336 | 0.0006 | **0.0326** | 0.0002 |
|         | 24 | 0.1128 | 0.0016 | 0.1151 | 0.0014 | 0.1126 | 0.0010 | 0.1673 | 0.0015 | 0.1148 | 0.0031 | 0.1141 | 0.0012 | 0.1139 | 0.0023 | **0.1054** | 0.0001 |
|         | 48 | 0.1560 | 0.0020 | 0.1509 | 0.0012 | 0.1558 | 0.0021 | 0.2434 | 0.0099 | 0.1511 | 0.0018 | 0.1535 | 0.0016 | 0.1523 | 0.0020 | **0.1438** | 0.0008 |
 
