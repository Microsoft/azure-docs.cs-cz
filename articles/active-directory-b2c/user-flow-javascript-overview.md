---
title: Verze JavaScriptu a rozložení stránky
titleSuffix: Azure AD B2C
description: Naučte se, jak povolit JavaScript a používat verze rozložení stránky v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f6d4849b02f320c7479469b4ee56be50e4f8dee
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840090"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Verze JavaScriptu a rozložení stránky v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C poskytuje sadu sbaleného obsahu obsahujícího jazyky HTML, CSS a JavaScript pro prvky uživatelského rozhraní ve vašich uživatelských tocích a vlastních zásadách. Pokud chcete pro své aplikace povolit JavaScript, musíte přidat element na [vlastní zásadu](custom-policy-overview.md) nebo ho na portálu povolit pro toky uživatelů, vybrat rozložení stránky a použít [b2clogin.com](b2clogin.md) ve svých žádostech.

Pokud máte v úmyslu povolit [JavaScriptový](javascript-samples.md) kód na straně klienta, je vhodné zajistit, aby prvky, na kterých je váš JavaScript založen, byly neměnné. V opačném případě by jakékoli změny mohly způsobit neočekávané chování na stránkách uživatele. Chcete-li těmto problémům zabránit, můžete vynutili použití rozložení stránky a určení verze rozložení stránky. Tím zajistíte, že všechny definice obsahu, na kterých jste vycházíte z JavaScriptu, jsou neměnné. I v případě, že nechcete povolit JavaScript, můžete zadat verzi rozložení stránky pro stránky.

## <a name="user-flows"></a>Toky uživatele

Ve **vlastnostech**toku uživatele lze povolit jazyk JavaScript, který také vynutil použití rozložení stránky. Pak můžete nastavit verzi rozložení stránky pro tok uživatele, jak je popsáno v následující části.

![Stránka vlastností toku uživatele s zvýrazněným nastavením povolit JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>Vybrat verzi rozložení stránky

Bez ohledu na to, jestli ve vlastnostech toku uživatele povolíte JavaScript, můžete zadat verzi rozložení stránky pro stránky toku uživatele. Otevřete tok uživatele a vyberte **rozložení stránky**. V části **název rozložení**vyberte stránku tok uživatele a zvolte **verzi rozložení stránky**.

Informace o různých verzích rozložení stránky naleznete v [protokolu změn verze](page-layout.md#version-change-log).

![Nastavení rozložení stránky na portálu s rozevírací nabídkou verze rozložení stránky](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>Vlastní zásady

Chcete-li povolit jazyk JavaScript ve vlastních zásadách, přidejte element **ScriptExecution** do elementu **RelyingParty** v souboru vlastní zásady. Další informace najdete v tématu [ukázky JavaScriptu pro použití v Azure Active Directory B2C](javascript-samples.md).

Bez ohledu na to, jestli ve vlastních zásadách povolíte JavaScript, můžete pro své stránky zadat verzi rozložení stránky. Další informace o určení rozložení stránky najdete v tématu [Výběr rozložení stránky v Azure Active Directory B2C pomocí vlastních zásad](page-layout.md).

## <a name="next-steps"></a>Další kroky

Informace o různých verzích rozložení stránky najdete v části **protokol změn verze** [v tématu Výběr rozložení stránky v Azure Active Directory B2C pomocí vlastních zásad](page-layout.md#version-change-log).

Můžete najít příklady použití JavaScriptu v [ukázkách JavaScriptu pro použití v Azure Active Directory B2C](javascript-samples.md).
