---
title: Pozvání interních uživatelů ke spolupráci B2B – Azure AD
description: Pokud máte interní uživatelské účty pro partnery, distributory, dodavatele, dodavatele a další hosty, můžete přejít na spolupráci Azure AD B2B tím, že se přihlásíte, abyste se přihlásili pomocí svých externích přihlašovacích údajů nebo přihlášení. Použijte buď PowerShell, nebo rozhraní API pro Microsoft Graph pozvánky.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576426"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Pozvání interních uživatelů ke spolupráci B2B

Před dostupností spolupráce Azure AD B2B můžou organizace spolupracovat s distributory, dodavateli, dodavateli a dalšími uživateli typu Host nastavením interních přihlašovacích údajů pro tyto uživatele. Pokud máte jako interní uživatele typu Host, můžete je místo toho pozvat na použití spolupráce B2B. Tito uživatelé typu Host B2B budou moci používat vlastní identity a přihlašovací údaje pro přihlášení a nebudete muset uchovávat hesla ani spravovat životní cyklus účtů.

Odeslání pozvánky na stávající interní účet vám umožní zachovat ID objektu, hlavní název uživatele, členství ve skupinách a přiřazení aplikací. Nemusíte ručně odstraňovat a znovu přiřazovat prostředky uživatele. Pokud chcete uživatele pozvat, použijte rozhraní API pro pozvání k předání interního uživatelského objektu i e-mailové adresy uživatele typu Host spolu s pozvánkou. Když uživatel pozvánku přijme, služba B2B změní stávající objekt interního uživatele na uživatele B2B. Až dál, uživatel se musí přihlásit ke službám cloudových prostředků pomocí svých přihlašovacích údajů B2B.

## <a name="things-to-consider"></a>Co je potřeba zvážit

- **Přístup k místním prostředkům**: po pozvání uživatele ke spolupráci B2B můžou pořád používat své interní přihlašovací údaje pro přístup k místním prostředkům. Můžete tomu zabránit tím, že resetujete nebo změníte heslo k internímu účtu. Výjimkou je [e-mailové ověřování heslem jednorázového hesla](one-time-passcode.md); Pokud se metoda ověřování uživatele změní na jednorázové heslo, nebude už moct používat své interní přihlašovací údaje.

- **Faktura**: Tato funkce nezmění oprávnění uživatele, takže automaticky nepřepne model fakturace uživatele na [externí identity měsíčně aktivních uživatelů (MAU)](external-identities-pricing.md). Pokud chcete pro uživatele aktivovat ceny MAU, změňte uživatele na `guest` . Všimněte si také, že váš tenant služby Azure AD musí být [propojený s předplatným Azure](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) , aby bylo možné aktivovat MAUou fakturaci.

- **Pozvánka je jednosměrná**: můžete pozvat interní uživatele, aby mohli používat spolupráci B2B, ale po přidání už přihlašovací údaje B2B nemůžete odebrat. Pokud chcete uživatele změnit zpátky na uživatele, který je jenom interní, budete muset objekt uživatele odstranit a vytvořit nový.

- **Týmy**: když uživatel přistupuje k týmům pomocí svých externích přihlašovacích údajů, jejich tenant nebude na začátku dostupný ve výběru tenanta týmů. Uživatel má přístup k týmům pomocí adresy URL, která obsahuje kontext tenanta, například: `https://team.microsoft.com/?tenantId=<TenantId>` . Potom bude tenant dostupný ve výběru tenanta týmů.

- **Místní synchronizující uživatelé**: u uživatelských účtů synchronizovaných mezi místním prostředím a cloudem zůstane místní adresář zdrojovou autoritou od jejich pozvání k používání spolupráce B2B. Všechny změny, které provedete v místním účtu, se synchronizují s cloudovým účtem, včetně zakázání nebo odstranění účtu. Proto nemůžete zabránit tomu, aby se uživatel přihlásil k místnímu účtu a zároveň zachoval svůj cloudový účet pouhým odstraněním místního účtu. Místo toho můžete nastavit heslo místního účtu na náhodný identifikátor GUID nebo jinou neznámou hodnotu.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Postup při pozvání interních uživatelů ke spolupráci B2B

K odeslání pozvánky B2B internímu uživateli můžete použít PowerShell nebo rozhraní API pro pozvání. Ujistěte se, že je e-mailová adresa, kterou chcete použít pro pozvánku, nastavená jako externí e-mailová adresa na objektu interního uživatele.

- Pro pozvánku je nutné použít e-mailovou adresu ve vlastnosti User. mail.
- Doména ve vlastnosti pošta uživatele musí odpovídat účtu, který používají k přihlášení. V opačném případě některé služby, jako jsou týmy, nebudou moci ověřit uživatele.

Ve výchozím nastavení Pozvánka uživateli pošle e-mail s informacemi o tom, že je pozvaní, ale můžete tento e-mail potlačit a místo toho poslat vlastní.

> [!NOTE]
> K odeslání vlastního e-mailu nebo jiné komunikaci můžete použít `New-AzureADMSInvitation` s nástrojem `-SendInvitationMessage:$false` pro pozvání uživatelů v tichém režimu a odeslání vlastní e-mailové zprávy na převedeného uživatele. Viz [rozhraní API pro spolupráci Azure AD B2B a přizpůsobení](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Použití PowerShellu k odeslání pozvánky B2B

Budete potřebovat modul Azure AD PowerShell verze 2.0.2.130 nebo novější. Pomocí následujícího příkazu se aktualizujte na nejnovější modul AzureAD PowerShellu a pozvěte interního uživatele ke spolupráci B2B:

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Použití rozhraní API pro pozvánky k odeslání pozvánky B2B

Následující ukázka ukazuje, jak zavolat rozhraní API pro pozvání k pozvání interního uživatele jako uživatele B2B.

```json
POST https://graph.microsoft.com/v1.0/invitations
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
        "id": "<<ID for the user you want to convert>>"
    }
}
```

Odpověď na rozhraní API je stejná jako odpověď, kterou dostanete při pozvání nového uživatele typu Host do adresáře.
## <a name="next-steps"></a>Další kroky

- [Uplatnění pozvánky B2B pro spolupráci](redemption-experience.md)
