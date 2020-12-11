---
title: Starší verze uživatelských toků v Azure Active Directory B2C | Microsoft Docs
description: Přečtěte si o starších verzích uživatelských toků dostupných v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7cdfd6cb8947fb3b56fcbfe92874c8b9b27a8db1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108123"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Starší verze uživatelských toků v Azure Active Directory B2C

> [!IMPORTANT]
> Tento článek popisuje metodu starší verze pro toky uživatelů, která nabízí verze V1 (připravené pro produkční prostředí) a verze V 1.1 a v2 (Preview) verze uživatelských toků. Jiná prostředí, než je veřejný cloud Azure, budou dál používat tuto starší metodu správy verzí. Ve veřejném cloudu Azure se tato metoda nahrazuje [novými **doporučenými** verzemi a verze **Preview**](user-flow-versions.md).
> 
Toky uživatelů v Azure Active Directory B2C (Azure AD B2C) vám pomůžou nastavit společné [zásady](user-flow-overview.md) , které plně popisují prostředí identity zákazníků. Mezi tyto prostředí patří registrace, přihlášení, resetování hesla nebo úpravy profilu.

V následující tabulce, pokud tok uživatele není identifikován jako **doporučený**, se považuje za *verzi Preview*. Pro produkční aplikace byste měli použít jenom Doporučené toky uživatelů.

## <a name="v1"></a>V1

| Tok uživatele | Doporučeno | Popis |
| --------- | ----------- | ----------- |
| Resetování hesla | Ano | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>Nastavení kompatibility tokenů</li><li>[Požadavky na složitost hesla](password-complexity.md)</li></ul> |
| Úpravy profilu | Ano | Umožňuje uživateli nakonfigurovat své atributy uživatele. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li></ul> |
| Přihlášení pomocí ROPC | Ne | Umožňuje uživateli s místním účtem, aby se přihlásil přímo v nativních aplikacích (není potřeba žádný prohlížeč). Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li></ul> |
| Přihlásit se | Ne | Umožňuje uživateli přihlásit se ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>Blokovat přihlášení</li><li>Vynutit resetování hesla</li><li>Zůstat přihlášeni (políčko zůstat přihlášeni)</ul><br>Pomocí tohoto toku uživatele nemůžete přizpůsobit uživatelské rozhraní. |
| Registrace | Ne | Umožňuje uživateli vytvořit účet. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Požadavky na složitost hesla](password-complexity.md)</li></ul> |
| Registrace a přihlášení | Ano | Umožňuje uživateli vytvořit účet nebo se přihlásit ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Požadavky na složitost hesla](password-complexity.md)</li></ul>|

## <a name="v11"></a>Verze 1.1

| Tok uživatele | Doporučeno | Popis |
| --------- | ----------- | ----------- |
| Resetování hesla v 1.1 | Ne | Umožní uživateli zvolit nové heslo po ověření e-mailu (k dispozici nové rozložení stránky). Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>Nastavení kompatibility tokenů</li><li>[Požadavky na složitost hesla](password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Tok uživatele | Doporučeno | Popis |
| --------- | ----------- | ----------- |
| Resetování hesla v2 | Ne | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>Nastavení kompatibility tokenů</li><li>[Ověřování věku](basic-age-gating.md)</li><li>[požadavky na složitost hesla](password-complexity.md)</li></ul> |
| Úprava profilu v2 | Ano | Umožňuje uživateli nakonfigurovat své atributy uživatele. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li></ul> |
| Přihlášení v2 | Ne | Umožňuje uživateli přihlásit se ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Ověřování věku](basic-age-gating.md)</li><li>Přizpůsobení přihlašovací stránky</li></ul> |
| Zaregistrovat v2 | Ne | Umožňuje uživateli vytvořit účet. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Ověřování věku](basic-age-gating.md)</li><li>[Požadavky na složitost hesla](password-complexity.md)</li></ul> |
| Registrace a přihlášení v2 | Ne | Umožňuje uživateli vytvořit účet nebo se přihlásit ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Ověřování věku](basic-age-gating.md)</li><li>[Požadavky na složitost hesla](password-complexity.md)</li></ul> |