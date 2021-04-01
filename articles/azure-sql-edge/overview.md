---
title: Co je Azure SQL Edge?
description: Další informace o Azure SQL Edge
keywords: Seznámení s SQL Edgem, co je SQL Edge, Přehled SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a74e12a5e9bdd2dfdbc6ac07b66798e517f6f426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395066"
---
# <a name="what-is-azure-sql-edge"></a>Co je Azure SQL Edge?

Azure SQL Edge je optimalizovaný relační databázový stroj pro nasazení IoT a IoT Edge. Poskytuje funkce pro vytváření vysoce výkonných vrstev pro ukládání a zpracování dat pro aplikace a řešení IoT. Azure SQL Edge poskytuje možnosti pro streamování, zpracování a analýzu relačních a nerelačních dat, jako jsou data JSON, Graph a Time-Series, což dává správné možnosti pro nejrůznější moderní aplikace IoT.

Azure SQL Edge je založený na nejnovějších verzích [SQL Server databázového stroje](/sql/sql-server/sql-server-technical-documentation), který poskytuje špičkové možnosti výkonu, zabezpečení a zpracování dotazů. Vzhledem k tomu, že je Azure SQL Edge postaven na stejném stroji jako [SQL Server](/sql/sql-server/sql-server-technical-documentation) a [Azure SQL](../azure-sql/index.yml), poskytuje stejnou oblast programování v jazyce Transact-SQL (T-SQL), která usnadňuje a urychluje vývoj aplikací nebo řešení, a zajišťuje přenositelnost aplikace mezi IoT Edgemi zařízeními, datovými centry a cloudem.

Co je video Azure SQL Edge na Channel 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-SQL-Edge/player]

## <a name="deployment-models"></a>Modely nasazení

Azure SQL Edge podporuje dva režimy nasazení.

- Připojené nasazení prostřednictvím Azure IoT Edge: Azure SQL Edge je k dispozici na Azure Marketplace a je možné ho nasadit jako modul pro [Azure IoT Edge](../iot-edge/about-iot-edge.md). Další informace najdete v tématu [nasazení serveru Azure SQL Edge](deploy-portal.md).<br>

![Diagram přehledu SQL Edge](media/overview/overview.png)

- Odpojené nasazení: image kontejneru Azure SQL Edge se dají z Docker Hub načíst a nasadit buď jako samostatný kontejner Docker, nebo do clusteru Kubernetes. Další informace najdete v tématu [nasazení Azure SQL Edge pomocí Docker](disconnected-deployment.md) a [nasazení kontejneru Edge SQL Azure v Kubernetes](deploy-kubernetes.md).

## <a name="editions-of-sql-edge"></a>Edice SQL Edge

SQL Edge je k dispozici ve dvou různých edicích nebo softwarových plánech. Tyto edice mají stejné sady funkcí a liší se jenom v souvislosti s jejich právy na používání a množstvím procesorů a paměti, které podporují.

   |**Plán**  |**Popis**  |
   |---------|---------|
   |Vývojář Azure SQL Edge  |  Pouze vývoj SKU, každý kontejner SQL Edge je omezený na až 4 jádra a 32 GB paměti.  |
   |Azure SQL Edge    |  Produkční skladová jednotka má každý kontejner SQL Edge omezený na až 8 jader a 64 GB paměti. |

## <a name="pricing-and-availability"></a>Ceny a dostupnost

Azure SQL Edge je teď všeobecně dostupný. Další informace o cenách a dostupnosti v konkrétních oblastech najdete v tématu [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/).

> [!IMPORTANT]
> Pro pochopení rozdílů funkcí mezi Azure SQL Edge a SQL Server a rozdíly mezi různými možnostmi Azure SQL Edge najdete informace v tématu [podporované funkce Edge SQL serveru Azure](features.md).

## <a name="streaming-capabilities"></a>Možnosti streamování  

Azure SQL Edge nabízí integrované možnosti streamování pro analýzy v reálném čase a složité zpracování událostí. Funkce streamování je sestavená pomocí stejných konstrukcí jako [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a podobných možností jako [Azure Stream Analytics na IoT Edge](../stream-analytics/stream-analytics-edge.md).

Modul streamování pro Azure SQL Edge je určený k zajištění nízké latence, odolnosti, efektivního využití šířky pásma a dodržování předpisů. 

Další informace o streamování dat v SQL Edge najdete v tématu [streamování dat](stream-data.md) .

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Machine Learning a umělé Možnosti inteligentních funkcí

Azure SQL Edge nabízí integrované funkce strojového učení a analýzy integrací prostředí Open Format ONNX (Open neuronové Network Exchange), které umožňuje výměnu modelů hloubkového učení a neuronové síťové modely mezi různými architekturami. Další informace o ONNX najdete [tady](https://onnx.ai/). ONNX runtime poskytuje flexibilitu při vývoji modelů v jazyce nebo nástrojích podle vašeho výběru, které je pak možné převést na formát ONNX pro spuštění v rámci SQL Edge. Další informace najdete v tématu [Machine Learning a umělá logika s ONNXem v SQL Edge](onnx-overview.md).

## <a name="working-with-azure-sql-edge"></a>Práce s Edge SQL Azure

Azure SQL Edge usnadňuje vývoj a správu aplikací pro snazší a vyšší produktivitu. Uživatelé můžou pomocí známých nástrojů a dovedností vytvářet skvělé aplikace a řešení pro své IoT Edge potřeby. Uživatel může vyvíjet v SQL Edge pomocí nástrojů, jako jsou tyto:

- [Azure Portal](https://portal.azure.com/) – webová aplikace pro správu všech služeb Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) – bezplatná klientská aplikace ke stažení pro správu jakékoli infrastruktury SQL, od SQL Server po SQL Database.
- [SQL Server Data Tools v aplikaci Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) – bezplatná klientská aplikace ke stažení pro vývoj SQL Server relačních databází, databází SQL, balíčků integračních služeb, Analysis Services datových modelů a sestav služby Reporting Services.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) – bezplatný, zaváděný a databázový Nástroj pro různé platformy pro data Professional s využitím řady místních a cloudových datových platforem v systémech Windows, MacOS a Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) – bezplatný a Open Source Editor kódu pro Windows, MacOS a Linux. Podporuje rozšíření, včetně [rozšíření MSSQL](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Server, Azure SQL Database a Azure synapse Analytics.


## <a name="next-steps"></a>Další kroky

- [Nasazení okraje SQL pomocí Azure Portal](deploy-portal.md)
- [Machine Learning a umělá logika pomocí SQL Edge](onnx-overview.md)
- [Sestavení komplexního řešení IoT pomocí SQL Edge](tutorial-deploy-azure-resources.md)