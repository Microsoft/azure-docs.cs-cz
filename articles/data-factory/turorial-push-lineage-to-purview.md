---
title: Nasdílení změn dat rodokmenu Data Factory do Azure Purview
description: Přečtěte si informace o tom, jak vložit data Data Factory do Azure dosah.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 3f2297ae619145ec19b53ba79d70b7c085cbcaab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361447"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Vložení dat Data Factory řádků do Azure dosah (Preview)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu použijete Data Factory uživatelské rozhraní (UI) k vytvoření kanálu, který spouští aktivity a data na řádcích sestav pro účet Azure dosah. Pak můžete zobrazit všechny informace o zadaném řádku v účtu Azure dosah.

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Azure Data Factory**. Pokud nemáte Azure Data Factory, přečtěte si téma [vytvoření Azure Data Factory](./quickstart-create-data-factory-portal.md).
* **Účet Azure dosah**. Účet dosah zachycuje všechna data na řádku generovaná objektem pro vytváření dat. Pokud nemáte účet Azure dosah, přečtěte si téma [Vytvoření Azure dosah](../purview/create-catalog-portal.md).


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Spuštění aktivit Data Factory a vkládání dat do služby Azure dosah
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>Krok 1: připojení Data Factory k účtu dosah
Přihlaste se ke svému účtu dosah na portálu dosah, přejdete do **centra pro správu**. Vyberte možnost **Data Factory** v **externích připojeních** a kliknutím na tlačítko **Nový** vytvořte připojení k novému Data Factory. 
[![Snímek obrazovky pro vytvoření připojení mezi Data Factory a účtem ](./media/data-factory-purview/connect-adf-to-purview.png) dosah ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

Na stránce s automaticky otevíraných oken můžete vybrat Data Factory, ke kterým se chcete připojit k tomuto účtu dosah. 
![Snímek obrazovky pro nové připojení](./media/data-factory-purview/new-adf-purview-connection.png)

Po vytvoření připojení můžete stav ověřit. **Připojeno** znamená, že spojení mezi Data Factory a tímto dosahem se úspěšně připojilo. 
> [!NOTE]
> Abyste mohli vytvořit připojení mezi Data Factory a Azure dosah, musíte v účtu dosah přiřadit kteroukoli z níže uvedených rolí a Data Factory role **přispěvatele** .
> - Vlastník
> - Správce uživatelských přístupů

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>Krok 2: spuštění aktivit kopírování a toku dat v Data Factory
V Data Factory můžete vytvářet kanály, aktivity kopírování a aktivity toku dat. Pro zachycení dat na řádku nepotřebujete žádnou další konfiguraci. Data na řádku budou automaticky zachycena během provádění aktivit.
![Snímek obrazovky aktivity kopírování a toku dat ](./media/data-factory-purview/adf-activities-for-lineage.png) Pokud nevíte, jak vytvářet aktivity kopírování a toku dat, přečtěte si téma [kopírování dat z úložiště objektů BLOB v Azure do databáze v Azure SQL Database pomocí Azure Data Factory](./tutorial-copy-data-portal.md) a [transformace dat pomocí mapování datových toků](./tutorial-data-flow.md).

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>Krok 3: spuštění aktivit balíčku Execute SSIS v Data Factory
Můžete vytvářet kanály, spouštět aktivity balíčku SSIS v Data Factory. Pro zachycení dat na řádku nepotřebujete žádnou další konfiguraci. Data na řádku budou automaticky zachycena během provádění aktivit.
![Snímek obrazovky s aktivitou spuštění balíčku SSIS](./media/data-factory-purview/ssis-activities-for-lineage.png)

Pokud si nejste jisti, jak vytvořit aktivity balíčku Execute SSIS, přečtěte si téma [spuštění balíčků SSIS v Azure](./tutorial-deploy-ssis-packages-azure.md).

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Krok 4: zobrazení informací o vydaných řádcích v účtu dosah
Vraťte se k účtu dosah. Na domovské stránce vyberte **Procházet assety**. Vyberte Asset, který chcete, a klikněte na kartu karty. Zobrazí se všechny informace o tomto řádku.
[![Snímek obrazovky s účtem ](./media/data-factory-purview/view-dataset.png) dosah ](./media/data-factory-purview/view-dataset.png#lightbox)

Pro aktivitu kopírování můžete zobrazit data o řádcích.
[![Snímek obrazovky kopie konce řádku ](./media/data-factory-purview/copy-lineage.png)](./media/data-factory-purview/copy-lineage.png#lightbox)

Zobrazí se také data o vydaných řádcích pro aktivitu toku dat.
[![Snímek obrazovky s tokem toku ](./media/data-factory-purview/dataflow-lineage.png) dat ](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> Pro účely aktivity toku dat podporujeme jenom zdroj a jímku. Počet řádků pro transformaci toku dat není dosud podporován.

Zobrazí se vám také data o vykonání aktivity balíčku SSIS.
[![Snímek obrazovky s SSIS ](./media/data-factory-purview/ssis-lineage.png)](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> V případě, že se má spustit SSIS aktivita balíčku, podporujeme jenom zdroj a cíl. Pro transformaci není ještě podporováno.

## <a name="next-steps"></a>Další kroky
[Uživatelská příručka k linii katalogu](../purview/catalog-lineage-user-guide.md)

[Připojení Data Factory k Azure dosah](connect-data-factory-to-azure-purview.md)