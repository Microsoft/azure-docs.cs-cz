---
title: Povolit poznámkové bloky v účtu Azure Cosmos DB (Preview)
description: Integrované poznámkové bloky Azure Cosmos DB umožňují analyzovat a vizualizovat data v rámci portálu. Tento článek popisuje, jak povolit tuto funkci pro účty Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: cf8da4379a7770e44e86663c02cc7a57da9d3452
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089772"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Povolit poznámkové bloky pro účty Azure Cosmos DB (Preview)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Integrované poznámkové bloky pro Azure Cosmos DB jsou momentálně dostupné v následujících oblastech Azure: Austrálie – východ, Východní USA, Východní USA 2, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Západní Evropa a Západní USA 2. Pokud chcete používat poznámkové bloky, [vytvořte nový účet s poznámkovým blokem](#enable-notebooks-in-a-new-cosmos-account) nebo [Povolte poznámkové bloky v existujícím účtu](#enable-notebooks-in-an-existing-cosmos-account) v některé z těchto oblastí.

Integrované poznámkové bloky Jupyter v Azure Cosmos DB umožňují analyzovat a vizualizovat data z Azure Portal. Tento článek popisuje, jak tuto funkci povolit pro váš účet služby Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Povolit poznámkové bloky v novém účtu Cosmos

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
1. Vyberte **vytvořit**  >  **databáze** prostředků  >  **Azure Cosmos DB** .
1. Na stránce **vytvořit Azure Cosmos DB účet** vyberte **poznámkové bloky** . 
 
    :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks.png" alt-text="Možnost výběru poznámkových bloků v Azure Cosmos DB vytvořit okno":::

1. Vyberte **Zkontrolovat a vytvořit** . Můžete přeskočit možnost **síť** a **značky** . 
1. Zkontrolujte nastavení účtu a pak vyberte **vytvořit** . Vytvoření účtu trvá několik minut. Počkejte, než se na stránce portálu zobrazí **dokončené vaše nasazení** . 

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Možnost výběru poznámkových bloků v Azure Cosmos DB vytvořit okno":::

1. Vyberte **Přejít k prostředku** a přejdete na stránku Azure Cosmos DB účet.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Možnost výběru poznámkových bloků v Azure Cosmos DB vytvořit okno":::

1. Přejděte do podokna **Průzkumník dat** . Nyní byste měli vidět pracovní prostor poznámkových bloků.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Možnost výběru poznámkových bloků v Azure Cosmos DB vytvořit okno":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Povolení poznámkových bloků v existujícím účtu Cosmos

Poznámkové bloky můžete také povolit u stávajících účtů. Tento krok je potřeba provést jenom jednou pro každý účet.

1. Přejděte do podokna **Průzkumník dat** v účtu Cosmos.
1. Vyberte **Povolit poznámkové bloky** .

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Možnost výběru poznámkových bloků v Azure Cosmos DB vytvořit okno":::

1. Tím se zobrazí výzva k vytvoření nového pracovního prostoru poznámkových bloků. Vyberte **Dokončit instalaci.**
1. Váš účet teď má povolený používání poznámkových bloků!

## <a name="create-and-run-your-first-notebook"></a>Vytvoření a spuštění prvního poznámkového bloku

Pokud chcete ověřit, jestli můžete použít poznámkové bloky, vyberte v části ukázkové poznámkové bloky jednu z poznámkových bloků. Tím se uloží kopie poznámkového bloku do svého pracovního prostoru a otevře se.

V tomto příkladu použijeme **soubor GettingStarted. ipynb** . 

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Možnost výběru poznámkových bloků v Azure Cosmos DB vytvořit okno":::

Spuštění poznámkového bloku:
1. Vyberte první buňku kódu, která obsahuje kód Pythonu. 
1. Vyberte **Spustit** , aby se buňka spustila. Tuto buňku můžete také spustit **stisknutím SHIFT + ENTER** .
1. Obnovte podokno prostředků, aby se zobrazila vytvořená databáze a kontejner.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Možnost výběru poznámkových bloků v Azure Cosmos DB vytvořit okno":::

Můžete také vybrat **Nový Poznámkový blok** pro vytvoření nového poznámkového bloku nebo nahrát existující soubor poznámkového bloku (. ipynb) tak, že v nabídce **Moje poznámkové bloky** vyberete **nahrát soubor** . 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Možnost výběru poznámkových bloků v Azure Cosmos DB vytvořit okno":::

## <a name="next-steps"></a>Další kroky

- Přečtěte si o výhodách [Azure Cosmos DB poznámkových blocích Jupyter](cosmosdb-jupyter-notebooks.md)
