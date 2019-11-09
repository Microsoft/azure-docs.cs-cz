---
title: Analýza změn zákazníků
titleSuffix: ML Studio (classic) - Azure
description: Případová studie vývoje integrovaného modelu pro analýzu a vyhodnocování změn zákazníků pomocí Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 8fd88c3bfad962f264efa030d0a3aea44e95dc8c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839764"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analýza změn zákazníků pomocí Azure Machine Learning Studio (Classic)
## <a name="overview"></a>Přehled
Tento článek představuje referenční implementaci projektu analýzy změn zákazníků, který je sestavený pomocí Azure Machine Learning Studio (Classic). V tomto článku probereme spojené obecné modely pro komplexní řešení problému s průmyslovými zákazníky v průmyslu. Měříme také přesnost modelů, které jsou vytvořeny pomocí Machine Learning, a vyhodnotit pokyny k dalšímu vývoji.  

### <a name="acknowledgements"></a>Potvrzení
Tento experiment jsme vyvinuli a otestovali Serge Berger, hlavními vědeckými daty v Microsoftu a Roger Bargy, dříve produktovým manažerem pro Microsoft Azure Machine Learning Studio (Classic). Tým dokumentace Azure si zaregistruje své znalosti a děkuje jim za sdílení tohoto dokumentu White Paper.

> [!NOTE]
> Data použitá pro tento experiment nejsou veřejně dostupná. Příklad vytvoření modelu strojového učení pro analýzu změn najdete v tématu: [Šablona modelu maloobchodních](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) změn v [Azure AI Gallery](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problém se změnami zákazníků
Firmy na trhu spotřebitelů a ve všech podnikových odvětvích musí zabývat se změnami. Někdy je četnost změn nadměrné a ovlivňuje rozhodování o zásadách. Tradičním řešením je předpovědět změny vysokého prohlížejících a řešit jejich potřeby prostřednictvím služby concierge, marketingových kampaní nebo pomocí speciálních štítků. Tyto přístupy se můžou lišit od odvětví až po obor. Můžou se dokonce lišit od konkrétního spotřebitelského clusteru s jiným v rámci jednoho oboru (například telekomunikace).

Běžným faktorem je, že firmy potřebují minimalizovat tyto zvláštní úsilí na uchovávání zákazníků. Proto by přirozený metodologie musela určit skóre každého zákazníka s pravděpodobností změny a vyřešit horních N. Nejvýhodnější můžou být zákazníci, kteří mají nejvyšší ziskovost. Ve složitějších scénářích je například funkce zisku zaměstnaná během výběru kandidátů na speciální využití. Tyto okolnosti jsou ale jenom součástí kompletní strategie, která se zabývat změnami. Podniky také musí vzít v úvahu riziko (a související toleranci rizik), úroveň a náklady na intervenci a plausible Segmentace zákazníků.  

## <a name="industry-outlook-and-approaches"></a>Odvětví Outlooku a přístupy
Sofistikované zpracování změn je znaménkem vyspělého odvětví. Klasický příklad je telekomunikační odvětví, ve kterém se předplatitelé často přepínají od jednoho poskytovatele k druhému. Toto dobrovolné změny se týkají. Kromě toho poskytovatelé shromáždili významné znalosti o *ovladačích*změn, což jsou faktory, které umožňují zákazníkům přepínat.

Například telefonní sluchátko nebo zařízení je dobře známým ovladačem v mobilním telefonu v podniku. V důsledku toho je oblíbená zásada pro nové předplatitele navýšit cenu telefonního telefonu a při upgradu účtuje celou cenu stávajícím zákazníkům. Historicky tato zásada vedla zákazníky skákající od jednoho poskytovatele k druhému za účelem získání nové slevy. Díky tomu mají poskytovatelé výzvy k upřesnění svých strategií.

Vysoká nestálost v nabídkách se sluchátky je faktorem, který rychle neověřuje modely změn, které jsou založené na současných modelech telefonního sluchátka. Kromě toho mobilní telefony neobsahují pouze telekomunikační zařízení, jsou také způsobem příkazy (Zvažte iPhone). Tyto sociální předpovědi jsou mimo rozsah běžných datových sad pro telekomunikace.

Výsledkem pro modelování je to, že nemůžete jednoduše navrhnout zásady zvuku tím, že Eliminujte známé důvody pro změny. V podstatě je **povinná**strategie průběžného modelování, včetně klasických modelů, které kvantifikují proměnné kategorií (například rozhodovací stromy).

Díky použití velkých objemů dat na svých zákaznících organizace provádějí analýzy velkých objemů dat (zejména zjišťování změn na základě velkých objemů dat) jako účinný přístup k problému. Další informace o přístupu k velkým objemům dat k problému se změnami najdete v části doporučení pro ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologie pro modelování změn zákazníků
Běžný proces řešení potíží pro řešení změn zákazníků je znázorněný na obrázcích 1-3:  

1. Model rizik vám umožní vzít v úvahu, jak akce ovlivňují pravděpodobnost a riziko.
2. Model zásahu vám umožní vzít v úvahu, jak může úroveň zásah ovlivnit pravděpodobnost změn a množství hodnoty životnosti zákazníka (CLV).
3. Tato analýza se zaměřuje na kvalitativní analýzu, která se předává do proaktivní marketingové kampaně, která cílí na segmenty zákazníků, aby poskytovala optimální nabídku.  

![Diagram znázorňující, jak odolnost proti riziku a modely rozhodování vykazují užitečné poznatky](./media/azure-ml-customer-churn-scenario/churn-1.png)

Tento přístup k dopředné vyhledávání je nejlepším způsobem, jak zacházet se změnami, ale obsahuje složitost: musíme vyvíjet Archetype a závislosti mezi modely v několika modelech. Interakce mezi modely může být zapouzdřená, jak je znázorněno v následujícím diagramu:  

![Diagram interakce modelu změn](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Obrázek 4: sjednocení Archetype s více modely*  

Interakce mezi modely je klíč, pokud budeme doručovat holistickýý přístup k udržení zákazníka. Každý model je nutně zhoršený v čase; Proto je architektura implicitní smyčka (podobně jako Archetype sada se standardem dolování dat, [***3***]).  

Celkový cyklus rizikových segmentů a rozkladu rizika pro marketing je stále zobecněnou strukturou, která se vztahuje na mnoho obchodních problémů. Analýza změn je jednoduše silným zástupcem této skupiny problémů, protože vykazuje všechny vlastnosti složitého podnikového problému, který neumožňuje zjednodušené prediktivní řešení. Sociální aspekty moderního přístupu k klidům nejsou v přístupu obzvláště zvýrazněné, ale sociální aspekty jsou zapouzdřené v Archetype modelování, stejně jako v jakémkoli modelu.  

Zajímavou alternativou je analýza velkých objemů dat. Dnešní telekomunikační a maloobchodní podniky shromažďují vyčerpávající údaje o svých zákaznících a můžeme snadno předpokládat, že nutnost připojení k více modelům se stane společným trendem, a to s ohledem na vznikající trendy, jako je Internet věcí a všudypřítomný. zařízení, která umožňují podniku využívat inteligentní řešení na více vrstvách.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementace Archetype modelování v Machine Learning Studio (Classic)
Jaký problém je popsaný, jaký je nejlepší způsob, jak implementovat integrovaný modelování a přístup k bodování? V této části ukážeme, jak to docílíme pomocí klasické verze Azure Machine Learning Studio.  

Přístup s více modely je nutné při navrhování globálních Archetype pro změny. I bodování (prediktivní) část přístupu by měla být vícenásobný model.  

Následující diagram znázorňuje prototyp, který jsme vytvořili, který využívá čtyři algoritmy bodování v Machine Learning Studio (Classic) k předpovědi změn. Důvodem použití přístupu přes více modelů není pouze vytvoření klasifikátoru objektu, aby se zvýšila přesnost, ale také k ochraně před převzetím služeb při selhání a k lepšímu výběru funkcí.  

![Snímek obrazovky znázorňující komplexní pracovní prostor studia (klasické) s mnoha propojenými moduly](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Obrázek 5: prototypy přístupu k modelování změn*  

Následující části obsahují další podrobnosti o modelu bodování prototypu, který jsme implementovali pomocí Machine Learning Studio (Classic).  

### <a name="data-selection-and-preparation"></a>Výběr a Příprava dat
Data použitá k sestavení modelů a zákazníků skóre byla získána z vertikálního řešení CRM s daty, která byla zakódována k ochraně ochrany osobních údajů zákazníků. Data obsahují informace o předplatných 8 000 v USA a kombinuje tři zdroje: zřizování dat (metadata odběru), data o aktivitách (použití systému) a zákaznická data podpory. Data nezahrnují informace o zákaznících, které se týkají podniku; nezahrnuje například věrnostní metadata ani skóre kreditů.  

Pro zjednodušení jsou procesy ETL a čištění dat mimo rozsah, protože předpokládáme, že Příprava dat již byla provedena jinde.

Výběr funkcí pro modelování vychází z předběžného bodování pro sadu předpokládaných hodnot, které jsou součástí procesu, který používá modul náhodných doménových struktur. Pro implementaci v Machine Learning Studio (Classic) jsme vypočítali průměrnou, mediánovou a rozsah pro zástupce funkcí. Přidali jsme například agregace pro kvalitativní data, jako jsou například minimální a maximální hodnoty pro aktivitu uživatele.

Zaznamenali jsme také dočasné informace za posledních šest měsíců. Analyzovali jsme data po dobu jednoho roku a zjistili jsme, že i v případě, že existovaly statisticky významné trendy, je vliv na změny značně snížen po šesti měsících.  

Nejdůležitějším bodem je, že celý proces, včetně ETL, výběru funkcí a modelování, byl implementován v Machine Learning Studio (Classic) pomocí datových zdrojů v Microsoft Azure.   

Následující diagramy ilustrují data, která byla použita.  

![Snímek obrazovky znázorňující ukázku dat použitých s nezpracovanými hodnotami](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Obrázek 6: výňatek ze zdroje dat (zakódováno)*  

![Snímek obrazovky znázorňující statistické funkce extrahované ze zdroje dat](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Obrázek 7: funkce extrahované ze zdroje dat*
 

> Všimněte si, že tato data jsou soukromá, takže model a data nelze sdílet.
> U podobných modelů, které používají veřejně dostupná data, se ale v tomto ukázkovém experimentu [Azure AI Gallery](https://gallery.azure.ai/): [výpovědi změny zákazníků](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Pokud se chcete dozvědět víc o tom, jak můžete model analýzy změn implementovat pomocí Cortana Intelligence Suite, doporučujeme vám také [Toto video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) od vedoucího programu Hyong tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmy používané v prototypu
Použili jsme následující čtyři algoritmy strojového učení k sestavení prototypu (bez přizpůsobení):  

1. Logistické regrese (LR)
2. Zesílený rozhodovací strom (BT)
3. Průměrný Perceptron (AP)
4. Podpora vektorového počítače (SVM)  

Následující diagram znázorňuje část plochy návrhu experimentu, která indikuje pořadí, ve kterém byly vytvořeny modely:  

![Snímek obrazovky s malým oddílem plátna experimentu studia](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Obrázek 8: vytváření modelů v Machine Learning Studio (klasické)*  

### <a name="scoring-methods"></a>Metody bodování
Zjistili jsme čtyři modely pomocí popisku školicí datové sady.  

Také jsme poslali datovou sadu bodování pro srovnatelný model sestavený pomocí stolní edice SAS Enterprise Miner 12. Naměřenou přesnost modelu SAS a všech čtyř Machine Learning Studioch (klasických) modelů.  

## <a name="results"></a>Výsledky
V této části jsme zjistili naše závěry o přesnosti modelů na základě datové sady bodování.  

### <a name="accuracy-and-precision-of-scoring"></a>Přesnost a přesnost bodování
Obecně platí, že implementace v klasické verzi Azure Machine Learning Studio je za přesností SAS o přibližně 10-15% (oblast pod křivkou nebo AUC).  

Nejdůležitější metrika v měně je však **nechybná** míra klasifikace: to znamená horních N změn, které byly předpovězeny tříděním, které jsou ve skutečnosti nezměněné a které ještě přijaly zvláštní ošetření? Následující diagram porovnává tuto četnost klasifikace pro všechny modely:  

![Oblast pod grafem křivky porovnáním výkonu 4 algoritmů](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Obrázek 9: oblast Passau prototypu pod křivkou*

### <a name="using-auc-to-compare-results"></a>Porovnání výsledků pomocí AUC
Plocha pod křivkou (AUC) je metrika, která představuje globální míru *separability* mezi distribucí výsledků pro pozitivní a negativní populaci. Je podobný jako tradiční graf operátoru přijímače (ROC), ale jeden důležitý rozdíl spočívá v tom, že metrika AUC nevyžaduje výběr prahové hodnoty. Místo toho shrnuje výsledky přes **všechny** možné volby. V kontrastu se na rozdíl od tradičního grafu ROC zobrazuje kladná míra na svislé ose a falešně pozitivní míra na vodorovné ose a prahová hodnota klasifikace se liší.   

AUC se používá jako měřítko pro různé algoritmy (nebo různé systémy), protože umožňuje porovnat modely pomocí jejich hodnot AUC. Toto je oblíbený přístup v oborech, jako jsou meteorology a biologie. Proto AUC představuje oblíbený nástroj pro vyhodnocování výkonu třídění.  

### <a name="comparing-misclassification-rates"></a>Porovnávání sazeb chybných klasifikací
V porovnání s použitím dat CRM s přibližně 8 000 odběry jsme porovnali sazby netřídění v dané datové sadě.  

* Míra klasifikace chyb SAS byla 10-15%.
* Frekvence chybných klasifikací Machine Learning Studio (Classic) byla 15-20% pro prvních 200-300 změn.  

V odvětví telekomunikací je důležité řešit pouze ty zákazníky, kteří mají nejvyšší riziko k tomu, že nabízejí služby concierge nebo jiné speciální zpracování. V takovém případě implementace Machine Learning Studio (Classic) dosahuje výsledků s použitím modelu SAS.  

Pomocí stejného tokenu je přesnost důležitější než přesnost, protože máme většinou zájem o správné klasifikaci potenciálních změn.  

Následující diagram z Wikipedii znázorňuje vztah mezi Lively a snadno srozumitelnou grafikou:  

![Dva cíle. V jednom cíli se zobrazují značky, které jsou volně seskupené, ale poblíž zápasy, která je označena jako nízká přesnost: dobrá pravdivost, nízká přesnost. Jiný cíl úzce seskupený, ale daleko od býků – s označením "nízká přesnost: špatná hodnota pravdivosti", dobrá přesnost "](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Obrázek 10: kompromisy mezi přesností a přesností*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Výsledky přesnosti a přesnosti pro model zesíleného rozhodovacího stromu
Následující graf zobrazuje nezpracované výsledky z bodování pomocí prototypu Machine Learning pro model zesíleného rozhodovacího stromu, který se stane nejpřesnější mezi čtyřmi modely:  

![Fragment kódu tabulky znázorňující přesnost, přesnost, odvolání, F-skóre, AUC, průměrnou ztrátu protokolu a ztrátu protokolu školení pro čtyři algoritmy](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Obrázek 11: zvýšení vlastností modelu rozhodovacího stromu*

## <a name="performance-comparison"></a>Porovnání výkonu
Porovnali jsme rychlost, s jakou byly data hodnocena pomocí modelů Machine Learning Studio (Classic) a srovnatelného modelu vytvořeného pomocí stolní edice SAS Enterprise Miner 12,1.  

Následující tabulka shrnuje výkon algoritmů:  

*Tabulka 1. Obecný výkon (přesnost) algoritmů*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Průměrný model |Nejlepší model |Neprovádí se |Průměrný model |

Modely hostované v Machine Learning Studio (Classic) převedly SAS 15-25% na rychlost spuštění, ale přesnost byla převážně na nominální hodnotě.  

## <a name="discussion-and-recommendations"></a>Diskuze a doporučení
V odvětví telekomunikací se některé postupy vyznamenaly k analýze změn, včetně:  

* Odvodit metriky pro čtyři základní kategorie:
  * **Entita (například předplatné)** . Zřizování základních informací o předplatném nebo zákazníkovi, který je předmětem změn.
  * **Aktivita**. Získejte všechny možné informace o využití, které se vztahují k entitě, například počet přihlášení.
  * **Zákaznická podpora**. Vyshromáždění informací z protokolů zákaznické podpory k označení, zda má předplatné nějaké problémy nebo interakce se zákaznickou podporou.
  * **Konkurenční a obchodní data**. Získejte případné informace o zákazníkovi (například může být nedostupná nebo obtížné sledovat).
* Použijte důležitost ke zvýšení výběru funkcí. To znamená, že model zesíleného rozhodovacího stromu je vždy příslibný přístup.  

Použití těchto čtyř kategorií vytvoří iluzi, že jednoduchý *deterministický* přístup, založený na indexech vytvořených v přiměřených faktorech na kategorii, by měl postačovat k identifikaci zákazníků, kteří mají riziko pro změny. I když se tento pojem jeví jako plausible, jedná se o nepravdivé porozumění. Důvodem je to, že se jedná o dočasný efekt a faktory přispívající k provozu jsou obvykle v přechodném stavu. To, co by zákazník mohl vycházet z dnešního dne, může být jiný zítra a v současné době se bude za šest měsíců lišit. Proto je model *pravděpodobnostní* nezbytný.  

Toto důležité sledování je často předané v podnikání, což obecně dává přednost business intelligence orientovanému přístupu k analýzám, a to hlavně proto, že je snazší prodávat a připustit přímočarý automatizaci.  

Snaha o samoobslužné analýzy pomocí Machine Learning Studio (Classic) se ale stane cenným zdrojem pro strojové učení týkající se změn.  

Další zajímavou funkcí, která přichází v rámci klasické verze Azure Machine Learning Studio, je možnost přidat vlastní modul do úložiště předdefinovaných modulů, které jsou již k dispozici. Tato funkce v podstatě vytvoří příležitost pro výběr knihoven a vytváření šablon pro vertikální trhy. Jedná se o významné odlišnosti klasické verze Azure Machine Learning Studio na trhu.  

Doufáme, že budeme v budoucnu pokračovat v tomto tématu, zejména v souvislosti s analýzou velkých objemů dat.
  

## <a name="conclusion"></a>Závěr
Tento dokument popisuje rozumnéý přístup k tomu, aby bylo možné řešit běžný problém se změnami zákazníků pomocí obecného rozhraní. Považujeme za prototyp modelů bodování a implementuje ho pomocí klasické verze Azure Machine Learning Studio. Nakonec jsme vyhodnotili přesnost a výkon řešení prototypu s ohledem na srovnatelné algoritmy v SAS.  

 

## <a name="references"></a>Odkazy
[1] prediktivní analýza: mimo předpovědi, W. McKnight, Správa informací, červenec/srpen 2011, p. 18-20.  

[2] Wikipedii článek: [přesnost a přesnost](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [zaostřené-DM 1,0: Průvodce dolováním dat krok za krokem](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing pro velké objemy dat: efektivnější zapojení zákazníků a jejich hodnoty](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Šablona modelu výpovědi](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) změn v [Azure AI Gallery](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Příloha
![Snímek prezentace v prototypu změn](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Obrázek 12: snímek prezentace v prototypu změn*
