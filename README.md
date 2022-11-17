# Files for analysis of C-CAT survival data.  
Supplementary code for the paper: Tamura T, et. al., Selection Bias Due to Delayed Comprehensive Genomic Profiling in Japan. Cancer Sci. 2022 Nov 12.  doi: 10.1111/cas.15651. Online ahead of print.  
[link for the paper](https://onlinelibrary.wiley.com/doi/10.1111/cas.15651)  
This explanation text was written in Japanese.  
C-CATのユーザーは基本的に日本人と思われますので、説明は日本語で記載します。  

# C-CATのデータにおける左側切断と右側打ち切りによる生存期間解析への影響
がんゲノム検査(CGP: comprehensive genomic profiling)を受ける患者は、発症後様々な治療を受けていき、全ての標準治療を終了してから検査にエントリーします（保険適応がそうなっています）。そのため検査登録前の情報が欠落してしまいます。これを左側切断と呼びます。進行が非常に早い患者はがんゲノム検査を受ける前に死亡してしまうこと、主治医と患者の考えによって検査のエントリー時期にばらつきが出ることから、C-CATに登録されている患者情報には少なからずバイアスが生じています。    
![Figure_1, explanation for left-truncation](github_1.png)    

また、化学療法の開始後の生存期間を考える場合、生存期間の開始日と観察開始日が異なることから、通常のKaplan-Meier estimatorでは正確な生存曲線を推定することが困難です。以下の様に、検査後早期の患者群は見かけ上良好な生存率を示しますが、経時的に生存曲線が左下にシフトしていき、徐々に真の曲線に近づいていきます。しかし、全ての患者が打ち切り無く死亡まで観察されない限りはバイアスがなくなることはありません（そしてそのようなことは現実的には不可能です）。    
![Figure_2, explanation for survival curve shift](github_2.png)  
SPT: survival prolonging therapy    

このバイアスの存在のため、現状ではC-CATのデータを用いた生存期間の解析は困難といえます。個人的には診断時のStageの記載が無いことも活用の幅を狭めている大きな要因と思いますが、3万症例を超えるビッグデータがあるにも関わらず、遺伝子変異プロファイルや免疫染色などの静的な情報のみしか活用できないというのはもったいないと感じました。  
日本ではクリニカルシークエンスの情報がC-CATに集約されていますが、アメリカでは多数のhigh-volume cancer centerが協力しAACR Project GENIEのもとに情報を集約し公開しています。  
[AACR Project GENIE](https://www.aacr.org/professionals/research/aacr-project-genie/)    

このGENIEのデータを用いた論文は複数発表されており、その中には何とかこの左側切断によるバイアスを解消しようとして、risk-set adjustmentという手法を使用したものがあります。しかし、この手法が使用できる状況は限定されており、これらの論文では不適切な使用となっています。    
[Publications from AACR Project GENIE](https://www.aacr.org/professionals/research/aacr-project-genie/aacr-project-genie-publications/)  
[左側切断によるバイアスの現状とRisk-set adjustmentの限界を述べた論文](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9190030/)  
[不適切な補正手法が用いられている論文](https://aacrjournals.org/cancerdiscovery/article/10/4/526/2419/Characteristics-and-Outcome-of-AKT1E17K-Mutant)    

左側切断による選択バイアスを評価する指標として、conditional Kendall tau統計量があります。これは、生存期間解析の評価開始日と観察開始日が異なる場合に、評価開始日から観察開始日までの期間と、評価開始日から最終観察日までの期間の相関係数を意味します。なお、cKendall tauがゼロの場合のみrisk-set adjustmentによるバイアス補正が妥当であることが示されています。  
[tranSurv: structural transformation methodでの補正手法](https://github.com/stc04003/tranSurv)    
![Figure_3, explanation for cKendall tau](github_3.png)    

C-CATのデータの場合、どのイベントを生存期間解析の評価開始日にすべきかは悩ましくあります。診断時のステージが不明であるため、「stage IVの患者が診断されてからの生存期間解析」という一般によく見られる解析は不可能です。使用された化学療法と使用開始日は分かるため、化学療法開始後の生存期間解析は可能ですので、各種組織型について進行期となってから最初に行われた化学療法からの生存期間解析を行うことにしました。この場合、cKendall tauが正の値であると「化学療法開始後早期にCGPを受けた患者は全生存期間が短い」、負の値であると「化学療法開始後早期にCGPを受けた患者は全生存期間が長い」ということになります。      

本研究の結果、日本においては基本的に全てのがん種でcKendall tauは正の値でした。なおアメリカのDana-Faberからの診断日を評価開始日とした報告では、診断時stage 1-3の患者ではcKendall tauが負、診断時stage 4の患者ではcKendall tauが概ねゼロとなっていました。日本とアメリカの大きな差異として、アメリカでは進行期や治療内容に関わらずどのタイミングでもCGPを受けることができる点が挙げられます。これの結果から推定される事柄として、進行期になる前に早めに検査を受けることが予後改善に繋がる、健康意識が高くCGP検査を早期に受けるような患者はそもそも予後が良い、あまりに進行した状態でCGPを受けても予後改善には繋がらない、といったことが考えられると思います。    

この左側切断による選択バイアスについては統計学者の間でも補正手法の開発が行われており、とくに日本からは[江村剛志](https://sites.google.com/view/takeshi-emura)先生が第一人者として知られています。左側切断による選択バイアスの補正手法はいくつかのR言語用のパッケージとして公開されています。  
[tranSurv: structural transformation methodでの補正手法](https://github.com/stc04003/tranSurv)  
[depend.truncation: copula-graphic estimatorでの補正手法](https://cran.r-project.org/web/packages/depend.truncation/index.html)    

しかし、tranSurvパッケージを使用して補正を試みたところ、大腸癌の初回化学療法後の生存曲線が右上に凸となり、臨床的に妥当性を欠く結果となってしまいました(下図実線)。なお、本検討のようにcKendall tauが正のデータにNumber at risk adjustmentの手法で補正を行うと過度に補正されて生存期間を極めて短く推定してしまいます(下図点線)。  
![Figure_4, bias adjustment with tranSurv package](github_4.png)  

現状のデータでは正確な全生存期間を推定できないことが分かったため、生存期間を二つに分割して考えることにしました。化学療法の開始からCGPまでの期間と、CGPから最終観察日までの期間については各患者から情報を得られます。そして、これらは左側切断がない生存情報であるため、通常のKaplan-Meier estimatorでの生存期間解析が可能です。全患者が必ずCGPを受けていますので、化学療法からCGPまでの期間は全く打ち切りがありません。CGPまでの期間で作成した生存曲線は概ね指数関数の様な形状でしたが、極めて初期の段階では傾きが小さく、あまりCGP検査を受ける症例が多くない傾向が見られました。この現象は様々な解釈が可能です。治療開始早期は化学療法の奏功率が高いが徐々に無効例が増加していくということなのかもしれません。本研究ではこの現象が「非常に進行の早い患者は化学療法実施中にも病状が進行するため、CGPを受ける機会を得ないまま死亡する」ということが原因とみなして解析を進めました。また、CGPまでの期間が中央値より短い半数の症例と長い半数の症例に分けて、CGP後の生存期間を通常のKaplan-Meier estimatorで推定すると、早期にCGPを受けた患者は予後が悪いことがわかりました。このことはcKendall tauが正であることと合致しています。  
![Figure_5, total survival devided into two periods](github_5.png)    


