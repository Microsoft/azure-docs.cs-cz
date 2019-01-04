---
title: Případ použití Data Factory - doporučení produktů
description: Seznamte se případem použití implementovaný s využitím Azure Data Factory společně s dalšími službami.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8ff100cd3fc1c9def10b4e585119414281b90d92
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017374"
---
# <a name="use-case---product-recommendations"></a>Příklad použití – doporučení produktu
Azure Data Factory je jednou z mnoha služeb používaný k implementaci akcelerátorů řešení Cortana Intelligence Suite.  Zobrazit [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) stránku Podrobnosti o této sadě. V tomto dokumentu popisujeme běžným případem použití, kteří uživatelé Azure už vyřešit a implementované pomocí služby Azure Data Factory a další služby komponent Cortana Intelligence.

## <a name="scenario"></a>Scénář
Prodejci se běžně chtít přesvědčit svým zákazníkům zakoupit produkty tím, že předloží s produkty, které jsou pravděpodobně zajímá a proto nejpravděpodobněji koupí. K tomu třeba prodejci přizpůsobit online prostředí svoje uživatele pomocí individuální doporučení produktů pro tento konkrétní uživatel. Tato individuální doporučení se má na základě jejich současné a historické nákupní chování data, informace o produktech, nově zavedená značky a produktům a zákaznickým segmentace dat.  Kromě toho můžete poskytují doporučení produktů uživatele na základě analýzy chování celkové využití všech svých uživatelů v kombinaci.

Cílem těchto prodejci je optimalizovat pro uživatele klikněte na prodej převody a získat vyšší prodejních výnosů.  Tento převod dosahují poskytováním doporučení produktů kontextové, na základě chování na základě zájmy zákazníka a akcí. Pro tento případ použití použijeme jako příklad firmám, které chcete optimalizovat pro své zákazníky prodejci. Ale tyto zásady platí i pro jakoukoli firmu, která chce zapojily své zákazníky po jeho zboží a služeb a zajištění lepších možností nákupu svým zákazníkům s individuální doporučení produktů.

## <a name="challenges"></a>Výzvy
Existují řadu výzev tuto plošku prodejci při implementaci tohoto typu případu použití. 

Nejprve musí z různých zdrojů dat, ingestovat data z různých velikostí a tvarů v místním prostředí i v cloudu. Tato data zahrnují data produktu, historických chování dat zákazníků a dat uživatele jako uživatel prohlíží web online maloobchodní prodej. 

Doporučení produktů druhý vybaveným a přizpůsobeným musí být přiměřeně a přesně počítá a předpovědět. Kromě produktu, značky a zákaznická data chování a prohlížeče prodejci potřebovat zahrnout zpětnou odezvu zákazníků za posledních nákupy zohlednit při určování nejvhodnější doporučení produktů pro uživatele. 

Doporučení třetí, musí být okamžitě dodávky uživateli k zajištění bezproblémového procházení a nákup prostředí a nejnovější a relevantní doporučení. 

A konečně maloobchodníci muset změřte efektivitu svůj přístup pomocí sledování celkového křížového a křížového kliknutím převod prodejní úspěchy a upravit tak, aby jejich budoucí doporučení.

## <a name="solution-overview"></a>Přehled řešení
Tento případ použití příkladu byl vyřešen a implementované skutečných uživatelů pomocí Azure Data Factory a dalším službám součástí Cortana Intelligence, včetně [HDInsight](https://azure.microsoft.com/services/hdinsight/) a [Power BI](https://powerbi.microsoft.com/).

Online prodejce používá úložiště objektů Blob v Azure, místnímu serveru SQL, Azure SQL DB a relační datové Tržiště jako jejich možnosti úložiště dat v průběhu pracovního postupu.  Úložiště objektů blob obsahuje informace o zákaznících, data o chování zákazníků a data informace o produktu. Data produktu. informace obsahuje informace o produktu značky a produkt katalogu uložené místně v SQL data warehouse. 

Všechna data se sloučí a předány do produktu systému doporučení k poskytování individuálních doporučení na základě zájmy zákazníka a akcí, zatímco uživatel prochází produktů v katalogu na webu. Zákazníci také zobrazit produkty, které se vztahují k produktu, které se díváte na základě celkové webu způsobů využití, které spolu nesouvisí žádné s jedním uživatelem.

![Diagram případu použití](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabajtů nezpracovaná webové soubory protokolu jsou generovány, každý den z webu online prodejce jako částečně strukturované soubory. Nezpracovaná webové soubory protokolů a informace o katalogu zákazníků a produktů je pravidelně přijatých do služby Azure Blob storage pomocí Data Factory pro přesuny globálně nasazených dat jako službu. Nezpracovaných souborů protokolu pro tento den dělí (podle roku a měsíce) v úložišti objektů blob pro dlouhodobé uložení.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) se používá k rozdělení nezpracovaných souborů protokolu v úložišti objektů blob a zpracovávat přijaté protokoly ve velkém měřítku pomocí skripty v Hivu a Pigu. Dělené webové protokoly dat je pak zpracovány extrahovat potřebné vstupy pro strojové učení doporučení systému, generovat individuální doporučení produktů.

Doporučení systému, použít pro strojové učení v tomto příkladu je open source machine learning platforma pro doporučení [Apache Mahout](http://mahout.apache.org/).  Žádné [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) nebo vlastního modelu lze použít ve scénáři.  Mahout model se používá k předpovědi podobnosti mezi položkami na webu na základě celkové způsobů využití a ke generování individuální doporučení podle jednotlivých uživatelů.

Nakonec sada výsledků individuální doporučení produktů se přesune do relačního datového tržiště za spotřebu na webu prodejce.  Sadu výsledků dotazu může také možné přistupovat přímo z úložiště objektů blob v jiné aplikaci, nebo přesunout do dalších úložišť pro ostatní uživatele a případy použití.

## <a name="benefits"></a>Výhody
Optimalizace své strategie doporučení produktů a zarovnání s obchodními cíli, splní řešení online prodejce prodeje a marketingových cílů. Kromě toho jsou schopny zprovoznění a správa pracovního postupu doporučení produktů způsobem efektivnější, spolehlivé a cenově výhodnější. Tento přístup provedených můžou snadno aktualizovat své modelu a vyladění jeho efektivity na základě míry prodejní úspěchy klikněte na převod. Pomocí služby Azure Data Factory studenti mohli opustit jejich správy časově náročné a nákladné ruční cloudových prostředků a přechod na správu prostředků na vyžádání v cloudu. Proto se studenti mohli šetřit čas a peníze a snížit jejich čas potřebný k nasazení řešení. Zobrazení rodokmenu dat a provozní službě service health se snadno vizualizovat a řešení potíží se sadou intuitivní monitorování služby Data Factory a uživatelské rozhraní, které jsou k dispozici na webu Azure Portal pro správu. Jejich řešení nyní je možné naplánovat a spravovat tak, aby spolehlivě vytvořený a doručí uživatelům dokončení dat a dat a zpracování závislosti se spravují automaticky bez zásahu člověka.

Zadáním této personalizované prostředí pro nákup online prodejce vytvořili zákazníka větší konkurenceschopnost a zajímavé prostředí a proto zvýšit spokojenost zákazníků prodejní a celková.

