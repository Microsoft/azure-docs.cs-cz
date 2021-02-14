---
title: Resetování stavu uplatnění uživatele typu Host – Azure AD
description: Přečtěte si, jak obnovit stav uplatnění pozvánky pro uživatele typu Host Azure Active Directory B2B v externích identitách služby Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: d843755847d074e00aec9ed2830cb873b6bb3382
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365408"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>Resetovat stav uplatnění pro uživatele typu Host

Jakmile uživatel typu Host uzná pozvánku na spolupráci B2B, může nastat situace, kdy budete muset aktualizovat přihlašovací údaje, například když:

- Uživatel chce přihlásit se pomocí jiného e-mailu a poskytovatele identity.
- Účet uživatele v jeho domovském tenantovi byl odstraněn a znovu vytvořen.
- Uživatel se přesunul do jiné společnosti, ale pořád potřebuje stejný přístup k vašim prostředkům.
- Odpovědnosti uživatele byly předány jinému uživateli.

Abyste mohli tyto scénáře spravovat dřív, museli jste ručně odstranit účet uživatele typu host z adresáře a znovu pozvat uživatele. Nyní můžete pomocí prostředí PowerShell nebo rozhraní API pro Microsoft Graph pozvánky obnovit stav uplatnění uživatele a znovu pozvat uživatele při zachování ID objektu uživatele, členství ve skupinách a přiřazení aplikací. Když uživatel uplatní novou pozvánku, hlavní název uživatele se nemění, ale přihlašovací jméno uživatele se změní na nový e-mail. Uživatel se pak může přihlásit pomocí nového e-mailu nebo e-mailu, který jste přidali do `otherMails` vlastnosti objektu uživatele.

## <a name="use-powershell-to-reset-redemption-status"></a>Obnovení stavu uplatnění pomocí PowerShellu

Nainstalujte nejnovější modul AzureADPreview PowerShellu a vytvořte novou pozvánku s `InvitedUserEMailAddress` nastavením na novou e-mailovou adresu a `ResetRedemption` nastavte na `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Obnovení stavu uplatnění pomocí rozhraní Microsoft Graph API

Pomocí [rozhraní API pro Microsoft Graph pozvánky](/graph/api/resources/invitation?view=graph-rest-1.0)nastavte `resetRedemption` vlastnost na `true` a zadejte novou e-mailovou adresu ve `invitedUserEmailAddress` Vlastnosti.

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>Další kroky

- [Přidání uživatelů spolupráce v B2B Azure Active Directory pomocí PowerShellu](customize-invitation-api.md#powershell)
- [Vlastnosti uživatele typu Host Azure AD B2B](user-properties.md)
