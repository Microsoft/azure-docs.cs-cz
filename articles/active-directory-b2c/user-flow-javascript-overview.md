---
title: JavaScript a verze rozložení stránky
titleSuffix: Azure AD B2C
description: Přečtěte si, jak povolit JavaScript a používat verze rozložení stránky ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185828"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript a verze rozložení stránky ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C poskytuje sadu baleného obsahu obsahujícího HTML, CSS a JavaScript pro prvky uživatelského rozhraní ve vašich uživatelských tocích a vlastních zásadách.

Povolení JavaScriptu pro vaše aplikace:

* Povolení v toku uživatelů pomocí portálu Azure
* Výběr [rozložení stránky](page-layout.md)
* Používejte [b2clogin.com](b2clogin.md) ve svých požadavcích

Pokud máte v úmyslu povolit kód [javascriptu](javascript-samples.md) na straně klienta, musí být prvky, na kterých je javascript zakládán, neměnné. Pokud nejsou neměnné, jakékoli změny mohou způsobit neočekávané chování na uživatelských stránkách. Chcete-li těmto problémům zabránit, vynuťte použití rozložení stránky a určete verzi rozložení stránky, abyste zajistili, že definice obsahu, na kterých jste javascript založili, jsou neměnné. JavaScript nelze povolit, můžete pro své stránky zadat verzi rozložení stránky.

## <a name="enable-javascript"></a>Povolení JavaScriptu

V toku uživatele **Vlastnosti**, můžete povolit JavaScript. Povolení JavaScriptu také vynucuje použití rozložení stránky. Potom můžete nastavit verzi rozložení stránky pro tok uživatele, jak je popsáno v další části.

![Stránka vlastností toku uživatele se zvýrazněným nastavením Povolit JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Výběr verze rozložení stránky

Bez ohledu na to, zda ve vlastnostech toku uživatele povolíte JavaScript, můžete určit verzi rozložení stránky pro stránky toku uživatelů. Otevřete tok uživatele a vyberte **rozložení stránky**. V části **NÁZEV ROZLOŽENÍ**vyberte stránku toku uživatele a zvolte verzi **rozložení stránky**.

Informace o různých verzích rozložení stránky najdete v [protokolu změn verzí rozložení stránky](page-layout.md).

![Nastavení rozložení stránky na portálu zobrazující rozevírací seznam verze rozložení stránky](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Další kroky

Příklady využití JavaScriptu najdete ve [vzorcích JavaScriptu pro použití ve službě Azure Active Directory B2C](javascript-samples.md).
