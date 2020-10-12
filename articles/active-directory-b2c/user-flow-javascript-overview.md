---
title: Verze JavaScriptu a rozložení stránky
titleSuffix: Azure AD B2C
description: Naučte se, jak povolit JavaScript a používat verze rozložení stránky v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 50644ad3be226648accba6a2f43d4ea068ff977c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258842"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Verze JavaScriptu a rozložení stránky v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C poskytuje sadu sbaleného obsahu obsahujícího jazyky HTML, CSS a JavaScript pro prvky uživatelského rozhraní ve vašich uživatelských tocích a vlastních zásadách.

Povolení JavaScriptu pro vaše aplikace:

* Povolte ji v uživatelském toku pomocí Azure Portal
* Vybrat [rozložení stránky](page-layout.md)
* Použití [b2clogin.com](b2clogin.md) v žádostech

Pokud máte v úmyslu povolit kód na straně klienta [JavaScript](javascript-samples.md) , prvky, na kterých založíte JavaScript, musí být neměnné. Pokud nejsou neměnné, můžou jakékoli změny způsobit neočekávané chování na stránkách uživatele. Chcete-li zabránit těmto problémům, vynutili použití rozložení stránky a určení verze rozložení stránky, aby bylo zajištěno, že definice obsahu, na kterých jste na svém JavaScriptu zakládáte, jsou neměnné. I v případě, že nechcete povolit JavaScript, můžete zadat verzi rozložení stránky pro stránky.

## <a name="enable-javascript"></a>Povolení JavaScriptu

Ve **vlastnostech**toku uživatele můžete povolit JavaScript. Povolení JavaScriptu taky vynutilo použití rozložení stránky. Pak můžete nastavit verzi rozložení stránky pro tok uživatele, jak je popsáno v následující části.

![Stránka vlastností toku uživatele s zvýrazněným nastavením povolit JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Vybrat verzi rozložení stránky

Bez ohledu na to, jestli ve vlastnostech toku uživatele povolíte JavaScript, můžete zadat verzi rozložení stránky pro stránky toku uživatele. Otevřete tok uživatele a vyberte **rozložení stránky**. V části **název rozložení**vyberte stránku tok uživatele a zvolte **verzi rozložení stránky (Preview)**.

Informace o různých verzích rozložení stránky naleznete v [protokolu změn verze rozložení stránky](page-layout.md).

![Nastavení rozložení stránky na portálu s rozevírací nabídkou verze rozložení stránky](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Další kroky

Můžete najít příklady použití JavaScriptu v [ukázkách JavaScriptu pro použití v Azure Active Directory B2C](javascript-samples.md).
