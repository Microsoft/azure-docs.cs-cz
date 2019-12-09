---
title: Přidat role aplikace a získat je od tokenu | Azure
titleSuffix: Microsoft identity platform
description: Naučte se přidávat role aplikací v aplikaci registrované v Azure Active Directory, přiřazovat k těmto rolím uživatele a skupiny a přijímat je v deklaraci identity `roles` v tokenu.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fe116b5ae26522c9bce0322123836a6e08d3cfd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917910"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Postupy: Přidání rolí aplikace do aplikace a jejich přijetí v tokenu

Řízení přístupu na základě role (RBAC) je oblíbeným mechanismem pro vynucování autorizace v aplikacích. Při použití RBAC správce udělí oprávnění k rolím, nikoli jednotlivým uživatelům nebo skupinám. Správce pak může přiřadit role různým uživatelům a skupinám a určovat, kdo má přístup k jakému obsahu a funkcím.

Pomocí RBAC s aplikačními rolemi a deklaracemi rolí můžou vývojáři bezpečně vymáhat autorizaci ve svých aplikacích s malým úsilím na jejich straně.

Dalším přístupem je použití skupin Azure AD a deklarací skupin, jak je znázorněno v [WebApp-GroupClaims-dotnet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Role skupin a aplikací Azure AD nejsou nijak vzájemně exkluzivní. je možné je použít společně pro zajištění ještě jemnějšího řízení přístupu.

## <a name="declare-roles-for-an-application"></a>Deklarace rolí pro aplikaci

Tyto aplikační role jsou definovány v [Azure Portal](https://portal.azure.com) v manifestu registrace aplikace.  Když se uživatel do aplikace přihlásí, Azure AD vygeneruje deklaraci `roles` pro každou roli, které uživatel získal individuálně pro uživatele, a z jejich členství ve skupinách.  Přiřazení uživatelů a skupin k rolím je možné provést prostřednictvím uživatelského rozhraní portálu nebo programově pomocí [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Deklarace rolí aplikace pomocí Azure Portal

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. V horním panelu vyberte svůj účet a pak **Přepněte adresář**.
1. Po otevření podokna **adresář a odběr** vyberte v seznamu **Oblíbené** nebo **všechny adresáře** klienta služby Active Directory, kde chcete aplikaci zaregistrovat.
1. V levém navigačním panelu vyberte **všechny služby** a zvolte **Azure Active Directory**.
1. V podokně **Azure Active Directory** vyberte **Registrace aplikací** a zobrazte seznam všech aplikací.
1. Vyberte aplikaci, ve které chcete role aplikace definovat. Pak vyberte **manifest**.
1. Upravte manifest aplikace tak, že vyhledáte nastavení `appRoles` a přidáte všechny své aplikační role.

     > [!NOTE]
     > Každá definice role aplikace v tomto manifestu musí mít pro vlastnost `id` jiný platný identifikátor GUID. 
     > 
     > Vlastnost `value` každé definice role aplikace by měla přesně odpovídat řetězcům, které jsou používány v kódu v aplikaci. Vlastnost `value` nesmí obsahovat mezery. Pokud k tomu dojde, při ukládání manifestu dojde k chybě.
     
1. Uložte manifest.

### <a name="examples"></a>Příklady

Následující příklad ukazuje `appRoles`, které můžete přiřadit k `users`.

> [!NOTE]
>`id` musí být jedinečný identifikátor GUID.

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
>`displayName` nesmí obsahovat mezery.

Můžete definovat aplikační role pro cílení na `users`, `applications`nebo obojí. Pokud je `applications`k dispozici, role aplikace se zobrazí jako oprávnění aplikace v okně **požadovaná oprávnění** . Následující příklad ukazuje aplikační roli, která je zacílená směrem k `Application`.

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

Počet definovaných rolí má vliv na omezení, která má manifest aplikace. Byly podrobně popsány na stránce [omezení pro manifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) .

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

## <a name="more-information"></a>Další informace

- [Autorizace ve webové aplikaci s využitím aplikačních rolí Azure AD &amp; deklaracích rolí (ukázka)](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims)
- [Používání skupin zabezpečení a rolí aplikací v aplikacích (video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory nyní s deklaracemi skupin a aplikačními rolemi](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles)
- [Azure Active Directory manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Přístupové tokeny AAD](access-tokens.md)
- [`id_tokens` AAD](id-tokens.md)
