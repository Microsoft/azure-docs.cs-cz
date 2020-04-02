---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 9ae820097f8515dc44c67d95366f96c241cb77a1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80523050"
---
## <a name="select-an-ingestion-type"></a>Výběr typu požití

V **případě typu Ingestování**vyberte jednu z následujících možností:
   * **z úložiště** - v poli **Odkaz na úložiště** přidejte adresu URL účtu úložiště. Pro účty privátního úložiště použijte [adresu URL objektu Blob SAS.](/azurevs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)
   
      ![Požití jedním kliknutím ze skladu](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **ze souboru** - vyberte **Procházet,** chcete-li soubor vyhledat, nebo soubor přetáhněte do pole.
  
      ![Požití jedním kliknutím ze souboru](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **z kontejneru** – do pole **Propojit s úložištěm** přidejte [adresu URL SAS](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) kontejneru a volitelně zadejte velikost vzorku.

      ![Požití jedním kliknutím z kontejneru](media/data-explorer-one-click-ingestion-types/from-container.png)

  Zobrazí se ukázka dat. Pokud chcete, můžete jej filtrovat tak, aby zobrazoval pouze soubory, které začínají určitými znaky. Když upravíte filtry, náhled se automaticky aktualizuje.
  
  Můžete například filtrovat všechny soubory, které začínají *slovem data* a končit příponou *.csv.gz.*

  ![Filtr požití jedním kliknutím](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
