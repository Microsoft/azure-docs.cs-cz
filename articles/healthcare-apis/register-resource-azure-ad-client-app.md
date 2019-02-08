---
title: Registrace aplikace prostředků v Azure Active Directory – rozhraní API služby Azure pro FHIR
description: Tento článek vysvětluje postup při registraci aplikace prostředků v Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e8305c5a69fa3fda29f4f1292b7faa59f8ec3608
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870142"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registrace aplikace prostředků v Azure Active Directory

V tomto článku se naučíte zaregistrovat prostředek (nebo rozhraní API) v Azure Active Directory. Aplikace prostředků je reprezentace server FHIR samotné rozhraní API Azure Active Directory a klientské aplikace můžou požádat o přístup k prostředku při ověřování. Aplikace prostředků se také označuje jako *cílovou skupinu* v hantýrce OAuth.

## <a name="app-registrations-in-azure-portal"></a>Registrace aplikací na webu Azure portal

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na levém navigačním panelu na **Azure Active Directory**.

2. V **Azure Active Directory** okně klikněte na **registrace aplikací (Preview)**:

    ![Azure portal. Registrace nové aplikace.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klikněte na tlačítko **registrace nové**.

## <a name="add-a-new-application-registration"></a>Přidat registrace nové aplikace

Zadejte podrobnosti pro novou aplikaci. Nejsou žádné zvláštní požadavky zobrazovaný název, ale nastavení identifikátoru URI serveru FHIR umožňuje snadno najít:

![Registrace nové aplikace](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

## <a name="set-identifier-uri-and-define-scopes"></a>Nastavte identifikátor URI a definice oborů

Aplikace prostředků má identifikátor URI (identifikátor URI ID aplikace), které klienty můžete použít při žádosti o přístup k prostředku. Tato hodnota se vyplní `aud` přístupového tokenu deklarací identity. Doporučuje se, že jste nastavili pomocí tohoto identifikátoru URI se identifikátor URI serveru FHIR. Smart FHIR aplikací, se předpokládá, *cílovou skupinu* je identifikátor URI serveru FHIR.

1. Klikněte na tlačítko **vystavit rozhraní API**

2. Klikněte na tlačítko **nastavit** vedle *identifikátor URI ID aplikace*.

3. Zadejte identifikátor URI a klikněte na tlačítko **Uložit**. Dobrý identifikátor URI by být identifikátor URI serveru FHIR.

4. Klikněte na tlačítko **přidat obor** a přidejte všechny rozsahy, které chcete definovat pro vás rozhraní API. Azure AD momentálně neumožňuje lomítka (`/`) v oboru názvů. Doporučujeme používat `$` místo. Obor, jako jsou `patient/*.read` by `patient$*.read`.

    ![Cílové skupiny a oboru](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

## <a name="define-application-roles"></a>Definování rolí aplikace

Rozhraní API služby Azure pro FHIR a OSS FHIR Server pro používání Azure [rolí aplikace Azure Active Directory](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) pro řízení přístupu na základě rolí. Chcete-li definovat, jaké role by měla být dostupná pro vaše rozhraní API serveru FHIR, otevřete aplikace prostředků [manifest](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/):

1. Klikněte na tlačítko **Manifest**:

    ![Aplikační role](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. V `appRoles` vlastnost, přidání rolí, které chcete uživatele nebo aplikace mít:

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

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak zaregistrovat aplikaci prostředků v Azure Active Directory. V dalším kroku nasaďte FHIR API v Azure.
 
>[!div class="nextstepaction"]
>[Nasazení Open FHIR zdrojového serveru](fhir-oss-powershell-quickstart.md)