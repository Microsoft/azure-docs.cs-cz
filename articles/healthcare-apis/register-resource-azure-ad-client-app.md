---
title: Registrace aplikace prostředků v Azure AD – Azure API pro FHIR
description: Zaregistrujte aplikaci prostředků (nebo rozhraní API) v Azure Active Directory, aby klientské aplikace mohly při ověřování vyžadovat přístup k prostředku.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: e92fab392dc73d8de0b7b2547e38b3f345562930
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975854"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registrace aplikace prostředků v Azure Active Directory

V tomto článku se dozvíte, jak v Azure Active Directory zaregistrovat aplikaci prostředků (nebo rozhraní API). Aplikace prostředků je Azure Active Directory reprezentace samotného rozhraní API serveru FHIR a klientské aplikace můžou požádat o přístup k prostředku při ověřování. Aplikace prostředků je také známá jako *cílová skupina* v agilním OAuth.

## <a name="azure-api-for-fhir"></a>Azure API for FHIR

Pokud používáte rozhraní API Azure pro FHIR, při nasazení služby se automaticky vytvoří aplikace prostředků. Pokud používáte rozhraní API Azure pro FHIR ve stejném tenantovi Azure Active Directory jako při nasazování vaší aplikace, můžete tento návod přeskočit a místo toho nasadit rozhraní API Azure pro FHIR, abyste mohli začít.

Pokud používáte jiného klienta Azure Active Directory (není přidruženo k vašemu předplatnému), můžete do svého tenanta pomocí PowerShellu importovat Azure API pro aplikaci prostředků FHIR:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

nebo můžete použít rozhraní příkazového řádku Azure:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Server FHIR pro Azure

Pokud používáte Open Source Server FHIR pro Azure, použijte následující postup k registraci aplikace prostředků.

### <a name="app-registrations-in-azure-portal"></a>Registrace aplikací v Azure Portal

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na levém navigačním panelu na **Azure Active Directory**.

2. V okně **Azure Active Directory** klikněte na **Registrace aplikací**:

    ![Azure Portal. Registrace nové aplikace](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klikněte na **novou registraci**.

### <a name="add-a-new-application-registration"></a>Přidat novou registraci aplikace

Vyplňte podrobnosti nové aplikace. Pro zobrazované jméno neexistují žádné zvláštní požadavky, ale jeho nastavení na identifikátor URI serveru FHIR usnadňuje hledání:

![Registrace nové aplikace](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>Nastavte identifikátor URI identifikátoru a definujte obory.

Aplikace prostředků má identifikátor URI identifikátoru (identifikátor URI ID aplikace), který můžou klienti použít při žádosti o přístup k prostředku. Tato hodnota naplní `aud` deklaraci přístupového tokenu. Doporučuje se, abyste tento identifikátor URI nastavili jako identifikátor URI vašeho serveru FHIR. Pro inteligentní aplikace FHIR se předpokládá, že *cílová skupina* je identifikátor URI serveru FHIR.

1. Klikněte na **zveřejnit rozhraní API** .

2. Klikněte na **nastavit** vedle *ID identifikátoru URI aplikace*.

3. Zadejte identifikátor URI identifikátoru a klikněte na **Uložit**. Identifikátor URI s dobrý identifikátorem by byl identifikátor URI vašeho serveru FHIR.

4. Klikněte na **Přidat obor** a přidejte všechny obory, které byste chtěli pro vaše rozhraní API definovat. Aby bylo možné v budoucnu udělit oprávnění k vaší aplikaci prostředků, je nutné přidat alespoň jeden obor. Pokud nemáte žádné konkrétní obory, které chcete přidat, můžete přidat user_impersonation jako obor.

![Cílová skupina a obor](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>Definování aplikačních rolí

Rozhraní Azure API pro FHIR a server OSS FHIR pro Azure používají pro řízení přístupu na základě role [Azure Active Directory aplikační role](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) . Pro definování rolí, které by měly být k dispozici pro rozhraní API serveru FHIR, otevřete [manifest](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/)aplikace prostředků:

1. Klikněte na **manifest**:

    ![Aplikační role](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. Do `appRoles` Vlastnosti přidejte role, které chcete, aby měli uživatelé nebo aplikace:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak zaregistrovat aplikaci prostředků v Azure Active Directory. Pak zaregistrujte vaši důvěrnou klientskou aplikaci.
 
>[!div class="nextstepaction"]
>[Registrace důvěrné klientské aplikace](register-confidential-azure-ad-client-app.md)