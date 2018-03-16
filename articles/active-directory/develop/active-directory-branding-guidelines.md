---
title: Branding pokyny pro aplikace | Microsoft Docs
description: "Komplexní pokyny k prostředkům orientovaných na vývojáře pro Azure Active Directory"
services: active-directory
documentationcenter: dev-center-name
author: skwan
manager: mtillman
editor: 
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: skwan
ms.custom: aaddev
ms.openlocfilehash: e5ce970913d767dbf6b13381cf18c1f7b05d252f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="branding-guidelines-for-applications"></a>Branding pokyny pro aplikace
Tento článek popisuje zásadami značky, který byste měli použít při vývoji aplikací s Azure Active Directory (Azure AD). Tyto pokyny vám pomůže nasměrovat vašim zákazníkům, když chtějí použít svůj pracovní nebo školní účet, spravované ve službě Azure AD, nebo osobní účet pro registrace a přihlášení do aplikace.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Osobní účty oproti pracovní nebo školní účty od Microsoftu
Microsoft spravuje dva typy uživatelských účtů:

* **Osobní účty** (dříve označované jako účet Windows Live ID). Tyto účty vyjádření vztahu mezi *jednotlivých* uživatelé a společnosti Microsoft a používají se pro přístup k příjemce zařízení a služeb společnosti Microsoft. Tyto účty jsou určené pro osobní použití.
* **Pracovní nebo školní účty.** Tyto účty se spravují společností Microsoft jménem organizace, které používají Azure Active Directory. Tyto účty se používají k přihlášení k Office 365 a jiné obchodní služby společnosti Microsoft.

Microsoft, které pracovní nebo školní účty jsou obvykle přiřazených koncovým uživatelům (zaměstnanci, studenty, federálních zaměstnanců) jejich organizace (společnosti, školy, státní úřad). Tyto účty jsou standardní přímo v cloudu, v platformě Azure AD, nebo do Azure AD synchronizované z místní adresář, jako je například Windows Server Active Directory. Společnost Microsoft *správce* pracovní nebo školní účty, ale účty vlastní a ovládá organizace.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Odkazy na účty Azure AD ve vaší aplikaci
Microsoft nezveřejňuje koncovým uživatelům Azure nebo názvy brand služby Active Directory a ani jeden z nich by vám měl.

* Jakmile jsou uživatelé přihlášení, byste měli použít název a co nejvíce logo organizace. Toto je lepší, než pomocí obecné podmínky, jako je "vaší organizace."
* Když uživatelé nejsou přihlášení, se seznamte s účty jako "pracovní nebo školní účty" a použít logo společnosti Microsoft k předání informací o tom, že tyto účty se spravují společností Microsoft. Nepoužívejte podmínky jako "enterprise účet", "obchodní účet" nebo "podnikový účet," což uživatele mást.

## <a name="user-account-pictogram"></a>Piktogramu účtu uživatele
V dřívějších verzích těchto pokynů doporučujeme pomocí piktogram "blue badge". Na základě názorů uživatelů a vývojáře, teď doporučujeme použití Microsoft logo místo. To vám pomůže uživatelům pomoct pochopit, že se můžete znovu použít účet, že používají s Office 365 nebo jiné Microsoft služby pro firmy pro přihlášení do aplikace.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Registrací a přihlášení pomocí služby Azure AD
Aplikace může představovat samostatné cesty pro registraci a přihlaste se a v následujících částech visual pokyny pro oba scénáře.

**Pokud vaše aplikace podporuje koncového uživatele pro zápis (například bezplatné zkušební verze nebo freemium modelu)**: můžete zobrazit **přihlášení** tlačítko, které umožňuje uživatelům přístup k vaší aplikaci pomocí svého pracovního účtu nebo svého osobního účtu. Azure AD se zobrazí výzva k povolení spuštění prvním přístupu k aplikaci.

**Pokud vaše aplikace vyžaduje oprávnění, která mohou pouze správci souhlas, nebo pokud vaše aplikace vyžaduje organizační licencování**: oddělíte pořízení správce z přihlášení uživatele. **"Get tuto aplikaci" tlačítko** přesměruje správci přihlásit a požádejte je o udělení souhlasu jménem uživatelů ve své organizaci. To má výhodu v podobě potlačení výzvy souhlasu koncoví uživatelé do vaší aplikace.

## <a name="visual-guidance-for-app-acquisition"></a>Visual pokyny pro získání aplikace
Odkaz na vaši "získat aplikaci" musí přesměruje uživatele na udělení přístupu službě Azure AD (autorizovat) můžete povolit správce organizace autorizovat aplikaci, aby měla přístup k datům jejich organizace, která je hostovaná společností Microsoft. Podrobnosti o tom, jak požádat o přístup, jsou popsané v [integrace aplikací s Azure Active Directory](active-directory-integrating-applications.md) článku.

Po správci souhlas do vaší aplikace, můžete zvolit přidat do prostředí Spouštěč aplikace Office 365 své uživatele (přístupný z waffle a [ https://portal.office.com/myapps ](https://portal.office.com/myapps)). Pokud chcete inzerovat tato možnost, můžete použít podmínky jako "Přidat do vaší organizace tuto aplikaci" a zobrazit tlačítko takto:

![Typy aplikací a scénáře](./media/active-directory-branding-guidelines/add-to-my-org.png)

Doporučujeme však, že napíšete vysvětlující text, aniž byste museli spoléhat na tlačítka. Příklad:

> *Pokud už používáte Office 365 nebo jiné obchodní služby společnosti Microsoft, můžete udělit < your_app_name > přístup k datům vaší organizace. To vám umožní uživatelům přistupovat < your_app_name > pomocí stávajícího pracovního účtu.*
> 
> 

## <a name="visual-guidance-for-sign-in"></a>Visual pokyny pro přihlášení
Aplikace zobrazeno tlačítko přihlášení, který přesměruje uživatele na přihlašovací koncového bodu, který odpovídá na protokol, který používáte pro integraci se službou Azure AD. V následujícím oddílu najdete podrobnosti na co by měl vypadat toto tlačítko.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogramu a "Přihlásit se pomocí Microsoft"
Je přidružení logo společnosti Microsoft a podmínky "With v přihlašovací společnosti Microsoft", které jednoznačně představují mezi jiných poskytovatelů identit Azure AD vaší aplikace mohou podporovat. Pokud nemáte k dispozici dostatek místa pro "Přihlášení v with Microsoft", je to v pořádku tak, aby zkrátil k "Přihlášení."

![Typy aplikací a scénáře](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Typy aplikací a scénáře](./media/active-directory-branding-guidelines/sign-in-light.png)

Můžete taky tmavý barevné schéma pro tlačítka.

![Typy aplikací a scénáře](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Typy aplikací a scénáře](./media/active-directory-branding-guidelines/sign-in-dark.png)

Ke stažení těchto bitových kopií pro použití ve vaší aplikaci, klikněte pravým tlačítkem na ten, který chcete použít a uložte ho do počítače. 

## <a name="branding-dos-and-donts"></a>Doporučené značky a nedoporučené postupy
**PROVEĎTE** použít "pracovní nebo školní účet" Další vysvětlení, který pomůže koncovému uživateli rozpoznat, zda se můžete použít v kombinaci s tlačítko "Sign in with Microsoft". **Nemáte** použít jiné podmínky, jako je například "enterprise účet", "obchodní účet" nebo "podnikový účet."

**Nemáte** použijte "Office 365 ID" nebo "Azure ID". Office 365 je také název příjemce nabídky od Microsoftu, který nepoužívá Azure AD pro ověřování.

**Nemáte** změnit logo společnosti Microsoft.

**Nemáte** vystavit koncovým uživatelům služby Active Directory nebo Azure značky. Je to v pořádku ale používat tyto podmínky s vývojáři, IT specialisté a správci.

## <a name="navigation-dos-and-donts"></a>Navigace doporučené a nedoporučené postupy
**PROVEĎTE** poskytují způsob, jak uživatelům Odhlásit se a přepněte do jiného uživatelského účtu. I když většina lidí má jeden osobní účet od společnosti Microsoft nebo sítě Facebook/Google nebo Twitter, kteří jsou často přidružen více než jedné organizace. Podpora pro více přihlášených uživatelů bude brzo.

