---
title: Pozvánka se ve spolupráci B2B – Azure Active Directory | Microsoft Docs
description: Popisuje možnosti uplatnění pozvánku spolupráce Azure AD B2B pro koncové uživatele, včetně smlouvu na zásady ochrany osobních údajů.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0a0c900bbfbb2778d8fabcbb71e339fd3dabcf47
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260011"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory s B2B spolupráce pozvánku k uplatnění kódu.

Pokud chcete spolupracovat se uživatelé z partnerských organizací prostřednictvím spolupráce B2B Azure Active Directory (Azure AD), můžete pozvat uživatele typu Host pro přístup k aplikacím sdílené. Po přidání hosta uživatele k adresáři prostřednictvím uživatelského rozhraní, nebo je uživatel vyzván pomocí prostředí PowerShell, uživatele typu Host musí procházet přes prvního souhlasu procesu, kde souhlasí [zásady ochrany osobních údajů](#privacy-policy-agreement). K tomu dochází v některém z následujících způsobů:

- Pozvání hosta odeslal rozešle přímý odkaz na sdílené aplikaci. Pozvané klikne na odkaz pro přihlášení, přijímá zásady ochrany osobních údajů a bezproblémově přistupuje k sdílený prostředek. (Uživatele guest stále obdrží e-mail Pozvánka s adresou URL uplatnění, ale než některých zvláštních případech již musí používat e-mailová pozvánka.)  
- Uživatele guest obdrží e-mailová pozvánka a klikne na adresu URL uplatnění kódu. V rámci prvního přihlásit se se výzva k přijměte zásady ochrany osobních údajů.

## <a name="redemption-through-a-direct-link"></a>Uplatnění prostřednictvím přímé propojení

Odesílatel pozvánky hosta můžete pozvat uživatele hosta odesláním se přímý odkaz na sdílené aplikaci. Pro uživatele typu Host prostředí uplatnění je stejně snadná jako přihlášení k aplikaci, která byla sdílet s nimi. Se můžete kliknutím na odkaz na aplikaci, přečtěte si a přijměte zásady ochrany osobních údajů a bezproblémově přístup k aplikaci. Ve většině případů klikněte na adresu URL se v e-mailová pozvánka už nemusí uživatele typu Host.

Pokud pozvat uživatele typu Host v uživatelském rozhraní nebo zvolili k odesílání e-mailová pozvánka jako součást pozvánku prostředí PowerShell, pozvaní uživatelé stále obdrží e-mailová pozvánka. Tento e-mail je užitečné v následujících případech speciální:

- Uživatel nemá účet Azure AD nebo účtu Microsoft (MSA). Před kliknou na odkaz, nebo adresa URL se můžou použít v e-mailová pozvánka v tomto případě uživatel musí vytvořit účet spravované služby. Dokončete proces automaticky vyzývá uživatele k vytvoření účet spravované služby.
- Objekt pozvané uživatele v některých případech nemusí mít e-mailovou adresu z důvodu konfliktu s kontaktní objektu (například Outlook kontaktní objekt). Uživatel v takovém případě musíte kliknout na adresu URL se v e-mailová pozvánka.
- Uživatel může přihlásit pomocí alias e-mailové adresy, který byl vyzván. (Alias další e-mailová adresa je přidružené k e-mailový účet.) Uživatel v takovém případě musíte kliknout na adresu URL se v e-mailová pozvánka.

Pokud tyto zvláštních případech jsou důležité pro vaši organizaci, doporučujeme pozvat uživatele pomocí metody, které i dál posílat e-mailová pozvánka. Navíc pokud uživatel není spadají pod jednu z těchto zvláštních případech, že klikněte na adresu URL v e-mailová pozvánka získat přístup.

## <a name="redemption-through-the-invitation-email"></a>Uplatnění prostřednictvím e-mailová pozvánka

Pokud pozvat prostřednictvím metody, která odešle e-mail pozvánku, uživatelé mohou také uplatněte Pozvánka prostřednictvím e-mailová pozvánka. Pozvané uživatele můžete klikněte na adresu URL se v e-mailu a pak si přečtěte a přijměte zásady ochrany osobních údajů. Proces je podrobněji popsané v tomto poli:

1.  Po pozvat, obdrží pozvaný Pozvánka prostřednictvím e-mailu, který se odesílá z **Microsoft Invitations**.
2.  Vybere pozvaný **Začínáme** e-mailu.
3.  Pokud pozvaný nemá účet Azure AD nebo účtu spravované služby, budou vyzváni k jeho vytvoření účtu spravované služby.
4.  Pozvané je přesměrován na **zkontrolujte oprávnění** obrazovku, kde můžete zkontrolovat pozváním organizace zásady ochrany osobních údajů a přijetí podmínek.

## <a name="privacy-policy-agreement"></a>Smlouva zásady ochrany osobních údajů

Po všechny uživatele guest přihlášení pro přístup k prostředkům v organizaci partnera poprvé, zobrazí se jim **zkontrolujte oprávnění** obrazovky. Zde se můžete zkontrolovat prohlášení o ochraně osobních údajů pozváním organizace. Uživatel musí přijmout využívat své informace podle zásad ochrany osobních údajů organizace pozváním pokračujte.

![Snímek obrazovky zobrazující uživatelská nastavení v přístupového panelu](media/redemption-experience/ConsentScreen.png) 

Informace o tom, jak jako správce klienta můžete se propojit k prohlášení o ochraně osobních údajů vaší organizace najdete v tématu [postupy: přidání informace o ochraně osobních údajů vaší organizace v Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Přidat uživatele Azure Active Directory s B2B spolupráce na portálu Azure](add-users-administrator.md)
- [Jak informační pracovníci přidat uživatele spolupráce B2B do Azure Active Directory](add-users-information-worker.md)
- [Přidat uživatele spolupráce Azure Active Directory B2B pomocí prostředí PowerShell](customize-invitation-api.md#powershell)
- [Nechte organizaci jako uživatel guest](leave-the-organization.md)