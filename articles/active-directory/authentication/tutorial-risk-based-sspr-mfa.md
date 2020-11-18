---
title: Ochrana přihlášení uživatelů na základě rizik v Azure Active Directory
description: V tomto kurzu se dozvíte, jak povolit Azure Identity Protection k ochraně uživatelů při zjištění rizikového přihlášení na svém účtu.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: a120e015bd8ca38e32bd8cbef1fd48f4caef8e44
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837800"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-ad-multi-factor-authentication-or-password-changes"></a>Kurz: použití zjišťování rizik pro přihlášení uživatelů k aktivaci Multi-Factor Authentication nebo změny hesla služby Azure AD

Pokud chcete chránit své uživatele, můžete nakonfigurovat zásady na základě rizik v Azure Active Directory (Azure AD), které automaticky reagují na rizikové chování. Zásady Azure AD Identity Protection můžou automaticky blokovat pokus o přihlášení nebo vyžadovat další akci, třeba vyžadovat změnu hesla nebo zobrazit výzvu pro Azure AD Multi-Factor Authentication. Tyto zásady pracují se stávajícími zásadami podmíněného přístupu Azure AD jako vyšší úrovně ochrany pro vaši organizaci. Uživatelé nemusí nikdy aktivovat rizikové chování v jedné z těchto zásad, ale vaše organizace je chráněná, pokud je proveden pokus o ohrožení zabezpečení.

> [!IMPORTANT]
> V tomto kurzu se dozvíte, jak povolit Multi-Factor Authentication služby Azure AD založené na rizicích.
>
> Pokud váš IT tým nepovolil možnost používat Multi-Factor Authentication Azure AD nebo máte problémy při přihlašování, obraťte se na helpdesk, kde najdete další pomoc.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pochopení dostupných zásad pro Azure AD Identity Protection
> * Povolit registraci Multi-Factor Authentication služby Azure AD
> * Zapnutí změn hesla na základě rizikové události
> * Zapnutí vícefaktorového ověřování na základě rizikové události
> * Testování zásad na základě rizik pro pokusy o přihlášení uživatelů

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Funkční tenant Azure AD s povolenou aspoň Azure AD Premium P2 nebo zkušební licencí.
    * V případě potřeby [ho vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Účet s oprávněními *globálního správce* .
* Služba Azure AD konfigurovaná pro Samoobslužné resetování hesla a Azure AD Multi-Factor Authentication
    * V případě potřeby [dokončete kurz a povolte Azure AD SSPR](tutorial-enable-sspr.md).
    * V případě potřeby [dokončete kurz, abyste povolili Multi-Factor Authentication služby Azure AD](tutorial-enable-azure-mfa.md).

## <a name="overview-of-azure-ad-identity-protection"></a>Přehled Azure AD Identity Protection

Každý den shromažďuje a analyzuje společnost Microsoft biliónyy anonymních signálů jako součást pokusů o přihlášení uživatelů. Tyto signály vám pomohou sestavovat vzorce dobrého přihlašování uživatelů a identifikovat potenciální rizikové přihlašovací pokusy. Azure AD Identity Protection může kontrolovat pokusy o přihlášení uživatelů a provádět další akce, pokud dojde k podezřelému chování:

Azure AD Identity Protection detekci rizik se můžou aktivovat některé z následujících akcí:

* Uživatelé s nevrácenými přihlašovacími údaji
* Přihlášení z anonymních IP adres.
* Nemožná cesta do neobvyklých umístění.
* Přihlášení z nakažených zařízení.
* Přihlášení z IP adres s podezřelou aktivitou.
* Přihlášení z neznámých umístění.

V Azure AD Identity Protection jsou k dispozici následující tři zásady pro ochranu uživatelů a reakci na podezřelou aktivitu. Můžete se rozhodnout zapnout nebo vypnout vynucení zásad, vybrat uživatele nebo skupiny, pro které se má zásada použít, a rozhodnout, jestli chcete blokovat přístup při přihlášení, nebo se dotázat na další akci.

* Zásady rizik uživatelů
    * Identifikuje a reaguje na uživatelské účty, které by mohly ohrozit přihlašovací údaje. Může vyzvat uživatele k vytvoření nového hesla.
* Zásady rizik přihlašování
    * Identifikuje a reaguje na podezřelé pokusy o přihlášení. Může uživateli vyzvat k poskytnutí dalších forem ověřování pomocí Multi-Factor Authentication služby Azure AD.
* Zásady registrace MFA
    * Zajistí, aby se uživatelé zaregistrovali Multi-Factor Authentication služby Azure AD. Pokud se zásady rizik přihlašování zobrazí pro MFA, musí být uživatel už zaregistrovaný pro Azure AD Multi-Factor Authentication.

Když zapnete zásadu pro uživatele nebo přihlašování rizikových zásad, můžete také zvolit prahovou hodnotu pro úroveň rizika – *nízká a vyšší*, *střední a vyšší* nebo *Vysoká*. Tato flexibilita vám umožní určit, jak agresivní mají být vynucovány jakékoli ovládací prvky pro podezřelé přihlašovací události.

Další informace o Azure AD Identity Protection najdete v tématu [co je Azure AD Identity Protection?](../identity-protection/overview-identity-protection.md)

## <a name="enable-mfa-registration-policy"></a>Povolit zásady registrace MFA

Azure AD Identity Protection obsahuje výchozí zásadu, která vám může pomáhat získat uživatele zaregistrované pro Azure AD Multi-Factor Authentication. Pokud k ochraně přihlašovacích událostí použijete další zásady, budete potřebovat uživatele, kteří už mají zaregistrovaný účet pro MFA. Když tyto zásady povolíte, nevyžadují, aby uživatelé v každé události přihlášení prováděli MFA. Zásada pouze kontroluje stav registrace uživatele a v případě potřeby požaduje, aby se zaregistroval předem.

Doporučuje se povolit zásady registrace MFA pro uživatele, u kterých chcete povolit další zásady Azure AD Identity Protection. Pokud chcete tuto zásadu povolit, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce.
1. Vyhledejte a vyberte **Azure Active Directory**, vyberte **zabezpečení** a potom v části nabídky *chránit* zvolte možnost **ochrana identity**.
1. V nabídce na levé straně vyberte **zásadu registrace MFA** .
1. Ve výchozím nastavení platí zásady pro *všechny uživatele*. V případě potřeby vyberte **přiřazení** a pak zvolte uživatele nebo skupiny, pro které chcete zásadu použít.
1. V části *ovládací prvky* vyberte **přístup**. Ujistěte se, že je zaškrtnuta možnost *vyžadovat registraci Azure AD MFA* , a pak zvolte **Vybrat**.
1. Nastavte **zásadu Vynutilit** na *zapnuto* a pak vyberte **Uložit**.

    ![Snímek obrazovky, jak vyžadovat, aby se uživatelé zaregistrovali pro vícefaktorové ověřování v Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>Povolit zásady rizik uživatelů pro změnu hesla

Microsoft spolupracuje při vyhledávání dvojic uživatelských jmen a hesel s výzkumnými pracovníky, orgány zajišťujícími vymáhání zákona, různými týmy zabezpečení v Microsoftu a dalšími důvěryhodnými zdroji. Když jedna z těchto párů odpovídá účtu ve vašem prostředí, můžete požádat o změnu hesla na základě rizika. Tato zásada a akce vyžaduje, aby uživatel před přihlášením aktualizoval heslo, aby se ujistil, že už dříve vystavené přihlašovací údaje nebudou fungovat.

Pokud chcete tuto zásadu povolit, proveďte následující kroky:

1. V nabídce na levé straně vyberte **zásady rizik uživatelů** .
1. Ve výchozím nastavení platí zásady pro *všechny uživatele*. V případě potřeby vyberte **přiřazení** a pak zvolte uživatele nebo skupiny, pro které chcete zásadu použít.
1. V části *podmínky* zvolte  **Vybrat podmínky > vyberte úroveň rizika** a pak zvolte *střední a vyšší*.
1. Klikněte na **Vybrat** a potom na **Hotovo**.
1. V části *přístup* vyberte **přístup**. Ujistěte se, že je zaškrtnuta možnost **umožnit přístup** a *vyžadovat změnu hesla* , a pak zvolte **Vybrat**.
1. Nastavte **zásadu Vynutilit** na *zapnuto* a pak vyberte **Uložit**.

    ![Snímek obrazovky s postupem povolení zásad rizik uživatelů v Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>Povolit zásady rizik přihlašování pro MFA

Většina uživatelů má normální chování, které je možné sledovat. Pokud spadají mimo tuto normu, může být rizikem, aby je bylo možné úspěšně přihlašovat. Místo toho můžete chtít tohoto uživatele zablokovat, nebo požádat, aby provedl službu Multi-Factor Authentication. Pokud uživatel úspěšně dokončí dotaz MFA, můžete zvážit platný pokus o přihlášení a udělit přístup k aplikaci nebo službě.

Pokud chcete tuto zásadu povolit, proveďte následující kroky:

1. V nabídce na levé straně vyberte **zásady rizik přihlašování** .
1. Ve výchozím nastavení platí zásady pro *všechny uživatele*. V případě potřeby vyberte **přiřazení** a pak zvolte uživatele nebo skupiny, pro které chcete zásadu použít.
1. V části *podmínky* zvolte  **Vybrat podmínky > vyberte úroveň rizika** a pak zvolte *střední a vyšší*.
1. Klikněte na **Vybrat** a potom na **Hotovo**.
1. V části *přístup* zvolte **vybrat ovládací prvek**. Ujistěte se, že je zaškrtnuta možnost **Povolení přístupu** a *vyžadovat službu Multi-Factor Authentication* , a pak zvolte **možnost vybrat**.
1. Nastavte **zásadu Vynutilit** na *zapnuto* a pak vyberte **Uložit**.

    ![Snímek obrazovky, jak povolit zásady pro rizikové přihlašování v Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>Testovat události rizikového podpisu

Většina přihlašovacích událostí uživatele neaktivuje zásady na základě rizik nakonfigurované v předchozích krocích. Uživateli se nikdy nezobrazí výzva k zadání dalších MFA nebo resetování hesla. Pokud jejich přihlašovací údaje zůstanou zabezpečené a jejich chování je konzistentní, jejich přihlašovací události budou úspěšné.

Chcete-li otestovat zásady Azure AD Identity Protection vytvořené v předchozích krocích, budete potřebovat způsob, jak simulovat rizikové chování nebo potenciální útoky. Postup provedení těchto testů se liší v závislosti na zásadách Azure AD Identity Protection, které chcete ověřit. Další informace o scénářích a krocích najdete v tématu [simulace detekce rizik v Azure AD Identity Protection](../identity-protection/howto-identity-protection-simulate-risk.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste dokončili testy a už nechcete mít povolené zásady na základě rizik, vraťte se ke každé zásadě, kterou chcete zakázat, a nastavte **zásadu Vynutilit** na *vypnuto*.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili zásady pro uživatele na základě rizik pro Azure AD Identity Protection. Naučili jste se:

> [!div class="checklist"]
> * Pochopení dostupných zásad pro Azure AD Identity Protection
> * Povolit registraci Multi-Factor Authentication služby Azure AD
> * Zapnutí změn hesla na základě rizikové události
> * Zapnutí vícefaktorového ověřování na základě rizikové události
> * Testování zásad na základě rizik pro pokusy o přihlášení uživatelů

> [!div class="nextstepaction"]
> [Další informace o Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)
