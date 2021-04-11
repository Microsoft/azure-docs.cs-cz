---
title: Nastavení vícefaktorového ověřování Azure pro virtuální počítače s Windows – Azure
description: Jak nastavit vícefaktorové ověřování Azure pro zvýšené zabezpečení na virtuálním počítači s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 12/10/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7ebf38226ff725865104707a3f28e7ce51a86c31
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445648"
---
# <a name="enable-azure-multifactor-authentication-for-windows-virtual-desktop"></a>Povolení vícefaktorového ověřování Azure pro virtuální počítače s Windows

>[!IMPORTANT]
> Pokud navštívíte tuto stránku z dokumentace k virtuálním plochám Windows (Classic), nezapomeňte po dokončení [vrátit se do dokumentace k virtuální ploše Windows (Classic)](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) .

Klient Windows pro virtuální počítače s Windows je vynikající volbou pro integraci virtuálního klienta s Windows s vaším místním počítačem. Když ale konfigurujete účet virtuální plochy Windows na klienta Windows, budete muset provést určitá opatření, abyste měli jistotu, že sami a uživatelé budou v bezpečí.

Při prvním přihlášení si klient vyžádá vaše uživatelské jméno, heslo a Azure vícefaktorového ověřování. Po tomto okamžiku si klient zapamatuje váš token z vaší podnikové aplikace Azure Active Directory (AD). Když vyberete možnost **pamatovat mě** na výzvu k zadání přihlašovacích údajů pro hostitele relace, uživatelé se můžou po restartování klienta přihlásit, aniž by museli znovu zadat svoje přihlašovací údaje.

I když je zapamatování přihlašovacích údajů pohodlné, může také provádět nasazení v podnikových scénářích nebo na osobních zařízeních méně zabezpečená. Pokud chcete chránit své uživatele, můžete se ujistit, že klient bude dál podávat dotazy na přihlašovací údaje vícefaktorového ověřování Azure. V tomto článku se dozvíte, jak nakonfigurovat zásady podmíněného přístupu pro virtuální plochu Windows, aby toto nastavení bylo povolené.

## <a name="prerequisites"></a>Požadavky

Tady je přehled toho, co budete potřebovat k zahájení práce:

- Přiřaďte uživatelům licenci, která zahrnuje Azure Active Directory Premium P1 nebo P2.
- Skupina Azure Active Directory s vašimi uživateli přiřazenými jako členové skupiny.
- Povolte Azure vícefaktorového ověřování pro všechny uživatele. Další informace o tom, jak to provést, najdete v tématu [jak pro uživatele vyžadovat dvoustupňové ověřování](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> Následující nastavení platí také pro [webového klienta virtuální plochy systému Windows](https://rdweb.wvd.microsoft.com/arm/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Tady je postup, jak vytvořit zásadu podmíněného přístupu, která při připojování k virtuálnímu počítači s Windows vyžaduje vícefaktorové ověřování:

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
2. Vyhledejte **Azure Active Directory**  >    >  **podmíněný přístup** zabezpečení.
3. Vyberte **nové zásady**.
4. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
5. V části **Přiřazení** vyberte **Uživatelé a skupiny**.
6. V části **Zahrnout** vyberte **Vybrat uživatele a skupiny**  >  **Uživatelé a skupiny** > vyberte skupinu, kterou jste vytvořili ve fázi [požadavků](#prerequisites) .
7. Vyberte **Hotovo**.
8. V části **cloudové aplikace nebo akce**  >  vyberte **vybrat aplikace**.
9. Vyberte jednu z následujících aplikací podle toho, kterou verzi virtuálního klienta Windows používáte.
   
   - Pokud používáte virtuální plochu Windows (Classic), vyberte tyto aplikace:
       
       - **Virtuální desktop Windows** (ID aplikace 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Klient virtuální plochy Windows** (ID aplikace fa4345a4-a730-4230-84a8-7d9651b86739), který vám umožní nastavit zásady pro webového klienta.
       
        Potom přejděte ke kroku 11.

   - Pokud používáte virtuální počítač s Windows, vyberte místo toho tuto aplikaci:
       
       -  **Virtuální desktop Windows** (ID aplikace 9cdead84-a844-4324-93f2-b2e6bb768d07)
       
        Potom můžete přejít ke kroku 10.

   >[!IMPORTANT]
   > Nevybírejte aplikaci nazvanou Windows Virtual Desktop Azure Resource Manager Provider (50e95039-B200-4007-bc97-8d5790743a63). Tato aplikace se používá jenom pro načtení uživatelského kanálu a neměla by mít vícefaktorové ověřování.
   > 
   > Pokud používáte virtuální plochu Windows (Classic), pokud zásada podmíněného přístupu zablokuje veškerý přístup a vyloučí jenom ID aplikací virtuálních počítačů s Windows, můžete to opravit přidáním ID aplikace 9cdead84-a844-4324-93f2-b2e6bb768d07 do zásady. Když nepřidáte toto ID aplikace, zablokuje se zjišťování prostředků virtuálních ploch Windows (Classic) v informačním kanálu.

10. Přejít na **podmínky**  >  **klientské aplikace** a pak vyberte, na kde chcete zásadu použít:
    
    - Vyberte možnost **prohlížeč** , pokud chcete, aby se zásady projevily u webového klienta.
    - Pokud chcete zásady použít pro ostatní klienty, vyberte **mobilní aplikace a klienti klasické pracovní plochy** .
    - Zaškrtněte obě políčka, pokud chcete zásady použít pro všechny klienty.
   
    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se stránkou klientské aplikace Uživatel vybral zaškrtávací políčko mobilní aplikace a klienti klasické pracovní plochy.](media/select-apply.png)

11. Po výběru aplikace zvolte **Vybrat** a potom vyberte **Hotovo**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se stránkou cloudové aplikace nebo akce Klientské aplikace virtuálních počítačů a virtuálních počítačů s Windows jsou zvýrazněné červeně.](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >Pokud chcete najít ID aplikace, kterou chcete vybrat, přejděte na **podnikové aplikace** a v rozevírací nabídce Typ aplikace vyberte **aplikace Microsoftu** .

12. V části **řízení přístupu**  >  **udělení** přístupu vyberte **udělit přístup**, **vyžadovat vícefaktorové ověřování** a pak **Vyberte**.
13. V části relace **řízení přístupu**  >  vyberte **četnost přihlášení**, nastavte hodnotu na požadovaný čas mezi jednotlivými výzvami a pak vyberte **Vybrat**. Například nastavení hodnoty na hodnotu **1** a jednotka na **hodiny** budou vyžadovat vícefaktorové ověřování, pokud se připojení spustí za hodinu za poslední.
14. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
15. Vyberte **vytvořit** a povolte tak zásady.

>[!NOTE]
>Když použijete webového klienta pro přihlášení k virtuálnímu klientovi Windows přes prohlížeč, protokol zobrazí seznam ID aplikace klienta jako a85cf173-4192-42f8-81fa-777a763e6e2c (klient virtuální plochy Windows). Je to proto, že klientská aplikace je interně propojená s ID aplikace serveru, na které se nastavily zásady podmíněného přístupu. 

## <a name="next-steps"></a>Další kroky

- [Další informace o zásadách podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Další informace o četnosti přihlašování uživatelů](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
