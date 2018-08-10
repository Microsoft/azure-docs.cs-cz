---
title: Získejte Začínáme s Azure MFA v cloudu
description: Microsoft Azure Multi-Factor Authentication Začínáme s podmíněným přístupem
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: c2d0f14bca2b9ab062c61407479ab45a0104ff0a
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716259"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>Nasazení cloudového ověřování Azure Multi-Factor Authentication

Začínáme se službou Azure Multi-Factor Authentication (Azure MFA) je jednoduchý proces.

Než začnete, ujistěte se, že jsou splněné následující požadavky:

* Účet globálního správce ve vašem tenantovi Azure AD. Pokud potřebujete pomoc při dokončení tohoto kroku, najdete v našem článku [Začínáme s Azure AD](../get-started-azure-ad.md)
* Správné licence přiřazené uživatelům. Pokud potřebujete další informace najdete v tématu [jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md)

## <a name="choose-how-to-enable"></a>Zvolte, jak povolit

**Povolené zásady podmíněného přístupu** – tato metoda je popsána v tomto článku. Je nejflexibilnější způsob, jak zapnout dvoustupňové ověřování pro vaše uživatele. Povolení používat pouze zásady podmíněného přístupu se dá použít pro Azure MFA v cloudu a je Prémiová funkce služby Azure AD.

Povolená služba Azure AD Identity Protection - Tato metoda používá zásady rizik služby Azure AD Identity Protection a vyžadovat dvoustupňové ověřování jen podle rizika přihlašování pro všechny cloudové aplikace. Tato metoda vyžaduje licencování Azure Active Directory P2. Další informace o této metody můžete najít v [Azure Active Directory Identity Protection](../identity-protection/overview.md#risky-sign-ins).

Povolené tak, že změníte uživatele stav - Toto je tradiční metody pro vyžadováním dvoustupňového ověřování. Funguje to i Azure MFA v cloudu a Azure MFA serveru. Pomocí této metody vyžaduje, aby uživatelé dvoustupňové ověřování **pokaždé, když** přihlásit a přepisuje zásady podmíněného přístupu. Další informace o této metody můžete najít v [vyžadování dvoustupňového ověřování pro uživatele](howto-mfa-userstates.md)

> [!Note]
> Další informace o licencí a cenách najdete na [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) a [ověřování službou Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) stránkách s cenami.

## <a name="choose-authentication-methods"></a>Zvolit metody ověřování

Povolte alespoň jednu metodu ověřování pro uživatele na základě požadavků vaší organizace. Zjistíme, že pokud je povolená pro uživatele aplikaci Microsoft Authenticator poskytuje nejlepší uživatelské prostředí. Pokud je potřeba pochopit, jaké metody jsou k dispozici a jak je nastavit, naleznete v článku [jaké metody ověřování jsou](concept-authentication-methods.md).

## <a name="get-users-to-enroll"></a>Získat uživatele k registraci

Jakmile povolíte zásady podmíněného přístupu, uživatelé si bude muset zaregistrovat při příštím používají aplikaci chráněnou zásadami. Pokud povolíte zásadu vyžadující vícefaktorové ověřování pro všechny uživatele na všechny cloudové aplikace tato akce může způsobit, že starostí pro vaše uživatele a helpdesk. Doporučuje se uživatele požádejte, aby zaregistrovat metody ověřování, které jsou předem pomocí portálu pro registraci v [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Mnoho organizací zjišťuje, že vytváření plakáty, karty tabulky a e-mailové zprávy pomáhá dokázal(a).

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Povolení služby Multi-Factor Authentication s podmíněným přístupem

Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu globálního správce.

### <a name="choose-verification-options"></a>Vyberte možnosti ověřování

Než povolíte ověřování Azure Multi-Factor Authentication, vaše organizace musíte určit, jaké možnosti ověřování umožňují. Pro účely tohoto cvičení povolit volání na telefon a text zprávy na telefon jsou obecné možnosti, že většina je možné použít. Další informace o metodách ověřování a jejich využití najdete v článku, [co jsou metody ověřování?](concept-authentication-methods.md)

1. Přejděte do **Azure Active Directory**, **uživatelé**, **ověřování službou Multi-Factor Authentication**
   ![přístup k ověřování službou Multi-Factor Authentication portál v okně uživatelé Azure AD na webu Azure portal](media/howto-mfa-getstarted/users-mfa.png) 
2. V nové záložce, která se otevře procházením **nastavení služby**
3. V části **možnosti ověření**, zaškrtněte následující políčka pro metody dostupné pro uživatele
   * Telefonní hovor
   * Textová zpráva na telefon

   ![Konfigurace metody ověření v kartě nastavení služby Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. Klikněte na **Uložit**.
5. Zavřít **nastavení služby** kartu

### <a name="create-conditional-access-policy"></a>Vytvoření zásad podmíněného přístupu

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu globálního správce.
1. Přejděte na **Azure Active Directory** a potom na **Podmíněný přístup**.
1. Vyberte **Nová zásada**.
1. Zadejte výstižný název zásady
1. V části **uživatelů a skupin**
   * Na **zahrnout** kartu, vyberte **všichni uživatelé** přepínací tlačítko
   * DOPORUČENO: Na **vyloučit** kartu, zaškrtněte políčko u **uživatelů a skupin** a zvolte skupinu, která má být použit pro vyloučení, když uživatelé nebudou mít přístup ke své metody ověřování.
   * Klikněte na **Hotovo**.
1. V části **cloudové aplikace**, vyberte **všechny cloudové aplikace** přepínací tlačítko
   * VOLITELNĚ: Na **vyloučit** , vyberte cloudové aplikace, které vaše organizace nevyžaduje vícefaktorové ověřování pro.
   * Klikněte na **Hotovo**.
1. V části **podmínky** oddílu
   * VOLITELNĚ: Pokud jste povolili službu Azure Identity Protection, můžete vyhodnotit riziko přihlášení jako součást této zásady.
   * VOLITELNĚ: Pokud jste nakonfigurovali důvěryhodných umístění nebo pojmenovaná umístění, můžete zahrnout nebo vyloučit ze zásad těchto umístěních.
1. Přesvědčte se, že přepínač **Udělit přístup** v části **Udělení** je vybraný.
    * Zaškrtněte políčko **Vyžadovat vícefaktorové ověřování**.
    * Klikněte na **Vybrat**.
1. Oddíl **Relace** přeskočte.
1. **Zapněte** přepínač **Povolit zásadu**.
1. Klikněte na **Vytvořit**

![Vytvoření zásad podmíněného přístupu povolit MFA pro uživatele Azure portal v pilotní skupině](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>Testování služby Azure Multi-Factor Authentication

Potvrďte, že funguje své zásady podmíněného přístupu, otestujte přihlášení na prostředek, který by neměla vyžadovat vícefaktorové ověřování a potom na webu Azure portal, který vyžaduje vícefaktorové ověřování.

1. Otevřete nové okno prohlížeče v režimu InPrivate nebo Incognito a přejděte na adresu [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Přihlaste se pomocí testovacího účtu uživatele, který jste vytvořili jako součást požadavků tohoto článku, a všimněte si, že by se neměla zobrazit výzva k vícefaktorovému ověřování.
   * Zavřete okno prohlížeče
2. Otevřete nové okno prohlížeče v režimu InPrivate nebo Incognito a přejděte na adresu [https://portal.azure.com](https://portal.azure.com).
   * Přihlaste se pomocí testovacího účtu uživatele, který jste vytvořili jako součást požadavků tohoto článku, a všimněte si, že nyní by se výzva k vícefaktorovému ověřování zobrazit měla.
   * Zavřete okno prohlížeče

## <a name="next-steps"></a>Další postup

Blahopřejeme, jste nastavili Azure Multi-Factor Authentication v cloudu.

Chcete-li nakonfigurovat další nastavení, jako jsou důvěryhodné IP adresy, vlastní hlasové zprávy a upozornění na podvod, najdete v článku [nastavení konfigurace Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)

Informace o správě nastavení pro ověřování Azure Multi-Factor Authentication najdete v článku [spravovat uživatelská nastavení pomocí ověřování Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md)

[Povolit sblížené registraci pro resetování hesla pomocí samoobslužné služby Azure Multi-Factor Authentication a Azure AD](concept-registration-mfa-sspr-converged.md)
