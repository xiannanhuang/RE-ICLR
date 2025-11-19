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
