---
title: Nastavení Multi-Factor Authentication Azure pro virtuální počítače s Windows – Azure
description: Jak nastavit Azure Multi-Factor Authentication pro zvýšení zabezpečení na virtuálním počítači s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 468597c82ae6c99aadeb33fafae78e3b3ed343cd
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213665"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Povolit Azure Multi-Factor Authentication pro virtuální počítače s Windows

Klient Windows pro virtuální počítače s Windows je vynikající volbou pro integraci virtuálního klienta s Windows s vaším místním počítačem. Když ale konfigurujete účet virtuální plochy Windows na klienta Windows, budete muset provést určitá opatření, abyste měli jistotu, že sami a uživatelé budou v bezpečí.

Po prvním přihlášení si klient vyžádá vaše uživatelské jméno, heslo a Azure MFA. Po tomto okamžiku si klient zapamatuje váš token z vaší podnikové aplikace Azure Active Directory (AD). Když vyberete možnost **Zapamatovat si mě**, uživatelé se můžou po restartování klienta přihlásit, aniž by museli znovu zadat svoje přihlašovací údaje.

I když je zapamatování přihlašovacích údajů pohodlné, může také provádět nasazení v podnikových scénářích nebo na osobních zařízeních méně zabezpečená. Pokud chcete chránit své uživatele, musíte se ujistit, že klient uchovává dotaz na přihlašovací údaje pro Azure Multi-Factor Authentication (MFA). V tomto článku se dozvíte, jak nakonfigurovat zásady podmíněného přístupu pro virtuální plochu Windows, aby toto nastavení bylo povolené.

## <a name="prerequisites"></a>Požadavky

Tady je přehled toho, co budete potřebovat k zahájení práce:

- Přiřaďte uživatelům licenci, která zahrnuje Azure Active Directory Premium P1 nebo P2.
- Skupina Azure Active Directory s vašimi uživateli přiřazenými jako členové skupiny.
- Povolte Azure MFA pro všechny uživatele. Další informace o tom, jak to provést, najdete v tématu [jak pro uživatele vyžadovat dvoustupňové ověřování](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> Následující nastavení platí také pro [webového klienta virtuální plochy systému Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

V této části se dozvíte, jak vytvořit zásadu podmíněného přístupu, která při připojování k virtuálnímu počítači s Windows vyžaduje vícefaktorové ověřování.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
2. Vyhledejte **Azure Active Directory**  >  **Security**  >  **podmíněný přístup**zabezpečení.
3. Vyberte **nové zásady**.
4. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
5. V části **Přiřazení** vyberte **Uživatelé a skupiny**.
   - V části **Zahrnout**vyberte **Vybrat uživatele a skupiny**  >  **Uživatelé a skupiny** > vyberte skupinu vytvořenou ve fázi požadavků.
   - Vyberte **Done** (Hotovo).
6. V části **cloudové aplikace nebo akce**  >  **Include**vyberte **vybrat aplikace**.
   - Zvolte **virtuální počítač s Windows** (ID aplikace 9cdead84-a844-4324-93f2-b2e6bb768d07), pak **Vyberte**a pak **Hotovo**.

     ![Snímek obrazovky se stránkou cloudové aplikace nebo akce Klientské aplikace virtuálních počítačů a virtuálních počítačů s Windows jsou zvýrazněné červeně.](media/cloud-apps-enterprise.png)

     >[!NOTE]
     >Pokud chcete najít ID aplikace, kterou chcete vybrat, přejděte na **podnikové aplikace** a v rozevírací nabídce Typ aplikace vyberte **aplikace Microsoftu** .

7. V části **řízení přístupu**  >  **udělení**přístupu vyberte **udělit přístup**, **vyžadovat vícefaktorové ověřování**a pak **Vyberte**.
8. V části relace **řízení přístupu**  >  **Session**vyberte **četnost přihlášení**, nastavte hodnotu na **1** a jednotku na **hodiny**a pak vyberte **Vybrat**.
9. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
10. Vyberte **vytvořit** a povolte tak zásady.

## <a name="next-steps"></a>Další kroky

- [Další informace o zásadách podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Další informace o četnosti přihlašování uživatelů](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
