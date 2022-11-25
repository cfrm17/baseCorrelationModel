# Base Correlation Model


The model serves the purpose of finding an appropriate correlation to value a collateral debt obligation (CDO) tranche from market information via base correlations. The model includes:

1.	Normal Copula Semi-closed Form Solution;
2.	New Method of Base Correlation Calibration;
3.	Extrapolation of Base Correlation Curve.

In the following of the report, the collateral pool of a CDO trade is defined as a set of N reference names, , in which each reference name is described by a hazard rate curve  , recovery rate  , and a notional amount  .  The collateral pool is tranched into M tranches with the tranche defined by an attachment point , a detachment point  , and a fixed time horizon   with fee payments at interval  .  

Within the current GSP credit derivatives framework a reduced form of default probability of a reference name has been implemented and well maintained. Let the default time of   reference name in the collateral pool be  , a deterministic risk-neutral hazard rate,  , is defined such that the default probability between s and s+ds  . With this definition the default probability functions built upon the hazard rate are
(1)	 = Survival probability,
(2)	 = Default probability,
(3)	  = Default probability density function.

•	Normal Copula Semi-Closed Form Model

Normal copula model has become the market standard to compute the joint default probabilities of a collateral pool, which is essential to value a CDO trade [4,5]. Within the GSP credit derivative modeling framework, a normal copula Monte Carlo (MC) simulation model has already implemented and approved [6]. 

A copula is a mathematical function that combines marginal probability into a joint distribution. For n uniform random variables,  , the joint distribution function is defined as the following.

(4)	 				

The function C is called the copula function.

The normal copula function is a multivariate cumulative normal distribution with correlation matrix  . Applying the normal copula function to the modelling of correlated default events of a collateral asset pool, the uniform random variables are mapped to the default probabilities with standard normal distribution. The default copula, or the cumulative joint default probability for the collateral pool with n assets, can be expressed as:

(5)	 	
			
where  is the standard cumulative normal distribution function with correlation matrix  .

	One-Factor Normal Copula Model and Analytical Solution

The normal copula function is actually an n-dimensional integral, which is hard to calculate directly if n is large.   We use the one-factor normal copula model to reduce the dimensionality in order to achieve an analytical solution. For the obligor in the collateral pool, we assume that its asset process follows 

(6)		 ,								

where  ,  are independent standard normal random variables, that is  .  The latent variable  is shared by all reference names in the collateral pool. From the above equation, the correlation between and   obligor, which could be viewed as the asset correlation  , can be found to have the following relationship 

(7)	 .

The assumption that   can be easily achieved for a flat correlation matrix by assuming  . With this assumption, the default probability of the   reference name conditional on the latent variable has the following form

(8)	 .	

(9)	 

The Normal copula can be proved to have the form

(10)	 .

Finally, we can express the one-factor normal default and survival copulas as the following

(11)	 

The n-dimensional copula is computed through a one-dimensional integral. 

	Valuation of a CDO Trade Using One-Factor Semi-Closed Form Solution

Even if the normal copula can be reduced into a one-dimensional integral shown in Eqs. (10) and (11), it is still quite difficult to be used directly in the valuation of CDO trades. Instead, we proceed with a loss function defined as

(12)		 ,				

where    is the loss given default (LGD) for the  reference name. 

In order to price a CDO tranche with an attachment point  and a detachment point , the expected loss function of the tranche is introduced, which has the form

(13)		 

The value of the protection leg and the fee leg can then be written as:


Fee Leg:

(14)		 .

Annuity of the fee leg:

 (15)		 ,

with the first term being the annuity of the fee payment stream and the second the fee accrual.

The b/e spread for the CDO tranche can be calculated by

(16)		 ,

and, if the traded spread    is given, the MTM reads

(17)		 .

	Iteration Approach To Calculate Expected Loss Distribution Function

In order to calculate the expected loss function denoted in Eq. (13), we need to the find the probability distribution function of the loss function. In general there are two approaches: 1) via the characteristic function and the moment generating function and 2) via iteration. 

By conditional independence, we could proceed with the characteristic function as proposed by Laurent and Gregory (2003)[4]: 


(18)		  ,

within which the characteristic function of the single reference name can be calculated separately with the results shown as 

(19)		 .

The probability distribution of the loss function can be calculated from the characteristic function.

The GSP toolbox adopts an iteration approach, which has been proved to be a more efficient method. This method was first proposed by L. Anderson et al. [8] and several other versions were later developed by several authors [7~10].

Conditional on the latent variable, we could find that the probability of zero loss is given by

(20)		 ,

while the probability of the reference name default could be written as

(21)		 

From the above two equations, we could find an iteration relationship by assuming that the reference name is added one by one. For example, when the reference is added, the following relationship holds 
 
(22)	 .

Once we got the default probability distribution conditional on the latent variable, the unconditional probability can be found:

(23)	 

Then the expected loss function can be written as

(24)	 ,

with   being the probability density function of expected loss.

With respect to the GSP iteration approach, it is worthy to note that

1.	The iteration adopted by the GSP model and denoted in this report is different from those specified by Anderson et al. [8] and Hull&White [7]. In the current GSP approach, the limitations on the LGD of each reference name are lifted by building loss function grid points without any approximation.  The iteration is then conducted by Eq. (22), which is directly over the reference name instead of the loss function. The advantage of this approach is that no approximation is needed; the disadvantage is that the simulation time will increase quickly as the heterogeneity of the LGD of the reference pool increases. 

2.	Gauss-Hermite method is used for the integration over the latent variable. As shown in Eqs.(8) and (9), the function is no longer smooth enough if the correlation is very large. Hence 0.9 is set to be the maximum value that the semi-closed form solution can predict a reliable solution. 

3.	The iteration is carried out quarterly and the integration over time, as denoted by Eqs.(14) and (15), is done by linear interpolation over the quarterly spaced expected loss function. 

The pertinent tests have been designed to assess these treatments and approximations in the following section.

•	Base Correlation Curve Calibration

In the initial version of the GSP toolbox, the base correlation is calculated based on the implied correlation, bearing the assumption that base correlation and implied correlation generates the same expected loss and are internally consistent. However, research shows that they are different and the base correlation should be built in a more self-consistent way [12].  

Taking the standard CDX North America index as an example ( , the base correlation curve is calculated by the following procedures:

1.	The 0~3% tranche base correlation is equal to the implied correlation. That is, the value of correlation is find such that the MTM of the 0~3% tranche is zero with 

(25)	 ,

where   is the up-front fee and   is the quoted spread of the equity tranche. Eq.(25) can be solved using the normal copula model in reverse.

2.	For base correlation of 0~7% tranche, we solve for the value of   that solves

(26)	 

where   and   are the value of protection and annuity of the fee leg, which are implied from the 0~3% tranche market quote and calculated from the first step.  

3.	The base correlation of the higher attachment point is calculated by following the same bootstrapping procedure based on the base correlation of the previous base tranche.

The new method, called MTM in the toolbox, is consistent with Refs. [12] and [13]. 
 
•	Base Correlation Curve Extrapolation and Interpolation

The base correlation curve is consisted of the correlation values of several base tranches. When applied to a bespoke tranche, it may encounter the situation that the bespoke tranche has the attachment point and/or detachment point below the first base tranche or higher than the last base tranche. For example, the first base tranche of CDX is 0~3% while the bespoke tranche could be 1.5%~2.5%. In order to find the appropriate correlation in this case, several extrapolation methods on the base correlation curve have been proposed in the submitted template.

In the toolbox, one can pick an extrapolation method by assuming a correlation value for 0~0% and 0~100% base tranches. For example, we could assume 0 point correlation be 1) 0, 2) the same as 0~3% tranche (a flat curve), and 3) an arbitrary value which could be a linear extrapolation of the curve up to 0~7% base tranche.   

To the best of our knowledge, there is no generally acceptable methodology of such extrapolation. Moreover, it is even doubtful if we could apply such method, because we actually extending market information to the unknown range.  

We argue that, if we need to make a choice, the flat extrapolation is appropriate for two reasons. First, it is more self-consistent. As discussed in Ref. [12], the base correlation is not a proper model of correlation skew. A flat extrapolation assumes no correlation skew within the extrapolated region (0~3% and 30~100% for CDX). As shown in Eqs.(14) and (15), assuming a flat base correlation curve fulfils the arbitrage-free requirements claimed in Ref. [12], hence it is more self-consistent. 

Second, a flat extrapolation is robust when applied to a bespoke tranche. If the attachment point and detachment points of a tranche are within the extrapolation range, a positive value of protection is assured by the flat extrapolation while other methods have the possibility of failure.

•	Benchmarking/Comparison to Other Models

Two models are employed as the benchmark/comparison model, which are:

1.	Normal Copula MC simulation

The normal copula model was first proposed as an MC simulation approach [5]. The GSP normal copula MC simulation model could serve as the benchmark model to test the closed form solution. 

In order to generate correlated defaults times using normal copula, a series of random variables   are first generated from an n-dimensional normal distribution with correlation   in each MC scenario. The default times for each obligor in the collateral pool   are obtained using

(25)	 


2.	FTD closed form solution 

If we assume a homogeneous collateral pool ( ) and set the notional of the first tranche equal to  , the first tranche becomes a first-to-default (FTD) trade. 

Within the one factor normal copula framework, this special FTD actually has a nice closed form solution. Instead of directly solving first default probability, we are interested in the probability that all names survive up to a particular point in time.

The joint distribution for survival time can be defined as follows given the definition of survival copula:

(26)	 .

We can then write the hazard rate and default probability density function for the collateral pool as the following:

(27)			 
(28)			 					

Then we treat this FTD trade just like a single name credit default swap (see https://finpricing.com/lib/FxAccumulator.html . Following this approach, the protection leg and fee leg have the forms.

(29)		 .								

(30)		 .			
This closed form solution does not involve iteration hence can be used as a alternative model to check the iteration method.


