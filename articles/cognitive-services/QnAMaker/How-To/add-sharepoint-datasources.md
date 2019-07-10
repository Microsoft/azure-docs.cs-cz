---
title: Soubory Sharepointu – QnA Maker
titleSuffix: Azure Cognitive Services
description: Přidejte zabezpečenou zdroje dat služby SharePoint do znalostní báze rozšiřuje ve znalostní bázi s dotazy a odpovědi, které mohou být zabezpečené pomocí služby Active Directory.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: ecb9777643296685d0dcc7cd5a177f2fe00d2580
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704637"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Přidejte zabezpečenou zdroje dat SharePoint do znalostní báze

Přidejte zabezpečenou zdroje dat služby SharePoint do znalostní báze rozšiřuje ve znalostní bázi s dotazy a odpovědi, které mohou být zabezpečené pomocí služby Active Directory. 

Když přidáte zabezpečené dokumentů služby SharePoint do znalostní báze, jako správce nástroje QnA Maker, musíte požádat o oprávnění služby Active Directory pro nástroj QnA Maker. Jakmile toto oprávnění je uveden ze Správce služby Active Directory pro nástroj QnA Maker pro přístup k Sharepointu, není třeba znovu zadat. Každé následné dokumentu přidání znalostní báze nebudete potřebovat autorizace, pokud má stejný prostředek služby SharePoint. 

Pokud správce nástroje QnA Maker znalostní báze knowledge base není správce služby Active Directory, musíte ke komunikaci se správcem služby Active Directory k dokončení tohoto procesu.

## <a name="add-supported-file-types-to-knowledge-base"></a>Přidat podporované typy souborů do znalostní báze

Můžete přidat všechny podporované nástroje QnA Maker [typy souborů](../Concepts/data-sources-supported.md) z webu služby SharePoint do znalostní báze. Možná budete muset udělit [oprávnění](#permissions) Pokud soubor prostředků je zabezpečený.

1. V knihovně s webem služby SharePoint, vyberte tlačítko se třemi tečkami nabídku souboru, `...`.
1. Zkopírujte adresu URL k souboru.

   ![Získáte adresu URL souboru SharePoint tak, že vyberete tři tečky nabídky Soubor pak zkopírujete adresu URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Na portálu pro nástroj QnA Maker na **nastavení** stránce [přidejte adresu URL](edit-knowledge-base.md#add-datasource) znalostní báze. 

### <a name="images-with-sharepoint-files"></a>Image se soubory služby SharePoint

Pokud soubory obrázků, ty nejsou extrahována. Po na páry QnA je extrahován soubor, můžete přidat bitovou kopii, z portálu QnA Maker.

Přidání bitové kopie s následující syntaxí markdownu: 

```markdown
![Explanation or description of image](URL of public image)
```

Text v hranatých závorkách a `[]`, vysvětluje bitovou kopii. Adresa URL v závorkách, `()`, je přímý odkaz na obrázek. 

Při testování pár QnA v panelu interaktivní testu na portálu pro nástroj QnA Maker na obrázku zobrazí se místo text v markdownu. Tato operace ověří image je možné veřejně načíst z klientské aplikace.

## <a name="permissions"></a>Oprávnění

Udělení oprávnění se stane, když zabezpečené soubor ze serveru SharePoint je přidán do znalostní báze. V závislosti na tom, jak nastavit SharePoint nahoru a oprávnění uživatele přidání souboru, to může vyžadovat:

* žádné další kroky – osoba přidávání souboru má všechna potřebná oprávnění.
* kroky i [znalostní báze správce](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) a [Správce služby Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Viz následující postup. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Správce znalostní báze Knowledge base: přidání zdroje dat služby SharePoint portálu QnA Maker

Když **správce nástroje QnA Maker** přidá zabezpečené dokumentů služby SharePoint do znalostní báze, inicializuje Správce znalostní báze žádost o oprávnění, která správce služby Active Directory je potřeba dokončit.

Požadavku začíná automaticky otevírané okno pro ověření účtu služby Active Directory. 

![Ověřit uživatelský účet](../media/add-sharepoint-datasources/authenticate-user-account.png)

Jakmile správce nástroje QnA Maker vybere tento účet, správce Azure Active Directory obdrží oznámení o tom, že musí povolit nástroj QnA Maker aplikace (ne správce nástroje QnA Maker) přístup k prostředku služby SharePoint. Správce Azure Active Directory, bude nutné provést pro každý prostředek na Sharepointu, ale ne každý dokument v prostředku. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Správce služby Active directory: udělení oprávnění ke čtení souboru pro nástroj QnA Maker

Správce služby Active Directory (ne správce nástroje QnA Maker) je potřeba udělit přístup ke QnA Maker pro přístup k prostředku služby SharePoint tak, že vyberete [tento odkaz](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) autorizovat aplikaci nástroje QnA Maker Portal SharePoint enterprise čtení souboru oprávnění. 

![Správce Azure Active Directory interaktivně uděluje oprávnění](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Udělit přístup z centra pro správu Azure Active Directory

1. Správce služby Active Directory přihlásí k webu Azure portal a otevře  **[podnikové aplikace](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)** . 

1. Vyhledejte `QnAMakerPortalSharePoint` vyberte aplikaci nástroje QnA Maker. 

    [![Vyhledejte QnAMakerPortalSharePoint v seznamu podnikových aplikací](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. V části **zabezpečení**, přejděte na stránku **oprávnění**. Vyberte **udělit souhlas správce služby pro organizaci**. 

    [![Vyberte ověřeného uživatele pro správce Active Directory](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Vyberte účet přihlašování s oprávněním k udělování oprávnění pro službu Active Directory. 


  
<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker. 

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Spolupráce na znalostní báze](collaborate-knowledge-base.md)
