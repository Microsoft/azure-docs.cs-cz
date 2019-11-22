---
title: Uplatnění pozvánky ve spolupráci B2B – Azure AD
description: Popisuje prostředí pro uplatnění pozvánky Azure AD B2B pro koncové uživatele, včetně souhlasu s podmínkami ochrany osobních údajů.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e85b0ae298589c0e0e051a24e5db89eae81db62
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272142"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory uplatnění pozvánky B2B pro spolupráci

Tento článek popisuje způsoby, kterými můžou uživatelé typu Host získat přístup k vašim prostředkům a k procesu souhlasu, ke kterým dojde. Pokud odešlete e-mailovou pozvánku na hosta, pozvánka zahrnuje odkaz, který může host uplatnit, aby získal přístup k vaší aplikaci nebo portálu. E-mail pozvánky je jedním z způsobů, jak hosté můžou získat přístup k vašim prostředkům. Jako alternativu můžete do svého adresáře přidat hosty a dát jim přímý odkaz na portál nebo aplikaci, kterou chcete sdílet. Bez ohledu na to, jakou metodu používají, se hostů prostřednictvím procesu souhlasu při prvním použití. Tento proces zajistí, že se vaši hosté dohodli na podmínky ochrany osobních údajů a přijmou [podmínky použití](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) , které jste si nastavili.

Když do svého adresáře přidáte uživatele typu Host, má uživatelský účet hosta stav souhlasu (zobrazitelný v PowerShellu), který je zpočátku nastavený na **PendingAcceptance**. Toto nastavení zůstane, dokud uživatel nepřijme pozvání a schválí vaše zásady ochrany osobních údajů a podmínky použití. Po tomto případě se stav souhlasu změní na **přijato**a na hostovi se již neprezentují stránky souhlasu.

## <a name="redemption-through-the-invitation-email"></a>Vyplacení prostřednictvím e-mailu s pozvánkou

Když do svého adresáře přidáte uživatele typu host [pomocí Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), pošle se na hostovi v procesu e-mail s pozvánkou. Můžete také odeslat e-maily pozvánky, když [používáte PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) k přidání uživatelů typu Host do adresáře. Tady je Popis prostředí hosta při uplatnění odkazu v e-mailu.

1. Host dostane [e-mail pozvánky](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) , která se pošle z **pozvánky Microsoftu**.
2. Host vybere v e-mailu **Začínáme** .
3. Pokud host nemá účet Azure AD, účet Microsoft (MSA) nebo e-mailový účet ve federované organizaci, zobrazí se jim výzva k vytvoření MSA (Pokud není povolená funkce [jednorázového hesla](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) , která nevyžaduje MSA).
4. Host se provede prostřednictvím [souhlasu](#consent-experience-for-the-guest) uživatele uvedeného níže.

## <a name="redemption-through-a-direct-link"></a>Uplatnění přes přímý odkaz

Jako alternativu k e-mailu s pozvánkou můžete hostům poskytnout přímý odkaz na vaši aplikaci nebo portál. Nejdřív je potřeba přidat uživatele typu Host do adresáře prostřednictvím [Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) nebo [PowerShellu](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Pak můžete použít kterýkoli z [přizpůsobitelných způsobů, jak nasadit aplikace pro uživatele](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), včetně odkazů přímo přihlašování. Když host použije přímý odkaz namísto e-mailu s pozvánkou, bude se dál nacházet pomocí prostředí pro vyjádření souhlasu po prvním čase.

> [!IMPORTANT]
> Přímý odkaz musí být specifický pro tenanta. Jinými slovy, musí obsahovat ID tenanta nebo ověřenou doménu, aby bylo možné hosta ověřit ve vašem tenantovi, kde se nachází sdílená aplikace. Společná adresa URL, jako je https://myapps.microsoft.com, nebude pro hosta fungovat, protože se přesměruje na svého domovského tenanta pro ověřování. Tady je několik příkladů přímých odkazů s kontextem tenanta:
 > - Přístupový panel aplikací: https://myapps.microsoft.com/?tenantid=&lt; ID tenanta&gt; 
 > - Přístupový panel aplikací pro ověřenou doménu: https://myapps.microsoft.com/&lt; ověřená doména&gt;
 > - Azure Portal: https://portal.azure.com/&lt; ID tenanta&gt;
 > - Individuální aplikace: viz jak použít [přímý odkaz na přihlášení](../manage-apps/end-user-experiences.md#direct-sign-on-links) .

V některých případech se doporučuje e-mailem pozvánky používat přímý odkaz. Pokud jsou tyto zvláštní případy pro vaši organizaci důležité, doporučujeme, abyste uživatele pozvali pomocí metod, které pořád odesílají e-mail s pozvánkami:
 - Uživatel nemá účet Azure AD, účet MSA nebo e-mailový účet ve federované organizaci. Pokud nepoužíváte funkci jednorázového hesla, Host musí uplatnit e-mail s pozvánkou, aby se provedou kroky pro vytvoření MSA.
 - Někdy je možné, že objekt pozvaného uživatele nemá e-mailovou adresu z důvodu konfliktu s objektem Contact (například objekt kontaktu aplikace Outlook). V takovém případě musí uživatel v e-mailu s pozvánkou kliknout na adresu URL pro uplatnění.
 - Uživatel se může přihlásit pomocí aliasu e-mailové adresy, která byla pozvána. (Alias je další e-mailová adresa přidružená k e-mailovému účtu.) V takovém případě musí uživatel v e-mailu s pozvánkou kliknout na adresu URL pro uplatnění.

## <a name="consent-experience-for-the-guest"></a>Prostředí pro vyjádření souhlasu hosta

Když se host přihlásí k přístupu k prostředkům v partnerské organizaci poprvé, provedou se následující stránky. 

1. Host zkontroluje stránku **oprávnění pro kontrolu** , která popisuje prohlášení o zásadách ochrany osobních údajů ve vaší organizaci. Uživatel musí **přijmout** informace v souladu se zásadami ochrany osobních údajů v organizaci, aby bylo možné pokračovat.

   ![Snímek obrazovky se stránkou pro kontrolu oprávnění](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Informace o tom, jak se může správce klienta připojit k prohlášení o zásadách ochrany osobních údajů vaší organizace, najdete [v tématu Postup: Přidání informací o ochraně osobních údajů vaší organizace v Azure Active Directory](https://aka.ms/adprivacystatement).

2. Pokud jsou podmínky použití nakonfigurované, Host se otevře a zkontroluje podmínky použití a pak vybere **přijmout**. 

   ![Snímek obrazovky s novými podmínkami použití](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > V části **správa** > ch **vztahů organizace** > **podmínky použití**můžete nakonfigurovat zobrazení [podmínek použití](../governance/active-directory-tou.md) .

3. Pokud není uvedeno jinak, Host se přesměruje na přístupový panel aplikace, který obsahuje seznam aplikací, ke kterým má host přístup.

   ![Snímek obrazovky s přístupovým panelem aplikací](media/redemption-experience/myapps.png) 

Ve vašem adresáři se u **pozvánky hosta přijala** hodnota **Ano**. Pokud byl vytvořen MSA, zobrazí se ve **zdroji** hosta **účet Microsoft**. Další informace o vlastnostech uživatelského účtu hosta najdete v tématu [Vlastnosti uživatele spolupráce Azure AD B2B](user-properties.md). 

## <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Přidat uživatele spolupráce Azure Active Directory B2B na webu Azure Portal](add-users-administrator.md)
- [Jak pracovníci s informacemi přidávají uživatelům spolupráci B2B k Azure Active Directory?](add-users-information-worker.md)
- [Přidání uživatelů spolupráce v B2B Azure Active Directory pomocí PowerShellu](customize-invitation-api.md#powershell)
- [Opuštění organizace jako uživatel typu Host](leave-the-organization.md)
