---
title: Přidat role aplikace a získat je od tokenu | Azure
titleSuffix: Microsoft identity platform
description: Naučte se přidávat role aplikací v aplikaci registrované v Azure Active Directory, přiřazovat k těmto rolím uživatele a skupiny a přijímat je v `roles` deklaraci identity v tokenu.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: be5cb1c1e6ff428b3c4d4305c915e07d3880839c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258383"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Postupy: Přidání rolí aplikace do aplikace a jejich přijetí v tokenu

Řízení přístupu na základě role (RBAC) je oblíbeným mechanismem pro vynucování autorizace v aplikacích. Při použití RBAC správce udělí oprávnění k rolím, nikoli jednotlivým uživatelům nebo skupinám. Správce pak může přiřadit role různým uživatelům a skupinám a určovat, kdo má přístup k jakému obsahu a funkcím.

Pomocí RBAC s aplikačními rolemi a deklaracemi rolí můžou vývojáři bezpečně vymáhat autorizaci ve svých aplikacích s malým úsilím na jejich straně.

Dalším přístupem je použití skupin Azure AD a deklarací skupin, jak je znázorněno v [WebApp-GroupClaims-dotnet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Role skupin a aplikací Azure AD nejsou nijak vzájemně exkluzivní. je možné je použít společně pro zajištění ještě jemnějšího řízení přístupu.

## <a name="declare-roles-for-an-application"></a>Deklarace rolí pro aplikaci

Tyto aplikační role jsou definovány v [Azure Portal](https://portal.azure.com) v manifestu registrace aplikace.  Když se uživatel přihlásí do aplikace, Azure AD vygeneruje `roles` deklaraci identity pro každou roli, které uživatel získal individuálně pro uživatele, a z jejich členství ve skupinách.  Přiřazení uživatelů a skupin k rolím je možné provést prostřednictvím uživatelského rozhraní portálu nebo programově pomocí [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Deklarace rolí aplikace pomocí Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů portálu vyberte ikonu **adresář + předplatné** .
1. V seznamu **Oblíbené** nebo **všechny adresáře** vyberte tenanta služby Active Directory, do kterého chcete aplikaci zaregistrovat.
1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.
1. V podokně  **Azure Active Directory** vyberte **Registrace aplikací** a zobrazte seznam všech aplikací.
1. Vyberte aplikaci, ve které chcete role aplikace definovat. Pak vyberte **manifest**.
1. Upravte manifest aplikace tak, že vyhledáte `appRoles` nastavení a přidáte všechny vaše aplikační role.

     > [!NOTE]
     > Každá definice role aplikace v tomto manifestu musí mít v kontextu manifestu pro danou vlastnost jiný platný identifikátor GUID `id` .
     >
     > `value`Vlastnost každé definice role aplikace by měla přesně odpovídat řetězcům, které jsou používány v kódu v aplikaci. `value`Vlastnost nemůže obsahovat mezery. Pokud k tomu dojde, při ukládání manifestu dojde k chybě.

1. Uložte manifest.

### <a name="examples"></a>Příklady

Následující příklad ukazuje `appRoles` , který lze přiřadit k `users` .

> [!NOTE]
>`id`Musí se jednat o jedinečný identifikátor GUID.

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
>`displayName`Může obsahovat mezery.

Můžete definovat aplikační role pro cílení `users` , `applications` nebo obojí. Pokud je k dispozici pro `applications` , role aplikací se zobrazí jako oprávnění aplikace v části **spravovat** oddíl > **oprávnění rozhraní api > přidat oprávnění > moje rozhraní API > zvolit oprávnění rozhraní API > aplikace**. Následující příklad ukazuje aplikační roli, která je zacílená směrem k `Application` .

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

Počet definovaných rolí má vliv na omezení, která má manifest aplikace. Byly podrobně popsány na stránce [omezení pro manifest](./reference-app-manifest.md#manifest-limits) .

### <a name="assign-users-and-groups-to-roles"></a>Přiřazení uživatelů a skupin k rolím

Po přidání rolí aplikace do aplikace můžete těmto rolím přiřadit uživatele a skupiny.

1. V podokně **Azure Active Directory** v navigační nabídce **Azure Active Directory** vlevo vyberte **podnikové aplikace** .
1. Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

     Pokud nevidíte tu aplikaci, kterou chcete zobrazit, pomocí různých filtrů v horní části seznamu **všechny aplikace seznam všech aplikací** omezte nebo posuňte seznam a vyhledejte svoji aplikaci.

1. Vyberte aplikaci, ve které chcete rolím přiřadit uživatele nebo skupinu zabezpečení.
1. V navigační nabídce levé části aplikace vyberte podokno **Uživatelé a skupiny** .
1. V horní části seznamu **uživatelů a skupin** vyberte tlačítko **Přidat uživatele** a otevřete podokno **Přidat přiřazení** .
1. V podokně **Přidat přiřazení** vyberte selektor **Uživatelé a skupiny** .

     Zobrazí se seznam uživatelů a skupin zabezpečení spolu s textovým polem, kde můžete vyhledat určitého uživatele nebo skupinu. Tato obrazovka umožňuje vybrat více uživatelů a skupin v jednom přechodu.

1. Až budete hotovi s výběrem uživatelů a skupin, přejděte kliknutím na tlačítko **Vybrat** v dolní části na další část.
1. V podokně **Přidat přiřazení** zvolte Výběr **role vybrat** . Zobrazí se všechny role deklarované dříve v manifestu aplikace.
1. Zvolte roli a stiskněte tlačítko **Vybrat** .
1. Stisknutím tlačítka **přiřadit** v dolní části dokončíte přiřazení uživatelů a skupin do aplikace.
1. Ověřte, že se uživatelé a skupiny, které jste přidali, zobrazují v seznamu aktualizovaných **uživatelů a skupin** .

### <a name="receive-roles-in-tokens"></a>Přijímání rolí v tokenech

Když se uživatelé, kteří jsou přiřazeni k různým rolím aplikace, přihlásí k aplikaci, jejich tokeny budou mít přiřazené role v `roles` deklaraci identity.

## <a name="next-steps"></a>Další kroky

- [Přidejte autorizaci pomocí rolí aplikace & role deklarace identity do webové aplikace ASP.NET Core.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Implementace autorizace ve svých aplikacích s využitím platformy Microsoft Identity Platform (video)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory nyní s deklaracemi skupin a aplikačními rolemi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifest aplikace Azure Active Directory](./reference-app-manifest.md)
- [Přístupové tokeny Azure AD](access-tokens.md)
- [Služba Azure AD `id_tokens`](id-tokens.md)
