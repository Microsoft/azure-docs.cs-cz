---
title: 'Přední dveře Azure: správce koncových bodů'
description: Tento článek poskytuje přehled služby Azure front-end Endpoint Manager.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099222"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>Co je služba Azure front dveří Standard/Premium (Preview) Endpoint Manager?

> [!NOTE]
> * Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Prohlédněte si [dokumentaci ke službě Azure front dveří](../front-door-overview.md).

Správce koncového bodu poskytuje přehled koncových bodů, které jste nakonfigurovali pro vaše přední dveře Azure. Koncový bod je logické seskupení domén a jejich přidružených konfigurací. Správce koncových bodů pomáhá spravovat kolekci koncových bodů pro operaci CRUD (operace vytvoření, čtení, aktualizace a odstranění). Pomocí Správce koncových bodů můžete spravovat následující elementy pro koncové body:

* Domény
* Skupiny původních
* Trasy
* Zabezpečení

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="Snímek obrazovky se správcem koncových bodů bez konfigurací" lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

Správce koncového bodu uvádí, kolik instancí každého prvku je vytvořeno v rámci koncového bodu. Zobrazí se také stav přidružení pro každý prvek. Můžete například vytvořit více domén a skupin původu a přiřadit mezi nimi přidružení různých tras.

> [!IMPORTANT]
> * Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v tématu [**doplňujících podmínek použití pro Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="linked-view"></a>Propojené zobrazení

Díky propojenému zobrazení v rámci správce koncových bodů můžete snadno identifikovat přidružení mezi prvky front-dveří Azure, jako jsou:

* Které domény jsou přidruženy k aktuálnímu koncovému bodu?
* Která skupina původu je přidružená k této doméně?
* Které zásady WAF jsou přidruženy k této doméně?

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="Snímek obrazovky správce koncových bodů s konfiguracemi" lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vytvořit frontu Standard/Premium](create-front-door-portal.md).
