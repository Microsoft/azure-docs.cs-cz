---
title: Analýza zákazníka změn pomocí Machine Learning | Microsoft Docs
description: Případová studie vývoje model integrované k analýze a vyhodnocování zákazníka změn
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 1333ffe2-59b8-4f40-9be7-3bf1173fc38d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: 6c64444fc8d42782065d42ed5ee0c193678bb1f1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="analyzing-customer-churn-using-azure-machine-learning"></a>Analýza zákazníka změn pomocí Azure Machine Learning
## <a name="overview"></a>Přehled
Tento článek představuje odkaz na implementaci projektu analysis změn zákazníka, vytvořené pomocí Azure Machine Learning. V tomto článku probereme přidružené obecné modely pro komplexní řešení tohoto problému změn průmyslových zákazníka. Také měřit přesnost modelů, které jsou vytvořeny pomocí Machine Learning a vyhodnocení pokyny pro vývoj Další.  

### <a name="acknowledgements"></a>Potvrzení
Tohoto experimentu byl vyvinutý a otestovat Serge Berger, vědecký pracovník objekt zabezpečení dat společnosti Microsoft a Roger Barga, dříve správce produktu pro Microsoft Azure Machine Learning. Děkujeme za jejich týmu dokumentace Azure uznává svoje znalosti a Děkujeme, že je tento dokument white paper sdílení.

> [!NOTE]
> Data pro tento experiment není veřejně dostupné. Příklad, jak vytvořit model strojového učení pro analýzu změn, naleznete v části: [prodejní změn šablony modelu](https://gallery.cortanaintelligence.com/Collection/Retail-Customer-Churn-Prediction-Template-1) v [Azure AI Gallery](http://gallery.cortanaintelligence.com/)
> 
> 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-problem-of-customer-churn"></a>Problém zákazníka změn
Firmy na trhu příjemce a ve všech odvětvích enterprise mají jak nakládat s změn. Někdy je příliš změn a vliv rozhodnutí o zásadách. Tradiční řešení je předpovědi vysokou tendenci churners a řešení jejich potřeb prostřednictvím služby concierge marketingových kampaní, nebo použitím zvláštní výjimky. Tato řešení se může lišit z odvětví odvětví. Jejich můžete i se liší podle konkrétní příjemce clusteru v rámci jednoho oboru (například telecommunications).

Běžné faktor je, že podnikům muset minimalizovat úsilí uchování tyto speciální zákazníka. Proto přirozené metodika by pro stanovení skóre každé zákazníků s pravděpodobnost změn a řešení top N ty, které jsou. Hlavní zákazníci může být těm, které jsou nejvíce ziskové. Například ve složitějších scénářích zisku funkce zaměstnání při výběru kandidáty pro speciální výjimku. Tyto aspekty jsou však pouze část dokončení strategie pro práci s změn. Firmy mají také vzít v účtu riziko (a odolnost vůči rizikům přidružené), úroveň a náklady na zásahu a segmentace vyhovující zákazníka.  

## <a name="industry-outlook-and-approaches"></a>Odvětví outlook a přístupů
Sofistikované zpracování změn je znak vyspělá oboru. Classic příkladem je telecommunications odvětví, kde Odběratelé, kteří znají často přepínat z jednoho poskytovatele do jiného. Tato dobrovolná změn je hlavním zájmem. Kromě toho zprostředkovatelé shromáždily významné znalosti o *změn ovladače*, což jsou faktory, které jednotka zákazníkům přepínače.

Například telefonu nebo zařízení volba je dobře známé faktorem změn v podniku mobilního telefonu. Oblíbené zásad v důsledku toho je subvencovat cenu telefonu pro nové odběratele a účtují úplné ceny pro stávající zákazníky služby pro upgrade. V minulosti tato zásada vedlo zákazníkům přepínání z jednoho poskytovatele do druhého a získat tak slevu nové. To, pak má poskytovatelům upřesnit jejich strategie výzva.

Vysoká volatility v telefonu nabídky je faktor, který rychle by způsobila neplatnost modely změn, které jsou založeny na aktuální telefonního sluchátka modelů. Kromě toho mobilních telefonů nejsou jenom telekomunikace zařízení, jsou i příkazy způsobem (zvažte iPhone). Tyto sociálních prognostické jsou nad rámec regulární telecommunications datových sad.

Net výsledek pro modelování je, že nelze navrhnout zásadu zvukové jednoduše tak, že odstraňuje známé důvody změn. Ve skutečnosti strategie průběžné modelování, včetně classic modely, které vyčíslení kategorií proměnné (například rozhodovací stromy), je **povinné**.

Použití velkých datových sad na zákazníky, fungují organizace analýzy velkých objemů dat (zejména detekce změn na základě velkých objemů dat) jako efektivní přístup k problému. Můžete najít další informace o velké objemy dat přístup k problému změn v doporučení v oddílu ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologie pro zákazníka změn modelu
Běžné řešení problémů procesu vyřešit zákazníka změn je znázorněn v obrázky 1 – 3:  

1. Model riziko umožňuje zvažte, jaký vliv na akce pravděpodobnosti a rizika.
2. Model zásahu můžete vzít v úvahu, jak úroveň zásahu mohou ovlivnit pravděpodobnost změn a množství zákazníků hodnotu doby života (CLV).
3. Tato analýza různě kvalitativní analýze, která je rozšířena na proaktivní marketingovou kampaň, která cílí zákaznické segmenty zajistit optimální nabídku.  

![][1]

Tento přístup dál vypadající je nejlepší způsob, jak považovat změn, ale se dodává s složitost: máme pro vývoj více modelu archetype a trasování závislosti mezi modely. Interakce mezi modely můžete zapouzdřené, jak je znázorněno v následujícím diagramu:  

![][2]

*Obrázek 4: Unified více modelu archetype*  

Interakce mezi modely je klíč, pokud jsme se k poskytování komplexní přístup k uchování zákazníka. Každý model nutně snižuje časem; Architektura je proto implicitní smyčky (podobně jako archetype ve standardní OSTRÉ DM dolování dat, nastavení [***3***]).  

Celkové cyklus riziko. rozhodnutí marketing segmentace nebo rozložené je stále zobecněný struktura, která se vztahuje na mnoha obchodních problémů. Analýza změn je jednoduše silné zástupce této skupiny problémů, protože je jádro vykazuje se všechny vlastnosti komplexní podnikové problému, který neumožňuje zjednodušené prediktivní řešení. Sociální aspektů moderní přístup k změn nejsou zvlášť zvýrazněných v přístupu, ale na sociálních aspekty jsou zapouzdřené v archetype modelování, jako by byly v kteréhokoli modelu služeb.  

Zde zajímavé přidání je analýzy velkých objemů dat. Dnešní telekomunikace a prodejní firmy shromažďování dat kompletní o zákazníků a jsme můžete snadno předvídáte, bude potřeba více modelu připojení běžné trendu, danou vznikajícími trendy, jako je například Internet věcí a všudypřítomný zařízení, která umožní obchodní využívat inteligentní řešení v několika vrstev.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementace archetype modelování v nástroji Machine Learning Studio
Zadané problém právě popsaný, co je nejlepší způsob, jak implementovat integrované modelování a vyhodnocování přístup? V této části popisuje, jak jsme udělat to pomocí Azure Machine Learning Studio.  

Při navrhování globální archetype pro změn, více modelu přístup musí je. I vyhodnocování (prediktivní) část tohoto přístupu by měla být více modelu.  

Následující diagram znázorňuje prototyp, že jsme vytvořili, který používá čtyři vyhodnocování algoritmy v Machine Learning Studio k předvídání změn. Není důvod více modelu přístup jenom pro účely vytvoření komplet klasifikátor:, zvýšit přesnost, ale také k ochraně proti přečerpání přizpůsobování a zlepšit výběr doporučený funkce.  

![][3]

*Obrázek 5: Prototyp změn modelování přístup*  

Následující části obsahují další podrobnosti o prototyp vyhodnocování model, který jsme implementovaná pomocí Machine Learning Studio.  

### <a name="data-selection-and-preparation"></a>Příprava a výběr dat
Data se používají k vytváření modelů a skóre zákazníkům byl získán z svislé řešení CRM, s daty matoucí k ochraně ochrana osobních údajů zákazníků. Datové pole obsahuje informace o 8000 odběry v USA a kombinuje tři zdroje: zřizování data (předplatné metadata), data aktivity (použití systému) a data podpory zákazníků. Data neobsahuje žádné obchodní související informace o zákaznících; nezahrnuje například věrného metadata nebo platební skóre.  

Pro jednoduchost ETL a čištění procesy data jsou mimo rozsah protože předpokládáme, že již má Příprava dat neudělalo jinde.   

Výběr funkce pro modelování je založena na předběžné násobek vyhodnocování sadu prognostické, součástí procesu, který používá modul náhodných doménové struktury. Pro implementaci v nástroji Machine Learning Studio jsme vypočítat střední, střední a rozsahy reprezentativní funkce. Například jsme přidali agregace kvalitativní data, jako je například minimální a maximální hodnoty pro aktivity uživatelů.    

Také jsme zachytit dočasné informace o posledních šest měsíců. Jsme analyzovali data po dobu jednoho roku a navázat jsme, že i když nebyly statisticky významný trendy, vliv na změn značně dojde ke snížení po dobu šesti měsíců.  

Nejvíce důležité je, že byl implementován celý proces, včetně ETL, výběr součástí a modelování v nástroji Machine Learning Studio, použití zdrojů dat v Microsoft Azure.   

Následující obrázky znázorňují data, která byla použita.  

![][4]

*Obrázek 6: Výňatek ze zdroje dat (matoucí)*  

![][5]

*Obrázek 7: Funkce extrahována ze zdroje dat*
 

> Všimněte si, že tato data jsou privátní, a proto nelze sdílet modelu a data.
> Ale pro podobné model pomocí veřejně dostupnými daty, najdete v této ukázce experiment v [Azure AI Galerie](http://gallery.cortanaintelligence.com/): [Telco zákazníka změn](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Další informace o tom, jak můžete implementovat model analysis změn pomocí Cortana Intelligence Suite, doporučujeme také [toto video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) podle hlavní programový manažer západní Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmy používané v prototypu
Použili jsme následující čtyři algoritmy strojového učení k vytvoření prototypu (žádné přizpůsobení):  

1. Logistic regression (LR)
2. Vylepšené rozhodovací strom (BT)
3. Zprůměrovanou perceptron (AP)
4. Support vector machine (SVM)  

Následující diagram znázorňuje část návrhovou plochu experimentu, který označuje pořadí, ve kterém byly vytvořeny modely:  

![][6]  

*Obrázek 8: Vytváření modelů v nástroji Machine Learning Studio*  

### <a name="scoring-methods"></a>Vyhodnocování metody
Jsme skóre pro magnitudu čtyři modely pomocí datovou sadu s popiskem školení.  

Odeslali jsme také vyhodnocování datovou sadu, která porovnatelný z hlediska model vytvořené pomocí edice plochy SAS Enterprise analýzy 12. Jsme měří přesnost modelu SAS a všechny čtyři modely Machine Learning Studio.  

## <a name="results"></a>Výsledky
V této části nabízejí jsme našich zjištění o přesnost modely, které jsou založené na vyhodnocování datovou sadu.  

### <a name="accuracy-and-precision-of-scoring"></a>Přesnost a přesnost vyhodnocování
Implementace v Azure Machine Learning je obecně za SAS přesnost asi 10 až 15 % (oblasti pod křivky nebo AUC).  

Nejdůležitější metriky v změn je však rychlost chybnou klasifikaci: tedy z hlavních churners jako předpokládaných pomocí třídění, který je ve skutečnosti se **není** změn a ještě nebyly přijaty zvláštní zacházení? Následující diagram porovná tato chybnou rychlost pro všechny modely:  

![][7]

*Obrázek 9: Passau prototypu oblast pod křivkou*

### <a name="using-auc-to-compare-results"></a>Pomocí AUC k porovnání výsledků
Oblasti v rámci křivky (AUC) je metrika, který představuje globální míru *separability* mezi distribuce skóre pro plnění kladné a záporné. Je podobná tradičním grafu příjemce operátor vlastnosti (ROC), ale jeden důležitý rozdíl je, že metrika AUC nevyžaduje vám vybrat prahovou hodnotu. Místo toho shrnuje výsledky přes **všechny** možné volby. Na rozdíl od tradiční ROC graf zobrazuje kladné rychlost na false kladné frekvence na vodorovné ose a svislé osy a prahovou hodnotu klasifikace se liší.   

AUC se obvykle používá jako měřítko vhodné pro různé algoritmy (nebo jiné systémy) protože umožňuje modely, který se má porovnat prostřednictvím jejich AUC hodnoty. Toto je Oblíbené přístup v odvětví, jako je například meteorologie a biosciences. Proto AUC představuje oblíbených nástroj pro vyhodnocování třídění výkonu.  

### <a name="comparing-misclassification-rates"></a>Porovnání sazby chybnou klasifikaci
Jsme na základě dat CRM přibližně 8 000 jednotek předplatných porovná s chybnou sazby na dotyčném datovou sadu.  

* Rychlost, jakou chybnou SAS se 10 až 15 %.
* Rychlost, jakou chybnou Machine Learning Studio se 15-20 % pro churners prvních 200 300.  

V odvětví telecommunications je důležité adres pouze zákazníků, kteří mají k nejvyšší ohrožení změn prostřednictvím nabídky je služba concierge nebo jiné speciální zacházení. V tomto ohledu dosahuje implementace Machine Learning Studio výsledky srovnatelné s modelu SAS.  

Ze stejného důvodu je důležitější než přesnost přesnost, protože jsme nejvíce zajímají správně klasifikace potenciální churners.  

Z Wikipedia následující diagram znázorňuje vztah v živá, snadno pochopit obrázek:  

![][8]

*Obrázek 10: Kompromis mezi přesnost*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Přesnost výsledků pro model vylepšené rozhodovací strom
Následující graf zobrazuje nezpracovaná výsledků vyhodnocování pomocí Machine Learning prototypu pro vylepšené rozhodovací strom modelu, který se stane být nejpřesnější mezi čtyři modely:  

![][9]

*Obrázek 11: Vylepšené rozhodovací strom modelu vlastnosti*

## <a name="performance-comparison"></a>Porovnání výkonu
Jsme porovná rychlosti, kdy byla data za použití modelů Machine Learning Studio a porovnatelný z hlediska model vytvořily s použitím plochy edice 12.1 analýzy Enterprise SAS.  

Následující tabulka shrnuje výkon algoritmy:  

*Tabulka 1. Obecný výkon (přesnost) algoritmů*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Průměrná modelu |Nejlepší modelu |Nevedou podle očekávání |Průměrná modelu |

Modely hostované v nástroji Machine Learning Studio outperformed SAS 15-25 % pro rychlost zpracování, ale přesnost byla z velké části na nominální.  

## <a name="discussion-and-recommendations"></a>Diskusní a doporučení
V odvětví telecommunications několik postupů této služby k analýze změn, včetně:  

* Odvození metriky pro čtyři základní kategorií:
  * **Entita (například předplatné)**. Zřídit základní informace o odběru nebo zákazníkovi, který je předmětem změn.
  * **Aktivita**. Získejte všechny informace o možných využití, které souvisí s entitou, například počet přihlášení.
  * **Zákaznická podpora**. Shromažďovat informace z protokolů podpory zákazníků k označení, zda u odběru byl problémy nebo interakce s zákaznickou podporu.
  * **Produktivní a obchodních dat**. Získat žádné možné informace o zákazníkovi (například může být k dispozici nebo je obtížné sledovat).
* Použijte význam výběr jednotky funkce. To vyplývá, že model vylepšené rozhodovací strom je vždy Slíbení přístup.  

Použití těchto čtyř kategorií vytváří iluzi, který jednoduchou *deterministickou* přístup, založený na indexy vytvořen na přiměřenou faktorech podle kategorií, měla by stačit k určení zákazníků znamenat nebezpečí změn. Bohužel i když tento pojem zdá se, že pravděpodobné, je false pochopení. Z důvodu je, že změn je efekt dočasné a faktory, které přispívají k změn se obvykle nachází ve přechodný stavy. Co vede zákazník zvážit ponechání dnes můžou být různé zítra a jistě bude jiný šest měsíců od tohoto okamžiku. Proto *pravděpodobnosti* je nezbytné v modelu.  

Tento důležité pozorování bývá často přehlížen v podnikání, které obvykle upřednostní business intelligence orientované přístup k analýze, nejčastěji, protože se jedná jednodušší prodeje a zavést přehledné automatizace.  

Ale potenciálu samoobslužné analytické funkce pomocí Machine Learning Studio, že je čtyř kategorií informací, divize nebo oddělení, se stupněm cenné zdroj pro machine learning o změn.  

Další zajímavé funkce už v Azure Machine Learning je schopnost přidejte vlastní modul do úložiště předdefinované modulů, které jsou již k dispozici. Tato funkce, které v podstatě vytvoří příležitost k výběru knihovny a vytvoření šablon pro vertikální skupiny. Jde důležité rozdílem Azure Machine Learning na trhu místě.  

Chcete-li pokračovat v tomto tématu v budoucnosti Doufáme, zejména související s analýzy velkých objemů dat.
  

## <a name="conclusion"></a>Závěr
Tento dokument popisuje rozumný přístup k řešení problému běžných změn zákazníka pomocí obecné rozhraní. Jsme považována za prototypu pro vyhodnocování modely a implementovat pomocí Azure Machine Learning. Nakonec jsme hodnoceno přesnost a výkon prototypu řešení s ohledem na porovnatelný z hlediska algoritmy v SAS.  

 

## <a name="references"></a>Odkazy
[1] prediktivní analýzy: nad rámec předpovědi, západ McKnight informace správy, července 2011, p.18-20. srpna.  

[2] Wikipedia článek: [přesnost](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [OSTRÉ DM 1.0: Průvodce dolování dat krok za krokem](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing velkých objemů dat: zákazníky oslovíte efektivněji a jednotky hodnota](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco změn šablony modelu](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) v [Azure AI Gallery](http://gallery.cortanaintelligence.com/) 
 

## <a name="appendix"></a>Příloha
![][10]

*Obrázek 12: Snímek prezentace na prototypu změn*

[1]: ./media/azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/azure-ml-customer-churn-scenario/churn-10.png
