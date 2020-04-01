---
title: Povolení služby Azure Multi-Factor Authentication
description: V tomto kurzu se dozvíte, jak povolit Azure Vícefaktorové ověřování pro skupinu uživatelů a otestovat výzvu sekundárnífaktor během události přihlášení.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253eb23be03c1cc0f2abf4ad1fed734426dc287d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77154820"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>Kurz: Zabezpečení událostí přihlášení uživatele pomocí azure multifaktorového ověřování

Vícefaktorové ověřování (MFA) je proces, při kterém je uživatel vyzván během události přihlášení pro další formy identifikace. Tato výzva by mohla být zadat kód na svém mobilním telefonu nebo poskytnout otisk prstu skenování. Pokud požadujete druhou formu ověřování, zabezpečení se zvýší, protože tento další faktor není něco, co je pro útočníka snadné získat nebo duplikovat.

Azure Multi-Factor ověřování a podmíněného přístupu zásady poskytují flexibilitu povolit vícefaktorové ověřování pro uživatele během konkrétních událostí přihlášení.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření zásad podmíněného přístupu, které povolí vícefaktorové ověřování Azure pro skupinu uživatelů
> * Konfigurace podmínek zásad, které výzvu pro vícefaktorové a finanční služby
> * Testování procesu MFA jako uživatele

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Pracovní klient Azure AD s povolenou licencí Azure AD Premium nebo zkušební licencí.
    * V případě potřeby [si jej vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Účet s oprávněními *globálního správce.*
* Uživatel bez oprávnění správce s heslem, které znáte, například *testuser*. Testování prostředí Azure Vícefaktorové ověřování pomocí tohoto účtu v tomto kurzu.
    * Pokud potřebujete vytvořit uživatele, přečtěte [si úvodní příručku: Přidání nových uživatelů do služby Azure Active Directory](../add-users-azure-active-directory.md).
* Skupina, jejíž členem je uživatel, jehož členem není správce, například *skupina MFA-Test-Group*. V tomto kurzu povolíte azure multifaktorové ověřování pro tuto skupinu.
    * Pokud potřebujete vytvořit skupinu, přečtěte si, jak [vytvořit skupinu a přidat členy ve službě Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásad podmíněného přístupu

Doporučený způsob, jak povolit a používat azure vícefaktorové ověřování je s zásady podmíněného přístupu. Podmíněný přístup umožňuje vytvářet a definovat zásady, které reagují na události přihlášení a požadují další akce před tím, než je uživateli udělen přístup k aplikaci nebo službě.

![Přehledový diagram fungování podmíněného přístupu k zabezpečení procesu přihlášení](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Zásady podmíněného přístupu mohou být podrobné a specifické s cílem posílit produktivitu uživatelů kdekoli a kdykoli, ale také chránit vaši organizaci. V tomto kurzu vytvoříme základní zásady podmíněného přístupu, které vyzve k mfa, když se uživatel přihlásí k portálu Azure. V pozdějším kurzu v této řadě nakonfigurujete Azure Multi-Factor Authentication pomocí zásad podmíněného přístupu založeného na rizicích.

Nejprve vytvořte zásady podmíněného přístupu a přiřaďte testovací skupinu uživatelů následujícím způsobem:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu s oprávněními *globálního správce.*
1. Vyhledejte a vyberte **službu Azure Active Directory**a v nabídce na levé straně zvolte **Zabezpečení.**
1. Vyberte **podmíněný přístup**, pak zvolte **+ Nové zásady**.
1. Zadejte název zásady, například *Pilot vícefaktoru*.
1. V části **Přiřazení**zvolte **Uživatelé a skupiny**a potom přepínací tlačítko **Vybrat uživatele a skupiny.**
1. Zaškrtněte **políčko Pro uživatele a skupiny**a **pak vyberte** procházet dostupné uživatele a skupiny Azure AD.
1. Vyhledejte a vyberte skupinu Azure AD, například *Skupinu testů více faktorů pro více faktorech*, a pak zvolte **Vybrat**.

    [![](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png "Select your Azure AD group to use with the Conditional Access policy")](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Chcete-li pro skupinu použít zásadu podmíněného přístupu, vyberte **Možnost Hotovo**.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Konfigurace podmínek pro vícefaktorové ověřování

Když jsou vytvořeny zásady podmíněného přístupu a je přiřazena testovací skupina uživatelů, definujte teď cloudové aplikace nebo akce, které zásadu aktivují. Tyto cloudové aplikace nebo akce jsou scénáře, které se rozhodnete vyžadovat další zpracování, například výzvu k příkazu mfa. Můžete se například rozhodnout, že přístup k finanční aplikaci nebo použití nástrojů pro správu vyžaduje jako další výzvu ověření.

V tomto kurzu nakonfigurujte zásady podmíněného přístupu tak, aby vyžadovaly vícefaktorové ověřování, když se uživatel přihlásí k portálu Azure.

1. Vyberte **Cloudové aplikace nebo akce**. Zásady podmíněného přístupu můžete použít na *všechny cloudové aplikace* nebo *Select Apps*. Chcete-li zajistit flexibilitu, můžete také vyloučit určité aplikace ze zásad.

    V tomto kurzu zvolte na stránce *Zahrnout* **přepínací** tlačítko Vybrat aplikace.

1. Zvolte **Vybrat**a projděte seznam dostupných událostí přihlášení, které lze použít.

    Pro účely tohoto kurzu zvolte **Microsoft Azure Management,** aby se zásady vztahovaly na události přihlášení na webu Azure Portal.

1. Chcete-li použít vybrané aplikace, zvolte **Vybrat**a potom **Hotovo**.

    ![Vyberte aplikaci Microsoft Azure Management, kterou chcete zahrnout do zásad podmíněného přístupu.](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Ovládací prvky přístupu umožňují definovat požadavky na uživatele, kterému má být udělen přístup, jako je potřeba schválené klientské aplikace nebo použití zařízení, které je připojené hybridní Azure AD. V tomto kurzu nakonfigurujte ovládací prvky přístupu tak, aby vyžadovaly vícefaktorové informace během události přihlášení na portál Azure.

1. V části *Access controls*zvolte **Grant**, potom zkontrolujte, jestli je vybrané přepínací tlačítko **Udělit přístup.**
1. Zaškrtněte políčko **Vyžadovat vícefaktorové ověřování**a pak zvolte **Vybrat**.

Zásady podmíněného přístupu lze nastavit na *pouze sestavu,* pokud chcete zjistit, jak by konfigurace měla vliv na uživatele, nebo *Vypnuto,* pokud nechcete, aby zásady použití právě teď. Jako testovací skupina uživatelů byla zaměřena na tento kurz, umožňuje povolit zásady a pak otestovat Azure Multi-Factor Authentication.

1. Nastavte přepínač *Povolit zásady* na **Zapnuto**.
1. Chcete-li použít zásadu podmíněného přístupu, vyberte **vytvořit**.

## <a name="test-azure-multi-factor-authentication"></a>Testování služby Azure Multi-Factor Authentication

Podívejme se na zásady podmíněného přístupu a azure vícefaktorové ověřování v akci. Nejprve se přihlaste k prostředku, který nevyžaduje vícefaktorové řízení takto:

1. Otevření nového okna prohlížeče v režimu InPrivate nebo inkognito a procházení[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Přihlaste se pomocí testovacího uživatele, který není správcem, například *testuser*. Neexistuje žádná výzva k dokončení Vícefaktorové finanční hod.
1. Zavřete okno prohlížeče.

Teď se přihlaste na portál Azure. Vzhledem k tomu, že portál Azure byl nakonfigurován v zásadách podmíněného přístupu tak, aby vyžadoval další ověření, zobrazí se výzva k vícefaktorovému ověřování Azure.

1. Otevřete nové okno prohlížeče v režimu InPrivate [https://portal.azure.com](https://portal.azure.com)nebo inkognito a přejděte na .
1. Přihlaste se pomocí testovacího uživatele, který není správcem, například *testuser*. Musíte se zaregistrovat a používat Azure Multi-Factor Authentication. Podle pokynů dokončete proces a ověřte, zda jste se úspěšně přihlásili k portálu Azure.

    ![Postupujte podle pokynů prohlížeče a potom na registrované matné výzvě k ověřování, abyste se přihlásili.](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Zavřete okno prohlížeče.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nechcete používat zásady podmíněného přístupu k povolení azure vícefaktorového ověřování nakonfigurovaného jako součást tohoto kurzu, odstraňte zásady pomocí následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyhledejte a vyberte **službu Azure Active Directory**a v nabídce na levé straně zvolte **Zabezpečení.**
1. Vyberte **Podmíněný přístup**, vyberte zásadu, kterou jste vytvořili, například *Pilot vícefaktoru.*
1. Zvolte **Odstranit**, potvrďte, že chcete zásadu odstranit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili Azure Multi-Factor Authentication pomocí zásad podmíněného přístupu pro vybranou skupinu uživatelů. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření zásad podmíněného přístupu pro povolení vícefaktorového ověřování Azure pro skupinu uživatelů Azure AD
> * Konfigurace podmínek zásad, které výzvu pro vícefaktorové a finanční služby
> * Testování procesu MFA jako uživatele

> [!div class="nextstepaction"]
> [Povolení zpětného zápisu hesla pro samoobslužné resetování hesla (SSPR)](tutorial-enable-writeback.md)
