# Files for analysis of C-CAT survival data.  
Supplementary code for the paper: Tamura T, et. al., Selection Bias Due to Delayed Comprehensive Genomic Profiling in Japan. Cancer Sci. 2022 Nov 12.  doi: 10.1111/cas.15651. Online ahead of print.  
This explanation text was written in Japanese.  
C-CATのユーザーは基本的に日本人と思われますので、説明は日本語で記載します。  

# C-CATのデータにおける左側切断と右側打ち切りによる生存期間解析への影響
がんゲノム検査を受ける患者は、発症後治療を受け、全ての標準治療を終了してから検査にエントリーします。進行が非常に早い患者はがんゲノム検査を受ける前に死亡してしまうこと、主治医と患者の考えによって検査のエントリー時期にばらつきが出ることから、C-CATに登録されている患者情報には少なからずバイアスが生じています。
