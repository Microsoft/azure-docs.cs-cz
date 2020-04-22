---
title: Pozvání interních uživatelů ke spolupráci B2B – Azure AD
description: Pokud máte interní uživatelské účty pro partnery, distributory, dodavatele, dodavatele a další hosty, můžete přejít na spolupráci Azure AD B2B tak, že je vyzvete k přihlášení pomocí vlastních externích přihlašovacích údajů nebo přihlášení. Použijte rozhraní API pro pozvání k Prostředí PowerShell nebo Microsoft Graph.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 783fc0fa6f6c4e6c918fa3ff5fe0b53a71fa0178
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680176"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Pozvání interních uživatelů ke spolupráci B2B

|     |
| --- |
| Pozvání interních uživatelů k použití spolupráce B2B je funkce veřejného náhledu služby Azure Active Directory. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). |
|     |

Před dostupností spolupráce Azure AD B2B mohou organizace spolupracovat s distributory, dodavateli, dodavateli a dalšími uživateli typu Host tím, že pro ně nastaví interní pověření. Pokud máte interní uživatele typu Host, jako je tento, můžete je pozvat k použití spolupráce B2B, abyste mohli využívat výhod Azure AD B2B. Uživatelé typu Host B2B budou moci k přihlášení používat vlastní identity a přihlašovací údaje a nebudete muset spravovat hesla ani spravovat životní cykly účtu.

Odesláním pozvánky na existující interní účet si můžete zachovat ID objektu tohoto uživatele, název uživatele, členství ve skupinách a přiřazení aplikací. Není nutné ručně odstranit a znovu pozvat uživatele nebo znovu přiřadit prostředky. Chcete-li pozvat uživatele, budete používat rozhraní API pozvánky předat interní objekt uživatele a e-mailovou adresu uživatele hosta spolu s pozvánkou. Když uživatel přijme pozvání, služba B2B změní existující objekt interního uživatele na uživatele B2B. Do budoucna se uživatel musí přihlásit ke službám cloudových prostředků pomocí svých pověření B2B. Mohou stále používat své interní pověření pro přístup k místním prostředkům, ale můžete tomu zabránit resetováním nebo změnou hesla v interním účtu.

> [!NOTE]
> Pozvánka je jednosměrná. Můžete pozvat interní uživatele k použití spolupráce B2B, ale pověření B2B po jejich přidání nelze odebrat. Chcete-li změnit uživatele zpět na uživatele pouze pro interní, budete muset odstranit objekt uživatele a vytvořit nový.

Zatímco ve verzi Public Preview nelze v těchto případech použít metodu popsanou v tomto článku pro pozvání interních uživatelů ke spolupráci b2B:

- Internímu uživateli již byla přiřazena licence serveru Exchange.
- Uživatel pochází z domény, která je nastavena pro přímou federaci ve vašem adresáři.
- Interní uživatel je účet pouze pro cloud a jejich hlavní účet není ve službě Azure AD.

V těchto případech pokud interní uživatel musí být změněn na uživatele B2B, měli byste odstranit interní účet a odeslat uživateli pozvánku pro spolupráci B2B.

**Místní synchronizovaní uživatelé**: Pro uživatelské účty, které jsou synchronizovány mezi místním a cloudem, zůstane místní adresář zdrojem autority i po pozvání k použití spolupráce B2B. Všechny změny, které provedete v místním účtu, se synchronizují s klientem cloudu, včetně zakázání nebo odstranění účtu. Proto nemůžete uživateli zabránit v přihlášení k místnímu účtu při zachování cloudového účtu pouhým odstraněním místního účtu. Místo toho můžete nastavit heslo místního účtu na náhodný identifikátor GUID nebo jinou neznámou hodnotu.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Jak pozvat interní uživatele na spolupráci B2B

Pomocí prostředí PowerShell nebo rozhraní API pro pozvánku můžete odeslat pozvánku B2B internímu uživateli. Ujistěte se, že e-mailová adresa, kterou chcete použít pro pozvánku, je nastavena jako externí e-mailová adresa interního uživatelského objektu.

- Pro uživatele pouze pro cloud použijte e-mailovou adresu ve vlastnosti User.OtherMails pro pozvánku.
- Pro místnísynchronizované uživatele je nutné použít hodnotu ve vlastnosti User.Mail pro pozvánku.
- Doména ve vlastnosti Pošty uživatele se musí shodovat s účtem, který používá k přihlášení. V opačném případě některé služby, jako je například Teams nebude moci ověřit uživatele.

Ve výchozím nastavení odešle pozvánka uživateli e-mail s oznámením, že byl pozván, ale můžete tento e-mail potlačit a místo toho odeslat vlastní.

> [!NOTE]
> Chcete-li odeslat vlastní e-mail nebo jinou komunikaci, můžete použít New-AzureADMSInvitation s -SendInvitationMessage:$false pozvat uživatele tiše a potom odeslat vlastní e-mailovou zprávu převedeného uživatele. Viz [Rozhraní API pro spolupráci azure AD B2B a přizpůsobení](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Odeslání pozvánky b2B pomocí PowerShellu

Pomocí následujícího příkazu můžete uživatele pozvat ke spolupráci b2B:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -searchstring "<<external email>>"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Odeslání pozvánky b2B pomocí rozhraní API pro pozvánku

Ukázka níže ukazuje, jak volat rozhraní API pozvání pozvat interního uživatele jako uživatele B2B.

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

Odpověď na rozhraní API je stejná odpověď, kterou dostanete, když pozvete nového uživatele typu Host do adresáře.

## <a name="next-steps"></a>Další kroky

- [Uplatnění pozvánky na spolupráci B2B](redemption-experience.md)
