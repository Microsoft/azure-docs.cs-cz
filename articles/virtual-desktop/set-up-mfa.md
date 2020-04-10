---
title: Nastavení vícefaktorového ověřování Azure pro Virtuální plochu Windows – Azure
description: Jak nastavit azure vícefaktorové ověřování pro zvýšení zabezpečení ve Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998481"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Nastavení služby Azure Multi-Factor Authentication

Klient systému Windows pro virtuální plochu systému Windows je vynikající volbou pro integraci virtuální plochy systému Windows do místního počítače. Pokud však nakonfigurujete účet Virtuální plochy systému Windows do klienta systému Windows, budete muset přijmout určitá opatření, abyste udrželi sebe a své uživatele v bezpečí.

Při prvním přihlášení se klient zeptá na vaše uživatelské jméno, heslo a Azure MFA. Za to, že při příštím přihlášení, klient si zapamatuje váš token z vaší podnikové aplikace Azure Active Directory (AD). Když vyberete **Zapamatovat**, uživatelé se mohou po restartování klienta přihlásit, aniž by museli znovu zadávat svá pověření.

I když je zapamatování přihlašovacích údajů pohodlné, může také způsobit, že nasazení ve scénářích Enterprise nebo osobních zařízeních budou méně bezpečná. Chcete-li chránit své uživatele, musíte se ujistit, že klient stále žádá o přihlašovací údaje Azure Multi-Factor Authentication (MFA). V tomto článku se zobrazí postup konfigurace zásad podmíněného přístupu pro virtuální plochu systému Windows, aby bylo možné toto nastavení povolit.

## <a name="prerequisites"></a>Požadavky

Zde je to, co budete potřebovat, abyste mohli začít:

- Přiřaďte všem uživatelům jednu z následujících licencí:
  - Microsoft 365 E3 nebo E5
  - Azure Active Directory Premium P1 nebo P2
  - Podniková mobilita + zabezpečení E3 nebo E5
- Skupina Azure Active Directory s uživateli přiřazenými jako členové skupiny.
- Povolte Azure MFA pro všechny uživatele. Další informace o tom, jak to provést, naleznete v [tématu Jak vyžadovat dvoustupňové ověření pro uživatele](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

>[!NOTE]
>Následující nastavení platí také pro [webového klienta Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Přihlásit se k zásadám podmíněného přístupu

1. Otevřete **službu Azure Active Directory**.

2. Přejděte na kartu **Všechny aplikace.** V rozevírací nabídce Typ aplikace vyberte **podnikové aplikace**a vyhledejte **klienta Virtuální plocha systému Windows**.

    ![Snímek obrazovky s kartou Všechny aplikace. Uživatel zadal do vyhledávacího panelu "klient a klient virtuální plochy systému Windows" a aplikace se objevila ve výsledcích hledání.](media/all-applications-search.png)

3. Vyberte **možnost Podmíněný přístup**.

    ![Snímek obrazovky zobrazující uživatele, který najedou kurzormyši nad kartu Podmíněný přístup.](media/conditional-access-location.png)

4. Vyberte **+ Nové zásady**.

   ![Snímek obrazovky stránky Podmíněného přístupu Uživatel najeví myší na nové tlačítko zásad.](media/new-policy-button.png)

5. Zadejte **název** **pravidla**a **vyberte** *název **skupiny,** kterou jste vytvořili v požadavcích.

6. Vyberte **vybrat**, pak vyberte **Hotovo**.

7. Dále otevřete **CloudOvé aplikace nebo akce**.

8. Na panelu **Vybrat** vyberte aplikaci **Windows Virtual Desktop** Enterprise.

    ![Snímek obrazovky se stránkou Cloudových aplikací nebo akcí Uživatel vybral aplikaci Virtuální plocha systému Windows zaškrtnutím políčka zaškrtnutí vedle ní. Vybraná aplikace je zvýrazněna červeně.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Na levé straně obrazovky byste měli také zobrazit vybranou aplikaci Klient virtuální plochy windows, jak je znázorněno na následujícím obrázku. Chcete-li, aby zásady fungovaly, potřebujete aplikace Windows Virtual Desktop desktop i Windows Virtual Desktop Client Enterprise.
    >
    > ![Snímek obrazovky se stránkou Cloudových aplikací nebo akcí Aplikace Windows Virtual Desktop a Windows Virtual Desktop Client jsou zvýrazněny červeně.](media/cloud-apps-enterprise-selected.png)

9. Vybrat **vybrat**

10. Dále otevřete **Grant** 

11. Vyberte **Vyžadovat vícefaktorové ověřování**, pak vyberte **Vyžadovat jeden z vybraných ovládacích prvků**.
   
    ![Snímek obrazovky stránky Grant. Je vybrána možnost Vyžadovat vícefaktorové ověřování.](media/grant-page.png)

    >[!NOTE]
    >Pokud máte zařízení zaregistrovaná mdm ve vaší organizaci a nechcete, aby se zobrazila výzva Vícefaktorové informace, můžete taky vybrat **Vyžadovat, aby zařízení bylo označeno jako vyhovující**.

12. Vyberte **možnost Relace**.

13. Nastavte **frekvenci přihlášení** na **Aktivní**a změňte její hodnotu na 1 **hodin .**

    ![Snímek obrazovky se stránkou Relace. Nabídka relace zobrazuje rozevírací nabídky frekvence přihlášení byly změněny na "1" a "Hodiny".](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Aktivní relace v prostředí Virtuální plocha systému Windows budou při změně zásad i nadále fungovat. Pokud se však odpojíte nebo odhlásíte, budete muset po 60 minutách znovu zadat své přihlašovací údaje. Při změně nastavení můžete prodloužit časový úsek, jak chcete (pokud je v souladu se zásadami zabezpečení vaší organizace).
    >
    >Výchozí nastavení je postupné okno 90 dnů, což znamená, že klient požádá uživatele, aby se znovu přihlásili, když se pokusí o přístup k prostředku poté, co jsou v počítači neaktivní po dobu 90 dnů nebo déle.

14. Povolte zásadu.

15. Chcete-li zásadu potvrdit, vyberte **vytvořit.**

Všechno je hotové. Neváhejte a otestujte zásady, abyste se ujistili, že seznam povolených letů funguje tak, jak má.
