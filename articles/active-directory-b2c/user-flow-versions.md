---
title: Verze toku uživatele v Azure Active Directory B2C | Microsoft Docs
description: Přečtěte si o verzích uživatelských toků dostupných v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40d21d3390396e0cb7e44d4e19598f9b0b691087
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185616"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Verze toku uživatele v Azure Active Directory B2C

Toky uživatelů v Azure Active Directory B2C (Azure AD B2C) vám pomůžou nastavit společné [zásady](user-flow-overview.md) , které plně popisují prostředí identity zákazníků. Mezi tyto prostředí patří registrace, přihlášení, resetování hesla nebo úpravy profilu. V Azure AD B2C můžete vybrat z kolekce doporučených uživatelských toků a zobrazovat náhled toků uživatelů.

Nové toky uživatelů se přidávají jako nové verze. Vzhledem k tomu, že toky uživatelů se stanou stabilní, budou doporučeny pro použití. Toky uživatelů jsou označeny jako **Doporučené** , pokud byly důkladně testovány. Toky uživatelů budou ve verzi Preview zváženy, dokud nebudou označeny jako doporučené. Použijte doporučený tok uživatele pro jakoukoli produkční aplikaci, ale vyberte z jiných verzí, abyste otestovali nové funkce, jakmile budou k dispozici. Neměli byste používat starší verze doporučených uživatelských toků.

>[!IMPORTANT]
> Pokud tok uživatele není identifikován jako **doporučený**, bude považován za *verzi Preview*. Pro produkční aplikace byste měli použít jenom Doporučené toky uživatelů.

## <a name="v1"></a>V1

| Tok uživatele | Doporučené | Popis |
| --------- | ----------- | ----------- |
| Resetování hesla | Ano | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>Nastavení kompatibility tokenů</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |
| Úpravy profilu | Ano | Umožňuje uživateli nakonfigurovat své atributy uživatele. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li></ul> |
| Přihlášení pomocí ROPC | Ne | Umožňuje uživateli s místním účtem, aby se přihlásil přímo v nativních aplikacích (není potřeba žádný prohlížeč). Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li></ul> |
| Přihlášení | Ne | Umožňuje uživateli přihlásit se ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>Blokovat přihlášení</li><li>Vynutit resetování hesla</li><li>Zůstat přihlášeni (políčko zůstat přihlášeni)</ul><br>Pomocí tohoto toku uživatele nemůžete přizpůsobit uživatelské rozhraní. |
| Registrace | Ne | Umožňuje uživateli vytvořit účet. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |
| Registrace a přihlášení | Ano | Umožňuje uživateli vytvořit účet nebo se přihlásit ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>Verze 1.1

| Tok uživatele | Doporučené | Popis |
| --------- | ----------- | ----------- |
| Resetování hesla v 1.1 | Ne | Umožní uživateli zvolit nové heslo po ověření e-mailu (k dispozici nové rozložení stránky). Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>Nastavení kompatibility tokenů</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Tok uživatele | Doporučené | Popis |
| --------- | ----------- | ----------- |
| Resetování hesla v2 | Ne | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>Nastavení kompatibility tokenů</li><li>[Omezení stáří](basic-age-gating.md)</li><li>[požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |
| Úprava profilu v2 | Ano | Umožňuje uživateli nakonfigurovat své atributy uživatele. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li></ul> |
| Přihlášení v2 | Ne | Umožňuje uživateli přihlásit se ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Omezení stáří](basic-age-gating.md)</li><li>Přizpůsobení přihlašovací stránky</li></ul> |
| Zaregistrovat v2 | Ne | Umožňuje uživateli vytvořit účet. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Omezení stáří](basic-age-gating.md)</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |
| Registrace a přihlášení v2 | Ne | Umožňuje uživateli vytvořit účet nebo se přihlásit ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](custom-policy-multi-factor-authentication.md)</li><li>[Omezení stáří](basic-age-gating.md)</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |
