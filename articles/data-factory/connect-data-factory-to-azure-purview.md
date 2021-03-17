---
title: Připojení Data Factory k Azure Purview
description: Informace o tom, jak připojit Data Factory k Azure dosah
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 44f093f96d0f4653a6fcca94aaa97264c93e3c7d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727936"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Připojení Data Factory k Azure dosah (Preview)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto článku se dozvíte, jak připojit Data Factory k Azure dosah a jak vykázat datové Azure Data Factory aktivity kopírovat data, tok dat a spustit balíček SSIS.


## <a name="connect-data-factory-to-azure-purview"></a>Připojení Data Factory k Azure dosah
Azure dosah je nová cloudová služba, kterou můžou uživatelé dat centrálně spravovat pomocí zásad správného řízení dat napříč cloudem a Prem prostředími. Svůj Data Factory můžete připojit k Azure dosah a připojení vám umožní používat Azure dosah pro zachytávání dat o vydaných kopiích, toku dat a spouštění balíčku SSIS. Datovou továrnu můžete připojit k Azure dosah dvěma způsoby:
### <a name="register-azure-purview-account-to-data-factory"></a>Registrace účtu Azure dosah pro Data Factory
1. Na portálu ADF můžete přejít na **Správa**  ->  **Azure dosah**. Vyberte **připojit k účtu dosah**. 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Snímek obrazovky pro registraci účtu dosah":::
2. Můžete si vybrat **z předplatného Azure** nebo **zadat ručně**. **Z předplatného Azure** můžete vybrat účet, ke kterému máte přístup. 
3. Po připojení byste měli být schopni zobrazit název účtu dosah v **účtu dosah** karty. 
4. K vyhledání dat můžete použít panel hledání v horním středu portálu Azure Data Factory. 

Pokud se zobrazí upozornění na portálu Azure Data Factory po registraci účtu Azure dosah, který se má Data Factory, vyřešte tento problém podle následujících kroků:

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Snímek obrazovky s upozorněním na registraci účtu dosah":::

1. Přejít na Azure Portal a najít datovou továrnu. Vyberte oddíl značky a podívejte se, jestli je značka s názvem **catalogUri**. V takovém případě prosím odpojte a znovu připojte účet Azure dosah na portálu ADF.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Snímek obrazovky, ve kterém se zobrazí značky registrace účtu dosah.":::

2. Ověřte, jestli je udělené oprávnění k registraci účtu Azure dosah do Data Factory. Viz [Jak připojit Azure Data Factory a Azure dosah](../purview/how-to-link-azure-data-factory.md#create-new-data-factory-connection)

### <a name="register-data-factory-in-azure-purview"></a>Registrace Data Factory ve službě Azure dosah
Informace o tom, jak zaregistrovat Data Factory v Azure dosah, najdete v tématu [Jak připojit Azure Data Factory a Azure dosah](../purview/how-to-link-azure-data-factory.md). 

## <a name="report-lineage-data-to-azure-purview"></a>Vykázat data o vzřádkování do Azure dosah
Když zákazníci spustí aktivitu kopírování, toku dat nebo spuštění balíčku SSIS ve službě Azure Data Factory, zákazníci mohou získat vztah závislosti a mít přehled o celém procesu pracovního postupu mezi zdroji dat a cílem.
Informace o tom, jak shromažďovat Azure Data Factory, najdete v tématu [Data Factory – řádek](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Další kroky
[Uživatelská příručka k linii katalogu](../purview/catalog-lineage-user-guide.md)

[Kurz: vkládání dat Data Factorych řádků do Azure dosah](turorial-push-lineage-to-purview.md)