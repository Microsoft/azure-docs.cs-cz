---
title: Povolit poznámkové bloky v účtu Azure Cosmos DB
description: Integrované poznámkové bloky Azure Cosmos DB umožňují analyzovat a vizualizovat data v rámci portálu. Tento článek popisuje, jak povolit tuto funkci pro účty Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 824a562cb5f0562a235d903fbd4e575bc9c22572
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672736"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts"></a>Povolit poznámkové bloky pro účty Azure Cosmos DB

Integrované poznámkové bloky Jupyter v Azure Cosmos DB umožňují analyzovat a vizualizovat data z Azure Portal. Tento článek popisuje, jak povolit tuto funkci pro účet Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Povolit poznámkové bloky v novém účtu Cosmos
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**.
1. Na stránce **vytvořit Azure Cosmos DB účet** vyberte **poznámkové bloky**. 
 
    ![Možnost výběru poznámkových bloků v Azure Cosmos DB vytvořit okno](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Vyberte **Zkontrolovat a vytvořit**. Můžete přeskočit možnost **síť** a **značky** . 
1. Zkontrolujte nastavení účtu a pak vyberte **vytvořit**. Vytvoření účtu trvá několik minut. Počkejte, než se na stránce portálu zobrazí **dokončené vaše nasazení**. 

    ![Podokno Oznámení portálu Azure Portal](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Vyberte **Přejít k prostředku** a přejdete na stránku Azure Cosmos DB účet. 

    ![Stránka Azure Cosmos DB účtu](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Přejděte do podokna **Průzkumník dat** . Nyní byste měli vidět pracovní prostor poznámkových bloků.

    ![Nový pracovní prostor pro poznámkové bloky Azure Cosmos DB](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Povolení poznámkových bloků v existujícím účtu Cosmos
Poznámkové bloky můžete také povolit u stávajících účtů. Tento krok je potřeba provést jenom jednou pro každý účet.

1. Přejděte do podokna **Průzkumník dat** v účtu Cosmos.
1. Vyberte **Povolit poznámkové bloky**.

    ![Vytvořit nový pracovní prostor poznámkových bloků v Průzkumník dat](media/enable-notebooks/enable-notebooks-workspace.png)
1. Tím se zobrazí výzva k vytvoření nového pracovního prostoru poznámkových bloků. Vyberte **Dokončit instalaci.**
1. Váš účet teď má povolený používání poznámkových bloků!

## <a name="create-and-run-your-first-notebook"></a>Vytvoření a spuštění prvního poznámkového bloku

Pokud chcete ověřit, jestli můžete použít poznámkové bloky, vyberte v části ukázkové poznámkové bloky jednu z poznámkových bloků. Tím se uloží kopie poznámkového bloku do svého pracovního prostoru a otevře se.

V tomto příkladu použijeme **soubor GettingStarted. ipynb**. 

![Zobrazit Poznámkový blok soubor GettingStarted. ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Spuštění poznámkového bloku:
1. Vyberte první buňku kódu, která obsahuje kód Pythonu. 
1. Vyberte **Spustit** , aby se buňka spustila. Tuto buňku můžete také spustit **stisknutím SHIFT + ENTER** .
1. Obnovte podokno prostředků, aby se zobrazila vytvořená databáze a kontejner.

    ![Spuštění poznámkového bloku Začínáme](media/enable-notebooks/run-first-notebook-cell.png)

Můžete také vybrat **Nový Poznámkový blok** pro vytvoření nového poznámkového bloku nebo nahrát existující soubor poznámkového bloku (. ipynb) tak, že v nabídce **Moje poznámkové bloky** vyberete **nahrát soubor** . 

![Vytvoření nebo nahrání nového poznámkového bloku](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si o výhodách [Azure Cosmos DB poznámkových blocích Jupyter](cosmosdb-jupyter-notebooks.md)
- Přečtěte si [, jak používat funkce a příkazy poznámkového bloku](use-notebook-features-and-commands.md)