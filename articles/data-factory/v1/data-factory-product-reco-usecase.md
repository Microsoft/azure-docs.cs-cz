---
title: Případ použití datové továrny – doporučení produktu
description: Informace o případu použití implementované pomocí Azure Data Factory spolu s dalšími službami.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 49ad9be7c70602132436b14234f01a4086d8e1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70139140"
---
# <a name="use-case---product-recommendations"></a>Případ použití – doporučení produktu
Azure Data Factory je jednou z mnoha služeb používaných k implementaci akcelerátorů řešení Cortana Intelligence Suite.  Podrobnosti o této sadě najdete na stránce [Cortana Intelligence Suite.](https://www.microsoft.com/cortanaanalytics) V tomto dokumentu popisujeme běžný případ použití, který uživatelé Azure už vyřešili a implementovali pomocí Azure Data Factory a dalších služeb komponent Cortana Intelligence.

## <a name="scenario"></a>Scénář
On-line prodejci běžně chtějí nalákat své zákazníky k nákupu výrobků tím, že jim předloží produkty, které jsou s největší pravděpodobností zájem, a proto s největší pravděpodobností koupit. Chcete-li toho dosáhnout, online prodejci musí přizpůsobit online prostředí svých uživatelů pomocí přizpůsobených doporučení produktů pro tohoto konkrétního uživatele. Tato personalizovaná doporučení mají být provedena na základě jejich aktuálních a historických údajů o chování při nakupování, informací o produktech, nově zavedených značek a údajů o segmentaci produktů a zákazníků.  Kromě toho mohou poskytnout doporučení produktu uživatele na základě analýzy celkového chování při použití od všech svých uživatelů dohromady.

Cílem těchto prodejců je optimalizovat pro konverze uživatele a prodeje a získat vyšší tržby.  Tohoto převodu dosahují tím, že poskytují kontextová doporučení produktů založená na chování založená na zájmech zákazníků a akcích. V tomto případě použití používáme online maloobchodníky jako příklad firem, které chtějí optimalizovat pro své zákazníky. Tyto zásady se však vztahují na všechny podniky, které chtějí zaujmout své zákazníky kolem svého zboží a služeb a zlepšit nákupní zkušenosti svých zákazníků s personalizovanými doporučeními produktů.

## <a name="challenges"></a>Výzvy
Existuje mnoho problémů, které on-line prodejci čelí při pokusu o implementaci tohoto typu případu použití. 

Za prvé, data různých velikostí a obrazců musí být ingestována z více zdrojů dat, a to jak v místním prostředí, tak v cloudu. Tato data zahrnují produktová data, historická data o chování zákazníků a uživatelská data při procházení online maloobchodního webu. 

Za druhé, personalizovaná doporučení produktů musí být rozumně a přesně vypočtena a předpovězena. Kromě chování produktů, značek a zákazníků a údajů o prohlížeči musí online prodejci také zahrnout zpětnou vazbu od zákazníků k minulým nákupům, aby zohlednili určení nejlepších doporučení produktu pro uživatele. 

Za třetí, doporučení musí být okamžitě doručitelné uživateli, aby poskytovaly bezproblémové procházení a nákup a poskytovaly nejnovější a relevantní doporučení. 

A konečně, maloobchodníci musí měřit účinnost svého přístupu sledováním celkových prodejních úspěchů prodávaných a křížových prodejů a přizpůsobení se jejich budoucím doporučením.

## <a name="solution-overview"></a>Přehled řešení
Tento příklad případu použití byl vyřešen a implementován skutečnými uživateli Azure pomocí Azure Data Factory a dalších služeb komponent Cortana Intelligence, včetně [HDInsight](https://azure.microsoft.com/services/hdinsight/) a [Power BI](https://powerbi.microsoft.com/).

Online prodejce používá úložiště objektů blob Azure, místní SQL server, Azure SQL DB a relační data mart jako své možnosti ukládání dat v celém pracovním postupu.  Úložiště objektů blob obsahuje informace o zákaznících, data o chování zákazníků a údaje o informacích o produktech. Údaje o informacích o produktu zahrnují informace o značce produktu a katalog produktů uložený místně v datovém skladu SQL. 

Všechna data jsou kombinována a vložena do systému doporučení produktu, aby poskytovala personalizovaná doporučení založená na zájmech a akcích zákazníků, zatímco uživatel prochází produkty v katalogu na webových stránkách. Zákazníci také vidí produkty, které souvisejí s produktem, na který se dívají, na základě celkových vzorců používání webových stránek, které nesouvisejí s žádným uživatelem.

![diagram případu použití](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabyty nezpracovaných souborů webového protokolu jsou generovány denně z webových stránek online prodejce jako polostrukturované soubory. Nezpracované soubory webového protokolu a informace o zákaznících a katalogu produktů se pravidelně ingestují do úložiště objektů blob Azure pomocí globálně nasazeného přesunu dat jako služby globálně nasazeným datovým pohybem. Nezpracované soubory protokolu pro tento den jsou rozděleny (podle roku a měsíce) v úložišti objektů blob pro dlouhodobé úložiště.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) se používá k rozdělení souborů nezpracovaných protokolů v úložišti objektů blob a zpracování ingemed protokolů ve velkém měřítku pomocí hive i pig skripty. Rozdělená data webových protokolů jsou pak zpracována tak, aby extrahovaly potřebné vstupy pro systém doporučení strojového učení, aby se vygenerovala přizpůsobená doporučení produktů.

Systém doporučení používaný pro strojové učení v tomto příkladu je platforma doporučení strojového učení s otevřeným zdrojovým kódem od [Apache Mahout](https://mahout.apache.org/).  Všechny [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) nebo vlastní model lze použít na scénář.  Mahoutův model se používá k předvídání podobnosti mezi položkami na webu na základě celkových vzorců využití a ke generování přizpůsobených doporučení založených na jednotlivých uživatelích.

Nakonec je sada výsledků přizpůsobených doporučení produktů přesunuta do relačního datového tržiště pro spotřebu na webových stránkách prodejce.  Sada výsledků může být také přístupné přímo z úložiště objektů blob jinou aplikací nebo přesunuty do dalších úložišť pro jiné spotřebitele a případy použití.

## <a name="benefits"></a>Výhody
Optimalizací strategie doporučení produktů a sladěním s obchodními cíli řešení splnilo obchodní cíle online prodejce. Kromě toho byli schopni zprovoznit a spravovat pracovní postup doporučení produktu efektivním, spolehlivým a nákladově efektivním způsobem. Tento přístup jim usnadnil aktualizaci modelu a doladění jeho účinnosti na základě měření úspěšnosti prodeje pro klikání na konverzi. Pomocí Azure Data Factory se jim podařilo opustit časově náročnou a nákladnou ruční správu cloudových prostředků a přejít na správu cloudových prostředků na vyžádání. Proto se jim podařilo ušetřit čas, peníze a zkrátit dobu nasazení řešení. Zobrazení datových linek a stav provozních služeb se staly snadno vizualizovatelným a odstraňováním problémů s intuitivním rozhraním pro monitorování a správu datové továrny, které je k dispozici na portálu Azure. Jejich řešení lze nyní naplánovat a spravovat tak, aby hotová data byla spolehlivě vytvářena a doručována uživatelům a závislosti dat a zpracování byly automaticky spravovány bez lidského zásahu.

Tím, že poskytuje tento personalizovaný zážitek z nakupování, on-line prodejce vytvořil konkurenceschopnější, poutavější zákaznickou zkušenost, a tím zvýšil prodej a celkovou spokojenost zákazníků.

