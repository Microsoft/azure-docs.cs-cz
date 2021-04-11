---
title: 'Rychlý Start: Začínáme – vytvoření pracovního prostoru synapse'
description: V tomto kurzu se dozvíte, jak vytvořit pracovní prostor synapse, vyhrazený fond SQL a fond Apache Spark bez serveru.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: f186acbe030dcbb0c2bad22586a8b2a5d1aa520d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259791"
---
# <a name="creating-a-synapse-workspace"></a>Vytváření pracovního prostoru synapse

V tomto kurzu se dozvíte, jak vytvořit pracovní prostor synapse, vyhrazený fond SQL a fond Apache Spark bez serveru. 

## <a name="prerequisites"></a>Požadavky

K dokončení kroků tohoto kurzu potřebujete mít přístup ke skupině prostředků, ke které jste přiřadili roli **vlastníka** . V této skupině prostředků vytvořte pracovní prostor synapse.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Vytvořte pracovní prostor synapse v Azure Portal

### <a name="start-the-process"></a>Spustit proces
1. Otevřete [Azure Portal](https://portal.azure.com)a na panelu hledání zadejte **synapse** , aniž byste museli vstoupit.
1. Ve výsledcích hledání v části **služby** vyberte **Azure synapse Analytics**.
1. Vyberte **Přidat** a vytvořte pracovní prostor.

## <a name="basics-tab--project-details"></a>Karta základy > podrobnosti o projektu
Vyplňte následující pole:

1. **Předplatné** – vyberte nějaké předplatné.
1. **Skupina prostředků** – použijte jakoukoli skupinu prostředků.
1. **Spravovaná skupina prostředků** – ponechte toto pole prázdné.

## <a name="basics-tab--workspace-details"></a>Karta základy > podrobnosti o pracovním prostoru
Vyplňte následující pole:

1. **Název pracovního prostoru** – vyberte libovolný globálně jedinečný název. V tomto kurzu použijeme **MyWorkspace**.
1. **Oblast** – vyberte libovolnou oblast.

V části **vybrat data Lake Storage Gen 2**:

1. Podle **názvu účtu** klikněte na **vytvořit nový** a pojmenujte nový účet úložiště **contosolake** nebo podobný, protože tento název musí být jedinečný.
1. Podle **názvu systému souborů** klikněte na **vytvořit nový** a pojmenujte **uživatele** IT. Tím se vytvoří kontejner úložiště s názvem **Uživatelé**. Pracovní prostor bude používat tento účet úložiště jako primární účet úložiště pro tabulky Spark a protokoly aplikací Spark.
1. Zaškrtněte políčko přiřadit roli Přispěvatel dat objektů BLOB úložiště v poli Data Lake Storage Gen2 účet. 

## <a name="completing-the-process"></a>Dokončuje se proces.
Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš pracovní prostor je připravený během několika minut.

> [!NOTE]
> Pokud chcete povolit funkce pracovního prostoru z existujícího vyhrazeného fondu SQL (dřív SQL DW), přečtěte si, [Jak povolit pracovní prostor pro vyhrazený fond SQL (dřív SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Otevřít synapse Studio

Po vytvoření pracovního prostoru Azure synapse máte dva způsoby, jak otevřít synapse Studio:

* Otevřete pracovní prostor synapse v [Azure Portal](https://portal.azure.com)v části **Přehled** v pracovním prostoru synapse vyberte **otevřít** v dialogovém okně Otevřít synapse Studio.
* Přejít na adresu `https://web.azuresynapse.net` a přihlaste se do svého pracovního prostoru.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Vložení ukázkových dat do primárního účtu úložiště
Pro mnoho příkladů v této příručce Začínáme budeme používat malou datovou sadu ukázek 100 tisíc řádků NYX taxislužby. Začneme tak, že ho umístíte do primárního účtu úložiště, který jste vytvořili pro daný pracovní prostor.

* Stáhněte si tento soubor do počítače: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* V synapse Studiio přejděte do centra dat. 
* Klikněte na možnost **propojeno**.
* V kategorii **Azure Data Lake Storae Gen2** uvidíte položku s názvem jako **MyWorkspace (primární-contosolake)** .
* Klikněte na kontejner s názvem **Uživatelé (primární)** .
* Klikněte na **nahrát** a vyberte `NYCTripSmall.parquet` soubor, který jste stáhli.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza pomocí neserverového fondu SQL](get-started-analyze-sql-on-demand.md)
