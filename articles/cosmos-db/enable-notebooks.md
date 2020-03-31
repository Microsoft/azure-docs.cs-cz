---
title: Povolení poznámkových bloků v účtu Azure Cosmos DB (preview)
description: Integrované poznámkové bloky Azure Cosmos DB umožňují analyzovat a vizualizovat data z portálu. Tento článek popisuje, jak povolit tuto funkci pro účty Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768023"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Povolení poznámkových bloků pro účty Azure Cosmos DB (preview)

> [!IMPORTANT]
> Integrované poznámkové bloky pro Azure Cosmos DB jsou momentálně dostupné v následujících oblastech Azure: Austrálie – východ, Východní USA, Východní USA 2, Severní Evropa, Střední– USA, Jihovýchodní Asie, Velká Británie – jih, Západní Evropa a Západní USA 2. Pokud chcete používat poznámkové bloky, [vytvořte nový účet s poznámkovými bloky](#enable-notebooks-in-a-new-cosmos-account) nebo [povolte poznámkové bloky na existujícím účtu](#enable-notebooks-in-an-existing-cosmos-account) v jedné z těchto oblastí.

Integrované poznámkové bloky Jupyter v Azure Cosmos DB vám umožní analyzovat a vizualizovat data z webu Azure Portal. Tento článek popisuje, jak tuto funkci povolit pro váš účet služby Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Povolení poznámkových bloků v novém účtu Cosmos
1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Vyberte **Vytvořit** > **databáze** > prostředků**Azure Cosmos DB**.
1. Na stránce **Vytvořit účet Azure Cosmos DB** vyberte **Poznámkové bloky**. 
 
    ![Vybrat možnost poznámkových bloků v okně Vytvoření Azure Cosmos DB](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Vyberte **Zkontrolovat a vytvořit**. Možnost **Síť** a **značky** můžete přeskočit. 
1. Zkontrolujte nastavení účtu a vyberte **Vytvořit**. Vytvoření účtu trvá několik minut. Počkejte, až se zobrazí stránka portálu **Vaše nasazení je dokončeno**. 

    ![Podokno Oznámení portálu Azure Portal](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Vyberte **Přejít na prostředek** a přejděte na stránku účtu Azure Cosmos DB. 

    ![Stránka účtu Azure Cosmos DB](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Přejděte do podokna **Průzkumník dat.** Nyní byste měli vidět pracovní prostor poznámkových bloků.

    ![Pracovní prostor nové poznámkové bloky Azure Cosmos DB](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Povolení poznámkových bloků v existujícím účtu Cosmos
Poznámkové bloky můžete také povolit u existujících účtů. Tento krok je třeba provést pouze jednou na účet.

1. Přejděte do podokna **Průzkumník dat** ve svém účtu Cosmos.
1. Vyberte **Povolit poznámkové bloky**.

    ![Vytvoření nového pracovního prostoru poznámkových bloků v Průzkumníku dat](media/enable-notebooks/enable-notebooks-workspace.png)
1. Tím se zobrazí výzva k vytvoření nového pracovního prostoru poznámkových bloků. Vyberte **Dokončit nastavení.**
1. Váš účet je nyní povolen pro používání poznámkových bloků!

## <a name="create-and-run-your-first-notebook"></a>Vytvoření a spuštění prvního poznámkového bloku

Pokud chcete ověřit, že můžete používat poznámkové bloky, vyberte jeden z poznámkových bloků v části Ukázkové poznámkové bloky. Tím uložíte kopii poznámkového bloku do pracovního prostoru a otevřete jej.

V tomto příkladu použijeme **GettingStarted.ipynb**. 

![Zobrazit poznámkový blok GettingStarted.ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Spuštění poznámkového bloku:
1. Vyberte první buňku kódu, která obsahuje kód Pythonu. 
1. Chcete-li buňku spustit, vyberte **spustit.** K spuštění buňky můžete také použít **Shift + Enter.**
1. Aktualizujte podokno prostředků, abyste viděli databázi a kontejner, který byl vytvořen.

    ![Spuštění poznámkového bloku Začínáme](media/enable-notebooks/run-first-notebook-cell.png)

Výběrem **nového poznámkového bloku** můžete také vytvořit nový poznámkový blok nebo nahrát existující soubor poznámkového bloku (.ipynb) výběrem **možnosti Nahrát soubor** z nabídky **Poznámkové bloky.** 

![Vytvoření nebo nahrání nového poznámkového bloku](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Další kroky

- Informace o výhodách [notebooků Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
