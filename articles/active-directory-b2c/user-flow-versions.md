---
title: Verze toku uživatele v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o verzích nástroje toky uživatelů, které jsou k dispozici v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ed57a9fa3b041961ce220e8f10d9aed5e7bef60e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511925"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Verze toku uživatele v Azure Active Directory B2C

>[!IMPORTANT]
> Libovolný tok uživatele uvedená v tomto článku se považuje za ve verzi public preview Pokud označen jako **doporučená**. Toky uživatelů doporučené byste měli používat jenom pro produkční aplikace.

Toky uživatelů v Azure Active Directory (Azure AD) B2C můžete nastavit společné [zásady](active-directory-b2c-reference-policies.md) , která plně popisují prostředí identity zákazníků. Tyto zkušenosti zahrnují resetování hesla registrace, přihlášení, nebo upravování profilu. V Azure AD B2C můžete vybrat z kolekce toky doporučené uživatelů a toky uživatelů pro verzi preview. 

Nové toky uživatelů se přidají jako nové verze. Toky uživatelů jsou stabilní, budete se doporučuje pro použití. Toky uživatelů jsou označeny jako **doporučená** Pokud jste byly testovány důkladně. Toky uživatelů se budou považovat za ve verzi preview, dokud nebude označen jako doporučená. Použít tok doporučené uživatele pro každou aplikaci v produkčním prostředí, ale neměli od ostatních verzí k testování nových funkcí, protože je k dispozici. Neměli byste používat starší verze toky doporučené uživatelů.

## <a name="v1"></a>V1

| Tok uživatele. | Doporučené | Popis |
| --------- | ----------- | ----------- |
| Resetování hesla | Ano | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatele, můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Nastavení kompatibility tokenů</li><li>[Požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Upravování profilu | Ano | Umožňuje uživatelům nakonfigurovat jejich atributy uživatele. Pomocí tohoto toku uživatele, můžete nakonfigurovat: <ul><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li></ul> |
| vlastník prostředku | Ne | Umožňuje uživateli místní účet přihlásit přímo v nativních aplikacích (nejsou potřeba žádné prohlížeč). Pomocí tohoto toku uživatele, můžete nakonfigurovat: <ul><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenů</li></ul> |
| Přihlášení | Ne | Umožňuje uživateli přihlásit ke svému účtu. Pomocí tohoto toku uživatele, můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>Přihlášení bloku</li><li>Vynutit resetování hesla</li><li>Neodhlašovat (políčko zůstat Přihlášeni)</ul><br>Nelze přizpůsobit uživatelské rozhraní se tento tok uživatele. |
| Registrace | Ne | Umožňuje uživateli vytvoření účtu služby. Pomocí tohoto toku uživatele, můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrace a přihlášení | Ano | Umožňuje uživateli vytvoření účtu služby nebo se přihlaste ke svému účtu. Pomocí tohoto toku uživatele, můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>V2

| Tok uživatele. | Doporučené | Popis |
| --------- | ----------- | ----------- |
| V2 pro resetování hesla | Ne | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatele, můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Nastavení kompatibility tokenů</li><li>[Věku](basic-age-gating.md)</li><li>[Požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Přihlaste se v2 | Ne | Umožňuje uživateli přihlásit ke svému účtu. Pomocí tohoto toku uživatele, můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Věku](basic-age-gating.md)</li><li>Přizpůsobení přihlašovací stránky</li></ul> |
| Zaregistrujte v2 | Ne | Umožňuje uživateli vytvoření účtu služby. Pomocí tohoto toku uživatele, můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Životnost tokenu](active-directory-b2c-reference-tokens.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Věku](basic-age-gating.md)</li><li>[Požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrace a přihlášení ve v2 | Ne | Umožňuje uživateli vytvoření účtu služby nebo se přihlaste svým účtem. Pomocí tohoto toku uživatele, můžete nakonfigurovat: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Věku](basic-age-gating.md)</li><li>[Požadavky na složitost hesla](active-directory-b2c-reference-password-complexity.md)</li></ul> |
