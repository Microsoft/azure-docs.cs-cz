---
title: Publikování poznámkových bloků do galerie poznámkových bloků Azure Cosmos DB
description: Naučte se stahovat poznámkové bloky z veřejné galerie, upravovat je a publikovat vlastní poznámkové bloky do galerie.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 58ae61bc9e1736b13bb1802e2f39d5ada045cb6a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039243"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Publikování poznámkových bloků do galerie poznámkových bloků Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB integrovaných notebooků Jupyter jsou přímo integrovány do vašich Azure Cosmos DBch účtů v Azure Portal. Pomocí těchto poznámkových bloků můžete data analyzovat a vizualizovat z Azure Portal. Integrované poznámkové bloky pro Azure Cosmos DB jsou aktuálně k dispozici v [mnoha oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Pokud chcete používat poznámkové bloky, [vytvořte nový účet Cosmos](create-cosmosdb-resources-portal.md) nebo [Povolte poznámkové bloky v existujícím účtu](enable-notebooks.md) v některé z těchto oblastí.

Prostředí poznámkového bloku v Azure Portal obsahuje některé ukázky publikované Azure Cosmos DB týmu. Má také veřejnou galerii, kde můžete publikovat a sdílet vlastní poznámkové bloky. Po publikování poznámkového bloku do galerie je k dispozici všem uživatelům Azure Cosmos DB, aby je mohli zobrazit a používat. V tomto článku se dozvíte, jak používat poznámkové bloky z veřejné galerie a jak svůj Poznámkový blok publikovat do galerie.

## <a name="download-a-notebook-from-the-gallery"></a>Stažení poznámkového bloku z Galerie

Pomocí následujících kroků můžete zobrazit a stáhnout poznámkové bloky z Galerie do svého pracovního prostoru vlastní poznámkové bloky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a přejděte k účtu Azure Cosmos DB, který je povolený v prostředí poznámkového bloku.

1. Přejděte na   >  kartu veřejné galerie v galerii **poznámkových bloků** Průzkumník dat  >    >   .

1. Přijměte [kodex chování](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)  před zobrazením nebo publikováním poznámkových bloků v galerii. Kodex chování je protokolován podle uživatele na úrovni předplatného. Když přepnete na jiné předplatné, musíte ho před přístupem k galerii přijmout znovu. Chcete-li přijmout kodex chování, zaškrtněte políčko a **pokračujte**:

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Přejděte do veřejné galerie poznámkových bloků a přijměte kodex chování.":::

1. Dále můžete přidat konkrétní Poznámkový blok na kartu Oblíbené položky nebo si ho stáhnout. Když si stáhnete Poznámkový blok, zkopíruje se do pracovního prostoru vašich poznámkových bloků, kde ho můžete spustit nebo upravit.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="Stáhněte si Poznámkový blok z Galerie do svého pracovního prostoru.":::

## <a name="publish-a-notebook-to-the-gallery"></a>Publikování poznámkového bloku do galerie

Když vytváříte vlastní poznámkové bloky nebo upravíte stávající poznámkové bloky tak, aby vyhovovaly jinému scénáři, můžete je chtít publikovat do galerie. Po publikování poznámkového bloku do galerie budou k němu mít přístup jiní uživatelé. Můžete [Prozkoumat galerii ukázek poznámkových bloků](https://cosmos.azure.com/gallery.html) a zobrazit aktuálně dostupné poznámkové bloky.

K publikování poznámkového bloku použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a přejděte k účtu Azure Cosmos DB, který je povolený v prostředí poznámkového bloku.

1. Přejděte na kartu poznámkové bloky **Průzkumník dat** poznámkových blocích  >    >   .

1. Přejít na Poznámkový blok, který chcete publikovat. Na panelu příkazů vyberte tlačítko **Uložit** a pak vyberte **publikovat do galerie**:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="Vyberte Poznámkový blok, který chcete publikovat do galerie.":::

   Možnost **publikovat do galerie** můžete najít také tak, že vyberete **...** tlačítko vedle názvu poznámkového bloku:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="Další přístup k výběru poznámkového bloku pro publikování do galerie.":::

1. Vyplňte formulář **publikovat do galerie** s následujícími podrobnostmi:

   * **Název:** Popisný název, který identifikuje váš Poznámkový blok.
   * **Popis:**  Krátký popis toho, co váš Poznámkový blok dělá.
   * **Značky:** Značky jsou volitelné a používají se k filtrování výsledků při hledání pomocí klíčového slova.
   * **Titulní obrázek:** Obrázek, který se používá na titulní stránce při publikování poznámkového bloku Můžete vybrat jednu z následujících možností:
   * **Vlastní image** – obrázek můžete nahrát z počítače. Vyberte soubor obrázku s poměrem stran 256x144.
   * **Adresa URL** – poskytněte veřejně PŘÍSTUPNOU adresu URL, kde se obrázek nachází.
   * **Pořídit snímek obrazovky** – snímek obrazovky otevřeného poznámkového bloku se automaticky povede a nahraje do verze Preview.
   * **Použijte první zobrazený** výstup první buňky, která má výstup zobrazení. Buňky, které zobrazují pouze Markdownu/text, se nepočítají jako výstup zobrazení.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="Vyplňte formulář publikovat do galerie.":::

   > [!NOTE]
   > Pokud používáte možnost **publikovat do galerie** z **...** u tlačítka vedle názvu poznámkového bloku se nezobrazí všechny možnosti **titulního obrázku** . Důvodem je, že Poznámkový blok možná není otevřený a Azure Cosmos DB nebude mít přístup k pořizovat snímku obrazovky nebo přístupu k prvnímu výstupu zobrazení.

1. Ověřte, že náhled vypadá v pořádku, a vyberte **publikovat**. Po publikování se tento poznámkový blok zobrazí ve veřejné galerii poznámkových bloků Azure Cosmos DB. Před publikováním se ujistěte, že jste odebrali veškerá citlivá data nebo výstup. Obsah poznámkového bloku se před publikováním vyhledá v případě porušení zásad Microsoftu. Dokončení tohoto procesu obvykle trvá několik sekund. Pokud jsou zjištěna nějaká porušení, zobrazí se na kartě oznámení zpráva. Pokud zjistíte případné porušení, váš Poznámkový blok nebude publikován a znovu ho publikujte.

   > [!NOTE]
   > Po publikování poznámkových bloků ve veřejné galerii je mohou zobrazit všichni Azure Cosmos DB uživatelé. Přístup není omezený na jedno předplatné nebo na úroveň organizace.

1. Po publikování poznámkového bloku v galerii ho můžete zobrazit na kartě **Moje publikovaná práce** . Můžete také odebrat nebo odstranit poznámkové bloky, které jste publikovali z veřejné galerie.

1. Můžete také nahlásit Poznámkový blok, který porušuje kodex chování. Pokud je zjištěno porušení, Cosmos DB automaticky odebere Poznámkový blok z galerie. Pokud je Poznámkový blok odebraný, uživatelé ho uvidí na kartě **Moje publikovaná práce** s odebraným poznámkou. Pokud chcete ohlásit Poznámkový blok, přejděte na  >  kartu veřejné galerie Průzkumník dat v galerii **poznámkových blocích**  >    >   . Otevřete Poznámkový blok, který chcete ohlásit, najeďte na tlačítko **nápovědy** v pravém horním rohu a vyberte možnost **nahlásit zneužití**.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="Nahlaste Poznámkový blok, který porušuje kodex chování.":::

## <a name="next-steps"></a>Další kroky

* Přečtěte si o výhodách [Azure Cosmos DB poznámkových blocích Jupyter](cosmosdb-jupyter-notebooks.md)
* [Použití funkcí a příkazů poznámkového bloku Pythonu](use-python-notebook-features-and-commands.md)
* [Použití funkcí a příkazů pro Poznámkový blok jazyka C#](use-csharp-notebook-features-and-commands.md)
* [Import poznámkových bloků z úložiště GitHub](import-github-notebooks.md)
