---
title: Verze toku uživatele v Azure Active Directory B2C | Microsoft Docs
description: Přečtěte si o verzích uživatelských toků dostupných v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c7b3ce6a4e72b90c6fd642b92d4a92eb02f4c92c
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063196"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Verze toku uživatele v Azure Active Directory B2C

>[!IMPORTANT]
> Tok uživatele uvedený v článku se považuje za ve verzi Public Preview, pokud není identifikovanýjako doporučený. Pro produkční aplikace byste měli použít jenom Doporučené toky uživatelů.

Toky uživatelů v Azure Active Directory B2C (Azure AD B2C) vám pomůžou nastavit společné [zásady](active-directory-b2c-reference-policies.md) , které plně popisují prostředí identity zákazníků. Mezi tyto prostředí patří registrace, přihlášení, resetování hesla nebo úpravy profilu. V Azure AD B2C můžete vybrat z kolekce doporučených uživatelských toků a zobrazovat náhled toků uživatelů.

Nové toky uživatelů se přidávají jako nové verze. Vzhledem k tomu, že toky uživatelů se stanou stabilní, budou doporučeny pro použití. Toky uživatelů jsou označeny jako **Doporučené** , pokud byly důkladně testovány. Toky uživatelů budou ve verzi Preview zváženy, dokud nebudou označeny jako doporučené. Použijte doporučený tok uživatele pro jakoukoli produkční aplikaci, ale vyberte z jiných verzí, abyste otestovali nové funkce, jakmile budou k dispozici. Neměli byste používat starší verze doporučených uživatelských toků.

## <a name="v1"></a>V1

| Tok uživatele | Doporučené | Popis |
| --------- | ----------- | ----------- |
| Resetování hesla | Ano | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Nastavení kompatibility tokenu</li><li>[Požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Úprava profilu | Ano | Umožňuje uživateli nakonfigurovat své atributy uživatele. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenu</li><li>Chování relace</li></ul> |
| Vlastník prostředku | Ne | Umožňuje uživateli s místním účtem, aby se přihlásil přímo v nativních aplikacích (není potřeba žádný prohlížeč). Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenu</li></ul> |
| Přihlášení | Ne | Umožňuje uživateli přihlásit se ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenu</li><li>Chování relace</li><li>Blokovat přihlášení</li><li>Vynutit resetování hesla</li><li>Zůstat přihlášeni (políčko zůstat přihlášeni)</ul><br>Pomocí tohoto toku uživatele nemůžete přizpůsobit uživatelské rozhraní. |
| Registrace | Ne | Umožňuje uživateli vytvořit účet. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenu</li><li>Chování relace</li><li>[Požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrace a přihlášení | Ano | Umožňuje uživateli vytvořit účet nebo se přihlásit ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenu</li><li>Chování relace</li><li>[Požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>V2

| Tok uživatele | Doporučené | Popis |
| --------- | ----------- | ----------- |
| Resetování hesla v2 | Ne | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Nastavení kompatibility tokenu</li><li>[Omezení stáří](basic-age-gating.md)</li><li>[požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Přihlášení v2 | Ne | Umožňuje uživateli přihlásit se ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenu</li><li>Chování relace</li><li>[Omezení stáří](basic-age-gating.md)</li><li>Přizpůsobení přihlašovací stránky</li></ul> |
| Registrace v2 | Ne | Umožňuje uživateli vytvořit účet. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenu</li><li>Chování relace</li><li>[Omezení stáří](basic-age-gating.md)</li><li>[Požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrace a přihlášení v2 | Ne | Umožňuje uživateli vytvořit účet nebo se přihlásit ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Omezení stáří](basic-age-gating.md)</li><li>[Požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul> |
