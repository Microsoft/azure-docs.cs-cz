---
title: Povolit poznámkové bloky v účtu Azure Cosmos DB (Preview)
description: Integrované poznámkové bloky Azure Cosmos DB umožňují analyzovat a vizualizovat data v rámci portálu. Tento článek popisuje, jak povolit tuto funkci pro účty Cosmos.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 02e8ad5f2b5326f947ba0bca6456ce9d9d3c27d7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692772"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Povolit poznámkové bloky pro účty Azure Cosmos DB (Preview)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Integrované poznámkové bloky pro Azure Cosmos DB jsou teď dostupné ve [29 oblastech](#supported-regions). Pokud chcete používat poznámkové bloky, [vytvořte nový účet Cosmos](#create-a-new-cosmos-account) nebo [Povolte poznámkové bloky v existujícím účtu](#enable-notebooks-in-an-existing-cosmos-account) v některé z těchto oblastí. 

Integrované poznámkové bloky Jupyter v Azure Cosmos DB umožňují analyzovat a vizualizovat data z Azure Portal. Tento článek popisuje, jak tuto funkci povolit pro váš účet služby Azure Cosmos DB.

## <a name="create-a-new-cosmos-account"></a>Vytvoření nového účtu Cosmos
Od 10. února 2021 budou mít nové účty Azure Cosmos vytvořené v jedné z [podporovaných oblastí](#supported-regions) automaticky povolené poznámkové bloky. Pro povolení poznámkových bloků není nutná žádná další konfigurace. Pomocí následujících pokynů vytvořte nový účet:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte **vytvořit**  >  **databáze** prostředků  >  **Azure Cosmos DB**.
1. Zadejte základní nastavení pro účet.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Stránka nového účtu pro službu Azure Cosmos DB":::

1. Vyberte **Zkontrolovat a vytvořit**. Můžete přeskočit možnost **síť** a **značky** . 
1. Zkontrolujte nastavení účtu a pak vyberte **vytvořit**. Vytvoření účtu trvá několik minut. Počkejte, než se na stránce portálu zobrazí **dokončené vaše nasazení**.

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Podokno Oznámení portálu Azure Portal":::

1. Vyberte **Přejít k prostředku** a přejdete na stránku Azure Cosmos DB účet.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Stránka Azure Cosmos DB účtu":::

1. Přejděte do podokna **Průzkumník dat** . Nyní byste měli vidět pracovní prostor poznámkových bloků.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Nový pracovní prostor pro poznámkové bloky Azure Cosmos DB":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Povolení poznámkových bloků v existujícím účtu Cosmos

Poznámkové bloky můžete také povolit u stávajících účtů. Tento krok je potřeba provést jenom jednou pro každý účet.

1. Přejděte do podokna **Průzkumník dat** v účtu Cosmos.
1. Vyberte **Povolit poznámkové bloky**.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Vytvořit nový pracovní prostor poznámkových bloků v Průzkumník dat":::

1. Tím se zobrazí výzva k vytvoření nového pracovního prostoru poznámkových bloků. Vyberte **Dokončit instalaci.**
1. Váš účet teď má povolený používání poznámkových bloků!

## <a name="create-and-run-your-first-notebook"></a>Vytvoření a spuštění prvního poznámkového bloku

Pokud chcete ověřit, jestli můžete použít poznámkové bloky, vyberte v části ukázkové poznámkové bloky jednu z poznámkových bloků. Tím se uloží kopie poznámkového bloku do svého pracovního prostoru a otevře se.

V tomto příkladu použijeme **soubor GettingStarted. ipynb**.

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Zobrazit Poznámkový blok soubor GettingStarted. ipynb":::

Spuštění poznámkového bloku:
1. Vyberte první buňku kódu, která obsahuje kód Pythonu.
1. Vyberte **Spustit** , aby se buňka spustila. Tuto buňku můžete také spustit **stisknutím SHIFT + ENTER** .
1. Obnovte podokno prostředků, aby se zobrazila vytvořená databáze a kontejner.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Spuštění poznámkového bloku Začínáme":::

Můžete také vybrat **Nový Poznámkový blok** pro vytvoření nového poznámkového bloku nebo nahrát existující soubor poznámkového bloku (. ipynb) tak, že v nabídce **Moje poznámkové bloky** vyberete **nahrát soubor** . 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Vytvoření nebo nahrání nového poznámkového bloku":::

## <a name="supported-regions"></a>Podporované oblasti
Integrované poznámkové bloky pro službu Azure Cosmos DB jsou v současné době k dispozici ve 29 oblastech Azure. U nových účtů Azure Cosmos vytvořených v těchto oblastech budou poznámkové bloky automaticky povolené. Poznámkové bloky jsou s vaším účtem bezplatné. 

- Austrálie – střed
- Austrálie – střed 2
- Austrálie – východ
- Austrálie – jihovýchod
- Brazílie – jih
- Střední Kanada
- Kanada – východ
- Indie – střed
- Střední USA
- East US
- USA – východ 2
- Francie – střed
- Francie – jih
- Německo – sever
- Německo – středozápad
- Japonsko – západ
- Jižní Korea – jih
- USA – středosever
- Severní Evropa
- Středojižní USA
- Southeast Asia
- Švýcarsko – sever
- Spojené arabské emiráty – střed
- Spojené království – jih
- Spojené království – západ
- USA – středozápad
- West Europe
- Západní Indie
- Západní USA 2

## <a name="next-steps"></a>Další kroky

* Přečtěte si o výhodách [Azure Cosmos DB poznámkových blocích Jupyter](cosmosdb-jupyter-notebooks.md)
* [Prozkoumat galerii ukázek poznámkových bloků](https://cosmos.azure.com/gallery.html)
* [Publikování poznámkových bloků do galerie poznámkových bloků Azure Cosmos DB](publish-notebook-gallery.md)
* [Použití funkcí a příkazů poznámkového bloku Pythonu](use-python-notebook-features-and-commands.md)
* [Použití funkcí a příkazů pro Poznámkový blok jazyka C#](use-csharp-notebook-features-and-commands.md)
* [Import poznámkových bloků z úložiště GitHub](import-github-notebooks.md)
