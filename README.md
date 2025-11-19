The novelty of ADAPT-Z seems limited as its design combines adapters and historical gradients, which are extensively studied in Parameter-Efficient Fine-Tuning (PEFT) and continual learning, respectively. The contribution is more of an engineering integration rather than a novel contribution. The authors should further emphasize their main contribution.

in Table 2, the reported performance for baselines FSNet and OneNet is highly questionable. Their MSEs are orders of magnitude worse than other methods and drastically inconsistent with their original papers. Such a stark discrepancy suggests potential issues with their implementation or the experimental setup, making a fair comparison questionable. The authors should clarify this significant discrepancy.

In Section 4.3.3, the authors claim that the performance of ADAPT-Z is correlated to the choice of feature layer and the optimal layers varying across datasets. However, the paper lacks a deeper analysis of the correlation between dataset characteristics and the best feature layer, leaving this critical design choice largely unexplained.

More recent prediction models should be compared to further validate the effectiveness of ADAPT-Z, e.g., TimeFilter [1] and TimeKAN [2].

# Novelty
Thank you for your suggestion.
Essentially, most online deployment or Parameter-Efficient Fine-Tuning (PEFT) work for time series prediction needs to address two issues: which parameters to update and how to update them. We propose that changes in data distribution stem from shifts in the underlying latent variables that determine the distribution. Therefore, we avoid the concept of updating parameters and propose a method to update feature representations instead. 

# performance for baselines FSNet and OneNet
Thank you for your valuable feedback.
In the original OneNet and FSNet papers, the reported results were based on experiments with data leakage. Specifically, for predicting the next k steps, at step t, these methods use the model’s predictions for steps t+1 to t+k, as well as the true values for steps t+1 to t+k to update parameters. But the true values for steps t+1 to t+k are not available at step t. This issue is also mentioned in the DSOF paper. Additionally, that paper conducted experiments without data leakage. In their Table 2, the results for FSNet and OneNet differ by orders of magnitude compared to the original models. To ensure a fair comparison, our experiments on OneNet and FSNet are entirely based on the data leakage-free version of the code provided in the official DSOF repository. 


#
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
