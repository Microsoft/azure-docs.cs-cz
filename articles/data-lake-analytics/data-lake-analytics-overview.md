---
title: Přehled Azure Data Lake Analytics
description: Díky službě Data Lake Analytics můžete podpořit své podnikání s využitím přehledů získaných z cloudových dat v jakémkoli měřítku.
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: overview
ms.date: 06/23/2017
ms.openlocfilehash: e49dd110c85573c034d593940e4d19a5466a1511
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220121"
---
# <a name="what-is-azure-data-lake-analytics"></a>Co je Azure Data Lake Analytics?

Azure Data Lake Analytics je služba analytických úloh na vyžádání, která zjednodušuje práci s velkými objemy dat. Namísto nasazení, konfigurace a ladění hardwaru můžete psát dotazy, transformovat data a extrahovat cenné informace. Analytická služba dokáže okamžitě zpracovávat úlohy libovolného rozsahu – stačí nastavit, jaký výkon potřebujete. Za úlohu platíte jenom tehdy, když je spuštěná, což je nákladově efektivní. 

## <a name="azure-data-lake-analytics-recent-update-information"></a>Informace o nejnovější aktualizaci Azure Data Lake Analytics

Služba Azure Data Lake Analytics Service se pro určitý účel aktualizuje na aperiodic bázi. Dál poskytujeme podporu pro tuto službu pomocí aktualizace komponent, verze beta verze Preview a tak dále. 

- Obecné informace o nejnovější aktualizaci najdete v tématu [co je nového v Data Lake Analytics?](data-lake-analytics-whats-new.md).
- Informace o jednotlivých aktualizacích najdete v [poznámkách k verzi Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).

## <a name="dynamic-scaling"></a>Dynamické škálování
  
Data Lake Analytics dynamicky zřizuje prostředky a umožňuje provádět analýzy terabajtů až petabajtů dat. Platíte pouze za výkon využitý při zpracování. Při zvětšování a zmenšování objemu uložených dat nebo množství využitých výpočetních prostředků nemusíte přepisovat kód. 

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>Rychlejší vývoj a inteligentnější ladění a optimalizace pomocí známých nástrojů
  
Data Lake Analytics se úzce integruje se sadou Visual Studio. Ke spouštění, ladění a optimalizaci kódu můžete používat známé nástroje. Vizualizace úloh U-SQL umožňuje sledovat běh kódu v reálných podmínkách a snadno identifikovat kritická místa výkonu a optimalizovat náklady.

## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>Jazyk U-SQL: jednoduchý a známý, výkonný a rozšiřitelný
  
Data Lake Analytics obsahuje dotazovací jazyk U-SQL, který rozšiřuje dobře známý, jednoduchý a deklarativní charakter jazyka SQL o výrazovou sílu jazyka C#. Jazyk U-SQL využívá stejný distribuovaný modul runtime, který využívá i interní datové jezero Microsoftu pro exabajtové objemy dat. Vývojáři SQL a .NET teď můžou zpracovávat a analyzovat svá data s využitím dovedností, které už mají.

## <a name="integrates-seamlessly-with-your-it-investments"></a>Hladká integrace s vašimi investicemi do IT
  
Služba Data Lake Analytics využívá vaše stávající investice do IT pro účely identity, správy a zabezpečení. Tento přístup usnadňuje řízení dat a umožňuje snadné rozšíření stávajících datových aplikací. Služba Data Lake Analytics je integrovaná se službou Active Directory pro účely oprávnění a správy uživatelů a obsahuje integrované monitorování a auditování.

## <a name="affordable-and-cost-effective"></a>Cenová dostupnost a nákladová efektivita

Služba Data Lake Analytics je nákladově efektivní řešení pro spouštění úloh s velkým objemem dat. Platíte za jednotlivé úlohy, kdy se zpracovávají data. Není potřeba žádný hardware, licence ani smlouvy o podpoře pro konkrétní službu. Při spuštění a dokončení úlohy systém automaticky vertikálně navyšuje a snižuje kapacitu, takže nikdy neplatíte za víc, než potřebujete. [Další informace o kontrole nákladů a úsporách](https://aka.ms/adlasavemoney).

## <a name="works-with-all-your-azure-data"></a>Možnost práce se všemi daty Azure
  
Data Lake Analytics spolupracuje s Azure Data Lake Storage pro nejvyšší výkon, propustnost a paralelní zpracování a funguje s Azure Storagemi objekty blob, Azure SQL Database a Azure synapse Analytics (dříve SQL Data Warehouse).


## <a name="next-steps"></a>Další kroky

* Podívejte se na Azure Data Lake Analytics Poslední aktualizace s využitím [novinek v Azure Data Lake Analytics?](data-lake-analytics-whats-new.md)
* Začínáme s Data Lake Analytics pomocí rozhraní příkazového řádku [Azure Portal](data-lake-analytics-get-started-portal.md)  |  [Azure PowerShell](data-lake-analytics-get-started-powershell.md)  |  [CLI](data-lake-analytics-get-started-cli.md)
* Správa Azure Data Lake Analytics pomocí [Azure Portal](data-lake-analytics-manage-use-portal.md)  |  [Azure PowerShell](data-lake-analytics-manage-use-powershell.md)  |  [CLI](data-lake-analytics-manage-use-cli.md)  |  [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md)  |  [Node.js](data-lake-analytics-manage-use-nodejs.md)
* [Kontrola nákladů a úspory s využitím služby Data Lake Analytics](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
