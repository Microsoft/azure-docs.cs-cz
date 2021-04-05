---
title: Verze toku uživatele v Azure Active Directory B2C | Microsoft Docs
description: Přečtěte si o verzích uživatelských toků dostupných v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1c05ff1bf1956943230bf523584025787495d57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517850"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Verze toku uživatele v Azure Active Directory B2C

Toky uživatelů v Azure Active Directory B2C (Azure AD B2C) vám pomůžou nastavit společné [zásady](user-flow-overview.md) , které plně popisují prostředí identity zákazníků. Mezi tyto prostředí patří registrace, přihlášení, resetování hesla nebo úpravy profilu. Následující tabulky popisují toky uživatelů, které jsou k dispozici v Azure AD B2C.

> [!IMPORTANT]
> Změnili jsme způsob, jakým označujeme verze toků uživatelů. Dříve jsme nabízeli verze v1 (připravené pro produkční prostředí) a verze v1.1 a v2 (Preview). Nyní jsme konsoliduje toky uživatelů do dvou verzí:
>
>- **Doporučené** toky pro uživatele jsou nové verze Preview toků uživatelů. Jsou důkladně testovány a kombinovány se všemi funkcemi starších verzí **v2** a **v 1.1** . Až dál, budou se udržovat a aktualizovat nové doporučené toky uživatelů. Po přechodu na tyto nové doporučené toky uživatelů budete mít přístup k novým funkcím, jakmile jsou vydány.
>- **Standardní** uživatelské toky, dříve označované jako **v1**, jsou všeobecně dostupné, uživatelské toky připravené pro produkční prostředí. Pokud jsou toky uživatelů klíčové a závisí na vysoce stabilních verzích, můžete pokračovat v používání standardních uživatelských toků a realizovat, že tyto verze nebudou zachované a aktualizované.
>
>Všechny toky pro uživatele starší verze Preview (V 1.1 a v2) jsou na cestě k vyřazení od **1. srpna 2021**. Pokud je to možné, důrazně doporučujeme, abyste se co nejdříve [přepnuli na nové **Doporučené** verze](#how-to-switch-to-a-new-recommended-user-flow) , abyste mohli vždycky využívat nejnovější funkce a aktualizace. *Tyto změny platí jenom pro veřejný cloud Azure. Další prostředí budou dál používat [starší verze uživatelských toků](user-flow-versions-legacy.md).*

## <a name="recommended-user-flows"></a>Doporučené toky uživatelů

Doporučené toky pro uživatele jsou verze Preview, které spojují nové funkce se staršími možnostmi verze V2 a V 1.1. Dál se budou udržovat a aktualizovat Doporučené toky uživatelů.

| Tok uživatele | Description |
| --------- | ----------- |
| Resetování hesla (Preview) | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>Nastavení kompatibility tokenů</li><li>[Ověřování věku](age-gating.md)</li><li>[požadavky na složitost hesla](password-complexity.md)</li></ul> |
| Úpravy profilu (Preview) | Umožňuje uživateli nakonfigurovat své atributy uživatele. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li></ul> |
| Přihlášení (Preview) | Umožňuje uživateli přihlásit se ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Ověřování věku](age-gating.md)</li><li>Přizpůsobení přihlašovací stránky</li></ul> |
| Zaregistrovat se (Preview) | Umožňuje uživateli vytvořit účet. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Ověřování věku](age-gating.md)</li><li>[Požadavky na složitost hesla](password-complexity.md)</li></ul> |
| Registrace a přihlášení (Preview) | Umožňuje uživateli vytvořit účet nebo se přihlásit ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Ověřování věku](age-gating.md)</li><li>[Požadavky na složitost hesla](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>Toky standardních uživatelů

Standardní uživatelské toky (dříve označované jako V1) jsou všeobecně dostupné, uživatelské toky připravené pro produkční prostředí. Standardní uživatelské toky se nebudou dál aktualizovat.

| Tok uživatele | Description |
| --------- | ----------- | ----------- |
| Resetování hesla | Umožňuje uživateli zvolit nové heslo po ověření e-mailu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>Nastavení kompatibility tokenů</li><li>[Požadavky na složitost hesla](password-complexity.md)</li></ul> |
| Úpravy profilu | Umožňuje uživateli nakonfigurovat své atributy uživatele. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li></ul> |
| Přihlásit se | Umožňuje uživateli přihlásit se ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>Blokovat přihlášení</li><li>Vynutit resetování hesla</li><li>Zůstat přihlášeni (políčko zůstat přihlášeni)</ul><br>Pomocí tohoto toku uživatele nemůžete přizpůsobit uživatelské rozhraní. |
| Registrace | Umožňuje uživateli vytvořit účet. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Požadavky na složitost hesla](password-complexity.md)</li></ul> |
| Registrace a přihlášení | Umožňuje uživateli vytvořit účet nebo se přihlásit ke svému účtu. Pomocí tohoto toku uživatelů můžete nakonfigurovat: <ul><li>[Vícefaktorové ověřování](multi-factor-authentication.md)</li><li>[Životnost tokenu](tokens-overview.md)</li><li>Nastavení kompatibility tokenů</li><li>Chování relace</li><li>[Požadavky na složitost hesla](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>Postup přepnutí na nový doporučený uživatelský tok

Chcete-li přepnout ze starší verze uživatelského toku na novou **doporučenou** verzi Preview, postupujte takto:

1. Pomocí kroků v tomto kurzu vytvořte nové zásady toku uživatelů [: Vytvořte toky uživatelů v Azure Active Directory](tutorial-create-user-flows.md). Při vytváření toku uživatele vyberte **doporučenou** verzi.

3. Nakonfigurujte nového toku uživatele se stejnými nastaveními, která byla nakonfigurovaná v zásadách starší verze.

4. Aktualizujte přihlašovací adresu URL vaší aplikace na nově vytvořenou zásadu.

5. Po otestování toku uživatele a potvrzení jeho fungování odstraňte starší tok uživatelů pomocí následujících kroků:
   1. V nabídce Přehled klienta Azure AD B2C vyberte **toky uživatelů**.
   2. Najděte tok uživatele, který chcete odstranit.
   3. V posledním sloupci vyberte kontextovou nabídku (**...**) a pak vyberte **Odstranit**.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>Můžu stále vytvářet starší verze verze V2 a V 1.1 uživatelských toků?

Nebudete moct vytvářet nové toky uživatelů založené na starších verzích verze V2 a V 1.1, ale můžete dál číst, aktualizovat a odstraňovat všechny starší toky uživatele verze V2 a V 1.1, které aktuálně používáte.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>Existuje nějaký důvod, jak pokračovat v používání starších uživatelských toků v2 a V 1.1?

Ne tak úplně. Nové **Doporučené** verze Preview obsahují stejné funkce jako verze V2 a v 1.1. Nic se neodebralo a ve skutečnosti teď obsahuje další funkce.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>Pokud nechci přepínat ze starších verzí v2 a 1.1, jak bude mít vliv na moji aplikaci?

Pokud používáte starší verzi verze v2 nebo V 1.1, vaše aplikace nebude ovlivněna touto změnou verzí. Pokud ale chcete získat přístup k novým funkcím nebo změnám zásad, budete muset přejít na nové **Doporučené** verze.

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Bude společnost Microsoft dál podporovat zásady toku uživatele starší verze v2 nebo V 1.1?

Starší verze V2 a V 1.1 toků uživatelů budou nadále plně podporovány.
