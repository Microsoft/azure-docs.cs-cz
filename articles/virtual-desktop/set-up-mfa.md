---
title: Nastavení Azure Multi-Factor Authentication pro virtuální počítače s Windows – Azure
description: Jak nastavit Azure Multi-Factor Authentication pro zvýšené zabezpečení na virtuálním počítači s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998481"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Nastavení služby Azure Multi-Factor Authentication

Klient Windows pro virtuální počítače s Windows je vynikající volbou pro integraci virtuálního klienta s Windows s vaším místním počítačem. Když ale konfigurujete účet virtuální plochy Windows na klienta Windows, budete muset provést určitá opatření, abyste měli jistotu, že sami a uživatelé budou v bezpečí.

Po prvním přihlášení si klient vyžádá vaše uživatelské jméno, heslo a Azure MFA. Po tomto okamžiku si klient zapamatuje váš token z vaší podnikové aplikace Azure Active Directory (AD). Když vyberete možnost **Zapamatovat si mě**, uživatelé se můžou po restartování klienta přihlásit, aniž by museli znovu zadat svoje přihlašovací údaje.

I když je zapamatování přihlašovacích údajů pohodlné, může také provádět nasazení v podnikových scénářích nebo na osobních zařízeních méně zabezpečená. Pokud chcete chránit své uživatele, musíte se ujistit, že klient uchovává dotaz na přihlašovací údaje pro Azure Multi-Factor Authentication (MFA). V tomto článku se dozvíte, jak nakonfigurovat zásady podmíněného přístupu pro virtuální plochu Windows, aby toto nastavení bylo povolené.

## <a name="prerequisites"></a>Požadavky

Tady je přehled toho, co budete potřebovat k zahájení práce:

- Přiřaďte všem uživatelům jednu z následujících licencí:
  - Microsoft 365 E3 nebo E5
  - Azure Active Directory Premium P1 nebo P2
  - Enterprise Mobility + Security E3 nebo E5
- Skupina Azure Active Directory s vašimi uživateli přiřazenými jako členové skupiny.
- Povolte Azure MFA pro všechny uživatele. Další informace o tom, jak to provést, najdete v tématu [jak pro uživatele vyžadovat dvoustupňové ověřování](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

>[!NOTE]
>Následující nastavení platí také pro [webového klienta virtuální plochy systému Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Přihlášení k zásadám podmíněného přístupu

1. Otevřete **Azure Active Directory**.

2. Přejít na kartu **všechny aplikace** V rozevírací nabídce Typ aplikace vyberte možnost **podnikové aplikace**a pak vyhledejte **klienta virtuální plochy Windows**.

    ![Snímek obrazovky karty všechny aplikace Uživatel zadal do panelu hledání "klient virtuální plochy Windows" a aplikace se zobrazí ve výsledcích hledání.](media/all-applications-search.png)

3. Vyberte **podmíněný přístup**.

    ![Snímek obrazovky zobrazující, že uživatel přejede ukazatelem myši na kartu podmíněný přístup](media/conditional-access-location.png)

4. Vyberte **+ Nová zásada**.

   ![Snímek obrazovky se stránkou podmíněného přístupu Uživatel přesune ukazatel myši na tlačítko Nová zásada.](media/new-policy-button.png)

5. Zadejte **název** **pravidla**a potom **Vyberte** * název **skupiny** , kterou jste vytvořili v části požadavky.

6. Vyberte **Vybrat**a potom vyberte **Hotovo**.

7. Pak otevřete **cloudové aplikace nebo akce**.

8. Na panelu **Vybrat** vyberte aplikace **Windows Virtual Desktop** Enterprise.

    ![Snímek obrazovky se stránkou cloudové aplikace nebo akce Uživatel vybral aplikaci pro virtuální plochu systému Windows zaškrtnutím políčka vedle ní. Vybraná aplikace je zvýrazněná červenou.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Na levé straně obrazovky by se měla zobrazit také klientská aplikace virtuální plochy Windows, jak je znázorněno na následujícím obrázku. Aby tato zásada fungovala, potřebujete pro podnikovou aplikaci virtuálních ploch Windows i klientské aplikace Windows.
    >
    > ![Snímek obrazovky se stránkou cloudové aplikace nebo akce Klientské aplikace virtuálních počítačů a virtuálních počítačů s Windows jsou zvýrazněné červeně.](media/cloud-apps-enterprise-selected.png)

9. Vybrat **Výběr**

10. Pak otevřete **grant** . 

11. Vyberte možnost **požadovat vícefaktorové ověřování**a pak vyberte **vyžadovat jeden z vybraných ovládacích prvků**.
   
    ![Snímek obrazovky se stránkou grant Je vybráno "vyžadovat vícefaktorové ověřování".](media/grant-page.png)

    >[!NOTE]
    >Pokud máte v organizaci zařízení zaregistrovaná v MDM a nechcete, aby se zobrazila výzva MFA, můžete také vybrat možnost **vyžadovat, aby zařízení bylo označené jako vyhovující**.

12. Vyberte možnost **relace**.

13. Nastavte **četnost přihlášení** na **aktivní**a pak změňte její hodnotu na **1 hod**.

    ![Snímek obrazovky se stránkou relace V nabídce relace se zobrazují rozevírací nabídky četnost přihlášení, které se změnily na 1 a hodiny.](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Aktivní relace v prostředí virtuálního počítače s Windows budou při změně zásady i nadále fungovat. Pokud se ale odpojíte nebo odhlásíte, budete muset znovu zadat svoje přihlašovací údaje po 60 minutách. Když změníte nastavení, můžete časový limit prodloužit tak, jak chcete (Pokud se zarovnává se zásadami zabezpečení vaší organizace).
    >
    >Výchozím nastavením je průběžné okno o 90 dní, což znamená, že klient požádá uživatele, aby se znovu přihlásil, když se pokusí o přístup k prostředku po dobu 90 dnů nebo déle.

14. Povolte tuto zásadu.

15. Vyberte **vytvořit** a potvrďte zásadu.

Všechno je hotové. Pokud chcete mít jistotu, že seznam povolených funguje tak, jak má, můžete tuto zásadu vyzkoušet.
