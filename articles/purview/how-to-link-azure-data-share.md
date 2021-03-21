---
title: Připojit ke sdílené složce Azure Data
description: Tento článek popisuje, jak připojit účet Azure Data Share pomocí Azure dosah k prohledání prostředků a sledovat datovou řadu.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553357"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Jak připojit Azure Data Share a Azure dosah

Tento článek popisuje, jak připojit účet [Azure Data Share](../data-share/overview.md) pomocí Azure dosah a řídit sdílené datové sady (odchozí i příchozí) ve vaší nemovitosti. Různé zásady správného řízení dat osoby můžou zjišťovat a sledovat objemy dat napříč hranicemi, jako jsou organizace, oddělení a i datová centra.

## <a name="common-scenarios"></a>Obvyklé situace

V případě, že se má za následek podrobnější informace o analýze základních příčin a analýze dopadů, se zaměřuje na řadu dat.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>Scénář 1:360 zobrazení datových sad, které jsou pro partnerských organizací nebo interní oddělení sdíleny/ven

Data důstojníci můžou zobrazit seznam všech datových sad, které jsou obousměrně sdílené se svými partnerskými organizacemi. Můžou hledat a zjišťovat datové sady podle názvu organizace a zobrazovat kompletní zobrazení všech odchozích a příchozích sdílených složek.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>Scénář 2: Analýza hlavní příčiny v datových sadách přicházejících do organizace (zobrazení uživatelů pro příchozí sdílené složky)

Sestava obsahuje nesprávné informace kvůli problémům s nadřazenými daty z účtu externího sdílení dat. Technici dat můžou porozumět výpadkům při selhání, být informováni o důvodech a dále kontaktovat vlastníka sdílené složky, aby opravili problémy, které způsobují jejich nesoulad s daty.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>Scénář 3: analýza dopadů u datových sad, které se procházejí mimo organizaci (zobrazení poskytovatele odchozích sdílených složek)

Producenti dat chtějí zjistit, kdo bude mít vliv na změnu své datové sady. Pomocí linie může producent dat snadno pochopit dopad interních nebo externích partnerů, kteří spotřebovávají data pomocí Azure Data Share.

## <a name="azure-data-share-and-purview-connected-experience"></a>Sdílení dat Azure a dosah prostředí s připojením

Pokud chcete připojit sdílenou složku Azure data a účet Azure dosah, postupujte takto:

1. Vytvořte účet dosah. V rámci účtu dosah budou shromážděny všechny informace o tomto datovém sdílení. Můžete použít existující účet dosah nebo vytvořit nový.

1. Připojte svoji sdílenou složku Azure k vašemu účtu dosah.

    1. Na portálu dosah můžete přejít do **centra pro správu** a připojit sdílenou složku Azure Data Share v části **externí připojení** .
    1. V horním panelu vyberte **+ Nový** , najděte sdílenou složku Azure data na postranním panelu a přidejte účet pro sdílení dat. Po připojení sdílené složky data k účtu dosah spusťte úlohu snímku, aby se v dosah zobrazovaly datové prostředky a informace o čárových položkách.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Centrum pro správu, které se má propojit s Azure Data Share":::

1. Spusťte snímek v Azure Data Share.

    - Jakmile se naváže připojení Azure Data Share s Azure dosah, můžete spustit snímek pro existující sdílené složky. 
    - Pokud nemáte žádné existující sdílené složky, otevřete na portálu Azure Data Share, abyste mohli [sdílet data](../data-share/share-your-data.md) [a přihlásit se k odběru sdílené složky](../data-share/subscribe-to-data-share.md).
    - Po dokončení snímku sdílení můžete zobrazit přidružené prostředky a zdroje dat pro sdílení dat v dosah.

1. Objevte účty pro sdílení dat a sdílejte informace v účtu dosah.

    - Na domovské stránce účtu dosah vyberte **Procházet podle typu Asset Type** a vyberte dlaždici **sdílená data Azure** . Můžete vyhledat název účtu, název sdílené složky, snímek sdílené složky nebo organizaci partnera. V opačném případě použijte filtry na stránce výsledků hledání pro název účtu, typ sdílení (odeslané vs. přijaté sdílené složky).

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="Sdílená složka Azure Data share na stránce výsledků hledání":::

    >[!Important]
    >Aby se prostředky pro sdílení dat zobrazovaly v dosah, musí být po připojení sdílené složky dat k dosah spuštěná úloha snímku.

1. Sledujte, které datové sady se sdílí s Azure Data Share.

    - Na stránce výsledků hledání dosah zvolte snímek sdílené složky (přijato/odesláno) a **Vyberte kartu se zobrazením čárového** grafu s nadřazenými a podřízenými závislostmi.

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Počet datových sad sdílených pomocí sdílené složky Azure":::

## <a name="next-steps"></a>Další kroky

- [Uživatelská příručka k linii katalogu](catalog-lineage-user-guide.md)
- [Odkaz na Azure Data Factory pro řádek](how-to-link-azure-data-factory.md)
