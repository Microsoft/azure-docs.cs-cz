---
title: Poradce při potížích se spoluprací b2B – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Nápravná opatření pro běžné problémy se spoluprací služby Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 03/19/2020
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f8bafb04d0a5d9c6d25a7ed7e155888d492e9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050786"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Poradce při potížích se spoluprací služby Azure Active Directory B2B

Tady jsou některé opravné prostředky pro běžné problémy s Azure Active Directory (Azure AD) B2B spolupráce.

   > [!IMPORTANT]
   > **března 2021**, Microsoft již nebude podporovat uplatnění pozvánek vytvořením nespravované účty Azure AD a tenanty pro scénáře spolupráce B2B. V rámci přípravy doporučujeme zákazníkům, aby se přihlásili k [jednorázovému ověření hesla e-mailem](one-time-passcode.md). Vítáme vaši zpětnou vazbu k této funkci veřejného náhledu a jsme nadšeni, že můžeme vytvořit ještě více způsobů spolupráce.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Přidal(a) jsem externího uživatele, ale nevidím ho v globálním adresáři nebo ve výběru osob

V případech, kdy externí uživatelé nejsou naplněny v seznamu, objekt může trvat několik minut replikovat.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Uživatel typu Host b2B se nezobrazuje ve výběru lidí na SharePointu Online/OneDrivu

Možnost vyhledávat existující uživatele typu Host v výběru lidí sharepointu Online (SPO) je ve výchozím nastavení vypnutá tak, aby odpovídala staršímu chování.

Tuto funkci můžete povolit pomocí nastavení ShowPeoplePickerSuggestionsForGuestUsers na úrovni klienta a kolekce webů. Funkci můžete nastavit pomocí rutin Set-SPOTenant a Set-SPOSite, které umožňují členům prohledávat všechny existující uživatele typu Host v adresáři. Změny v oboru klienta nemají vliv na již zřízené weby SPO.

## <a name="invitations-have-been-disabled-for-directory"></a>Pozvánky byly zakázány pro adresář

Pokud budete upozorněni, že nemáte oprávnění k pozvání uživatelů, ověřte, zda je váš uživatelský účet oprávněn pozvat externí uživatele v rámci služby Azure Active Directory > Nastavení uživatelů > Externí uživatelé > Nastavení externí spolupráce:

![Snímek obrazovky s nastavením Externí uživatelé](media/troubleshoot/external-user-settings.png)

Pokud jste tato nastavení nedávno upravili nebo uživateli přiřadili roli Pozvaní hosté, může dojít ke zpoždění 15 až 60 minut, než se změny projeví.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Uživateli, kterého jsem pozval(a), se při uplatnění zobrazuje chyba

Běžné chyby zahrnují:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Správce pozvané zakázal vytváření uživatelů EmailVerified v tenantovi.

Při pozvání uživatelů, jejichž organizace používá Azure Active Directory, ale kde konkrétní účet uživatele neexistuje (například uživatel neexistuje v Azure AD contoso.com). Správce contoso.com může mít zásady, které brání uživatelům v vytváření. Uživatel se musí poradit se svým správcem, aby zjistil, zda jsou externí uživatelé povoleni. Správce externího uživatele může potřebovat povolit uživatelům ověřených e-mailů ve své doméně (viz tento [článek](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) o povolení ověřených uživatelů e-mailu).

![Při konstatování, že tenant nepovoluje e-mailové ověřené uživatele, došlo k chybě.](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Externí uživatel již neexistuje ve federované doméně.

Pokud používáte federační ověřování a uživatel ještě neexistuje ve službě Azure Active Directory, uživatel nemůže být pozván.

Chcete-li tento problém vyřešit, musí správce externího uživatele synchronizovat účet uživatele se službou Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Jak se\#' ', což není obvykle platný znak, synchronizovat s Azure AD?

"\#" je vyhrazený znak v upn pro spolupráci Azure AD B2B nebo externí uživatele, protože pozvaný účet user@contoso.com se stane user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Proto \# v upn y pocházející z místního není povoleno přihlášení k portálu Azure. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Při přidávání externích uživatelů do synchronizované skupiny se zobrazuje chyba

Externí uživatele lze přidat pouze do skupin "přiřazeno" nebo "Zabezpečení" a nikoli do skupin, které jsou místní.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Externí uživatel neobdržel e-mail, který by mohl uplatnit.

Pozvaný uživatel by se měl obrátit na svého isp nebo spamfiltr, aby se ujistil, že je povolena následující adresa:Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Všiml jsem si, že vlastní zpráva není součástí pozvánek občas

V souladu se zákony o ochraně osobních údajů naše api nezahrnují vlastní zprávy do e-mailové pozvánky, pokud:

- Pozvaný nemá e-mailovou adresu v zvoucím tenantovi
- Když objekt zabezpečení služby Appservice odešle pozvánku

Pokud je pro vás tento scénář důležitý, můžete potlačit náš e-mail s pozvánkou k rozhraní API a odeslat jej prostřednictvím e-mailového mechanismu podle vašeho výběru. Obraťte se na právního zástupce vaší organizace, abyste se ujistili, že všechny e-maily, které tímto způsobem odešlete, jsou v souladu se zákony na ochranu osobních údajů.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Při pokusu o přihlášení k prostředku Azure se zobrazí chyba "AADSTS65005".

Uživatel, který má účet hosta, se nemůže přihlásit a zobrazuje se následující chybová zpráva:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

Uživatel má uživatelský účet Azure a je virální tenant, který byl opuštěný nebo nespravovaný. Navíc neexistují žádné globální nebo správci společnosti v tenantovi.

Chcete-li tento problém vyřešit, je nutné převzít opuštěného klienta. Odkazovat na [převzetí nespravovaného adresáře jako správce ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover). Musíte také přistupovat k internetu DNS pro příponu domény v otázce, aby bylo možné poskytnout přímý důkaz, že máte kontrolu nad oborem názvů. Po návratu klienta do spravovaného stavu, proberte se zákazníkem, zda opuštění uživatelů a ověřeného názvu domény je nejlepší volbou pro jejich organizaci.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Uživatel typu Host s klientem just-in-time nebo "virální" nemůže obnovit své heslo

Pokud je klient identity just-in-time (JIT) nebo virové tenanta (což znamená, že je samostatný, nespravované klientazure), pouze uživatel typu Host můžete resetovat své heslo. Někdy organizace [převezme správu virových tenantů,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) které jsou vytvořeny, když zaměstnanci používají své pracovní e-mailové adresy k registraci služeb. Poté, co organizace převezme virové tenanta, pouze správce v této organizaci můžete obnovit heslo uživatele nebo povolit sspr. V případě potřeby můžete jako zvoucí organizace odebrat uživatelský účet hosta z adresáře a znovu odeslat pozvánku.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>Uživatel typu Host nemůže používat modul AzureAD PowerShell V1

listopadu 18, 2019, uživatelé typu Host ve vašem adresáři (definované jako uživatelské účty, kde **userType** vlastnost rovná **Guest**) jsou blokovány používat modul AzureAD PowerShell V1. Do budoucna uživatel bude muset být buď členem uživatele (kde **userType** rovná **člen**) nebo použít modul AzureAD PowerShell V2.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>V tenantovi Azure us government nemůžu pozvat uživatele typu Host pro spolupráci B2B

V rámci cloudu Azure US Government je spolupráce B2B momentálně podporovaná jenom mezi klienty, kteří jsou v cloudu Azure US Government a oba podporují spolupráci B2B. Pokud pozvete uživatele v tenantovi, který není součástí cloudu Azure US Government nebo který ještě nepodporuje spolupráci B2B, zobrazí se chyba. Podrobnosti a omezení najdete [v tématu Azure Active Directory Premium P1 a P2 Varianty](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

## <a name="next-steps"></a>Další kroky

[Získejte podporu pro spolupráci B2B](get-support.md)
