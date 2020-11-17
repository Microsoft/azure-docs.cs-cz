---
title: Použití Azure Sentinel watchlists
description: Tento článek popisuje, jak pomocí Azure Sentinel watchlists prozkoumat hrozby, importovat obchodní data, vytvořit seznamy povolených dat a rozšířit data událostí.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: fd3c8a08e5512d15be4dfb26ca3eff151d08386f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651358"
---
# <a name="use-azure-sentinel-watchlists"></a>Použití Azure Sentinel watchlists

Azure Sentinel watchlists umožňuje shromažďování dat z externích zdrojů dat pro korelaci s událostmi ve vašem prostředí Azure Sentinel. Po vytvoření můžete watchlists použít ve svých hledáních, pravidlech detekce, loveckí hrozeb a odpovědích playbooky. Watchlists se ukládají do vašeho pracovního prostoru Sentinel Azure jako dvojici název-hodnota a ukládají se do mezipaměti pro optimální výkon dotazů a nízkou latenci.

Obvyklé scénáře použití watchlists zahrnují:

- Rychlým importem IP adres, hodnot hash souborů a dalších dat ze souborů CSV můžete rychle **prozkoumat hrozby** a reagovat na incidenty. Po importu můžete páry název-hodnota seznamu ke zhlédnutí použít pro spojení a filtry v pravidlech výstrah, loveckých hrozbách, sešitech, poznámkových blocích a obecných dotazech.

- **Import obchodních dat** jako watchlists Například importujte seznamy uživatelů s privilegovaným přístupem k systému nebo ukončené zaměstnance a potom pomocí seznamu ke zhlédnutí Vytvořte seznamy povolených a zakázaných aplikací, které se používají ke zjištění nebo zabránění uživatelům v přihlášení k síti.

- **Omezení únavy výstrah**. Vytvořte seznamy povolených pro potlačení výstrah ze skupiny uživatelů, jako jsou uživatelé z autorizovaných IP adres, které provádějí úlohy, které by obvykle aktivovaly výstrahu, a zabraňují nedovoleným událostem v zobrazování výstrah.

- **Obohacení dat události** Pomocí watchlists můžete rozšířit data události pomocí kombinací název-hodnota odvozené z externích zdrojů dat.

## <a name="create-a-new-watchlist"></a>Vytvořit nový seznamu ke zhlédnutí

1. V Azure Portal přejděte na **Azure Sentinel**  >  **Configuration**  >  **seznamu ke zhlédnutí** a pak vyberte **Přidat nový**.

    > [!div class="mx-imgBorder"]
    > ![Nový seznamu ke zhlédnutí](./media/watchlists/sentinel-watchlist-new.png)

1. Na stránce **Obecné** zadejte název, popis a alias pro seznamu ke zhlédnutí a pak vyberte **Další**.

    > [!div class="mx-imgBorder"]
    > ![Obecná stránka seznamu ke zhlédnutí](./media/watchlists/sentinel-watchlist-general.png)

1. Na stránce **zdroj** vyberte typ datové sady, nahrajte soubor a pak vyberte **Další**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="seznamu ke zhlédnutí zdrojová stránka" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > Nahrávání souborů je v současné době omezené na soubory o velikosti až 3,8 MB.

1. Zkontrolujte informace, ověřte, zda je správná, a pak vyberte **vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Stránka seznamu ke zhlédnutí Revize](./media/watchlists/sentinel-watchlist-review.png)

    Po vytvoření seznamu ke zhlédnutí se zobrazí oznámení.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="oznámení o úspěšném vytvoření seznamu ke zhlédnutí" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>Použití watchlists v dotazech

1. V Azure Portal přejděte na **Azure Sentinel**  >  **Configuration**  >  **seznamu ke zhlédnutí**, vyberte seznamu ke zhlédnutí, který chcete použít, a pak vyberte **Zobrazit v Log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="použití watchlists v dotazech" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. Položky ve vašem seznamu ke zhlédnutí jsou automaticky extrahovány pro váš dotaz a zobrazí se na kartě **výsledky** . Následující příklad ukazuje výsledky extrakce polí **servername** a **IPAddress** .

    > [!NOTE]
    > Časové razítko v dotazech bude v uživatelském rozhraní dotazu i v naplánovaných upozorněních ignorováno.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="dotazy s poli seznamu ke zhlédnutí" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
## <a name="use-watchlists-in-analytics-rules"></a>Použití watchlists v pravidlech analýz

Pokud chcete použít watchlists v pravidlech analýzy, přejděte z Azure Portal na **Azure Sentinel**  >  **Configuration**  >  **Analytics** a vytvořte pravidlo pomocí `_GetWatchlist('<watchlist>')` funkce v dotazu.

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="použití watchlists v pravidlech analýz" lightbox="./media/watchlists/sentinel-watchlist-analytics-rule.png":::

## <a name="view-list-of-watchlists-aliases"></a>Zobrazit seznam aliasů watchlists

Pokud chcete získat seznam aliasů seznamu ke zhlédnutí, přejděte z Azure Portal do části **Azure Sentinel**  >  **General**  >  **logs** a spusťte následující dotaz: `_GetWatchlistAlias` . Seznam aliasů můžete zobrazit na kartě **výsledky** .

> [!div class="mx-imgBorder"]
> ![seznam watchlists](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak používat watchlists ve službě Azure Sentinel k rozšíření dat a zlepšení vyšetřování. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.