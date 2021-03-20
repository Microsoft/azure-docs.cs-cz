---
title: Soubory SharePointu – QnA Maker
description: Přidejte do znalostní báze zabezpečené zdroje dat SharePointu, abyste mohli rozšířit znalostní bázi s dotazy a odpověďmi, které je možné zabezpečit pomocí služby Active Directory.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 0832b54e02cabecb0b1f0e7af600b8adc621a8b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584766"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Přidání zabezpečeného zdroje dat SharePointu do znalostní báze

Přidejte do znalostní báze zabezpečené cloudové zdroje dat SharePointu, abyste mohli rozšířit znalostní bázi s dotazy a odpověďmi, které je možné zabezpečit pomocí služby Active Directory.

Když do znalostní báze přidáte zabezpečený dokument služby SharePoint, jako je třeba správce QnA Maker, musíte požádat o QnA Maker oprávnění služby Active Directory. Jakmile toto oprávnění udělí správce služby Active Directory, aby se QnA Maker pro přístup k SharePointu, nemusí se znovu nadávat. Každý následující dokument přidaný do znalostní báze nebude vyžadovat autorizaci, pokud se nachází ve stejném prostředku služby SharePoint.

Pokud správce znalostní báze QnA Maker Knowledge Base není správce služby Active Directory, bude nutné komunikovat se správcem služby Active Directory a dokončit tento proces.

## <a name="prerequisites"></a>Předpoklady

* Cloudová služba SharePoint – QnA Maker používá Microsoft Graph pro oprávnění. Pokud je váš SharePoint v místním prostředí, nebudete se moct extrahovat ze SharePointu, protože Microsoft Graph nebude moct určit oprávnění.
* Formát adresy URL – QnA Maker podporují jenom adresy URL SharePointu, které jsou vygenerované pro sdílení a mají formát. `https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Přidání podporovaných typů souborů do znalostní báze Knowledge Base

Z webu služby SharePoint do znalostní báze můžete přidat všechny [typy souborů](../index.yml) podporované QnA maker. Je možné, že budete muset udělit [oprávnění](#permissions) , pokud je prostředek souboru zabezpečený.

1. Z knihovny s webem služby SharePoint vyberte nabídku se třemi tečkami v souboru `...` .
1. Zkopírujte adresu URL souboru.

   ![Adresu URL souboru SharePointu získáte tak, že vyberete nabídku se třemi tečkami souboru a pak zkopírujete adresu URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Na portálu QnA Maker na stránce **Nastavení** přidejte adresu URL do znalostní báze.

### <a name="images-with-sharepoint-files"></a>Obrázky se soubory SharePointu

Pokud soubory obsahují obrázky, nejsou extrahovány. Obrázek můžete přidat z portálu QnA Maker po extrakci souboru do párů QnA.

Přidejte bitovou kopii s následující syntaxí Markdownu:

```markdown
![Explanation or description of image](URL of public image)
```

Text v hranatých závorkách, `[]` Popis obrázku. Adresa URL v závorkách `()` je přímý odkaz na obrázek.

Při testování páru QnA na interaktivním panelu testu se zobrazí obrázek místo Markdownu textu v portálu QnA Maker. Tím se ověří, že image se dá veřejně načíst z klienta aplikace.

## <a name="permissions"></a>Oprávnění

K udělení oprávnění dojde, když se do znalostní báze přidá zabezpečený soubor ze SharePointového serveru. V závislosti na tom, jak je SharePoint nastavený, a oprávnění osoby, která tento soubor přidává, může to vyžadovat:

* žádné další kroky – osoba, která soubor přidala, má všechna potřebná oprávnění.
* kroky pro [správce znalostní báze](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) i [Správce služby Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Projděte si níže uvedené kroky.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Správce znalostní báze: přidání zdroje dat SharePointu na portál QnA Maker

Když **správce QnA maker** přidá do znalostní báze zabezpečený dokument služby SharePoint, zahájí správce znalostní báze požadavek na oprávnění, které musí dokončit Správce služby Active Directory.

Požadavek začíná automaticky otevírané okno pro ověření účtu služby Active Directory.

![Ověřit uživatelský účet](../media/add-sharepoint-datasources/authenticate-user-account.png)

Jakmile nástroj QnA Maker Manager vybere účet, bude správce Azure Active Directory dostávat oznámení, že musí aplikaci QnA Maker (nikoli správce QnA Maker) přistupovat k prostředku služby SharePoint. Správce Azure Active Directory bude muset provést tento postup pro každý prostředek SharePointu, ale ne všechny dokumenty v tomto prostředku.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Správce služby Active Directory: Udělte oprávnění ke čtení souboru QnA Maker

Správce služby Active Directory (ne správce QnA Maker) musí pro přístup QnA Maker k prostředku služby SharePoint udělit přístup, a to tak, že vyberete [Tento odkaz](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) k autorizaci, QnA Maker má podniková aplikace SharePoint Portal oprávnění ke čtení souboru.

![Správce Azure Active Directory uděluje oprávnění interaktivně.](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

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
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Udělení přístupu z centra pro správu Azure Active Directory

1. Správce služby Active Directory se přihlásí k Azure Portal a otevře **[podnikové aplikace](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**.

1. Vyhledejte `QnAMakerPortalSharePoint` aplikaci QnA maker vyberte.

    [![Hledat QnAMakerPortalSharePoint v seznamu podnikových aplikací](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. V části **zabezpečení** pokračujte na **oprávnění**. Vyberte **udělit souhlas správce pro organizaci**.

    [![Vybrat ověřeného uživatele pro správce služby Active Directory](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Vyberte účet Sign-On s oprávněním pro udělení oprávnění pro službu Active Directory.




## <a name="add-sharepoint-data-source-with-apis"></a>Přidání zdroje dat SharePointu pomocí rozhraní API

Existuje alternativní řešení, které umožňuje přidat nejnovější obsah služby SharePoint přes rozhraní API pomocí služby Azure Blob Storage, a to níže v následujících krocích: 
1.  Stáhnout soubory SharePoint lokálně. Uživatel, který volá rozhraní API, musí mít přístup k SharePointu. 
1.  Nahrajte je do úložiště objektů BLOB v Azure. Tím se vytvoří zabezpečený sdílený přístup [pomocí tokenu SAS.](../../../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works) 
1. Předejte adresu URL objektu BLOB vygenerovanou tokenem SAS na rozhraní API služby QnA Maker. Pokud chcete, aby otázka mohla vyextrahovat z těchto souborů, je nutné přidat typ souboru přípony jako ' &EXT = PDF ' nebo ' &EXT = doc ' na konci adresy URL před předáním do rozhraní API služby QnA Maker.


<!--
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

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) or [updating a knowledge base](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spolupráce na znalostní bázi](../index.yml)
