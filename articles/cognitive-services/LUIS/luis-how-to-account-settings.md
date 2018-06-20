---
title: Spravovat nastavení svého účtu v LUIS | Microsoft Docs
description: Použití LUIS webu ke správě nastavení svého účtu.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 76300b307c8e87a3dd108fc53d3b30a53d3276dd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263920"
---
# <a name="manage-your-luis-account"></a>Správa vašeho účtu LUIS
Dvě důležité informace pro účet LUIS jsou uživatelský účet a pro tvorbu klíč. Vaše přihlašovací údaje je spravován na [account.microsoft.com](https://account.microsoft.com). Klíč pro vytváření obsahu je spravovat z [LUIS][LUIS] webu **nastavení** stránky. 

## <a name="authoring-key"></a>Vytváření klíče

Tento jeden, oblast pro tvorbu klíče na **nastavení** stránky, umožňuje vytvářet aplikace z [LUIS][LUIS] webu a taky [ vytváření rozhraní API](https://aka.ms/luis-authoring-api). V zájmu usnadnění, je povoleno vytváření klíč aby [omezené](luis-boundaries.md) počet koncový bod dotazuje každý měsíc. 

![Stránka nastavení LUIS](./media/luis-how-to-account-settings/account-settings.png)

Vytváření klíč se používá pro všechny aplikace, které vlastníte, a také všechny aplikace, které jsou uvedeny jako spolupracovníka.

## <a name="authoring-key-regions"></a>Vytváření klíčových oblastí
Vytváření klíč je specifické pro [vytváření oblast](luis-reference-regions.md#publishing-regions). Klíč nefunguje v jiné oblasti. 

## <a name="reset-authoring-key"></a>Resetovat klíč pro tvorbu
Pokud vaše vytváření klíče je ohrožena obnovte klíč. Klíč se resetuje na všech aplikacích [LUIS] webu. Pokud vytváříte aplikace prostřednictvím rozhraní API pro vytváření, budete muset změnit hodnotu `Ocp-Apim-Subscription-Key` do nového klíče. 

## <a name="delete-account"></a>Odstranit účet
V tématu [úložiště dat a odebrání](luis-concept-data-storage.md#accounts) informace o jaká data se odstraní při odstranění účtu. 

## <a name="azure-active-directory-tenant-user"></a>Uživatele klienta Azure Active Directory
LUIS používá standardní toku souhlasu Azure Active Directory (Azure AD). 

Správce klienta by měl spolupracovat přímo s uživatele, který potřebuje udělení přístupu k použití LUIS ve službě Azure AD. 

Nejprve uživatel přihlásí do LUIS a vidí v místním dialogovém okně, které vyžadují schválení správce. Uživatel kontaktuje správce tenanta než budete pokračovat. 

Druhý správce klienta do LUIS přihlásí a zobrazí automaticky otevíraný dialog toku souhlasu. Toto je dialogové okno Správce musí udělit oprávnění pro uživatele. Jakmile správce přijímá oprávnění, uživatel je moci pokračovat s LUIS.

Pokud správce klienta nebude Přihlaste se k LUIS, můžete přístup správce [souhlas](https://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=65920ba3-ab61-4a9b-9b10-505e5ce61b58) pro LUIS. 

Pokud správce klienta chce pouze určitým uživatelům používat LUIS, podívejte se na to [identity blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Uživatelské účty s více e-mailů pro spolupracovníky
Pokud přidáte spolupracovníci LEOŠ aplikaci, musíte zadat přesné e-mailovou adresu, musí používat LEOŠ jako spolupracovníka spolupracovníka. Přestože Azure Active Directory (Azure AD) umožňuje jenom jednoho konkrétního uživatele tak, aby měl více než jeden e-mailový účet používá zcela zaměnitelným významem, LEOŠ vyžaduje, aby uživatel k přihlášení pomocí e-mailová adresa zadaná v seznamu spolupracovníka. 


## <a name="next-steps"></a>Další postup

Další informace o vaší [vytváření klíč](luis-concept-keys.md#authoring-key). 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
