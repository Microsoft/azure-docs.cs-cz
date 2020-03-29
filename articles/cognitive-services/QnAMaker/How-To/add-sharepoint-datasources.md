---
title: SharePoint ové soubory – QnA Maker
description: Přidejte zabezpečené zdroje dat služby SharePoint do znalostní báze, která obohatí znalostní bázi o otázky a odpovědi, které mohou být zabezpečeny službou Active Directory.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294885"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Přidání zabezpečeného zdroje dat služby SharePoint do znalostní báze

Přidejte zabezpečené cloudové zdroje dat služby SharePoint do znalostní báze, která obohatí znalostní bázi o otázky a odpovědi, které mohou být zabezpečeny službou Active Directory.

Pokud přidáte zabezpečený dokument služby SharePoint do znalostní báze, musíte jako správce programu QnA Maker požádat o oprávnění služby Active Directory pro program QnA Maker. Jakmile je toto oprávnění uděleno ze správce služby Active Directory službě QnA Maker pro přístup ke službě SharePoint, nemusí být uděleno znovu. Každý další doplnění dokumentu do znalostní báze nebude vyžadovat autorizaci, pokud je ve stejném zdroji služby SharePoint.

Pokud správce znalostní báze QnA Maker není správcem služby Active Directory, budete muset ke dokončení tohoto procesu komunikovat se správcem služby Active Directory.

## <a name="prerequisites"></a>Požadavky

* Cloudový SharePoint – QnA Maker používá Microsoft Graph pro oprávnění. Pokud je sharepoint místní, nebudete moct ze SharePointu extrahovat, protože Microsoft Graph nebude moct určit oprávnění.
* Formát URL – QnA Maker podporuje pouze adresy URL služby SharePoint, které jsou generovány pro sdílení a jsou ve formátu`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Přidání podporovaných typů souborů do znalostní báze Knowledge Base

Do znalostní báze můžete přidat všechny [typy souborů](../Concepts/content-types.md) podporovaných programem QnA Maker z webu služby SharePoint. Pokud je prostředek souboru zabezpečený, bude pravděpodobně muset udělit [oprávnění.](#permissions)

1. V knihovně se sharepointovým webem vyberte nabídku `...`se třemi tečkami souboru .
1. Zkopírujte adresu URL souboru.

   ![Adresu URL souboru sharepointu získáte tak, že vyberete nabídku tři tečky souboru a pak ji zkopírujete.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Na portálu QnA Maker na stránce **Nastavení** [přidejte adresu URL](manage-knowledge-bases.md#edit-knowledge-base) do znalostní báze.

### <a name="images-with-sharepoint-files"></a>Obrázky se soubory SharePointu

Pokud soubory obsahují obrázky, nejsou extrahovány. Obrázek můžete přidat z portálu QnA Maker po extrahování souboru do párů QnA.

Přidejte obrázek s následující syntaxí značky:

```markdown
![Explanation or description of image](URL of public image)
```

Text v hranatých `[]`závorkách , vysvětluje obrázek. Adresa URL v závorce , `()`je přímým odkazem na obrázek.

Když otestujete dvojici QnA v interaktivním testovacím panelu, na portálu QnA Maker se obrázek zobrazí místo textu značky. Tím ověříte, že bitovou kopii lze veřejně načíst z klientské aplikace.

## <a name="permissions"></a>Oprávnění

K udělení oprávnění dojde, když je zabezpečený soubor ze serveru SharePoint přidán do znalostní báze. V závislosti na nastavení sharepointového bodu a oprávnění osoby, která soubor přidává, to může vyžadovat:

* žádné další kroky - osoba, která přidává soubor, má všechna potřebná oprávnění.
* kroky [správce znalostní báze](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) i [správce služby Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Podívejte se na níže uvedené kroky.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Správce znalostní báze: přidání zdroje dat SharePointu na portálqnA Maker

Pokud **správce programu QnA Maker** přidá zabezpečený dokument služby SharePoint do znalostní báze, správce znalostní báze iniciuje žádost o oprávnění, kterou musí správce služby Active Directory dokončit.

Požadavek začíná automaticky otevíraným číslem pro ověření účtu služby Active Directory.

![Ověřit uživatelský účet](../media/add-sharepoint-datasources/authenticate-user-account.png)

Jakmile správce QnA Maker vybere účet, správce služby Azure Active Directory obdrží oznámení, že potřebuje povolit aplikaci QnA Maker (nikoli správce QnA Maker) přístup k prostředkům SharePointu. Správce služby Azure Active Directory to bude muset udělat pro každý zdroj SharePointu, ale ne každý dokument v tomto prostředku.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Správce služby Active Directory: udělit souboru přístup pro čtení do programu QnA Maker

Správce služby Active Directory (nikoli správce služby QnA Maker) musí udělit přístup k programu QnA Maker, aby mohl přistupovat k prostředkům služby SharePoint, a to výběrem [tohoto odkazu,](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) aby povolil podnikovou aplikaci QnA Maker Portal SharePoint, která má oprávnění ke čtení souborů.

![Správce služby Azure Active Directory uděluje oprávnění interaktivně](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

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
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Udělení přístupu z Centra pro správu Služby Azure Active Directory

1. Správce služby Active Directory se přihlásí k portálu Azure a otevře **[podnikové aplikace](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**.

1. Vyhledejte `QnAMakerPortalSharePoint` vybranou aplikaci QnA Maker.

    [![Hledání qnAmakerportalsharepoint v seznamu podnikových aplikací](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. V části **Zabezpečení**přejděte na **oprávnění**. Vyberte **Udělit souhlas správce pro organizaci**.

    [![Výběr ověřeného uživatele pro správce služby Active Directory](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Vyberte přihlašovací účet s oprávněními k udělení oprávnění pro službu Active Directory.



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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spolupracujte na své znalostní bázi](collaborate-knowledge-base.md)
