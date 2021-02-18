---
title: Použití služby Azure front-end Standard/Premium s sdílením prostředků mezi zdroji
description: Naučte se používat AFD (Azure front Branch) ke sdílení prostředků mezi zdroji (CORS).
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098837"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>Používání Azure front-bran Standard/Premium s sdílením prostředků mezi zdroji (CORS)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

## <a name="what-is-cors"></a>Co je CORS?

CORS (sdílení prostředků mezi zdroji) je funkce HTTP, která umožňuje webové aplikaci spuštěné v jedné doméně přistupovat k prostředkům v jiné doméně. Aby se snížila pravděpodobnost útoků prostřednictvím skriptování mezi weby, všechny moderní webové prohlížeče implementují omezení zabezpečení označované jako [zásady stejného původu](https://www.w3.org/Security/wiki/Same_Origin_Policy). To brání webové stránce v volání rozhraní API v jiné doméně.  CORS poskytuje zabezpečený způsob, jak umožníte jednomu zdroji (zdrojové doméně) volat rozhraní API v jiném zdroji.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Jak to funguje

Existují dva typy požadavků CORS, *jednoduchých požadavků* a *složitých požadavků.*

### <a name="for-simple-requests"></a>Pro jednoduché požadavky:

1. Prohlížeč odešle požadavek CORS s jinou **původní** hlavičkou požadavku HTTP. Hodnota tohoto záhlaví je zdrojem, který sloužil nadřazenou stránku, která je definována jako kombinace *protokolu,* *domény* a *portu.*  Když se stránka z https \: //www.contoso.com pokusí získat přístup k datům uživatele v Fabrikam.com původu, pošle se následující Hlavička požadavku na Fabrikam.com:

   `Origin: https://www.contoso.com`

2. Server může reagovat s některým z následujících způsobů:

   * Hlavička **Access-Control-Allow-Origin** v odpovědi, která označuje, který zdrojový web je povolen. Příklad:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Kód chyby HTTP, například 403, pokud Server nepovoluje požadavek na více zdrojů po kontrole původní hlavičky

   * Hlavička **Access-Control-Allow-Origin** , která má zástupný znak, který umožňuje všechny zdroje:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Pro komplexní požadavky:

Komplexní požadavek je požadavek CORS, ve kterém se před odesláním skutečné žádosti CORS vyžaduje, aby se v prohlížeči poslal *požadavek na kontrolu před výstupem* (tj. Předběžný test). Požadavek na předběžné kontroly požádá o oprávnění serveru, pokud může původní požadavek CORS pokračovat a je `OPTIONS` požadavek na stejnou adresu URL.

> [!TIP]
> Další podrobnosti o tocích CORS a běžných nástrah najdete v [Průvodci CORS pro rozhraní REST API](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Scénáře se zástupnými znaky nebo jedním zdrojem
CORS v Azure front-end budou fungovat automaticky bez další konfigurace, pokud je hlavička **Access-Control-Allow-Origin** nastavená na zástupný znak (*) nebo na jeden počátek.  Přední dveře Azure budou ukládat první odpověď do mezipaměti a v důsledku požadavků budou použity stejné hlavičky.

Pokud se už požadavky na přední dveře Azure nastavily ještě před tím, než je CORS nastavené na svém původu, budete muset vyprázdnit obsah v obsahu koncového bodu, aby se obsah znovu načítat pomocí hlavičky **Access-Control-Allow-Origin** .

## <a name="multiple-origin-scenarios"></a>Scénáře s více zdroji
Pokud potřebujete povolit, aby určitý seznam zdrojů byl pro CORS povolený, věci budou mít trochu složitější. K tomuto problému dochází, když CDN ukládá do mezipaměti hlavičku **Access-Control-Allow-Origin** pro první původ CORS.  Když jiný původ CORS vytvoří jinou žádost, CDN bude **používat hlavičku Access-Control-Allow-Origin** v mezipaměti, která se neshoduje. Existuje několik způsobů, jak tento problém vyřešit.

### <a name="azure-front-door-rule-set"></a>Sada pravidel pro přední dveře Azure

U front-bran Azure můžete vytvořit pravidlo v nastavení pravidel pro přední dveře Azure [nastavené](concept-rule-set.md) na kontrolu záhlaví **původu** na žádosti. Pokud se jedná o platný zdroj, vaše pravidlo nastaví hlavičku **Access-Control-Allow-Origin** se správnou hodnotou. V tomto případě se hlavička **Access-Control-Allow-Origin** ze zdrojového serveru souboru ignoruje a modul pravidel AFD plně spravuje povolené zdroje CORS.

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="Snímek obrazovky s příkladem pravidel se sadou pravidel":::

> [!TIP]
> Do pravidla můžete přidat další akce, které upraví další hlavičky odpovědí, jako je například **Access-Control-Allow-Methods**.
> 

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [vytvořit Front Door](create-front-door-portal.md).
