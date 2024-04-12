# DUW KDD2024
## Additional Response for reviewer V4Re
6. **The scenario of clients randomly dropout & selecting a certain portion of clients for each round**

For these scenarios, the server pre-generates a large pool of unique keys, exceeding the potential number of clients, which is feasible in a cross-silo setting with typically hundreds of clients. Each new client receives a unique key from this pool, distinct from those previously assigned. If clients drop out, their keys are reserved, avoiding reassignment even if they rejoin later. This approach prevents watermark collisions, whether clients drop out randomly or a subset is selected for each training round.
To verify this point, we also add one experiment w.r.t. different participation portion of clients in FL in the following table.
Participation portion|Acc|$\Delta$Acc|WSR|WSR\_Gap|TAcc|
-|-|-|-|-|-|
100\%|0.5583|0.0003|1.0000|0.9998|1.0000|
 20\%|0.4899|0.0666|1.0000|1.0000|1.0000|
 10\%|0.4769|0.0559|1.0000|0.9999|1.0000|

 According to the results, lower participation portions of clients will cause more degradation of the standard FL accuracy, but it will not affect the WSR and tracking accuracy (TAcc), which indicates that it will not cause any watermark collision problem.

7. **The baseline solutions chosen in this article appear to lack strength**

Thank you very much for your comment! The main contribution of this paper is the framework, which uses an encoder to generate client-unique triggers and a decoder to assign client-unique target labels to avoid watermarking collisions, and they combined to enable the tracking infringers. The watermarking itself can be replaced by different centralized backdoor-based watermarkings, such as [R1], and a specific choice is not the focus of this paper. DUW is a flexible framework for distinguishing different clients, where different watermarking methods can be plugged in. We agree with the reviewer that a comparison of watermark approaches is an interesting study and we will consider them for our future work. 

[R2] explored both the model extraction attack and the model evasion attacks for watermarking. We take one model extraction attack, knockoff, as an example and show the results in Table 3. To enrich the attack baseline, we also add one more evasion attack following the setting in WAFFLE [38] as the attack baseline. For this attack, to distinguish between the ID and OoD samples,  STL10 cropped to the same size as the training data is adopted as the real OoD samples, and the local training data for each attacker is adopted as the ID data.
The detector model architecture is based on the FL training model with a replaced classifier layer. The attacker trains the detector model based on the OoD and ID data for $20$ epochs. The FPR (false positive rate) and TPR (true positive rate) for real OoD and TPR for the verification dataset are shown in the following table. 
Inject dataset|FPR|TPR|
-|-|-|
USPS|0\%|65.8\%|
Random noise|0\%|**25.0\%**|
Jigsaw|0\%|74.2\%|

The lowest TPR for the verification dataset (the ratio of verification samples correctly identified as OoD) at 25\% indicates that the OoD detector cannot distinguish the verification data from ID samples during inference time. We also observe that the TPR for the verification dataset is closely related to the type of watermark inject dataset. Random noise will be the best choice for defense.

8. **Malicious participants collude with each other**

In Section 4.3, we explore a collaborative attack with 10 clients attacking the model simultaneously, representing a form of collaboration. Despite potential collusion, unique watermarks assigned to each client prevent joint detection of the same watermark. Experimental results show the robustness of DUW.

9. **Results on the CIFAR-10 dataset in Table 3**

Table 3 shows the results for the model extraction attack: knockoff. Since the model extraction attack will first query the victim model for the label of an auxiliary dataset, and then re-train the victim model on the annotated dataset. For some cases, such as this CIFAR-10 case, although it can decrease the WSR by 33.62%, it will sacrifice a lot of standard task accuracy (ΔAcc=42.78%) during this re-training process. For CIFAR-100 and Digits, although they can maintain a standard accuracy close to the original model, they also resume the WSR to 88.28% and 97.80%, respectively. Thus, our conclusion for the model extraction attack is that there is a trade-off between resuming standard accuracy and decreasing WSR. But for all the cases, the tracking accuracy (TAcc) remains 100%, which means no watermark collision happens after the attack.

10. **$\sigma$ parameter**

We define $\sigma=0.9$ in our experiments. When WSR>$\sigma$, the ownership can be verified. According to our experimental results, most of the WSRs we achieved are close to 1.0.

11. **Are 𝑀𝑠 = ( 𝑓𝑠 , ℎ𝑠 ) and 𝑀𝑠 = ( 𝑓𝑠 , 𝐷) equivalent?**

We use this equation to represent the alteration of the architecture of $M_s$. The classifier head $h_s$ of the suspect model is replaced by the decoder $D$.

