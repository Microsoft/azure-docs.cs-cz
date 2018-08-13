---
title: Pokyny pro aplikace pro Branding | Dokumentace Microsoftu
description: Komplexní pokyny k prostředkům orientovaných na vývojáře pro službu Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.reviewer: arielgo
ms.custom: aaddev, signin_art
ms.openlocfilehash: 78a0b9bf7f49cc41c8d78287bcbe54c5c88f0809
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "39597901"
---
# <a name="branding-guidelines-for-applications"></a>Pokyny pro aplikace pro Branding

Tento článek popisuje značce, kterou byste měli použít při vývoji aplikací s Azure Active Directory (Azure AD). Tyto pokyny vám pomůže nasměrovat vaši zákazníci chtějí použít svůj pracovní nebo školní účet, spravované ve službě Azure AD, nebo jejich osobní účet pro registraci a přihlášení do vaší aplikace.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Osobní účty vs. pracovní nebo školní účty od Microsoftu

Microsoft spravuje dva typy uživatelských účtů:

* **Osobní účty** (dříve Windows Live ID). Tyto účty představují vztah mezi *jednotlivé* uživatelé a společnosti Microsoft a jsou použity pro přístup k zařízení uživatelů a služeb od Microsoftu. Tyto účty jsou určené pro osobní použití.
* **Pracovní nebo školní účty.** Tyto účty se spravují společností Microsoft jménem organizace, které používají Azure Active Directory. Tyto účty se používají pro přihlášení k Office 365 a dalším firemním službám od Microsoftu.

Microsoft, které pracovní nebo školní účty jsou obvykle přiřazených koncovým uživatelům (zaměstnanci, studenty, federálním zaměstnancům) podle jejich organizace (společnosti, škola, státním úřadu). Tyto účty standardní přímo v cloudu (na platformě Azure AD) nebo do služby Azure AD synchronizované z místního adresáře, jako je například Windows Server Active Directory. Společnost Microsoft se *správce* pracovní nebo školní účty, ale účty jsou vlastněné a řídit ho organizace.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Odkazující na účty Azure AD v aplikaci

Microsoft nezveřejňuje koncovým uživatelům Azure nebo služby Active Directory jmény, značkami a ani byste měli.

* Po přihlášení uživatele pomocí názvu a loga co nejvíc organizace. Toto je lepší než při použití obecných výrazů jako "vaší organizace."
* Pokud nejste přihlášení uživatelů, přečtěte si ke svým účtům jako "pracovní nebo školní účty" a sdělit, že Microsoft spravuje tyto účty pomocí loga společnosti Microsoft. Nepoužívejte jako "účet organizace," "obchodní účet" nebo "podnikový účet," který mást uživatele.

## <a name="user-account-pictogram"></a>Piktogram účtu uživatele

Ve starší verzi těchto pokynů je doporučeno použití piktogram "blue oznámení" BADGE "". Na základě názorů uživatelů a vývojář jsme teď doporučujeme používat Microsoft logo místo. Logo Microsoftu pomůže uživatelům pomoct pochopit, že se můžete znovu použít účet, že při používání s Office 365 nebo jiné Microsoft služby pro firmy pro přihlášení do vaší aplikace.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Registrace a přihlášení pomocí Azure AD

Vaše aplikace může představovat samostatné cesty pro registraci a přihlaste se a v následujících částech visual pokyny pro oba scénáře.

**Pokud vaše aplikace podporuje (například bezplatné zkušební verze nebo freemium modelu) registrace koncového uživatele**: můžete zobrazit **přihlášení** tlačítko, které umožňuje uživatelům přístup k vaší aplikaci pomocí svého pracovního účtu nebo jejich osobní účet. Azure AD se zobrazí výzva k povolení spuštění při prvním přístupu k vaší aplikaci.

**Pokud vaše aplikace vyžaduje oprávnění, která jenom správci můžou udělit souhlas s, nebo pokud vaše aplikace vyžaduje organizační licencování**: oddělují admin pořízení z přihlášení uživatele. **Kliknutím na tlačítko "získat tuto aplikaci"** přesměruje správce přihlásit a požádejte ho, chcete-li udělit souhlas jménem uživatelů ve své organizaci, který má výhodu v podobě potlačení výzev k udělení souhlasu koncových uživatelů do vaší aplikace.

## <a name="visual-guidance-for-app-acquisition"></a>Vizuální pokyny k získání aplikací

Odkaz na vaši "stáhněte si aplikaci" musí přesměrovat uživatele na udělení přístupu Azure AD (Povolit) stránky, aby správce ve vaší organizaci autorizovat aplikaci tak, aby přístup k datům organizace, která je hostovaná společností Microsoft. V jsou popsány podrobnosti o tom, jak požádat o přístup [integrace aplikací s Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md) článku.

Až do vaší aplikace udělit souhlas správce, můžete zvolit se přidá do prostředí Spouštěč aplikací Office 365 svým uživatelům (přístupné z dlaždice a z [ https://portal.office.com/myapps ](https://portal.office.com/myapps)). Pokud chcete inzerovat tuto funkci, můžete použít termíny jako "Přidání této aplikace pro vaši organizaci" a zobrazí tlačítko jako v následujícím příkladu:

![Typy aplikací a scénářů](./media/howto-add-branding-in-azure-ad-apps/add-to-my-org.png)

Doporučujeme však, že napíšete vysvětlující text, aniž byste museli spoléhat na tlačítkách. Příklad:

> *Pokud už používáte Office 365 nebo jiné obchodní služby od Microsoftu, můžete udělit < your_app_name > přístup k datům vaší organizace. To vám umožní uživatelům přístup k < your_app_name > pomocí svých existujících účtů v práci.*

Stažení oficiální logo společnosti Microsoft pro použití ve vaší aplikaci, klikněte pravým tlačítkem na ta, kterou chcete použít a uložit ho do počítače.

| Prostředek                                | Formát PNG | Formát SVG |
| ------------------------------------ | ---------- | ---------- |
| Logo Microsoftu  | ![Microsoft logo PNG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_MSSymbol_19.png) | ![Microsoft logo SVG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_MSSymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Vizuální pokyny k přihlášení

Vaše aplikace zobrazit tlačítko přihlásit, který přesměruje uživatele na přihlašovací koncového bodu, který odpovídá protokol, který používáte pro integraci s Azure AD. Následující oddíl obsahuje podrobné informace na toto tlačítko by měl vypadat.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogram a "Přihlásit se účtem Microsoft."

Je přidružení logo společnosti Microsoft a podmínky "Přihlašování pomocí Microsoft", které jednoznačně představují mezi jinými poskytovateli identity Azure AD vaší aplikace mohou podporovat. Pokud nemáte k dispozici dostatek místa pro "Sign in with Microsoft", je v pořádku pro zkrácení "Přihlásit." Světlé a tmavé barevné schéma můžete použít pro tlačítka.

Následující diagram znázorňuje, že společnost Microsoft doporučuje redlines při použití prostředky s vaší aplikací. Redlines platí pro "Přihlašování pomocí Microsoft" nebo kratší verzi "Sign in".

![Přihlásit se účtem Microsoft redlines](./media/howto-add-branding-in-azure-ad-apps/Sign-in-with-Microsoft-redlines.png)

Stažení oficiální Image pro použití ve vaší aplikaci, klikněte pravým tlačítkem na ta, kterou chcete použít a uložit ho do počítače.

| Prostředek                                | Formát PNG | Formát SVG |
| ------------------------------------ | ---------- | ---------- |
| Přihlásit se účtem Microsoft (tmavý motiv)  | ![Přihlaste se tlačítko tmavý motiv PNG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_dark.png) | ![Přihlaste se pomocí Microsoft tlačítko tmavý motiv SVG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_dark.svg) |
| Přihlásit se účtem Microsoft (světlý motiv) | ![Přihlaste se tlačítko světlý motiv PNG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_light.png) | ![Přihlaste se pomocí Microsoft tlačítko světlý motiv SVG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_light.svg) |
| Přihlaste se (tmavý motiv)                 | ![Stručně řečeno podepsat tlačítko tmavý motiv PNG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_dark_short.png) | ![Stručně řečeno podepsat tlačítko tmavý motiv SVG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_dark_short.svg) |
| Přihlaste se (světlý motiv)                | ![Stručně řečeno podepsat tlačítko světlý motiv PNG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_light_short.png) | ![Stručně řečeno podepsat tlačítko světlý motiv SVG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_light_short.svg) |


## <a name="branding-dos-and-donts"></a>Přizpůsobení prostředí značce ubírají pro a proti

**PROVEĎTE** použít "pracovní nebo školní účet" Další vysvětlení usnadňují koncovým uživatelům rozpoznat, jestli se může používat v kombinaci s tlačítko "Přihlašování pomocí Microsoft". **Není** použít jiné termíny jako "účet podnikové", "obchodní účet" nebo "podnikový účet."

**Není** použijte "ID pro Office 365" nebo "Azure ID". Office 365 je také název příjemce nabídky z Microsoftu a který se nepoužívá pro ověřování Azure AD.

**Není** změnit logo společnosti Microsoft.

**Není** zpřístupňují koncovým uživatelům značky Azure nebo služby Active Directory. Nevadí, ale chcete-li tyto podmínky použití s vývojáři, IT specialisté a správci.

## <a name="navigation-dos-and-donts"></a>Navigace ubírají pro a proti

**PROVEĎTE** poskytují způsob, jakým uživatelé se odhlásit a přepněte do jiného uživatelského účtu. I když většina lidí má jeden osobní účet od společnosti Microsoft nebo Facebook/Google nebo Twitter, lidé jsou často spojeny s více než jedné organizace. Podpora pro více přihlášených uživatelů bude dostupný brzy.
