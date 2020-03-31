---
title: Přidání rolí aplikace a jejich získání z tokenu | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak přidat role aplikací v aplikaci registrované ve službě Azure Active `roles` Directory, přiřadit k těmto rolím uživatele a skupiny a obdržet je v deklaraci v tokenu.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3a911db36fd03ebcb5e0fc53d4d7f36d68648249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399089"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Postup: Přidání rolí aplikací do aplikace a jejich přijetí v tokenu

Řízení přístupu na základě rolí (RBAC) je populární mechanismus pro vynucení autorizace v aplikacích. Při použití RBAC, správce uděluje oprávnění k rolím, nikoli jednotlivým uživatelům nebo skupinám. Správce pak může přiřadit role různým uživatelům a skupinám, aby řídil, kdo má přístup k jakému obsahu a funkcím.

Pomocí RBAC s rolemi aplikací a deklaracemi rolí můžou vývojáři bezpečně vynutit autorizaci ve svých aplikacích s malým úsilím z jejich strany.

Dalším přístupem je použití skupin Azure AD a deklarací skupin, jak je znázorněno na [webu WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Skupiny Azure AD a role aplikací se v žádném případě vzájemně nevylučují. mohou být použity v tandemu k zajištění ještě jemnějšího odstupňovaného řízení přístupu.

## <a name="declare-roles-for-an-application"></a>Deklarovat role pro aplikaci

Tyto role aplikace jsou definovány na [portálu Azure](https://portal.azure.com) v manifestu registrace aplikace.  Když se uživatel přihlásí do aplikace, Azure `roles` AD vydává deklaraci pro každou roli, která byla uživateli udělena jednotlivě uživateli a z jejich členství ve skupině.  Přiřazení uživatelů a skupin k rolím lze provést prostřednictvím uživatelského rozhraní portálu nebo programově pomocí [aplikace Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Deklarování rolí aplikací pomocí portálu Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Na panelu nástrojů portálu vyberte ikonu **Adresář + Odběr.**
1. V seznamu **Oblíbené položky** nebo **Všechny adresáře** zvolte klienta služby Active Directory, ve kterém chcete aplikaci zaregistrovat.
1. Na webu Azure Portal vyhledejte a vyberte **Službu Azure Active Directory**.
1. V podokně **Azure Active Directory** vyberte Registrace **aplikací,** chcete-li zobrazit seznam všech aplikací.
1. Vyberte aplikaci, ve které chcete definovat role aplikace. Pak vyberte **Manifest**.
1. Upravte manifest aplikace vyhledáním `appRoles` nastavení a přidáním všech rolí aplikace.

     > [!NOTE]
     > Každá definice role aplikace v tomto manifestu musí mít jiný platný `id` identifikátor GUID v kontextu manifestu pro vlastnost.
     >
     > Vlastnost `value` každé definice role aplikace by měla přesně odpovídat řetězcům, které se používají v kódu v aplikaci. Vlastnost `value` nemůže obsahovat mezery. Pokud ano, zobrazí se při uložení manifestu chyba.

1. Uložte seznam.

### <a name="examples"></a>Příklady

Následující příklad `appRoles` ukazuje, že můžete `users`přiřadit k .

> [!NOTE]
>Musí `id` se jednat o jedinečný identifikátor GUID.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>Mezery `displayName` nesmí obsahovat.

Role aplikace můžete definovat `users` `applications`tak, aby cílily , nebo obojí. Pokud jsou `applications`k dispozici , role aplikace se zobrazí jako oprávnění aplikace v okně **Požadovaná oprávnění.** Následující příklad ukazuje roli aplikace `Application`zaměřenou na .

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

Počet definovaných rolí ovlivňuje limity, které má manifest aplikace. Byly podrobně projednány na stránce [manifestlimitů.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits)

### <a name="assign-users-and-groups-to-roles"></a>Přiřazení uživatelů a skupin k rolím

Po přidání rolí aplikace do aplikace můžete k těmto rolím přiřadit uživatele a skupiny.

1. V podokně **Azure Active Directory** vyberte podnikové **aplikace** z levé navigační nabídky Azure **Active Directory.**
1. Výběrem **možnosti Všechny aplikace** zobrazíte seznam všech aplikací.

     Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte různé filtry v horní části seznamu **Všechny aplikace** k omezení seznamu nebo posuňte seznam dolů a vyhledejte aplikaci.

1. Vyberte aplikaci, ve které chcete přiřadit uživatele nebo skupinu zabezpečení k rolím.
1. V levém navigačním menu aplikace vyberte podokno **Uživatelé a skupiny.**
1. V horní části seznamu **Uživatelé a skupiny** vyberte tlačítko **Přidat uživatele** a otevřete podokno **Přidat přiřazení.**
1. V podokně **Přidat přiřazení** vyberte volič **i uživatelé a skupiny.**

     Zobrazí se seznam uživatelů a skupin zabezpečení spolu s textovým polem pro vyhledávání a vyhledání určitého uživatele nebo skupiny. Tato obrazovka umožňuje vybrat více uživatelů a skupin najednou.

1. Po dokončení výběru uživatelů a skupin se stisknutím tlačítka **Vybrat** dole přesuňte na další část.
1. Zvolte vybrat volič **role** z podokna **Přidat přiřazení.** Zobrazí se všechny role deklarované dříve v manifestu aplikace.
1. Zvolte roli a stiskněte tlačítko **Vybrat.**
1. Stisknutím tlačítka **Přiřadit** dole dokončete přiřazení uživatelů a skupin do aplikace.
1. Zkontrolujte, zda se uživatelé a skupiny, které jste přidali, zobrazují v aktualizovaném seznamu **Uživatelů a skupin.**

## <a name="more-information"></a>Další informace

- [Přidání autorizace pomocí rolí aplikací & deklarací identity rolí do webové aplikace ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Použití skupin zabezpečení a rolí aplikací ve vašich aplikacích (Video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, teď s deklaracemi skupiny a rolemi aplikací](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifest aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Tokeny přístupu AAD](access-tokens.md)
- [AAD`id_tokens`](id-tokens.md)
