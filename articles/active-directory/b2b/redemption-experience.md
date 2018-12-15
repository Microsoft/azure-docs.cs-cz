---
title: Uplatnění pozvání v spolupráce B2B – Azure Active Directory | Dokumentace Microsoftu
description: Popisuje prostředí uplatnění pozvání spolupráce B2B ve službě Azure AD pro koncové uživatele, včetně smlouvy na zásady ochrany osobních údajů.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f580c78fa2114905a0ef28f51feee50361717a27
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435323"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Uplatnění pozvání spolupráce služby Azure Active Directory s B2B

Spolupracovat s uživatelů z partnerských organizací prostřednictvím spolupráce B2B ve službě Azure Active Directory (Azure AD), můžete pozvat uživatele typu Host k aplikacím sdílené. Po přidání uživatele typu Host do adresáře prostřednictvím uživatelského rozhraní, nebo je uživatel vyzván prostřednictvím prostředí PowerShell, aby uživatelé typu Host musí projít první souhlasu procesu, kde vyjadřují souhlas se [zásady ochrany osobních údajů](#privacy-policy-agreement). K tomu dochází v některém z následujících způsobů:

- Odesílatel pozvánky hostů rozešle přímý odkaz na sdílenou aplikací. Pozvaný klikne na odkaz pro přihlášení, přijme podmínky ochrany osobních údajů a bez problémů přistupuje ke sdílenému prostředku. (Uživatel typu Host stále přijímá zvací e-mail s adresou URL uplatnění, ale kromě některých zvláštních případech ho už nebude potřeba používat e-mailová pozvánka.)  
- Uživatel typu Host dostane e-mailová pozvánka a klikne uplatnění adresy URL. V rámci první znak-in se mu výzva k přijměte zásady ochrany osobních údajů.

## <a name="redemption-through-a-direct-link"></a>Uplatnění prostřednictvím přímého odkazu

Odesílatel pozvánky hostů můžete pozvat uživatele typu Host odesláním navýšení kapacity [přímý odkaz na sdílené aplikace](../manage-apps/end-user-experiences.md#direct-sign-on-links). Pro uživatele typu Host prostředí uplatnění je jednoduché. stačí aplikaci, která se sdílí s nimi. Můžete kliknutím na odkaz na aplikaci, zkontrolujte a přijměte zásady ochrany osobních údajů a pak bezproblémově přistupovat k aplikaci. Ve většině případů uživatelé typu Host už nemusí klikněte na adresu URL uplatnění v e-mailová pozvánka.

Pokud pozvat uživatele typu Host přes uživatelské rozhraní nebo jste zvolili k odesílání e-mailová pozvánka jako součást pozvánku prostředí PowerShell, pozvaný uživatel stále obdrží e-mailová pozvánka. Tento e-mail je vhodný pro následující zvláštní případy:

- Uživatel nemá účet Azure AD nebo účet Microsoft (MSA). V takovém případě musí uživatel vytvořit MSA, před kliknou na odkaz, nebo můžou použít adresu URL uplatnění v e-mailová pozvánka. Dokončete proces automaticky zobrazí výzvu k vytvoření MSA.
- Někdy objekt pozvaný uživatel nemá e-mailovou adresu z důvodu konfliktu s objektem kontaktů (například Outlook kontaktní objekt). V tomto případě uživatel musí kliknout na adresu URL uplatnění v e-mailová pozvánka.
- Uživatel může přihlásit pomocí alias e-mailové adresy, který je pozván. (Alias je další e-mailovou adresu, přidružené k e-mailový účet.) V tomto případě uživatel musí kliknout na adresu URL uplatnění v e-mailová pozvánka.

Pokud tyto speciální případy jsou důležité pro vaši organizaci, doporučujeme pozvat uživatele pomocí metody, které i dál posílat e-mailová pozvánka. Také pokud uživatel nemá spadají pod jednu z těchto zvláštní případy, že klikněte na adresu URL e-mailem pozvání získat přístup.

## <a name="redemption-through-the-invitation-email"></a>Uplatnění pozvání, pošlete e-mailem

Pozváni pomocí metody, která se odešle e-mailové pozvánce, uživatelé mohou také využít pozvání prostřednictvím e-mailová pozvánka. Pozvaného uživatele můžete klikněte na adresu URL uplatnění v e-mailu a pak si přečtěte a přijměte zásady ochrany osobních údajů. Proces je podrobněji popsaný tady:

1.  Po pozvat, pozvaný přijme pozvánku prostřednictvím e-mailu, který se odesílá z **Microsoft Invitations**.
2.  Vybere pozvaný **Začínáme** e-mailu.
3.  Pokud pozvaný nemá účet Azure AD nebo MSA, jsou vyzváni k vytvoření MSA.
4.  Pozvaný je přesměrován na **kontrola oprávnění** obrazovky, ve kterém můžete zkontrolovat zvoucí organizaci zásady ochrany osobních údajů a přijetí podmínek.

## <a name="privacy-policy-agreement"></a>Dohoda o ochraně osobních údajů zásad

Po každý uživatel typu Host přihlášení pro přístup k prostředkům v organizaci partnera poskytujícího poprvé, zobrazí se jim **kontrola oprávnění** obrazovky. Tady se můžete zkontrolovat zvoucí organizaci zásady ochrany osobních údajů. Uživatel musí přijmout použijte své informace podle zásady ochrany osobních údajů zvoucí organizaci, abyste mohli pokračovat.

![Snímek obrazovky zobrazující nastavení uživatele v přístupovém panelu](media/redemption-experience/ConsentScreen.png) 

Informace o tom, jak jako správce tenanta můžete propojit prohlášení o ochraně osobních údajů vaší organizace, najdete v části [s návody: Přidejte informace o ochraně osobních údajů vaší organizace v Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="terms-of-use"></a>Podmínky použití

Podmínky použití pro uživatele typu Host může předložit během procesu uplatnění. úvodní pomocí Azure AD Terms of použít funkci. Ve službě Azure Active Directory, můžete přístup k této funkci v rámci **spravovat** > **organizační vztahy** > **podmínky použití** nebo v části **Zabezpečení** > **podmíněného přístupu** > **podmínky použití**. Podrobnosti najdete v tématu [Azure AD Terms of použít funkci](../governance/active-directory-tou.md).

![Snímek obrazovky zobrazující nové podmínky použití](media/redemption-experience/organizational-relationships-terms-of-use.png) 

## <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Přidat uživatele spolupráce Azure Active Directory B2B na webu Azure Portal](add-users-administrator.md)
- [Jak informačních pracovníků vynutit přidat uživatele spolupráce B2B do Azure Active Directory](add-users-information-worker.md)
- [Přidat uživatele spolupráce Azure Active Directory B2B pomocí Powershellu](customize-invitation-api.md#powershell)
- [Opustit organizaci jako uživatele typu Host](leave-the-organization.md)