---
title: Připojení Data Factory k Azure Purview
description: Informace o tom, jak připojit Data Factory k Azure dosah
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: d1a07a3733bddc1516a4d2e19f3cac31a7702954
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944932"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Připojení Data Factory k Azure dosah (Preview)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto článku se dozvíte, jak připojit datovou továrnu k Azure dosah a jak vykázat datové toky pro aktivity ADF kopírovat data, tok dat a spustit balíček SSIS.

## <a name="connect-data-factory-to-azure-purview"></a>Připojení objektu pro vytváření dat ke službě Azure dosah
Azure dosah je nová cloudová služba, kterou můžou uživatelé dat centrálně spravovat pomocí zásad správného řízení dat napříč cloudem a Prem prostředími. Datovou továrnu můžete připojit k Azure dosah a připojení vám umožní využít Azure dosah pro zachycení dat pro kopírování, tok dat a spouštění balíčku SSIS. Informace o tom, jak zaregistrovat datovou továrnu v Azure dosah, najdete v tématu [Postup připojení Azure Data Factory a Azure dosah](../purview/how-to-link-azure-data-factory.md). 

## <a name="report-lineage-data-to-azure-purview"></a>Vykázat data o vzřádkování do Azure dosah
Když zákazníci spustí aktivitu kopírování, toku dat nebo spuštění balíčku SSIS ve službě Azure Data Factory, zákazníci mohou získat vztah závislosti a mít přehled o celém procesu pracovního postupu mezi zdroji dat a cílem.
Informace o tom, jak shromažďovat Azure Data Factory, najdete v tématu [Data Factory – řádek](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Další kroky
[Uživatelská příručka k linii katalogu](../purview/catalog-lineage-user-guide.md)

[Kurz: vkládání dat Data Factorych řádků do Azure dosah](turorial-push-lineage-to-purview.md)