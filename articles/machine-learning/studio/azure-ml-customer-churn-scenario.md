---
title: Analýza změn zákazníků
titleSuffix: ML Studio (classic) - Azure
description: Případová studie vývoje integrovaného modelu pro analýzu a vyhodnocování změn zákazníků pomocí Azure Machine Learning Studio (classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 4cf918abae51ca330054ef86e57095d29a21a37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204524"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analýza změn zákazníků pomocí Azure Machine Learning Studio (klasická)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Přehled
Tento článek představuje referenční implementaci projektu analýzy změn zákazníků, který je vytvořen pomocí Azure Machine Learning Studio (classic). V tomto článku se zabýváme přidruženými obecnými modely pro holisticky řešení problému průmyslových změn zákazníků. Měříme také přesnost modelů, které jsou vytvořeny pomocí strojového učení, a vyhodnocujeme pokyny pro další vývoj.  

### <a name="acknowledgements"></a>Poděkování
Tento experiment byl vyvinut a testován Serge Bergerem, hlavním datovým vědcem společnosti Microsoft, a Rogerem Bargou, dříve produktovým manažerem pro Microsoft Azure Machine Learning Studio (klasika). Dokumentační tým Azure vděčně oceňuje jejich odborné znalosti a děkuje jim za sdílení této bílé knihy.

> [!NOTE]
> Data použitá pro tento experiment nejsou veřejně dostupná. Příklad, jak vytvořit model strojového učení pro analýzu změn, najdete v [tématu: Šablona modelu maloobchodních změn](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) v [Galerii Azure AI](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problém konve zákazníků
Podniky na spotřebitelském trhu a ve všech odvětvích podnikání se musí vypořádat s konvemi. Někdy je konve nadměrná a ovlivňuje politická rozhodnutí. Tradičním řešením je předpovídat churners s vysokým sklonem a řešit jejich potřeby prostřednictvím služby concierge, marketingových kampaní nebo použitím speciálních dispensací. Tyto přístupy se mohou v jednotlivých odvětvích lišit. Mohou se dokonce lišit od konkrétního spotřebitelského klastru k jinému v rámci jednoho odvětví (například telekomunikací).

Společným faktorem je, že podniky musí minimalizovat tyto speciální úsilí o udržení zákazníků. Přirozenou metodikou by tedy bylo skóre každého zákazníka s pravděpodobností konve a řešení nejvyšších N. Nejlepší zákazníci mohou být ty nejziskovější. Například v sofistikovanějších scénářích se při výběru kandidátů na zvláštní výjimku používá funkce zisku. Tyto úvahy jsou však pouze součástí kompletní strategie pro řešení změn. Podniky musí také brát v úvahu riziko (a související toleranci vůči riziku), úroveň a náklady na intervenci a přijatelnou segmentaci zákazníků.  

## <a name="industry-outlook-and-approaches"></a>Výhled a přístupy průmyslu
Sofistikované zacházení s konveje je známkou zralého průmyslu. Klasickým příkladem je telekomunikační průmysl, kde je známo, že předplatitelé často přecházejí z jednoho poskytovatele na druhého. Tato dobrovolná konve je prvořadým zájmem. Poskytovatelé navíc nashromáždili významné znalosti o *ovladačích konve*, což jsou faktory, které vedou zákazníky k přechodu.

Například, telefon nebo zařízení volba je známý řidič konve v mobilním telefonu podnikání. V důsledku toho je populární politikou dotovat cenu za sluchátko pro nové předplatitele a účtovat plnou cenu stávajícím zákazníkům za upgrade. Historicky tato politika vedla k tomu, že zákazníci skákali od jednoho poskytovatele k druhému, aby získali novou slevu. To zase přimělo poskytovatele, aby své strategie zpřesňovali.

Vysoká volatilita v nabídce sluchátek je faktorem, který rychle ruší modely konve, které jsou založeny na aktuálních modelech sluchátek. Kromě toho, mobilní telefony nejsou jen telekomunikační zařízení, ale jsou také módní prohlášení (zvažte iPhone). Tyto sociální prediktory jsou mimo rozsah běžných telekomunikačních datových souborů.

Čistý výsledek pro modelování je, že nelze navrhnout zvuk zásady jednoduše odstraněním známé důvody pro konve. Ve skutečnosti je **povinná**strategie průběžného modelování, včetně klasických modelů, které kvantifikují kategorické proměnné (například rozhodovací stromy).

Pomocí velkých objemových datových sad na svých zákaznících provádějí organizace analýzu velkých objemů dat (zejména detekci konve na základě velkých objemů dat) jako účinný přístup k problému. Další informace o přístupu k problému změn o velkých objemech naleznete v části Doporučení na ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodika modelování konve zákazníků
Na obrázcích 1-3 je znázorněn běžný proces řešení problémů pro řešení změn zákazníků:  

1. Rizikový model umožňuje zvážit, jak akce ovlivňují pravděpodobnost a riziko.
2. Intervenční model umožňuje zvážit, jak by úroveň zásahu mohla ovlivnit pravděpodobnost změn a množství hodnoty životnosti zákazníka (CLV).
3. Tato analýza se hodí ke kvalitativní analýze, která je eskalována na proaktivní marketingovou kampaň, která se zaměřuje na segmenty zákazníků, aby poskytla optimální nabídku.  

![Diagram znázorňující, jak tolerance rizika a rozhodovací modely poskytují užitečné přehledy](./media/azure-ml-customer-churn-scenario/churn-1.png)

Tento přístup hledící dopředu je nejlepší způsob, jak zacházet s konve, ale přichází s složitostí: musíme vyvinout vícemodelový archetyp a trasování závislostí mezi modely. Interakce mezi modely může být zapouzdřena, jak je znázorněno na následujícím diagramu:  

![Diagram interakce modelu churn](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Obrázek 4: Jednotný vícemodelový archetyp*  

Interakce mezi modely je klíčová, máme-li zajistit holistický přístup k udržení zákazníků. Každý model nutně degraduje v průběhu času; proto architektura je implicitní smyčka (podobně jako archetyp nastavený standardem dolování dat CRISP-DM, [***3***]).  

Celkový cyklus segmentace/rozkladu rizika a rozhodování o marketingu je stále zobecněnou strukturou, která je použitelná pro mnoho obchodních problémů. Analýza konve je prostě silným zástupcem této skupiny problémů, protože vykazuje všechny rysy složitého obchodního problému, který neumožňuje zjednodušené prediktivní řešení. Sociální aspekty moderního přístupu k konve nejsou nijak zvlášť zdůrazněny v přístupu, ale sociální aspekty jsou zapouzdřeny v modelování archetypu, jak by tomu bylo v každém modelu.  

Zajímavým doplňkem je analýza velkých objemů dat. Dnešní telekomunikační a maloobchodní podniky shromažďují vyčerpávající údaje o svých zákaznících a my můžeme snadno předvídat, že potřeba vícemodelové konektivity se stane společným trendem vzhledem k novým trendům, jako je internet věcí a všudypřítomné zařízení, která umožňují podnikům využívat inteligentní řešení na více vrstvách.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementace modelovacího archetypu ve Studiu strojového učení (klasika)
Vzhledem k popsanému problému, jaký je nejlepší způsob, jak implementovat integrovaný modelovací a bodový přístup? V této části ukážeme, jak jsme toho dosáhli pomocí Azure Machine Learning Studio (klasické).  

Přístup více modelů je nutné při navrhování globální archetyp pro konve. Dokonce i bodování (prediktivní) část přístupu by měla být vícemodelová.  

Následující diagram znázorňuje prototyp, který jsme vytvořili, který využívá čtyři algoritmy bodování v Machine Learning Studio (klasické) předpovědět konve. Důvodem pro použití přístupu s více modely je nejen vytvoření klasifikátoru souboru pro zvýšení přesnosti, ale také pro ochranu před nadměrnou montáží a zlepšení výběru normativních funkcí.  

![Snímek obrazovky znázorňující složitý pracovní prostor Studio (klasický) s mnoha propojenými moduly](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Obrázek 5: Prototyp přístupu modelování konve*  

Následující části obsahují další podrobnosti o modelu hodnocení prototypu, který jsme implementovali pomocí Machine Learning Studio (classic).  

### <a name="data-selection-and-preparation"></a>Výběr a příprava dat
Data použitá k sestavení modelů a skóre zákazníků byla získána z vertikálního řešení CRM, přičemž data byla zamlžena, aby byla chráněna soukromí zákazníků. Data obsahují informace o 8 000 předplatných v USA a kombinují tři zdroje: zřizování dat (metadata předplatného), data o aktivitách (využití systému) a data zákaznické podpory. Data neobsahují žádné obchodní informace o zákaznících; například neobsahuje věrnostní metadata nebo kreditní skóre.  

Pro jednoduchost jsou procesy ETL a čištění dat mimo rozsah, protože předpokládáme, že příprava dat již byla provedena jinde.

Výběr funkcí pro modelování je založen na předběžném vyhodnocování významnosti sady prediktorů, které jsou součástí procesu, který používá modul náhodné doménové struktury. Pro implementaci v Machine Learning Studio (klasické), jsme vypočítali průměr, medián a rozsahy pro reprezentativní funkce. Například jsme přidali agregace pro kvalitativní data, jako jsou minimální a maximální hodnoty pro aktivitu uživatele.

Také jsme zachytili časové informace za posledních šest měsíců. Analyzovali jsme data za jeden rok a zjistili jsme, že i když došlo ke statisticky významným trendům, účinek na konve se po šesti měsících výrazně snižuje.  

Nejdůležitější je, že celý proces, včetně ETL, výběr funkcí a modelování byl implementován v Machine Learning Studio (klasické), pomocí zdrojů dat v Microsoft Azure.   

Následující diagramy znázorňují data, která byla použita.  

![Snímek obrazovky s ukázkou dat použitých s nezpracovanými hodnotami](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Obrázek 6: Výňatek ze zdroje dat (zamlžený)*  

![Snímek obrazovky se statistickými funkcemi extrahovanými ze zdroje dat](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Obrázek 7: Funkce extrahované ze zdroje dat*
 

> Všimněte si, že tato data jsou soukromá, a proto nelze sdílet model a data.
> Pro podobný model s využitím veřejně dostupných dat však naleznete v tomto ukázkovém experimentu v [galerii Azure AI](https://gallery.azure.ai/): [Telco Customer Churn](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Chcete-li se dozvědět více o tom, jak můžete implementovat model analýzy konve pomocí Cortana Intelligence Suite, doporučujeme také [toto video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) od Senior Program Manager Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmy použité v prototypu
K sestavení prototypu jsme použili následující čtyři algoritmy strojového učení (bez přizpůsobení):  

1. Logistická regrese (LR)
2. Posílený rozhodovací strom (BT)
3. Průměrný perceptron (AP)
4. Podpůrný vektorový stroj (SVM)  

Následující diagram znázorňuje část návrhu plochy experimentu, která označuje pořadí, ve kterém byly vytvořeny modely:  

![Snímek obrazovky s malou částí plátna studiového experimentu](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Obrázek 8: Vytváření modelů ve studiu Strojového učení (klasické)*  

### <a name="scoring-methods"></a>Metody hodnocení
Čtyři modely jsme získali pomocí popisované trénovací datové sady.  

Také jsme odeslali datovou sadu hodnocení do srovnatelného modelu vytvořeného pomocí desktopové edice SAS Enterprise Miner 12. Změřili jsme přesnost modelu SAS a všech čtyř modelů Machine Learning Studio (klasické).  

## <a name="results"></a>Výsledky
V této části uvádíme naše zjištění o přesnosti modelů na základě datové sady bodování.  

### <a name="accuracy-and-precision-of-scoring"></a>Přesnost a přesnost bodování
Obecně platí, že implementace v Azure Machine Learning Studio (klasické) je za Přesnost SAS přibližně o 10-15% (Plocha pod křivkou nebo AUC).  

Nejdůležitější metrikou v konve je však míra chybné klasifikace: to znamená, že z nejlepších n churners, jak předpověděl klasifikátor, který z nich ve skutečnosti nekonve, a přesto obdržel zvláštní zacházení? **not** Následující diagram porovnává tuto míru chybné klasifikace pro všechny modely:  

![Plocha pod křivkovým grafem porovnávající výkon 4 algoritmů](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Obrázek 9: Plocha prototypu Pasova pod křivkou*

### <a name="using-auc-to-compare-results"></a>Použití AUC k porovnání výsledků
Oblast pod křivkou (AUC) je metrika, která představuje globální měřítko *oddělitelnosti* mezi rozdělením skóre pro pozitivní a negativní populace. Je podobný grafu tradiční charakteristiky operátora přijímače (ROC), ale jeden důležitý rozdíl je, že metrika AUC nevyžaduje, abyste zvolili prahovou hodnotu. Místo toho shrnuje výsledky nad **všemi** možnými volbami. Naproti tomu tradiční graf ROC zobrazuje kladnou míru na svislé ose a falešně pozitivní míru na vodorovné ose a prahová hodnota klasifikace se liší.   

AUC se používá jako měřítko hodnoty pro různé algoritmy (nebo různé systémy), protože umožňuje porovnávání modelů pomocí jejich hodnot AUC. To je populární přístup v odvětvích, jako je meteorologie a biovědy. AUC tedy představuje populární nástroj pro hodnocení výkonu klasifikátoru.  

### <a name="comparing-misclassification-rates"></a>Porovnání míry chybné klasifikace
Porovnali jsme míry chybné klasifikace na dané datové sadě pomocí dat CRM přibližně 8 000 odběrů.  

* Míra chybné klasifikace SAS byla 10–15 %.
* Míra chybné klasifikace Machine Learning Studio (klasická) byla 15-20% pro top 200-300 churners.  

V telekomunikačním průmyslu je důležité oslovit pouze ty zákazníky, kteří mají nejvyšší riziko, že se mohou rozplývat, a to tím, že jim nabídneme službu concierge nebo jiné zvláštní zacházení. V tomto ohledu implementace Machine Learning Studio (classic) dosahuje výsledků na stejné úrovni jako model SAS.  

Ze stejného důvodu je přesnost důležitější než přesnost, protože se většinou zajímáme o správnou klasifikaci potenciálních churners.  

Následující diagram z Wikipedie znázorňuje vztah v živé, snadno pochopitelné graf:  

![Dva cíle. Jeden cíl ukazuje hit značky volně seskupeny, ale v blízkosti býků-oko označené "Nízká přesnost: dobrá pravdivost, špatná přesnost. Další cíl pevně seskupeny, ale daleko od býků-oko označené "Nízká přesnost: špatná pravdivost, dobrá přesnost"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Obrázek 10: Kompromis mezi přesností a přesností*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Výsledky přesnosti a přesnosti pro posílený model rozhodovacího stromu
Následující graf zobrazuje nezpracované výsledky z bodování pomocí prototypu Machine Learning pro model posíleného rozhodovacího stromu, který je shodou že nejpřesnější ze čtyř modelů:  

![Fragment tabulky zobrazující přesnost, přesnost, odvolání, F-skóre, AUC, průměrná ztráta protokolu a ztráta protokolu školení pro čtyři algoritmy](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Obrázek 11: Zesílené charakteristiky modelu rozhodovacího stromu*

## <a name="performance-comparison"></a>Porovnání výkonu
Porovnali jsme rychlost, s jakou byla data hodnocena pomocí modelů Machine Learning Studio (klasické) a srovnatelného modelu vytvořeného pomocí desktopové edice SAS Enterprise Miner 12.1.  

Následující tabulka shrnuje výkon algoritmů:  

*Tabulka 1. Celkový výkon (přesnost) algoritmů*

| LR | BT | AP | Svm |
| --- | --- | --- | --- |
| Průměrný model |Nejlepší model |Nedostatečné výsledky |Průměrný model |

Modely hostované v Machine Learning Studio (klasické) překonaly SAS o 15-25% pro rychlost provádění, ale přesnost byla z velké části na stejné úrovni.  

## <a name="discussion-and-recommendations"></a>Diskuse a doporučení
V telekomunikačním průmyslu se objevilo několik postupů pro analýzu konve, včetně:  

* Odvodit metriky pro čtyři základní kategorie:
  * **Entita (například předplatné)**. Poskytování základních informací o předplatném nebo zákazníkovi, který je předmětem změn.
  * **Aktivita**. Získejte všechny možné informace o použití, které se vztahují k entitě, například počet přihlášení.
  * **Zákaznická podpora**. Informace o sklizni z protokolů zákaznické podpory označte, zda předplatné mělo problémy nebo interakce se zákaznickou podporou.
  * **Konkurenceschopné a obchodní údaje**. Získejte veškeré možné informace o zákazníkovi (například může být nedostupné nebo těžko sledovatelné).
* Použijte důležitost k výběru funkcí jednotky. To znamená, že posílený rozhodovací strom model je vždy slibný přístup.  

Použití těchto čtyř kategorií vytváří iluzi, že jednoduchý *deterministický* přístup založený na indexech vytvořených na základě přiměřených faktorů podle kategorie by měl postačovat k identifikaci zákazníků ohrožených změnou. Bohužel, i když se tato představa zdá věrohodná, je to falešné pochopení. Důvodem je, že konve je časový efekt a faktory přispívající k konve jsou obvykle v přechodných stavech. To, co zákazníka vede k tomu, aby zvážil odchod dnes, může být zítra jiné a určitě to bude za šest měsíců jiné. Proto je *pravděpodobnostní* model nutností.  

Toto důležité pozorování je často přehlíženo v podnikání, které obecně preferuje přístup k analýze orientovaný na business intelligence, hlavně proto, že je to snadnější prodej a připouští přímou automatizaci.  

Příslib samoobslužné analýzy pomocí Machine Learning Studio (klasické) je však, že čtyři kategorie informací, klasifikované podle divize nebo oddělení, se stanou cenným zdrojem pro strojové učení o konve.  

Další vzrušující funkce přicházející v Azure Machine Learning Studio (klasické) je možnost přidat vlastní modul do úložiště předdefinovaných modulů, které jsou již k dispozici. Tato funkce v podstatě vytváří příležitost k výběru knihoven a vytváření šablon pro vertikální trhy. Je to důležitý diferenciátor Azure Machine Learning Studio (klasické) na trhu.  

Doufáme, že v tomto tématu budeme pokračovat i v budoucnu, zejména v souvislosti s analýzou velkých objemů dat.
  

## <a name="conclusion"></a>Závěr
Tento článek popisuje rozumný přístup k řešení společného problému změn zákazníků pomocí obecného rámce. Zvažovali jsme prototyp pro vyhodnocování modelů a implementovali ho pomocí Azure Machine Learning Studio (classic). Nakonec jsme vyhodnotili přesnost a výkon prototypového řešení s ohledem na srovnatelné algoritmy v SAS.  

 

## <a name="references"></a>Odkazy
[1] Prediktivní analýza: Beyond the Predictions, W. McKnight, Information Management, červenec/srpen 2011, s. 18-20.  

[2] Článek na Wikipedii: [Přesnost a přesnost](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Průvodce dolování dat krok za krokem](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big Data Marketing: Zapojte své zákazníky efektivněji a zvýšit hodnotu](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Šablona modelu telco konve](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) v [Galerii Azure AI](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Příloha
![Snímek prezentace na prototypu konve](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Obrázek 12: Snímek prezentace na prototypu konve*
