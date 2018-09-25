---
title: Přidání role aplikace v aplikaci Azure Active Directory zaregistrované a přijímat je v tokenu
description: Zjistěte, jak přidat aplikace role v aplikaci registrovanou v Azure Active Directory, přiřazení uživatelů a skupin pro tyto role a přijímat je v `roles` deklarací identity v tokenu.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 6321823eed00cffc6565471ec1ba3b4d846cd027
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948702"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Postupy: Přidání rolí aplikace ve vaší aplikaci a přijímat je v tokenu

Řízení přístupu na základě role (RBAC) je mechanismus oblíbených vynutit autorizaci v aplikacích. Při použití RBAC, správce udělí oprávnění k rolím a nikoli na jednotlivé uživatele nebo skupiny. Správce pak můžete přiřadit role pro různé uživatele a skupiny, které se řídí, kdo má přístup k jaké obsah a funkce.

Pomocí RBAC se aplikační role a Role deklarací identity, můžou vývojáři bezpečně vynutit autorizaci ve svých aplikacích s malou úsilí na jejich část.

Další možností je používat skupiny Azure AD a deklarace skupiny, jak je znázorněno v [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Azure AD skupin a rolí aplikace se vzájemně vylučují; v žádném smyslu jejich lze současně poskytují i na jemnější kontrolu jemněji přístup.

## <a name="declare-roles-for-an-application"></a>Deklarace role pro aplikaci

Pracovníci v těchto rolích aplikace jsou definovány v [webu Azure portal](https://portal.azure.com) v registraci manifestu aplikace.  Když se uživatel přihlásí do aplikace, služby Azure AD vydá `roles` deklarace identity pro každou roli, která se uživateli udělila jednotlivě pro uživatele a jejich členství ve skupině.  Přiřazení uživatelů a skupin k rolím může být Hotovo prostřednictvím uživatelského rozhraní na portálu nebo programově pomocí [Microsoft Graphu](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Deklarace role aplikace pomocí webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na horním panelu, vyberte svůj účet a potom **přepnout adresář**.
1. Jednou **adresář a předplatné** podokně se otevře, zvolte tenanta Active Directory, ve kterém chcete registrace vaší aplikace z **Oblíbené** nebo **všechny adresáře**seznamu.
1. Vyberte **všechny služby** v levém navigačním podokně a zvolte **Azure Active Directory**.
1. V **Azure Active Directory** vyberte **registrace aplikací** zobrazíte seznam všech aplikací.

     Pokud nevidíte aplikaci, kterou má, zobrazí se zde, pomocí různých filtrů v horní části **registrace aplikací** seznamu k omezení seznamu nebo přejděte dolů v seznamu vyhledejte aplikace.

1. Vyberte aplikace, které chcete definovat role aplikace v.
1. V okně pro vaši aplikaci, vyberte **Manifest**.
1. Upravit manifest aplikace vyhledáním `appRoles` nastavení a přidání všechny aplikační role.

     > [!NOTE]
     > Každá definice role v tomto manifestu musí mít jiný platný **Guid** pro vlastnost "Id". `"value"` Vlastnosti jednotlivých rolí by měl přesně odpovídat řetězců se používají v kódu v aplikaci.
     
1. Uložte manifest.

### <a name="examples"></a>Příklady

Následující příklad ukazuje `appRoles` , které můžete přiřadit `users`.

> [!NOTE]
>  `id` Musí být jedinečný identifikátor GUID.

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

Můžete definovat role aplikace k cíli `users`, `applications`, nebo obojí. Pokud je k dispozici na `applications`, role aplikace se zobrazí jako oprávnění aplikace v **požadovaná oprávnění** okno. Následující příklad ukazuje roli aplikace zaměřené na `Application`.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "Consumer Apps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

### <a name="assign-users-and-groups-to-roles"></a>Přiřazení uživatelů a skupin k rolím

Po přidání role aplikace ve vaší aplikaci, můžete přiřadit těmto rolím uživatelů a skupin.

1. V **Azure Active Directory** vyberte **podnikové aplikace** z **Azure Active Directory** navigační nabídce vlevo.
1. Vyberte **všechny aplikace** zobrazíte seznam všech aplikací.

     Pokud nevidíte aplikaci, kterou má, zobrazí se zde, pomocí různých filtrů v horní části **všechny aplikace** seznamu k omezení seznamu nebo přejděte dolů v seznamu vyhledejte aplikace.

1. Vyberte aplikaci, ve kterém chcete přiřadit k rolím uživatelů nebo skupin zabezpečení.
1. Vyberte **uživatelů a skupin** podokno v levé navigační nabídce aplikace.
1. V horní části **uživatelů a skupin** seznamu, vyberte **přidat uživatele** tlačítko Otevřít **přidat přiřazení** podokně.
1. Vyberte **uživatelů a skupin** pro výběr **přidat přiřazení** podokně.

     Seznam uživatelů a skupin zabezpečení se zobrazí spolu s textového pole hledání a vyhledejte uživatele nebo skupinu. Tato obrazovka umožňuje vybrat najednou více uživatelů a skupin.

1. Po dokončení výběru uživatelů a skupin, stiskněte **vyberte** tlačítko na konci článku přejít k další části.
1. Zvolte **vybrat roli** pro výběr **přidat přiřazení** podokně. Všechny role, které jsou deklarovány dříve v manifestu aplikace se zobrazí.
1. Vyberte roli a stiskněte klávesu **vyberte** tlačítko.
1. Stisknutím klávesy **přiřadit** tlačítko v dolní části na dokončení přiřazení uživatelů a skupin k aplikaci.
1. Potvrďte, že uživatelé a skupiny, které jste přidali se zobrazují v aktualizovaném **uživatelů a skupin** seznamu.

## <a name="more-information"></a>Další informace

- [Autorizace ve webové aplikaci pomocí Azure AD aplikační role &amp; deklarace rolí (ukázka)](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapp-roleclaims/)
- [Pomocí skupin zabezpečení a aplikační role ve vašich aplikacích (Video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, nyní se deklarace skupiny a aplikační role](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles)
- [Manifest aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [AAD přístupové tokeny](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)
