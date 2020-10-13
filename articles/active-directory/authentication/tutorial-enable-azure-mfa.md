---
title: Povolení služby Azure Multi-Factor Authentication
description: V tomto kurzu se naučíte, jak povolit Azure Multi-Factor Authentication pro skupinu uživatelů a otestovat během přihlašovací události sekundární faktor.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddb252d7ba5534269d3da1e14064740690879816
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963801"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>Kurz: zabezpečení událostí přihlašování uživatelů pomocí Azure Multi-Factor Authentication

Multi-Factor Authentication (MFA) je proces, při kterém se uživateli zobrazí výzva k přihlášení v případě dalších forem identifikace. Tato výzva by mohla být zadat kód na své cellphone nebo poskytnout kontrolu otiskem prstu. Pokud požadujete druhou formu ověřování, zabezpečení se zvyšuje, protože tento přídavný faktor není něco, co by mohlo útočník snadno získat nebo duplikovat.

Azure Multi-Factor Authentication a zásady podmíněného přístupu umožňují uživatelům během specifických přihlašovacích událostí povolit MFA pro uživatele.

> [!IMPORTANT]
> V tomto kurzu se dozvíte správce, jak povolit Azure Multi-Factor Authentication.
>
> Pokud váš IT tým nepovolil možnost použít Azure Multi-Factor Authentication nebo máte problémy při přihlašování, obraťte se na helpdesk, kde najdete další pomoc.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření zásady podmíněného přístupu pro povolení služby Azure Multi-Factor Authentication pro skupinu uživatelů
> * Konfigurace podmínek zásad, které se dotazují na MFA
> * Testování procesu MFA jako uživatel

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Funkční tenant Azure AD s povolenou aspoň Azure AD Premium P1 nebo zkušební licencí.
    * V případě potřeby [ho vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Účet s oprávněními *globálního správce* .
* Uživatel bez oprávnění správce s heslem, které znáte, například *testuser*. Pomocí tohoto účtu v tomto kurzu otestujete prostředí Azure Multi-Factor Authentication pro koncové uživatele.
    * Pokud potřebujete vytvořit uživatele, přečtěte si téma [rychlý Start: přidání nových uživatelů do Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Skupina, pro kterou je uživatel bez oprávnění správce členem, jako je například *MFA-test-Group*. V tomto kurzu povolíte Azure Multi-Factor Authentication pro tuto skupinu.
    * Pokud potřebujete vytvořit skupinu, přečtěte si téma Postup [Vytvoření skupiny a přidání členů v Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Doporučený způsob, jak povolit a používat Azure Multi-Factor Authentication, jsou zásady podmíněného přístupu. Podmíněný přístup umožňuje vytvářet a definovat zásady, které reagují na události přihlášení a vyžadují další akce před tím, než se uživateli udělí přístup k aplikaci nebo službě.

![Diagram s přehledem toho, jak podmíněný přístup funguje k zabezpečení procesu přihlašování](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Zásady podmíněného přístupu můžou být podrobné a specifické s cílem umožnit uživatelům, aby byli produktivní všude a kdykoli, ale také chránit vaši organizaci. V tomto kurzu vytvoříme základní zásady podmíněného přístupu, které se zobrazí, když se uživatel přihlásí k Azure Portal. V pozdějším kurzu této série nakonfigurujete Azure Multi-Factor Authentication pomocí zásad podmíněného přístupu na základě rizika.

Nejdřív vytvořte zásadu podmíněného přístupu a přiřaďte svou testovací skupinu uživatelů následujícím způsobem:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu s oprávněními *globálního správce* .
1. Vyhledejte a vyberte **Azure Active Directory**a pak v nabídce na levé straně zvolte **zabezpečení** .
1. Vyberte **podmíněný přístup**a pak zvolte **+ Nová zásada**.
1. Zadejte název zásady, jako je například *MFA pilot*.
1. V části **přiřazení**zvolte **Uživatelé a skupiny**a pak přepínač **Vybrat uživatele a skupiny** .
1. Zaškrtněte políčko pro **uživatele a skupiny**a pak **Vyberte možnost** procházení dostupných uživatelů a skupin Azure AD.
1. Vyhledejte a vyberte skupinu Azure AD, například *MFA-test-Group*, a pak zvolte **Vybrat**.

    [![Vyberte skupinu Azure AD, kterou chcete použít se zásadami ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) podmíněného přístupu.](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Pokud chcete pro skupinu použít zásady podmíněného přístupu, vyberte **Hotovo**.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Konfigurace podmínek pro Multi-Factor Authentication

Pomocí vytvořených zásad podmíněného přístupu a testovací skupiny uživatelů teď definujte cloudové aplikace nebo akce, které tyto zásady aktivují. Tyto cloudové aplikace nebo akce jsou scénáře, které se rozhodnete vyžadovat další zpracování, například dotazování na MFA. Můžete se třeba rozhodnout, že přístup k finanční aplikaci nebo použití nástrojů pro správu vyžaduje jako další výzvu k ověření.

V tomto kurzu nakonfigurujte zásady podmíněného přístupu tak, aby vyžadovaly MFA, když se uživatel přihlásí k Azure Portal.

1. Vyberte **Cloudové aplikace nebo akce**. Zásady podmíněného přístupu můžete použít pro *všechny cloudové aplikace* nebo pro *Výběr aplikací*. K zajištění flexibility můžete z těchto zásad taky vyloučit určité aplikace.

    Pro tento kurz na stránce *zahrnutí* klikněte na přepínač **vybrat aplikace** .

1. Zvolte **Vybrat**a pak procházejte seznamem dostupných přihlašovacích událostí, které se dají použít.

    V tomto kurzu vyberte **Microsoft Azure Management** , takže zásada platí pro události přihlášení Azure Portal.

1. Pokud chcete použít vybrané aplikace, klikněte na **Vybrat**a potom na **Hotovo**.

    ![Vyberte aplikaci pro správu Microsoft Azure, kterou chcete zahrnout do zásad podmíněného přístupu.](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Řízení přístupu vám umožní definovat požadavky na udělení přístupu uživateli, jako je třeba potřeba schválené klientské aplikace nebo použití zařízení, které je připojené k hybridní službě Azure AD. V tomto kurzu nakonfigurujte ovládací prvky přístupu tak, aby během přihlašovací události pro Azure Portal vyžadovaly MFA.

1. V části *řízení přístupu*zvolte **udělit**a pak se ujistěte, že je vybrán přepínač **udělit přístup** .
1. Zaškrtněte políčko pro **vyžadovat vícefaktorové ověřování**a pak zvolte **Vybrat**.

Zásady podmíněného přístupu se dají nastavit na *sestavu jenom* v případě, že se chcete podívat, jak by konfigurace ovlivnila uživatele, nebo pokud nechcete, aby zásady použití byly v tuto chvíli *vypnuté* . Jako testovací skupina uživatelů cílících na tento kurz umožňuje povolit zásadu a pak otestovat Multi-Factor Authentication Azure.

1. Nastavte přepínač *Povolit zásady* na **zapnuto**.
1. Pokud chcete zásady podmíněného přístupu použít, vyberte **vytvořit**.

## <a name="test-azure-multi-factor-authentication"></a>Testování služby Azure Multi-Factor Authentication

Pojďme se podívat na vaše zásady podmíněného přístupu a Azure Multi-Factor Authentication v akci. Nejprve se přihlaste k prostředku, který nepotřebuje vícefaktorové ověřování, následovně:

1. Otevřete nové okno prohlížeče v režimu InPrivate nebo anonymním a přejděte na [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Přihlaste se pomocí testovacího uživatele bez správce, jako je například *testuser*. Pro dokončení vícefaktorového ověřování není k dispozici žádná výzva.
1. Zavřete okno prohlížeče.

Teď se přihlaste k Azure Portal. Protože Azure Portal byl nakonfigurován v zásadách podmíněného přístupu, aby vyžadovala dodatečné ověření, zobrazí se výzva Azure Multi-Factor Authentication.

1. Otevřete nové okno prohlížeče v režimu InPrivate nebo anonymním a přejděte na [https://portal.azure.com](https://portal.azure.com) .
1. Přihlaste se pomocí testovacího uživatele bez správce, jako je například *testuser*. Je nutné, abyste se zaregistrovali a mohli používat Azure Multi-Factor Authentication. Podle pokynů dokončete proces a ověřte, zda jste se úspěšně přihlásili k Azure Portal.

    ![Postupujte podle pokynů v prohlížeči a potom se přihlaste k zaregistrovanému dotazu na službu Multi-Factor Authentication.](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Zavřete okno prohlížeče.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nechcete zásady podmíněného přístupu povolit, aby se Azure Multi-Factor Authentication nakonfiguroval jako součást tohoto kurzu, odstraňte tyto zásady pomocí následujících kroků:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Vyhledejte a vyberte **Azure Active Directory**a pak v nabídce na levé straně zvolte **zabezpečení** .
1. Vyberte **podmíněný přístup**a pak vyberte zásadu, kterou jste vytvořili, jako je například *MFA pilot* .
1. Zvolte **Odstranit**a pak potvrďte, že chcete zásadu odstranit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili Azure Multi-Factor Authentication používáním zásad podmíněného přístupu pro vybranou skupinu uživatelů. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření zásady podmíněného přístupu pro povolení služby Azure Multi-Factor Authentication pro skupinu uživatelů Azure AD
> * Konfigurace podmínek zásad, které se dotazují na MFA
> * Testování procesu MFA jako uživatel

> [!div class="nextstepaction"]
> [Povolení zpětného zápisu hesla pro Samoobslužné resetování hesla (SSPR)](./tutorial-enable-sspr-writeback.md)