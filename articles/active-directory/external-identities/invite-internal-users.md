---
title: Pozvání interních uživatelů ke spolupráci B2B – Azure AD
description: Pokud máte interní uživatelské účty pro partnery, distributory, dodavatele, dodavatele a další hosty, můžete přejít na spolupráci Azure AD B2B tím, že se přihlásíte, abyste se přihlásili pomocí svých externích přihlašovacích údajů nebo přihlášení. Použijte buď PowerShell, nebo rozhraní API pro Microsoft Graph pozvánky.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c03c2c55988df04cc45ef4a1d66d959513c1626d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908593"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Pozvání interních uživatelů ke spolupráci B2B

> [!NOTE]
> Pozvání interních uživatelů k používání spolupráce B2B je funkce veřejné verze Preview Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Před dostupností spolupráce Azure AD B2B můžou organizace spolupracovat s distributory, dodavateli, dodavateli a dalšími uživateli typu Host nastavením interních přihlašovacích údajů pro tyto uživatele. Pokud máte tyto interní uživatele typu Host, můžete je pozvat, aby používaly spolupráci B2B, abyste mohli využít výhod Azure AD B2B. Uživatelé typu Host B2B budou moci používat vlastní identity a přihlašovací údaje pro přihlášení a nebudete muset uchovávat hesla ani spravovat životní cyklus účtů.

Odeslání pozvánky na stávající interní účet vám umožní zachovat ID objektu, hlavní název uživatele, členství ve skupinách a přiřazení aplikací. Nemusíte ručně odstraňovat a znovu přiřazovat prostředky uživatele. Pokud chcete uživatele pozvat, použijte rozhraní API pro pozvání k předání interního uživatelského objektu i e-mailové adresy uživatele typu Host spolu s pozvánkou. Když uživatel pozvánku přijme, služba B2B změní stávající objekt interního uživatele na uživatele B2B. Až dál, uživatel se musí přihlásit ke službám cloudových prostředků pomocí svých přihlašovacích údajů B2B. Můžou pořád používat své interní přihlašovací údaje pro přístup k místním prostředkům, ale můžete to zabránit tím, že resetujete nebo změníte heslo k internímu účtu.

> [!NOTE]
> Pozvánka je jednosměrná. Můžete pozvat interní uživatele, aby mohli používat spolupráci B2B, ale po přidání už přihlašovací údaje B2B nemůžete odebrat. Pokud chcete uživatele změnit zpátky na uživatele, který je jenom interní, budete muset objekt uživatele odstranit a vytvořit nový.

V rámci verze Public Preview se metoda popsaná v tomto článku pro pozvání interních uživatelů do spolupráce B2B nedá použít v těchto instancích:

- Internímu uživateli je již přiřazena licence k systému Exchange.
- Uživatel je z domény, která je nastavená pro přímou federaci v adresáři.
- Interní uživatel je účet jenom pro Cloud a jeho hlavní účet není ve službě Azure AD.

V těchto případech, pokud musí být interní uživatel změněn na uživatele B2B, byste měli interní účet odstranit a poslat uživateli pozvánku na spolupráci B2B.

**Místní synchronizující uživatelé**: u uživatelských účtů synchronizovaných mezi místním prostředím a cloudem zůstane místní adresář zdrojovou autoritou od jejich pozvání k používání spolupráce B2B. Všechny změny, které provedete v místním účtu, se synchronizují s cloudovým účtem, včetně zakázání nebo odstranění účtu. Proto nemůžete zabránit tomu, aby se uživatel přihlásil k místnímu účtu a zároveň zachoval svůj cloudový účet pouhým odstraněním místního účtu. Místo toho můžete nastavit heslo místního účtu na náhodný identifikátor GUID nebo jinou neznámou hodnotu.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Postup při pozvání interních uživatelů ke spolupráci B2B

K odeslání pozvánky B2B internímu uživateli můžete použít PowerShell nebo rozhraní API pro pozvání. Ujistěte se, že je e-mailová adresa, kterou chcete použít pro pozvánku, nastavená jako externí e-mailová adresa na objektu interního uživatele.

- Pro uživatele jenom pro Cloud použijte e-mailovou adresu v vlastnosti User. OtherMails pozvánky.
- U místních synchronizovaných uživatelů musíte pro pozvánku použít hodnotu ve vlastnosti User. mail.
- Doména ve vlastnosti pošta uživatele musí odpovídat účtu, který používají k přihlášení. V opačném případě některé služby, jako jsou týmy, nebudou moci ověřit uživatele.

Ve výchozím nastavení Pozvánka uživateli pošle e-mail s informacemi o tom, že je pozvaní, ale můžete tento e-mail potlačit a místo toho poslat vlastní.

> [!NOTE]
> K odeslání vlastního e-mailu nebo jiné komunikaci můžete použít New-AzureADMSInvitation with-SendInvitationMessage: $false pro pozvání uživatelů v tichém režimu a odeslání vlastní e-mailové zprávy na převedeného uživatele. Viz [rozhraní API pro spolupráci Azure AD B2B a přizpůsobení](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Použití PowerShellu k odeslání pozvánky B2B

K pozvání uživatele ke spolupráci B2B použijte následující příkaz:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
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
