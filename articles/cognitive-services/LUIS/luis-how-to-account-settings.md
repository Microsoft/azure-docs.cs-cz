---
title: Spravovat nastavení svého účtu v LUIS | Microsoft Docs
description: Použití LUIS webu ke správě nastavení svého účtu.
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/08/2018
ms.author: diberry
ms.openlocfilehash: 963a7f8c196702ea899ddfe31e6187a15eb5f683
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223204"
---
# <a name="manage-account-and-authoring-key"></a>Správa účtu a klíč pro tvorbu
Dvě důležité informace pro účet LUIS jsou uživatelský účet a pro tvorbu klíč. Přihlašovací údaje se spravuje na [account.microsoft.com](https://account.microsoft.com). Spravuje se z vašeho klíče pro vytváření obsahu [LUIS](luis-reference-regions.md) webu **nastavení** stránky. 

## <a name="authoring-key"></a>Klíč pro tvorbu

Tento jeden, oblast pro tvorbu klíče na **nastavení** stránce umožňuje vytvářet všechny aplikace [LUIS](luis-reference-regions.md) webu i na [rozhraní API pro vytváření](https://aka.ms/luis-authoring-api). Pro zjednodušení vytváření klíč má povoleno provést [omezené](luis-boundaries.md) počet koncových bodů dotazuje každý měsíc. 

![Stránka nastavení LUIS](./media/luis-how-to-account-settings/account-settings.png)

Vytváření klíč se používá pro všechny aplikace, které vlastníte, jakož i všechny aplikace, které jsou uvedené jako spolupracovníka.

## <a name="authoring-key-regions"></a>Vytváření klíčových oblastech
Klíč pro vytváření obsahu je specifická pro [pro vytváření oblasti](luis-reference-regions.md#publishing-regions). Klíč nebude fungovat v různých oblastech. 

## <a name="reset-authoring-key"></a>Obnovit klíč pro tvorbu
Pokud pro tvorbu klíče je ohrožena obnovte klíč. Klíč se resetuje na všech aplikacích [LUIS](luis-reference-regions.md) webu. Pokud vytváříte aplikace pomocí rozhraní API pro vytváření, budete muset změnit hodnotu `Ocp-Apim-Subscription-Key` do nového klíče. 

## <a name="delete-account"></a>Odstranit účet
Zobrazit [úložiště dat a odebrání](luis-concept-data-storage.md#accounts) informace o tom, jaká data se odstraní při odstranění účtu. 

## <a name="azure-active-directory-tenant-user"></a>Uživatel tenanta Azure Active Directory
LUIS používá standardní toku souhlasu Azure Active Directory (Azure AD). 

Správce klienta by měl spolupracovat přímo s uživatele, který potřebuje udělení přístupu k použití LUIS ve službě Azure AD. 

Nejprve uživatel přihlásí do LUIS a vidí v místním dialogovém okně, které vyžadují schválení správce. Uživatel kontaktuje správce tenanta, než budete pokračovat. 

Druhý správce klienta do LUIS přihlásí a zobrazí automaticky otevíraný dialog toku souhlasu. Toto je dialogové okno Správce musí udělit oprávnění pro uživatele. Jakmile správce přijímá oprávnění, uživatel je moci pokračovat s LUIS.

Pokud správce klienta nebude Přihlaste se k LUIS, můžete přístup správce [souhlas](https://account.activedirectory.windowsazure.com/r#/applications) pro LUIS. 

![Oprávnění Azure active directory web aplikace](./media/luis-how-to-account-settings/tenant-permissions.png)

Pokud správce klienta chce pouze určitým uživatelům používat LUIS, podívejte se na to [identity blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Uživatelské účty s více e-mailů pro spolupracovníky.
Pokud chcete přidat spolupracovníky na aplikaci LUIS, zadáváte přesné e-mailovou adresu, musí používat službu LUIS jako spolupracovník spolupracovníka. Zatímco Azure Active Directory (Azure AD) umožňuje jednoho uživatele má více než jednu e-mailový účet používat Zaměnitelně, LUIS vyžaduje, aby uživatel přihlásit se prostřednictvím e-mailovou adresu, uvedený v seznamu spolupracovníka. 


## <a name="next-steps"></a>Další postup

Další informace o vaší [vytváření klíč](luis-concept-keys.md#authoring-key). 

