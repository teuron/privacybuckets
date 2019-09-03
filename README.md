# core
An implementation of Privacy Buckets: A numerical tool to calculate privacy loss

Implementation for the publication S. Meiser, E. Mohammadi, "Tight on Budget? Tight Bounds for r-Fold Approximate Differential Privacy", Proceedings of the 25th ACM Conference on Computer and Communications Security (CCS), 2018 [1]

On ePrint: https://eprint.iacr.org/2017/1034.pdf

For how to use, see example.py. 

For an example how to create your own bucket_distribution, see example_custom_bucketing.py

For a analytic extension of the numerical buckets distribution with many additional insights, see Sommer et al. "Privacy loss classes: The central limit theorem in differential privacy." [2]

## The correct choice of number_of_buckets and factor (Voodoo Ritual)
Given two distributions distribution_A and distribution_B, we want to evaluate the delta for a given epsilon. For details, see [1] and [2].

The number_of_buckets defines the number of discretization steps the probability mass of events o of distribution_A will be distributed over, according to the privacy loss 

    L_A/B(o) = log  Pr[o <- distribution_A] / Pr[o <- distribution_B]. 

The logarithm of the factor ( log(factor) ) is the average additive distance of the privacy losses for two events o that land in neighbouring buckets. The bucket_distribution can handle everything with a privacy loss 

    L_A/B(o) in [-log(factor)*number_of_buckets/2, log(factor)*number_of_buckets/2]. 

Everything that does not fit in the bucket_distribution array of consecutive buckets of length number_of_buckets will be put in the infinity_bucket if the privacy loss is too big, or in the minus_n bucket if the privacy loss is too small respectively. The number_of_buckets and the factor have to be chosen such that the mass in the infinity (and/or minus_n) bucket is acceptable for the required computational performance. 

For beginners, it is fine to set the number_of_buckets to 100'000. This is sufficient for most scenarios. 

The factor, however, should be chosen carefully. Let the factor be arbitrary but fixed, then the probability mass of distribution_A of all events o with 

	L_A/B(o) > log(factor) * number_of_buckets / 2 

will land in the infinity bucket. And similar for the minus_n bucket. The 2 comes from the fact that half of the buckets handle the case where L_A/B(o) < 0. Therefore, choose the factor according to the acceptable probability mass outside the buckets (that will, under composition, contribute to the error exponentially):
	
        max( |max_loss|, |min_loss| ) < log(factor) *  number_of_buckets / 2

    <=> factor = exp( max( |max_loss|, |min_loss| )  * 2 /  number_of_buckets )
 	
 with  
      
      max_tolearable_infty_bucket   = Pr [  L_A/B(o) > max_loss, o <- distribution_A ] 
      max_tolearable_minus_n_bucket = Pr [  L_A/B(o) < min_loss, o <- distribution_A ] 

For more details, see the examples\*.py provided, and [1] and [2]. 

If yout want to improve or automate the factor search, there is a helpfull insight given in [2]. It states that all bucketd_distributions converge towards a Gaussian distribution quite quickly under composition. The shape of this Gaussian is completely defined by the mean and variance of the initial bucket_distribution. Moreover, this work gives a precise and numericly stable formula to esimate the tail of such a Gaussian under composition without the need of convolving the bucket_distribution. Using this knowledge, the amount of probability mass that will end up in the infinity_bucket can be estimated. 

Another improvement based on the moving Gaussian-shaped bucket-distribution might be to let the bucket-distribution move with the mean of the composed privacy loss distribution (such that the mean is always in the middle of the in-memory bucket_distribution, saving a lot of computations and memory). For additional details, contact the authors of [1,2]. 

# privacy web-page
The privacy web-page is built on top of the python web framework django and can be started from the directory "privacy_webpage/webpage" with the command "python3 manage.py runserver"

# slides
The slides used to present privacy buckets at CCS'18

# References

[1] S. Meiser, E. Mohammadi, "Tight on Budget? Tight Bounds for r-Fold Approximate Differential Privacy", Proceedings of the 25th ACM Conference on Computer and Communications Security (CCS), 2018 (https://eprint.iacr.org/2017/1034.pdf)

[2] Sommer DM, Meiser S, Mohammadi E. Privacy loss classes: The central limit theorem in differential privacy. Proceedings on Privacy Enhancing Technologies. 2019 Apr (https://eprint.iacr.org/2018/820.pdf)


