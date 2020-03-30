---
title: Co je Azure SQL Database Edge? | Dokumentace Microsoftu
description: Další informace o Azure SQL Database Edge
keywords: úvod do okraje databáze SQL,co je sql database edge, přehled okraje databáze SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246700"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Co je Azure SQL Database Edge Preview?

Azure SQL Database Edge Preview je optimalizovaný relační databázový stroj zaměřený na nasazení IoT a IoT Edge. Poskytuje funkce pro vytvoření vysoce výkonné vrstvy pro ukládání a zpracování dat pro aplikace a řešení IoT. Azure SQL Database Edge poskytuje funkce pro streamování, zpracování a analýzu relačních a nerelačních dat, jako jsou data JSON, grafů a časových řad, což z něj činí správnou volbu pro celou řadu moderních aplikací IoT.

Azure SQL Database Edge je postavenna nejnovějších verzích [Microsoft SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), který poskytuje špičkové funkce výkonu, zabezpečení a zpracování dotazů. Vzhledem k tomu, že Azure SQL Database Edge je postaven na stejném modulu jako SQL Server a Azure SQL Database, poskytuje stejnou oblast programování T-SQL, která usnadňuje a zrychluje vývoj aplikací nebo řešení a současně umožňuje aplikaci přenositelnost mezi zařízeními IoT Edge, datovými centry a cloudem přímo vpřed.

## <a name="deployment-models"></a>Modely nasazení

Azure SQL Database Edge je k dispozici na Azure Marketplace a dá se nasadit jako modul pro [Azure IoT Edge](../iot-edge/about-iot-edge.md). Další informace naleznete [v tématu Deploy Azure SQL Database Edge](deploy-portal.md).<br>

![Diagram přehledu sql database edge](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Edice sql database edge

SQL Database Edge je k dispozici se třemi různými edicemi nebo softwarovými plány. Tyto edice mají identické sady funkcí a liší se pouze z hlediska jejich práv k použití a množství procesoru/paměti, které podporují.

   |**Plánování**  |**Popis**  |
   |---------|---------|
   |Vývojář Azure SQL Database Edge  |  Vývoj pouze sku, každý kontejner SQL Database Edge je omezen na až 4 jádra a 32 GB paměti  |
   |Azure SQL Database Edge    |  Produkční sku, každý kontejner SQL Database Edge je omezen na až 8 jader a 64 GB paměti . |

## <a name="pricing-and-availability"></a>Ceny a dostupnost

Azure SQL Database je aktuálně ve verzi preview. Další informace o cenách a dostupnosti najdete v [tématu Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Informace o rozdílech mezi funkcemi mezi Azure SQL Database Edge a SQL Server a také rozdíly mezi různými možnostmi Azure SQL Database Edge najdete v tématu [funkce databáze SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

## <a name="streaming-capabilities"></a>Možnosti streamování  

Azure SQL Database Edge poskytuje integrované funkce streamování pro analýzy v reálném čase a komplexní zpracování událostí. Funkce streamování je vytvořená pomocí stejných konstrukcí jako [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a poskytuje podobné funkce jako [Azure Stream Analytics na IoT Edge](../stream-analytics/stream-analytics-edge.md).

Modul streamování pro Azure SQL Database Edge je navržený pro nízkou latenci, odolnost proti chybám, efektivní využití šířky pásma a dodržování předpisů.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Schopnosti strojového učení a umělé inteligence

Azure SQL Database Edge poskytuje integrované funkce strojového učení a analýzy integrací modulu runtime ONNX (Open Neural Network Exchange), který umožňuje výměnu modelů hloubkového učení a neuronové sítě mezi různými architekturami. Další informace o ONNX naleznete [zde](https://onnx.ai/). Modul runtime ONNX poskytuje flexibilitu při vývoji modelů v jazyce nebo nástrojích podle vašeho výběru, které pak lze převést do formátu ONNX pro spuštění v rámci SQL Database Edge. Další informace naleznete [v tématu Machine Learning and Artificial Intelligence with ONNX in SQL Database Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Práce s Azure SQL Database Edge

Azure SQL Database Edge usnadňuje a udržuje aplikace. Uživatelé mohou využít všechny známé nástroje a dovednosti k vytváření skvělých aplikací a řešení pro své potřeby ioT edge. Uživatel může vyvíjet v SQL Database Edge pomocí následujících nástrojů:

- [Portál Azure](https://portal.azure.com/) – webová aplikace pro správu všech služeb Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) - bezplatná klientská aplikace ke stažení pro správu jakékoli infrastruktury SQL, od SQL Serveru po databázi SQL.
- [Datové nástroje SQL Serveru v sadě Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) – bezplatná klientská aplikace ke stažení pro vývoj relačních databází serveru SQL Server, databází SQL, balíčků integračních služeb, datových modelů analysis services a sestav reportingservices.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) – bezplatný databázový nástroj pro různé platformy, který lze stáhnout, pro odborníky na data pomocí místních a cloudových datových platforem Microsoftu ve Windows, MacOS a Linuxu.
- [Visual Studio Code](https://code.visualstudio.com/docs) – bezplatný, ke stažení, open source editor kódu pro Windows, macOS a Linux. Podporuje rozšíření, včetně [rozšíření mssql](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Server, Azure SQL Database a Azure SQL Data Warehouse.


## <a name="next-steps"></a>Další kroky

- Podrobnosti týkající se cen a dostupnosti najdete v [tématu Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Požádejte o povolení Azure SQL Database Edge pro vaše předplatné.
- Chcete-li začít, podívejte se na následující:
  - [Nasazení okraje databáze SQL prostřednictvím portálu Azure](deploy-portal.md)
  - [Strojové učení a umělá inteligence s sql database edge](onnx-overview.md)
