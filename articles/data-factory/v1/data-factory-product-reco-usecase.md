---
title: Případ použití Data Factory – doporučení pro produkty
description: Seznamte se s případem použití implementovaným pomocí Azure Data Factory společně s jinými službami.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: b20eef90cebab59f823e1766d28eb355f317463d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387185"
---
# <a name="use-case---product-recommendations"></a>Případ použití – doporučení pro produkty
Azure Data Factory je jedna z mnoha služeb, které se používají k implementaci Cortana Intelligence Suite akcelerátorů řešení.  Podrobnosti o této sadě najdete na stránce [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) . V tomto dokumentu popisujeme běžný případ použití, který uživatelé Azure už vyřešili a implementovali pomocí Azure Data Factory a dalších služeb Cortana Intelligence komponent.

## <a name="scenario"></a>Scenario
Online prodejci obvykle chtějí přesvědčit své zákazníky, aby si mohli koupit produkty tím, že je prezentují s produkty, které nejpravděpodobněji chtějí zajímat, a proto se s největší pravděpodobně koupí. K tomu je potřeba, aby online prodejci přizpůsobili online uživatelské prostředí pomocí individuálních doporučení produktu pro konkrétního uživatele. Tato individuální doporučení se provádějí na základě jejich současného a historických dat o chování při nákupu, informací o produktech, nově zavedených značek a dat segmentace produktů a zákazníků.  Kromě toho můžou poskytnout doporučení pro uživatele, která jsou založená na analýze celkového chování při používání ze všech jejich uživatelů v kombinaci.

Cílem těchto prodejců je optimalizovat pro převody uživatelů na prodej a získat vyšší tržby za prodej.  Dosahují tohoto převodu poskytováním kontextových doporučení týkajících se chování na základě zájmů a akcí zákazníků. Pro účely tohoto případu použití používáme online prodejci jako příklad firem, které chcete pro zákazníky optimalizovat. Nicméně tyto zásady se vztahují na všechny firmy, které chtějí zapojit své zákazníky do svého zboží a služeb a zdokonalit jejich nákupy zákazníků pomocí individuálních doporučení produktu.

## <a name="challenges"></a>Výzvy
Při pokusu o implementaci tohoto typu případu použití se může vymezit mnoho výzev online prodejců. 

Nejdřív je potřeba, aby data různých velikostí a tvarů byla ingestovaná z více zdrojů dat, jak místně, tak v cloudu. Tato data zahrnují data produktu, historická data o chování zákazníků a uživatelská data, protože uživatel prochází online prodejní lokalitu. 

Druhé, individuální doporučení produktu musí být rozumně a přesně vypočítáno a předpovězeno. Online prodejci navíc potřebují kromě dat o produktech, značkách a zákaznících taky na základě zákaznických a zákaznických údajů zahrnout zpětnou vazbu od zákazníků v minulých nákupech až po určení nejvhodnějších doporučení produktu pro uživatele. 

Třetí, doporučení je potřeba okamžitě doručovat uživateli, aby poskytovali bezproblémové procházení a nákup a poskytovali nejnovější a relevantní doporučení. 

Maloobchodní prodejci si nakonec potřebují změřit účinnost jejich přístupu sledováním celkového počtu úspěšných a prodávaných prodejů v rámci převodu a úprav na jejich budoucí doporučení.

## <a name="solution-overview"></a>Přehled řešení
Tento ukázkový případ použití byl vyřešen a implementován skutečnými uživateli Azure pomocí Azure Data Factory a dalších Cortana Intelligence služeb komponent, včetně [HDInsight](https://azure.microsoft.com/services/hdinsight/) a [Power BI](https://powerbi.microsoft.com/).

Online prodejce používá úložiště objektů BLOB v Azure, místní SQL Server, Azure SQL Database a relační datové tržiště jako své možnosti úložiště dat v celém pracovním postupu.  Úložiště objektů BLOB obsahuje informace o zákaznících, údaje o chování zákazníků a data informací o produktech. Údaje o produktu obsahují informace o značkách produktu a katalog produktů uložený místně ve službě Azure synapse Analytics. 

Všechna data jsou kombinována a podávána do systému doporučení produktu, aby mohla doručovat přizpůsobená doporučení na základě zájmů a akcí zákazníků, zatímco uživatel prochází produkty v katalogu na webu. Zákazníci také uvidí produkty, které souvisejí s produktem, na základě celkových vzorů používání webu, které se nevztahují k jednomu uživateli.

![Diagram případu použití](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabajty nezpracovaných souborů webového protokolu se vygenerují každý den z online webu maloobchodního prodejce jako částečně strukturované soubory. Nezpracované soubory webových protokolů a informace o katalogu zákazníků a produktů se pravidelně ingestují do úložiště objektů BLOB v Azure pomocí globálně nasazeného přesunu dat Data Factory jako služby. Nezpracované soubory protokolu pro daný den jsou rozdělené na oddíly (po rocích a měsíčně) ve službě BLOB Storage pro dlouhodobé ukládání.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) se používá k vytvoření oddílů nezpracovaných souborů protokolů v úložišti objektů BLOB a zpracování zpracovaných protokolů ve velkém měřítku pomocí obou skriptů pro podregistr i prasečí. Data protokolů v děleném webovém protokolu se pak zpracují za účelem extrakce potřebných vstupů pro systém doporučení pro strojové učení, aby se vygenerovala přizpůsobená doporučení produktu.

Systém doporučení, který se používá pro strojové učení v tomto příkladu, je open source doporučení pro strojové učení z [Apache Mahout](https://mahout.apache.org/).  V případě může být použit libovolný [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) nebo vlastní model.  Model Mahout se používá k předvídání podobnosti mezi položkami na webu na základě celkových vzorů použití a k vygenerování individuálních doporučení na základě jednotlivých uživatelů.

Nakonec se sada výsledků individuálních doporučení produktu přesune do relačního datového tržiště, aby je mohla spotřebovat web prodejce.  K sadě výsledků lze také získat přímý odkaz z úložiště objektů BLOB jiné aplikace nebo přesunutí do dalších úložišť pro ostatní příjemce a případy použití.

## <a name="benefits"></a>Výhody
Díky optimalizaci strategie pro doporučení produktu a jejich vyrovnání s obchodními cíli řešení splnilo prodejní a marketingové cíle online prodejce. Kromě toho byli schopni zprovoznění a spravovat pracovní postup doporučení produktu efektivně, spolehlivým a cenově efektivním způsobem. Tento přístup usnadňuje aktualizaci svého modelu a vyladění jeho efektivity na základě míry úspěšnosti prodejního kliknutí na převod. Pomocí Azure Data Factory bylo možné opustit svou časově náročnou a náročnou správu cloudových prostředků a přejít na správu cloudových prostředků na vyžádání. Proto by bylo možné ušetřit čas, peníze a zkrátit čas na nasazení řešení. Zobrazení datových linií a stav provozní služby se nedají snadno vizualizovat a řešit pomocí Data Factory intuitivního uživatelského rozhraní pro monitorování a správu, které je dostupné Azure Portal. Jejich řešení je teď možné naplánovat a spravovat, aby byla dokončená data spolehlivě vyráběná a dodávaná uživatelům a aby se závislosti dat a zpracování automaticky spravovaly bez zásahu člověka.

Díky této individuální možnosti nákupu si online prodejce vytvořil více konkurenčních a působivých zákaznických zkušeností a zvyšuje tak tržby a celkovou spokojenost zákazníků.

