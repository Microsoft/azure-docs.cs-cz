---
title: Verze toku uživatelů ve službě Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Přečtěte si o verzích toků uživatelů dostupných ve službě Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185616"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Verze toku uživatelů ve službě Azure Active Directory B2C

Toky uživatelů ve službě Azure Active Directory B2C (Azure AD B2C) vám pomohou nastavit společné [zásady,](user-flow-overview.md) které plně popisují prostředí identity zákazníků. Mezi tyto možnosti patří registrace, přihlášení, resetování hesla nebo úpravy profilu. V Azure AD B2C, můžete vybrat z kolekce doporučených toků uživatelů a náhledu toků uživatelů.

Nové toky uživatelů jsou přidány jako nové verze. Jak se toky uživatelů stanou stabilními, budou doporučeny pro použití. Toky uživatelů jsou označeny jako **Doporučené,** pokud byly důkladně testovány. Toky uživatelů budou v náhledu považovány, dokud nebudou označeny jako doporučené. Použijte doporučený tok uživatele pro všechny produkční aplikace, ale vyberte z jiných verzí pro testování nových funkcí, jakmile budou k dispozici. Neměli byste používat starší verze doporučených uživatelských toků.

>[!IMPORTANT]
> Pokud tok uživatele není identifikován jako **Doporučeno**, je považován za ve *verzi preview*. Pro produkční aplikace byste měli používat pouze doporučené toky uživatelů.

## <a name="v1"></a>V1

| Tok uživatele | Doporučené | Popis |
| --------- | ----------- | ----------- |
| Resetování hesla | Ano | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](custom-policy-multi-factor-authentication.md)</li><li>Nastavení kompatibility tokenů</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |
| Úpravy profilu | Ano | Umožňuje uživateli konfigurovat jejich uživatelské atributy. Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li></ul> |
| Přihlášení pomocí ROPC | Ne | Umožňuje uživateli s místním účtem přihlásit přímo v nativních aplikacích (není vyžadován žádný prohlížeč). Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li></ul> |
| Přihlášení | Ne | Umožňuje uživateli přihlásit se ke svému účtu. Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](custom-policy-multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>Blokovat přihlášení</li><li>Vynutit resetování hesla</li><li>Mějte přihlášen (KMSI)</ul><br>Uživatelské rozhraní nelze přizpůsobit pomocí tohoto toku uživatele. |
| Registrace | Ne | Umožňuje uživateli vytvořit účet. Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](custom-policy-multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |
| Registrace a přihlášení | Ano | Umožňuje uživateli vytvořit účet nebo se přihlásit ke svému účtu. Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](custom-policy-multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| Tok uživatele | Doporučené | Popis |
| --------- | ----------- | ----------- |
| Obnovení hesla v1.1 | Ne | Umožňuje uživateli vybrat nové heslo po ověření e-mailu (nové rozložení stránky k dispozici). Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](custom-policy-multi-factor-authentication.md)</li><li>Nastavení kompatibility tokenů</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Tok uživatele | Doporučené | Popis |
| --------- | ----------- | ----------- |
| Reset hesla v2 | Ne | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](custom-policy-multi-factor-authentication.md)</li><li>Nastavení kompatibility tokenů</li><li>[Ověřování věku](basic-age-gating.md)</li><li>[požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |
| Úpravy profilu v2 | Ano | Umožňuje uživateli konfigurovat jejich uživatelské atributy. Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li></ul> |
| Přihlásit se v2 | Ne | Umožňuje uživateli přihlásit se ke svému účtu. Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](custom-policy-multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Ověřování věku](basic-age-gating.md)</li><li>Vlastní nastavení přihlašovací stránky</li></ul> |
| Zaregistrujte se v2 | Ne | Umožňuje uživateli vytvořit účet. Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](custom-policy-multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Ověřování věku](basic-age-gating.md)</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |
| Zaregistrujte se a přihlaste se do 2 | Ne | Umožňuje uživateli vytvořit účet nebo přihlásit svůj účet. Pomocí tohoto toku uživatele můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](custom-policy-multi-factor-authentication.md)</li><li>[Ověřování věku](basic-age-gating.md)</li><li>[Požadavky na složitost hesla](user-flow-password-complexity.md)</li></ul> |
