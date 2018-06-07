---
title: Případ použití Data Factory - doporučení produktů
description: Další informace o případ použití implementovaná pomocí Azure Data Factory spolu s jinými službami.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3328a7c80a05a48b3c04ad5492a7eafe62059681
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622461"
---
# <a name="use-case---product-recommendations"></a>Příklad použití – doporučení produktu
Azure Data Factory je jedním z mnoha služby použít k implementaci řešení akcelerátorů Cortana Intelligence Suite.  V tématu [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics) stránku Podrobnosti o této sady. V tomto dokumentu jsme popisují běžné případ použití, který Azure uživatelé už vyřeší a implementovaná pomocí Azure Data Factory a dalším službám součásti Cortana Intelligence.

## <a name="scenario"></a>Scénář
Prodejci běžně chcete přesvědčit jejich zákazníci zakoupit produktů je prezentací s produkty, které jsou nejčastěji zajímat a proto pravděpodobně koupit. K tomu potřeba prodejci přizpůsobit své uživatele online prostředí pomocí přizpůsobené produktu doporučení pro tento konkrétní uživatel. Tato přizpůsobené doporučení se má na základě jejich aktuálním a historickém shopping data o chování, informace o produktu, nově přináší značek a segmentace dat produktu a zákazníka.  Kromě toho můžete poskytují doporučení produktů uživatele na základě analýzy chování celkové využití z jejich uživatelé kombinaci.

Cílem těchto prodejců je optimalizovat pro uživatele klikněte na tlačítko Prodej převody a vám vyšší výnosy prodeje.  Se zavedením kontextové, na základě chování produktu doporučení na základě zájmů zákazníka a akce dosáhnout tento převod. Pro tento případ použití používáme prodejci jako příklad firmám, které chcete optimalizovat pro své zákazníky. Ale tyto zásady se vztahují na všechny podnikání, které chce zaujmout svým zákazníkům kolem jeho zboží a služeb a zajištění lepších možností nákupní svým zákazníkům s doporučeními přizpůsobené produktu.

## <a name="challenges"></a>Výzvy
Existuje mnoho problémů s této prodejci setkávají při pokusu o implementaci tohoto typu případ použití. 

Nejdřív musíte data různých tvarů a obrazců konzumaci z více zdrojů dat, jak místně a v cloudu. Tato data zahrnují data produktu, data o chování historických zákazníka a data uživatele jako uživatel prohlíží web online prodejní. 

Doporučení produktů druhou, přizpůsobené musí být přiměřené a přesně vypočítat a předpovědět. Kromě produktu, značky a chování a prohlížeč data zákazníků třeba prodejci také zahrnout názory zákazníků na posledních nákupy zohlednit při určování nejlepší doporučení produktů pro uživatele. 

Doporučení třetí, musí být okamžitě dodávky uživateli, aby zadejte plynulé procházení a nákup prostředí a poskytovat doporučení nejnovější a relevantní. 

Nakonec prodejců muset měřit efektivitu jejich přístup pomocí funkce sledování celkového až prodává a cross prodává klikněte na převod prodeje úspěchy a upravit na jejich budoucí doporučení.

## <a name="solution-overview"></a>Přehled řešení
Tento případ použití příklad je vyřešeno a implementované skutečných Azure uživatele pomocí Azure Data Factory a dalším službám součásti Cortana Intelligence, včetně [HDInsight](https://azure.microsoft.com/services/hdinsight/) a [Power BI](https://powerbi.microsoft.com/).

Online prodejce používá úložiště objektů Blob v Azure, místní SQL server, databázi SQL Azure a relační datové Tržiště jako jejich možnosti ukládání dat v celém pracovním postupu.  Úložiště objektů blob obsahuje informace o zákazníkovi, data o chování zákazníků a data informace o produktu. Data produktu informace obsahují informace o produktu značky a produkt katalogu uložené místně v SQL data warehouse. 

Všechna data je kombinaci a dodáni do systému doporučení produktu zajistit přizpůsobený doporučení na základě zájmů zákazníka a akcí, když uživatel prohlíží produktů v katalogu na webu. Zákazníci také zobrazit produkty, které se vztahují k produktu, které jsou prohlížení podle celkové webu vzorce, které nejsou v relaci žádné s jedním uživatelem.

![diagram případu použití](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabajty nezpracovaná webové soubory protokolu jsou generovány denně z webu online prodejce jako částečně strukturovaných soubory. Soubory protokolu nezpracovaná webové a informace o katalogu zákazníka a produkt je konzumována pravidelně do Azure Blob storage pomocí služby Data Factory přesun globálně nasazené dat jako službu. V souborech protokolů nezpracovaná den jsou rozdělena na oddíly (podle roku a měsíce) v úložišti objektů blob pro dlouhodobé uložení.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) se používá k vytvoření oddílů nezpracovaných log soubory v úložišti objektů blob a zpracovávat ingestovaný protokolů škálované použití Hive a Pig skriptů. Oddílů webové protokoly dat je pak zpracovávají extrahovat potřebné vstupy pro machine learning doporučení systému a generovat doporučení přizpůsobené produktu.

Systém doporučení použít pro strojového učení v tomto příkladu je otevřeným zdrojem strojového učení platformy doporučení z [Apache Mahout](http://mahout.apache.org/).  Všechny [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) nebo vlastní modelu lze použít pro tento scénář.  Mahout model se používá k předvídání podobnosti mezi položky na webu na základě celkového způsobů využití a ke generování přizpůsobených doporučení na základě jednotlivé uživatele.

Nakonec sadu výsledků dotazu z přizpůsobené produktu doporučení je přesunuta do relační datové tržiště pro používání webu prodejce.  Sada výsledků může také být přístup přímo z úložiště objektů blob v jiné aplikaci, nebo přesunuta do další úložiště pro ostatní příjemce a případy použití.

## <a name="benefits"></a>Výhody
Optimalizace jejich strategie doporučení produktu a zarovnání s obchodními cíli, splní řešení online prodejce prodeje a marketingu cíle. Kromě toho jsou schopny zprovoznit a spravovat pracovní postup produktu doporučení efektivní, spolehlivou a cenově způsobem. Přístupu usnadnit pro ně k aktualizaci jejich modelu a systém doladit jeho účinnost podle míry prodeje úspěchů klikněte na převod. Pomocí Azure Data Factory byly moct abandon jejich správa prostředků časově náročná a nákladná ruční cloudu a přesunout do správy prostředků cloudu na vyžádání. Proto že byli schopni ušetřit čas, peníze a který snižuje doba pro jejich nasazení řešení. Zobrazení rodokmenu dat a stavu provozní služby se stala usnadňuje vizualizaci a Poradce při potížích s intuitivní monitorování objektu pro vytváření dat a správu uživatelského rozhraní, které jsou k dispozici na portálu Azure. Své řešení lze nyní naplánováno a spravovat tak, že dokončení data jsou spolehlivě vytváří a doručeny uživatelům a dat a zpracování závislosti jsou automaticky spravovány bez lidského zásahu.

Díky této přizpůsobené nákupní rozhraní online prodejce vytvořit více produktivní, nestačí, aby zákazník prostředí a proto zvýšit spokojenost zákazníků prodeje a celková.

