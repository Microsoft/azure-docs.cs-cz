---
title: "Přehled Microsoft Azure Data Lake Analytics | Dokumentace Microsoftu"
description: "Data Lake Analytics je služba Azure pro velké objemy dat, která umožňuje využívat data k podpoře podnikání díky informacím získaným z vašich dat v cloudu, bez ohledu na jejich velikost a umístění."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: 316c35fa4b04bdb251c2b9ae14f6b32f4e4bf939
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Přehled Microsoft Azure Data Lake Analytics

## <a name="what-is-azure-data-lake-analytics"></a>Co je Azure Data Lake Analytics?
Azure Data Lake Analytics je služba analytických úloh na vyžádání, která zjednodušuje práci s velkými objemy dat. Namísto nasazení, konfigurace a ladění hardwaru můžete psát dotazy, transformovat data a extrahovat cenné informace. Analytická služba dokáže okamžitě zpracovávat úlohy libovolného rozsahu – stačí nastavit, jaký výkon potřebujete. Za úlohu platíte jenom tehdy, když je spuštěná, což je nákladově efektivní. Analytická služba podporuje také jazyk U-SQL, který kombinuje výhody jazyka SQL a sílu imperativního kódu. Jazyk U-SQL umožňuje analyzovat data napříč SQL Serverem v Azure a službami Data Lake Store, Azure SQL Database a Azure SQL Data Warehouse.

## <a name="dynamic-scaling"></a>Dynamické škálování
  
Služba Data Lake Analytics je navržena pro cloudové škálování a výkon.  Dynamicky zřizuje prostředky a umožňuje provádět analýzy terabajtů nebo dokonce exabajtů dat. Po dokončení úlohy postupně automaticky ukončí prostředky, takže platíte jenom za využitý výpočetní výkon. Při zvětšování a zmenšování objemu uložených dat nebo množství využitých výpočetních prostředků nemusíte přepisovat kód. Můžete se soustředit pouze na obchodní logiku, a nikoli na způsob zpracování a ukládání rozsáhlých datových sad.

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>Rychlejší vývoj a inteligentnější ladění a optimalizace pomocí známých nástrojů
  
Služba Data Lake Analytics je těsně integrovaná se sadou Visual Studio, takže můžete spouštět, ladit a optimalizovat kód pomocí známých nástrojů. Vizualizace úloh U-SQL umožňuje sledovat běh kódu v reálných podmínkách a snadno identifikovat kritická místa výkonu a optimalizovat náklady.


## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>Jazyk U-SQL: jednoduchý a známý, výkonný a rozšiřitelný
  
Data Lake Analytics obsahuje dotazovací jazyk U-SQL, který rozšiřuje dobře známý, jednoduchý a deklarativní charakter jazyka SQL o výrazovou sílu jazyka C#. Jazyk U-SQL je založený na stejném distribuovaném modulu runtime, který využívají systémy velkých objemů dat ve společnosti Microsoft. Miliony vývojářů SQL a .NET teď můžou zpracovávat a analyzovat svá data pomocí dovedností, které už mají.

## <a name="integrates-seamlessly-with-your-it-investments"></a>Hladká integrace s vašimi investicemi do IT
  
Služba Data Lake Analytics může využívat vaše stávající investice do IT pro účely identity, správy, zabezpečení a datových skladů. Tento přístup usnadňuje řízení dat a umožňuje snadné rozšíření stávajících datových aplikací. Služba Data Lake Analytics je integrovaná se službou Active Directory pro účely oprávnění a správy uživatelů a obsahuje integrované monitorování a auditování.

## <a name="affordable-and-cost-effective"></a>Cenová dostupnost a nákladová efektivita

Služba Data Lake Analytics je nákladově efektivní řešení pro spouštění úloh s velkým objemem dat. Platíte za jednotlivé úlohy, kdy se zpracovávají data. Není potřeba žádný hardware, licence ani smlouvy o podpoře pro konkrétní službu. Při spuštění a dokončení úlohy systém automaticky vertikálně navyšuje a snižuje kapacitu, takže nikdy neplatíte za víc, než potřebujete. [Další informace o kontrole nákladů a úsporách](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c).
    
## <a name="works-with-all-your-azure-data"></a>Možnost práce se všemi daty Azure
  
Data Lake Analytics spolupracuje s Azure Data Lake Store na zajištění nejvyššího výkonu, propustnosti a paralelizace a funguje s objekty blob Azure Storage, službou Azure SQL Database a datovým skladem Azure.

## <a name="next-steps"></a>Další kroky
 
  * Začínáme se službou Data Lake Analytics pomocí webu [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli2.md)
  * Správa služby Azure Data Lake Analytics pomocí webu [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md)
  * [Kontrola nákladů a úspory s využitím služby Data Lake Analytics](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
