---
title: Analýza změn zákazníků
titleSuffix: ML Studio (classic) - Azure
description: Případová studie vývoje integrovaného modelu pro analýzu a vyhodnocování změn zákazníků pomocí Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 903e3f3dcbcc72289fc82ec59dec0305b6adbc17
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150914"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analýza změn zákazníků pomocí Azure Machine Learning Studio (Classic)
## <a name="overview"></a>Přehled
Tento článek představuje referenční implementaci projektu analýzy změn zákazníků, který je sestavený pomocí Azure Machine Learning Studio (Classic). V tomto článku se podíváme na přidružených modelech obecné pro holistické řešení problému přechodu firemních zákazníků změn. Můžeme také měřit přesnost modelů, které jsou vytvořeny pomocí Machine Learning a posoudit pokynů pro další vývoj.  

### <a name="acknowledgements"></a>Potvrzení
Tento experiment jsme vyvinuli a otestovali Serge Berger, hlavními vědeckými daty v Microsoftu a Roger Bargy, dříve produktovým manažerem pro Microsoft Azure Machine Learning Studio (Classic). Děkujeme za jejich má tým dokumentace Azure potvrzuje své znalosti a díky pro tento dokument white paper pro sdílení obsahu.

> [!NOTE]
> Data pro tento experiment není veřejně k dispozici. Příklad vytvoření modelu strojového učení pro analýzu změn najdete v tématu: [Šablona modelu maloobchodních](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) změn v [Azure AI Gallery](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problém výpovědi zákazníků
Firmy na trhu příjemce a ve všech odvětvích enterprise mají se změny. Někdy je příliš vysoká četnost změn a ovlivňuje rozhodnutí o zásadách. Tradiční řešení je k předvídání vysoce tendence churners a řešit jejich potřeby prostřednictvím služby concierge marketingových kampaní, nebo použitím zvláštní výjimky. Tyto přístupy se může lišit od odvětví průmyslu. Můžete dokonce se liší podle konkrétního příjemce clusteru v rámci jednoho oboru (například telekomunikace).

Běžné faktorem je, podniky potřebují, chcete-li minimalizovat tyto aktivity dál rozšiřuji uchování speciální zákazníka. Proto by bylo skóre každý zákazník s pravděpodobnost výpovědi a řešit N nejlepších ty přirozené metody. Hlavní zákazníky může být těm, které jsou nejvíce ziskové. Ve složitějších scénářích je například zisk funkce použijí při výběru kandidáty pro zvláštní výjimku. Tyto aspekty jsou však pouze součástí komplexní strategie pro řešení změn. Také firem vzít v účtu rizika (a související odolnosti vůči rizikům), úroveň a náklady na zásah a segmentu přesvědčivého zákazníků.  

## <a name="industry-outlook-and-approaches"></a>Outlook odvětví a přístupů
Sofistikované zpracování změn je znak až po zralé odvětví. Klasickým příkladem je odvětví telekomunikace, ve kterém se ví často přepínání z jednoho poskytovatele do jiného předplatitele. Těchto dobrovolné častých změn dat je primárním zájmem. Kromě toho poskytovatelé shromáždili významné znalosti o *ovladačích*změn, což jsou faktory, které umožňují zákazníkům přepínat.

Například telefonu nebo zařízení je dobře známé ovladač četností změn ve firmě mobilního telefonu. Oblíbené zásady v důsledku toho je subvencovat cena telefonu pro nové předplatitele a účtovat plné ceny pro stávající zákazníky na upgrade. Tyto zásady v minulosti vedlo k zákazníkům přepínání z jednoho poskytovatele do druhého a získat tak nové slevu. To, pak má zprostředkovatele pro upřesnění jejich strategie výzva.

Vysoká volatility v nabídkách telefonního sluchátka je faktor, který rychle zruší platnost modely četností změn, které jsou založeny na aktuální modely telefonu. Kromě toho mobilní telefony nejsou pouze telekomunikační zařízení, jsou také podporuje příkazy (vezměte v úvahu iPhone). Tyto sociální prediktory jsou nad rámec regulární telekomunikace datových sad.

Net výsledek pro modelování je zvukový zásad nelze navrhnout jednoduše tak, že odstranění známých důvody pro změny. V podstatě je **povinná**strategie průběžného modelování, včetně klasických modelů, které kvantifikují proměnné kategorií (například rozhodovací stromy).

Použití velkých datových sad na zákazníky, organizace provádění analýzy velkých objemů dat (zejména detekce změn na základě velkých objemů dat) jako efektivního přístupu k problému. Můžete najít další informace o přístupu velkých objemů dat na problém ve doporučení v části ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologie k fluktuaci zákazníků modelu
Běžné řešení problémů procesu k vyřešení výpovědi zákazníků je znázorněn v obrázky 1 – 3:  

1. Model rizika umožňuje zvažte, jaký vliv na akce pravděpodobnosti a rizika.
2. Model zásah můžete vzít v úvahu, jak úroveň zásah by mohly ovlivnit pravděpodobnost výpovědi a množství zákazníků hodnotu doby života (CLV).
3. Tato analýza různě kvalitativní analýze, který je eskalován jej proaktivní marketingovou kampaň, která cílí na segmenty zákazníků k zajištění optimální nabídky.  

![Diagram znázorňující, jak odolnost proti riziku a modely rozhodování vykazují užitečné poznatky](./media/azure-ml-customer-churn-scenario/churn-1.png)

Tento přístup vpřed vypadající je nejlepší způsob, jak zpracovávat změny, ale obsahuje složitost: máme pro vývoj vícemodelová archetype a trasování závislosti mezi modely. Interakce mezi modely lze zapouzdřit, jak je znázorněno v následujícím diagramu:  

![Diagram interakce modelu změn](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Obrázek 4: sjednocení Archetype s více modely*  

Interakce mezi modely je klíč, pokud se nám poskytovat holistický přístup k udržení zákazníků. Každý model je nutně zhoršený v čase; Proto je architektura implicitní smyčka (podobně jako Archetype sada se standardem dolování dat, [***3***]).  

Celkové cyklu riziko rozhodnutí marketingových segmentace/rozložené je stále zobecněný strukturu, která platí pro mnoho obchodních problémů. Analýzy změn je jednoduše silné zástupce této skupiny problémů, protože vykazuje osobnostní rysy komplexní obchodní problém, který neumožňuje zjednodušené prediktivní řešení. Sociální aspektů moderní přístup ke změny nejsou zejména zvýrazněno přístup, ale na sociálních sítích aspekty jsou zapouzdřeny v archetype modelování, jako by byly v jakékoli modelu.  

Doplněk zajímavé zde je analýzy velkých objemů dat. Dnešní telekomunikace a prodejní firmy shromažďování vyčerpávající dat o svých zákazníků a jsme můžete snadno předvídat, potřebné pro více modelů připojení se stanou běžné trend dané nově vznikající trendy, jako je například Internet věcí a všudypřítomná zařízení, které umožňují firmy, abyste mohli využívat inteligentní řešení na více úrovních.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementace Archetype modelování v Machine Learning Studio (Classic)
Jaký problém je popsaný, jaký je nejlepší způsob, jak implementovat integrovaný modelování a přístup k bodování? V této části ukážeme, jak se to provádí pomocí Azure Machine Learning Studio (Classic).  

Více modelů přístup je nezbytnost při navrhování globální archetype pro změny. I bodování (prediktivní) část přístup by měl být více modelů.  

Následující diagram znázorňuje prototyp, který jsme vytvořili, který využívá čtyři algoritmy bodování v Machine Learning Studio (Classic) k předpovědi změn. Důvod pro více modelů přístup je jenom pro účely vytvoření třídění skupiny stromů zvýšit přesnost, ale také pro ochranu před útoky over-pass-the přizpůsobování a zlepšení výběr Doporučené funkce.  

![Snímek obrazovky znázorňující komplexní pracovní prostor studia (klasické) s mnoha propojenými moduly](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Obrázek 5: prototypy přístupu k modelování změn*  

Následující části obsahují další podrobnosti o modelu bodování prototypu, který jsme implementovali pomocí Machine Learning Studio (Classic).  

### <a name="data-selection-and-preparation"></a>Výběr dat a příprava
Data se používají k vytváření modelů a zákazníci skóre byl získán z svislé řešení CRM, s daty obfuskovaný na ochranu soukromí zákazníků. Data obsahují informace o předplatných 8 000 v USA a kombinuje tři zdroje: zřizování data (metadata odběru), data o aktivitě (použití systému) a data podpory zákazníků. Data nezahrnují informace o zákaznících, které se týkají podniku; nezahrnuje například věrnostní metadata ani skóre kreditů.  

Pro zjednodušení ETL a procesy pro čištění dat jsou mimo rozsah protože předpokládáme, že už má přípravu dat neprovedlo jinde.

Výběr funkce pro modelování je založená na významu předběžné vyhodnocení sady prediktory, součástí procesu, který používá modul náhodné doménové struktury. Pro implementaci v Machine Learning Studio (Classic) jsme vypočítali průměrnou, mediánovou a rozsah pro zástupce funkcí. Například jsme přidali agregace kvalitativní dat, jako je například minimální a maximální hodnoty pro aktivity uživatelů.

Také zachyceného dočasné informace pro posledních šest měsíců. Analyzovali jsme data po dobu jednoho roku a nastavení jsme, že i v případě, že došlo k statisticky významná trendy, vliv na četnosti změn je výrazně oslabená po šesti měsících.  

Nejdůležitějším bodem je, že celý proces, včetně ETL, výběru funkcí a modelování, byl implementován v Machine Learning Studio (Classic) pomocí datových zdrojů v Microsoft Azure.   

Následující obrázky znázorňují data, která byla použita.  

![Snímek obrazovky znázorňující ukázku dat použitých s nezpracovanými hodnotami](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Obrázek 6: výňatek ze zdroje dat (zakódováno)*  

![Snímek obrazovky znázorňující statistické funkce extrahované ze zdroje dat](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Obrázek 7: funkce extrahované ze zdroje dat*
 

> Všimněte si, že tato data jsou privátní a proto není možné model a data sdílet.
> U podobných modelů, které používají veřejně dostupná data, se ale v tomto ukázkovém experimentu [Azure AI Gallery](https://gallery.azure.ai/): [výpovědi změny zákazníků](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Pokud se chcete dozvědět víc o tom, jak můžete model analýzy změn implementovat pomocí Cortana Intelligence Suite, doporučujeme vám také [Toto video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) od vedoucího programu Hyong tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmy používané v prototypu
Následující čtyři algoritmů strojového učení jsme použili k vytvoření prototypu (bez přizpůsobení):  

1. Logistické regrese (LR)
2. Posílený rozhodovací strom (BT)
3. Průměrné perceptron (AP)
4. Podpora vector machine (SVM)  

Následující diagram znázorňuje části návrhové plochy experiment, který určuje pořadí, ve kterém byly vytvořeny modely:  

![Snímek obrazovky s malým oddílem plátna experimentu studia](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Obrázek 8: vytváření modelů v Machine Learning Studio (klasické)*  

### <a name="scoring-methods"></a>Vyhodnocení metody
Jsme skóre čtyři modely s využitím s popiskem trénovací datové sady.  

Můžeme také odeslat bodování datovou sadu, která srovnatelné model sestavený pomocí klasické pracovní plochy edice SAS Enterprise Miner 12. Naměřenou přesnost modelu SAS a všech čtyř Machine Learning Studioch (klasických) modelů.  

## <a name="results"></a>Výsledky
V této části Představujeme naše poznatky o přesnost modelů, které jsou založené na hodnocení datové sadě.  

### <a name="accuracy-and-precision-of-scoring"></a>Přesnost a přesnost bodování
Obecně platí, že implementace v Azure Machine Learning Studio (Classic) je za přesností SAS přibližně o 10-15% (oblast pod křivkou nebo AUC).  

Nejdůležitější metrika v měně je však **nechybná** míra klasifikace: to znamená horních N změn, které byly předpovězeny tříděním, které jsou ve skutečnosti nezměněné a které ještě přijaly zvláštní ošetření? Následující diagram porovnává tohoto kurzu chybnou pro všechny modely:  

![Oblast pod grafem křivky porovnáním výkonu 4 algoritmů](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Obrázek 9: oblast Passau prototypu pod křivkou*

### <a name="using-auc-to-compare-results"></a>Použití AUC k porovnání výsledků
Plocha pod křivkou (AUC) je metrika, která představuje globální míru *separability* mezi distribucí výsledků pro pozitivní a negativní populaci. Se podobá tradiční grafu příjemce operátor charakteristiku (roc s více TŘÍDAMI), ale jeden důležitý rozdíl je, že AUC metrika není nutné zvolit prahovou hodnotu. Místo toho shrnuje výsledky přes **všechny** možné volby. Naproti tomu tradiční roc s více TŘÍDAMI graf zobrazuje míru pozitivních výsledků svislá osa a míru falešně pozitivních výsledků na vodorovné ose a prahovou hodnotu klasifikace se liší.   

AUC se používá jako měřítko pro různé algoritmy (nebo různé systémy), protože umožňuje porovnat modely pomocí jejich hodnot AUC. Toto je oblíbený přístup v oborech jako jsou meteorologické a biosciences. Proto AUC představuje oblíbený nástroj pro vyhodnocení výkonu třídění.  

### <a name="comparing-misclassification-rates"></a>Porovnání chybnou sazby
Porovnáním chybnou sazby na této datové sadě s použitím dat CRM přibližně 8 000 předplatných.  

* Míra chybnou SAS se 10 až 15 %.
* Frekvence chybných klasifikací Machine Learning Studio (Classic) byla 15-20% pro prvních 200-300 změn.  

V odvětví telekomunikace je potřeba vyřešit pouze zákazníci, kteří mají nejvyšší riziko pro změny tím, že je nabízí službu concierge nebo jiné speciální zacházení. V takovém případě implementace Machine Learning Studio (Classic) dosahuje výsledků s použitím modelu SAS.  

Ze stejného důvodu je důležitější než přesnost přesnost, protože nás zajímají hlavně správně klasifikace potenciální churners.  

Z Wikipedia následující diagram znázorňuje vztah živá, snadno pochopitelné obrázku:  

![Dva cíle. V jednom cíli se zobrazují značky, které jsou volně seskupené, ale poblíž zápasy, která je označena jako nízká přesnost: dobrá pravdivost, nízká přesnost. Jiný cíl úzce seskupený, ale daleko od býků – s označením "nízká přesnost: špatná hodnota pravdivosti", dobrá přesnost "](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Obrázek 10: kompromisy mezi přesností a přesností*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Přesnost výsledků pro model posíleného rozhodovacího stromu
Následující graf zobrazí nezpracované výsledky vyhodnocování pomocí prototypu Machine Learning pro model Posílený rozhodovací strom, což je nejpřesnější mezi čtyři modely:  

![Fragment kódu tabulky znázorňující přesnost, přesnost, odvolání, F-skóre, AUC, průměrnou ztrátu protokolu a ztrátu protokolu školení pro čtyři algoritmy](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Obrázek 11: zvýšení vlastností modelu rozhodovacího stromu*

## <a name="performance-comparison"></a>Porovnání výkonu
Porovnali jsme rychlost, s jakou byly data hodnocena pomocí modelů Machine Learning Studio (Classic) a srovnatelného modelu vytvořeného pomocí stolní edice SAS Enterprise Miner 12,1.  

Následující tabulka shrnuje výkon algoritmy:  

*Tabulka 1. Obecný výkon (přesnost) algoritmů*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Průměrná modelu |Nejlepší Model |Nevedou podle očekávání |Průměrná modelu |

Modely hostované v Machine Learning Studio (Classic) převedly SAS 15-25% na rychlost spuštění, ale přesnost byla převážně na nominální hodnotě.  

## <a name="discussion-and-recommendations"></a>Informace a doporučení
V odvětví telekomunikace vznikly několik postupů k analýze četnosti změn, včetně:  

* Odvození metriky pro čtyři základní kategorie:
  * **Entita (například předplatné)** . Zřízení základních informací o odběru a/nebo zákazníkovi, který je předmětem změn.
  * **Aktivita**. Získáte všechny informace o možných využití, která souvisí s entitou, například počet přihlášení.
  * **Zákaznická podpora**. Získejte informace z protokolů podpory zákazníků k označení, zda u odběru byl problémy nebo interakce s zákaznickou podporu.
  * **Konkurenční a obchodní data**. Získat všechny informace o možných o zákazníkovi (například může být obtížné sledovat nebo není k dispozici).
* Použijte význam pro výběr funkcí jednotky. Z toho vyplývá, že model posíleného rozhodovacího stromu je vždy slibně přístup.  

Použití těchto čtyř kategorií vytvoří iluzi, že jednoduchý *deterministický* přístup, založený na indexech vytvořených v přiměřených faktorech na kategorii, by měl postačovat k identifikaci zákazníků, kteří mají riziko pro změny. Bohužel i když se vyskytují zdá se, že přesvědčivého, je false principy. Důvodem je, že změny jsou dočasné účinek a faktory přispívající k změn dat se obvykle nacházejí v přechodném stavu. Čeho zákazník vzít v úvahu byste museli opustit ještě dnes se může lišit zítra, a jistě bude různých šest měsíců od této chvíle. Proto je model *pravděpodobnostní* nezbytný.  

Tato důležité zjišťování je často přehlédnuta ve firmě, což obecně upřednostňuje analýzy a business intelligence objektově orientovaný přístup, většinou, protože se jedná jednodušší prodávat a zavést jednoduché automatizace.  

Snaha o samoobslužné analýzy pomocí Machine Learning Studio (Classic) se ale stane cenným zdrojem pro strojové učení týkající se změn.  

Další zajímavou funkcí přicházejících v Azure Machine Learning Studio (Classic) je možnost přidat vlastní modul do úložiště předdefinovaných modulů, které jsou již k dispozici. Tato funkce, které vytvoří v podstatě možnost vybrat knihovny a vytvořit šablony pro vertikální trhy. Jedná se o důležité odlišnosti Azure Machine Learning Studio (Classic) na trhu.  

Věříme, že chcete pokračovat v tomto tématu v budoucnu, zejména související s analýzy velkých objemů dat.
  

## <a name="conclusion"></a>Závěr
Tento dokument popisuje rozumné přístup k řešení běžných problémů výpovědi zákazníků pomocí obecného rozhraní. Pokládáme se za prototyp modelů bodování a implementujeme ho pomocí Azure Machine Learning Studio (Classic). Nakonec jsme posuzuje přesnost a výkon prototypu řešení s ohledem na srovnatelné algoritmy v SAS.  

 

## <a name="references"></a>Odkazy
[1] prediktivní analýza: mimo předpovědi, W. McKnight, Správa informací, červenec/srpen 2011, p. 18-20.  

[2] Wikipedii článek: [přesnost a přesnost](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [zaostřené-DM 1,0: Průvodce dolováním dat krok za krokem](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing pro velké objemy dat: efektivnější zapojení zákazníků a jejich hodnoty](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Šablona modelu výpovědi](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) změn v [Azure AI Gallery](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Příloha
![Snímek prezentace v prototypu změn](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Obrázek 12: snímek prezentace v prototypu změn*
