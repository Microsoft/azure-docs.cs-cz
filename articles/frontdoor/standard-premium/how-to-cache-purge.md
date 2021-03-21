---
title: Vymazání mezipaměti v Azure front-end Standard/Premium (Preview)
description: Tento článek vám pomůže pochopit, jak vyprázdnit mezipaměť na úrovni Standard/Premium pro Azure.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099059"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Vymazání mezipaměti v Azure front-end Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Assety služby Azure front-Time Standard/Premium jsou prostředky do mezipaměti, dokud nevyprší hodnota TTL (Time to Live) prostředku. Pokaždé, když klient požádá o Asset s hodnotou TTL (vypršela hodnota TTL), prostředí front Azure načte novou aktualizovanou kopii assetu, aby vypracovala požadavek, a pak uloží obnovenou mezipaměť.

Osvědčeným postupem je zajistit, aby vaši uživatelé vždycky získali nejnovější kopii svých assetů. Způsob, jak to provést, je nasdílet vaše prostředky pro každou aktualizaci a publikovat je jako nové adresy URL. Přední dveře Azure (Standard/Premium) okamžitě načtou nové prostředky pro další požadavky klientů. Někdy možná budete chtít vyprázdnit obsah uložený v mezipaměti ze všech hraničních uzlů a všechny tak vynutit načtení nových aktualizovaných prostředků. Důvodem, kdy chcete vymazat obsah uložený v mezipaměti, je, že jste provedli nové aktualizace aplikace nebo chcete aktualizovat prostředky, které obsahují nesprávné informace.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Předpoklady

Přečtěte si [mezipaměť front-dveří Azure](concept-caching.md) , abyste zjistili, jak funguje ukládání do mezipaměti.

## <a name="configure-cache-purge"></a>Konfigurace vyprázdnění mezipaměti

1. V profilu front-dveří Azure klikněte na stránku Přehled s prostředky, které chcete vyprázdnit, a pak vyberte **vyprázdnit mezipaměť**.

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="Snímek obrazovky s vyprázdněním mezipaměti na stránce s přehledem":::

1. Vyberte koncový bod a doménu, které chcete vymazat z hraničních uzlů. *(Můžete vybrat více než jednu doménu.)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="Snímek stránky vyprázdnění mezipaměti":::

1. Chcete-li vymazat všechny prostředky, vyberte možnost **vyprázdnit všechny prostředky pro vybrané domény**. V opačném případě v části **cesty** zadejte cestu k jednotlivým assetům, které chcete vymazat.

Tyto formáty jsou podporované v seznamech cest, které se mají vyprázdnit:

* **Vyprázdnit jednu cestu**: vyprázdnit jednotlivé prostředky zadáním úplné cesty prostředku (bez protokolu a domény) s příponou souboru, například/Pictures/strasbourg.png.
* **Kořenová doména vyprázdnění**: vyprázdní kořen koncového bodu znakem "/*" v cestě.

Vyprázdnění mezipaměti na frontě Azure Standard/preium se nerozlišuje bez velkých a malých písmen. Kromě toho se dotazují na řetězec nezávislá, což znamená, že vymazání adresy URL vyprázdní všechny variace řetězce dotazu. 

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vytvořit frontu Standard/Premium](create-front-door-portal.md).
