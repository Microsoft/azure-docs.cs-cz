---
title: Uplatnění pozvání v spolupráce B2B – Azure Active Directory | Dokumentace Microsoftu
description: Popisuje prostředí uplatnění pozvání spolupráce B2B ve službě Azure AD pro koncové uživatele, včetně smlouvy na zásady ochrany osobních údajů.
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
ms.openlocfilehash: a80eaa134130195fce00ee6a4d68851e478c4532
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052468"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Uplatnění pozvání spolupráce služby Azure Active Directory s B2B

Tento článek popisuje způsoby uživatele typu Host můžete přístup k prostředkům a procesu souhlas, že dojde k. Při odeslání e-mailová pozvánka hosta, e-mailové pozvánce obsahuje odkaz může uplatnit hosta Chcete-li získat přístup k aplikaci nebo portál. E-mailová pozvánka je pouze jedním ze způsobů, hosté může získávat přístup k vašim prostředkům. Jako alternativu můžete přidat hosty do adresáře a poskytnout přímý odkaz na portálu nebo aplikace, kterou chcete sdílet. Bez ohledu na to, v případě metody, které používají hosté projdete první souhlasu procesu. Tento proces zajistí, že hosté svůj souhlas s podmínkami ochrany osobních údajů a přijmout všechny [podmínky použití](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) jste nastavili.

Při přidání uživatele typu Host do adresáře má uživatelský účet guest (zobrazitelné v prostředí PowerShell) souhlasu stav, který je zpočátku nastaven **PendingAcceptance**. Toto nastavení zůstane, dokud hosta přijme pozvánku a souhlasí, že zásady ochrany osobních údajů a podmínky použití. Poté stav souhlasu se změní na **přijato**, a na stránkách souhlasu už přítomných hosta.

## <a name="redemption-through-the-invitation-email"></a>Uplatnění pozvání, pošlete e-mailem

Po přidání uživatele typu Host do adresáře podle [pomocí webu Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), odešle e-mail pozvánku do hostovaného v procesu. Můžete také odeslat e-mailových pozvánek, až budete [pomocí prostředí PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) přidat uživatele typu Host do adresáře. Tady je popis prostředí hosta, kdy se uplatnit na odkaz v e-mailu.

1. Host dostane [e-mailová pozvánka](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) , která se odesílá z **Microsoft Invitations**.
2. Vybere hosta **Začínáme** e-mailu.
3. Pokud hosta ve federované organizaci nemá účet Azure AD, účet Microsoft (MSA) nebo e-mailový účet, budete vyzváni k vytvoření MSA (Pokud [jednorázové heslo](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) je povolena funkce, která nevyžaduje, aby MSA ).
4. Host se projdete [souhlas prostředí](#consent-experience-for-the-guest) je popsáno níže.

## <a name="redemption-through-a-direct-link"></a>Uplatnění prostřednictvím přímého odkazu

Jako alternativu k e-mailové pozvánce můžete udělit Host přímý odkaz do své aplikace nebo portálu. Je nutné nejprve přidat uživatele typu Host do adresáře prostřednictvím [webu Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) nebo [Powershellu](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Pak můžete použít některý z [přizpůsobitelné způsoby, jak nasazovat aplikace uživatelům](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), včetně přímé odkazy přihlašování. Když hosta používá přímý odkaz místo e-mailové pozvánce, jejich provedeme stále vás prostředí pro vyjádření souhlasu první.

> [!IMPORTANT]
> Přímý odkaz musí být specifickým pro tenanta. Jinými slovy musí obsahovat ID tenanta nebo ověřené domény, Host lze ověřit ve vašem tenantovi, kde se nachází sdílená aplikace. Běžné adresu URL, jako jsou https://myapps.microsoft.com nebude fungovat pro hosta, protože bude přesměrována na jejich domovském tenantovi pro ověřování. Tady jsou některé příklady přímé odkazy s kontextem tenanta:
 > - Přístupový panel aplikací: https://myapps.microsoft.com/?tenantid=&lt; id klienta&gt; 
 > - Přístupový panel aplikací pro ověřenou doménu: https://myapps.microsoft.com/&lt; ověřené domény&gt;
 > - Azure portal: https://portal.azure.com/&lt; id klienta&gt;
 > - Jednotlivé aplikace: jak používat [přímý odkaz přihlašování](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Existují případy, kdy se doporučuje namísto přímého odkazu používat e-mailová pozvánka. Pokud tyto speciální případy jsou důležité pro vaši organizaci, doporučujeme pozvat uživatele pomocí metody, které i dál posílat e-mailová pozvánka:
 - Uživatel nemá účet Azure AD, MSA nebo e-mailový účet ve federované organizaci. Pokud používáte funkci jednorázové heslo, je potřeba uplatnit e-mailová pozvánka pro vás kroky pro vytvoření MSA hosta.
 - Někdy objekt pozvaný uživatel nemá e-mailovou adresu z důvodu konfliktu s objektem kontaktů (například Outlook kontaktní objekt). V tomto případě uživatel musí kliknout na adresu URL uplatnění v e-mailová pozvánka.
 - Uživatel může přihlásit pomocí alias e-mailové adresy, který je pozván. (Alias je další e-mailovou adresu, přidružené k e-mailový účet.) V tomto případě uživatel musí kliknout na adresu URL uplatnění v e-mailová pozvánka.

## <a name="consent-experience-for-the-guest"></a>Prostředí pro vyjádření souhlasu pro hosta

Když Host přihlásí k přístupu k prostředkům v organizaci partnera poskytujícího poprvé, že projdete na následujících stránkách. 

1. Revize hostovaného **kontrola oprávnění** stránka s popisem zvoucí organizaci zásady ochrany osobních údajů. Uživatel se musí **přijmout** použijte své informace podle zásady ochrany osobních údajů zvoucí organizaci, abyste mohli pokračovat.

   ![Snímek obrazovky zobrazující stránku zkontrolujte oprávnění](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Informace o tom, jak jako správce tenanta můžete propojit prohlášení o ochraně osobních údajů vaší organizace, najdete v části [s návody: Přidejte informace o ochraně osobních údajů vaší organizace v Azure Active Directory](https://aka.ms/adprivacystatement).

2. Pokud jsou nakonfigurované podmínky použití, Host se otevře a zkontroluje podmínky použití a potom vybere **přijmout**. 

   ![Snímek obrazovky zobrazující nové podmínky použití](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Můžete nakonfigurovat viz [podmínky použití](../governance/active-directory-tou.md) v **spravovat** > **organizační vztahy** > **podmínky použití**.

3. Pokud není uvedeno jinak, Host přesměruje k přístupovému panelu aplikací, který obsahuje seznam aplikací, ke kterým přístup hosta.

   ![Snímek obrazovky ukazující na přístupovém panelu aplikací](media/redemption-experience/myapps.png) 

Do vašeho adresáře, Host **Pozvánka se přijala** hodnotu **Ano**. Pokud byl vytvořen MSA, hosta **zdroj** ukazuje **Account Microsoft**. Další informace o vlastnostech uživatelského účtu guest, naleznete v tématu [vlastnosti uživatele spolupráce B2B ve službě Azure AD](user-properties.md). 

## <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Přidat uživatele spolupráce Azure Active Directory B2B na webu Azure Portal](add-users-administrator.md)
- [Jak informačních pracovníků vynutit přidat uživatele spolupráce B2B do Azure Active Directory](add-users-information-worker.md)
- [Přidat uživatele spolupráce Azure Active Directory B2B pomocí Powershellu](customize-invitation-api.md#powershell)
- [Opustit organizaci jako uživatele typu Host](leave-the-organization.md)
