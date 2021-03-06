---
title: Resetování stavu uplatnění uživatele typu Host – Azure AD
description: Přečtěte si, jak obnovit stav uplatnění pozvánky pro uživatele typu Host Azure Active Directory B2B v externích identitách služby Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5bfce7ef2621cbe3bbbfdd95bf9a75e427c8cbd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531881"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>Resetovat stav uplatnění pro uživatele typu Host (Preview)

Jakmile uživatel typu Host uzná pozvánku na spolupráci B2B, může nastat situace, kdy budete muset aktualizovat přihlašovací údaje, například když:

- Uživatel chce přihlásit se pomocí jiného e-mailu a poskytovatele identity.
- Účet uživatele v jeho domovském tenantovi byl odstraněn a znovu vytvořen.
- Uživatel se přesunul do jiné společnosti, ale pořád potřebuje stejný přístup k vašim prostředkům.
- Odpovědnosti uživatele byly předány jinému uživateli.

Abyste mohli tyto scénáře spravovat dřív, museli jste ručně odstranit účet uživatele typu host z adresáře a znovu pozvat uživatele. Nyní můžete pomocí prostředí PowerShell nebo rozhraní API pro Microsoft Graph pozvánky obnovit stav uplatnění uživatele a znovu pozvat uživatele při zachování ID objektu uživatele, členství ve skupinách a přiřazení aplikací. Když uživatel uplatní novou pozvánku, hlavní název uživatele se nemění, ale přihlašovací jméno uživatele se změní na nový e-mail. Uživatel se pak může přihlásit pomocí nového e-mailu nebo e-mailu, který jste přidali do `otherMails` vlastnosti objektu uživatele.

## <a name="reset-the-email-address-used-for-sign-in"></a>Resetování e-mailové adresy používané pro přihlášení

Pokud se chce uživatel přihlásit pomocí jiného e-mailu:

1. Ujistěte se, že je nová e-mailová adresa přidána do `mail` `otherMails` vlastnosti nebo objektu User. 
2.  Nahraďte e-mailovou adresu ve `InvitedUserEmailAddress` vlastnosti novou e-mailovou adresou.
3. Chcete-li obnovit stav uplatnění uživatele, použijte jednu z níže uvedených metod.

> [!NOTE]
>Ve verzi Public Preview máme dvě doporučení:
>- Při resetování e-mailové adresy uživatele na novou adresu doporučujeme nastavit `mail` vlastnost. Tímto způsobem může uživatel uplatnit pozvánku tím, že se přihlásí k adresáři Kromě použití odkazu na uplatnění v pozvánce.
>- Když resetujete stav uživatele typu Host B2B, nezapomeňte to udělat v kontextu uživatele. Volání jenom pro aplikace se v tuto chvíli nepodporují.
>
## <a name="use-powershell-to-reset-redemption-status"></a>Obnovení stavu uplatnění pomocí PowerShellu

Nainstalujte nejnovější modul AzureADPreview PowerShellu a vytvořte novou pozvánku s `InvitedUserEmailAddress` nastavením na novou e-mailovou adresu a `ResetRedemption` nastavte na `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Obnovení stavu uplatnění pomocí rozhraní Microsoft Graph API

Pomocí [rozhraní API pro Microsoft Graph pozvánky](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true)nastavte `resetRedemption` vlastnost na `true` a zadejte novou e-mailovou adresu ve `invitedUserEmailAddress` Vlastnosti.

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
