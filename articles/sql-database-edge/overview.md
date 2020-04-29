---
title: Co je Azure SQL Database Edge? | Dokumentace Microsoftu
description: Další informace o Azure SQL Database Edge
keywords: Seznámení s Edge SQL Database, co je Edge SQL Database, přehled Edge pro SQL Database
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246700"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Co je Azure SQL Database Edge Preview?

Azure SQL Database Edge Preview je optimalizovaný relační databázový stroj pro nasazení IoT a IoT Edge. Poskytuje funkce pro vytváření vysoce výkonných vrstev pro ukládání a zpracování dat pro aplikace a řešení IoT. Azure SQL Database Edge poskytuje možnosti pro streamování, zpracování a analýzu relačních a nerelačních dat, jako jsou data JSON, Graph a Time-Series, což dává správné možnosti pro nejrůznější moderní aplikace IoT.

Azure SQL Database Edge je postaven na nejnovějších verzích [databázového stroje Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), který poskytuje špičkové možnosti výkonu, zabezpečení a zpracování dotazů. Vzhledem k tomu, že Azure SQL Database Edge je postaven na stejném stroji jako SQL Server a Azure SQL Database, poskytuje stejnou oblast programování v T-SQL, která usnadňuje a urychluje vývoj aplikací nebo řešení a zároveň zajišťuje přenositelnost aplikace mezi IoT Edgemi zařízeními, datovými centry a cloudem.

## <a name="deployment-models"></a>Modely nasazení

Azure SQL Database Edge je k dispozici v Azure Marketplace a lze jej nasadit jako modul pro [Azure IoT Edge](../iot-edge/about-iot-edge.md). Další informace najdete v tématu [nasazení Azure SQL Database Edge](deploy-portal.md).<br>

![Diagram s přehledem SQL Database Edge](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Edice SQL Database Edge

SQL Database Edge je k dispozici se třemi různými edicemi nebo softwarovými plány. Tyto edice mají stejné sady funkcí a liší se jenom v souvislosti s jejich právy na používání a množstvím procesorů a paměti, které podporují.

   |**Plánování**  |**Popis**  |
   |---------|---------|
   |Vývojář Azure SQL Database Edge  |  Pouze vývoj SKU, každý SQL Database hraniční kontejner je omezen na 4 jádra a 32 GB paměti.  |
   |Azure SQL Database Edge    |  Produkční skladová jednotka má každý kontejner SQL Database Edge omezený na až 8 jader a 64 GB paměti. |

## <a name="pricing-and-availability"></a>Ceny a dostupnost

Azure SQL Database je v tuto chvíli ve verzi Preview. Další informace o cenách a dostupnosti najdete v tématu [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Pro pochopení rozdílů funkcí mezi Azure SQL Database hranou a SQL Server a rozdíly mezi různými možnostmi Azure SQL Database Edge si přečtěte téma [funkce databáze SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

## <a name="streaming-capabilities"></a>Možnosti streamování  

Azure SQL Database Edge nabízí integrované možnosti streamování pro analýzy v reálném čase a složité zpracování událostí. Funkce streamování je sestavená pomocí stejných konstrukcí jako [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a poskytuje podobné možnosti jako [Azure Stream Analytics na IoT Edge](../stream-analytics/stream-analytics-edge.md).

Modul streamování pro Azure SQL Database Edge je určený pro nízkou latenci, odolnost proti chybám a efektivní využití šířky pásma a dodržování předpisů.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Machine Learning a umělé Možnosti inteligentních funkcí

Azure SQL Database Edge nabízí integrované možnosti strojového učení a analýzy integrací prostředí Open Format ONNX (Open neuronové Network Exchange), které umožňuje výměnu modelů hloubkového učení a neuronové síťové modely mezi různými architekturami. Další informace o ONNX najdete [tady](https://onnx.ai/). ONNX runtime poskytuje flexibilitu při vývoji modelů v jazyce nebo nástrojích podle vašeho výběru, které se pak dají převést na formát ONNX pro provádění v rámci SQL Database Edge. Další informace najdete v tématu [Machine Learning a umělá logika s ONNX v SQL Database Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Práce s Azure SQL Database Edge

Azure SQL Database Edge usnadňuje vývoj a správu aplikací. Uživatelé můžou pomocí známých nástrojů a dovedností vytvářet skvělé aplikace a řešení pro své IoT Edge potřeby. Uživatel může vyvíjet v SQL Database Edge pomocí nástrojů, jako jsou tyto:

- [Azure Portal](https://portal.azure.com/) – webová aplikace pro správu všech služeb Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) – bezplatná klientská aplikace ke stažení pro správu jakékoli infrastruktury SQL, od SQL Server po SQL Database.
- [SQL Server Data Tools v aplikaci Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) – bezplatná klientská aplikace ke stažení pro vývoj SQL Server relačních databází, databází SQL, balíčků integračních služeb, Analysis Services datových modelů a sestav služby Reporting Services.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) – bezplatný, zaváděný a databázový Nástroj pro různé platformy pro data Professional s využitím řady místních a cloudových datových platforem v systémech Windows, MacOS a Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) – bezplatný a Open Source Editor kódu pro Windows, MacOS a Linux. Podporuje rozšíření, včetně [rozšíření MSSQL](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Server, Azure SQL Database a Azure SQL Data Warehouse.


## <a name="next-steps"></a>Další kroky

- Podrobnosti o cenách a dostupnosti najdete v tématu [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Požadavek na povolení Azure SQL Database Edge pro vaše předplatné.
- Informace o tom, jak začít, najdete v následujících tématech:
  - [Nasazení SQL Database Edge přes Azure Portal](deploy-portal.md)
  - [Machine Learning a umělá inteligentní funkce s využitím SQL Database Edge](onnx-overview.md)
