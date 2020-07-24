---
title: Nastavení Multi-Factor Authentication Azure pro virtuální počítače s Windows – Azure
description: Jak nastavit Azure Multi-Factor Authentication pro zvýšení zabezpečení na virtuálním počítači s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 47b1a3a44c494560dde9ffdab004ea576f434ffe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091296"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Povolení služby Azure Multi-Factor Authentication pro službu Windows Virtual Desktop

>[!IMPORTANT]
> Pokud navštívíte tuto stránku z dokumentace ke 2019., ujistěte se, že jste po dokončení [vrátili do dokumentace ke 2019](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) .

Klient Windows pro virtuální počítače s Windows je vynikající volbou pro integraci virtuálního klienta s Windows s vaším místním počítačem. Když ale konfigurujete účet virtuální plochy Windows na klienta Windows, budete muset provést určitá opatření, abyste měli jistotu, že sami a uživatelé budou v bezpečí.

Po prvním přihlášení si klient vyžádá vaše uživatelské jméno, heslo a Azure MFA. Po tomto okamžiku si klient zapamatuje váš token z vaší podnikové aplikace Azure Active Directory (AD). Když vyberete možnost **Zapamatovat si mě**, uživatelé se můžou po restartování klienta přihlásit, aniž by museli znovu zadat svoje přihlašovací údaje.

I když je zapamatování přihlašovacích údajů pohodlné, může také provádět nasazení v podnikových scénářích nebo na osobních zařízeních méně zabezpečená. Pokud chcete chránit své uživatele, musíte se ujistit, že klient uchovává dotaz na přihlašovací údaje pro Azure Multi-Factor Authentication (MFA). V tomto článku se dozvíte, jak nakonfigurovat zásady podmíněného přístupu pro virtuální plochu Windows, aby toto nastavení bylo povolené.

## <a name="prerequisites"></a>Předpoklady

Tady je přehled toho, co budete potřebovat k zahájení práce:

- Přiřaďte uživatelům licenci, která zahrnuje Azure Active Directory Premium P1 nebo P2.
- Skupina Azure Active Directory s vašimi uživateli přiřazenými jako členové skupiny.
- Povolte Azure MFA pro všechny uživatele. Další informace o tom, jak to provést, najdete v tématu [jak pro uživatele vyžadovat dvoustupňové ověřování](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> Následující nastavení platí také pro [webového klienta virtuální plochy systému Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Tady je postup, jak vytvořit zásadu podmíněného přístupu, která při připojování k virtuálnímu počítači s Windows vyžaduje vícefaktorové ověřování:

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
2. Vyhledejte **Azure Active Directory**  >  **Security**  >  **podmíněný přístup**zabezpečení.
3. Vyberte **nové zásady**.
4. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
5. V části **Přiřazení** vyberte **Uživatelé a skupiny**.
6. V části **Zahrnout**vyberte **Vybrat uživatele a skupiny**  >  **Uživatelé a skupiny** > vyberte skupinu, kterou jste vytvořili ve fázi [požadavků](#prerequisites) .
7. Vyberte **Hotovo**.
8. V části **cloudové aplikace nebo akce**  >  **Include**vyberte **vybrat aplikace**.
9. Vyberte jednu z následujících skupin aplikací na základě používané verze virtuálního počítače s Windows.
   - Pokud používáte verzi spadající do 2019, vyberte tyto dvě aplikace:
       - **Virtuální desktop Windows** (ID aplikace 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Klient virtuální plochy Windows** (ID aplikace fa4345a4-a730-4230-84a8-7d9651b86739)
   - Pokud používáte vydání jarní 2020, vyberte místo toho tyto dvě aplikace:
       -  **Virtuální desktop Windows** (ID aplikace 9cdead84-a844-4324-93f2-b2e6bb768d07)
       -  **Klient virtuální plochy Windows** (ID aplikace a85cf173-4192-42f8-81fa-777a763e6e2c)

   >[!IMPORTANT]
   > Klientské aplikace virtuálních ploch Windows se používají pro webového klienta. Nevybírejte ale aplikaci s názvem Windows Virtual Desktop Azure Resource Manager Provider (50e95039-B200-4007-bc97-8d5790743a63). Tato aplikace se používá jenom pro načtení uživatelského kanálu a neměla by mít MFA.
  
1. Po výběru aplikace zvolte **Vybrat**a potom vyberte **Hotovo**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky se stránkou cloudové aplikace nebo akce Klientské aplikace virtuálních počítačů a virtuálních počítačů s Windows jsou zvýrazněné červeně.](media/cloud-apps-enterprise.png)

   >[!NOTE]
   >Pokud chcete najít ID aplikace, kterou chcete vybrat, přejděte na **podnikové aplikace** a v rozevírací nabídce Typ aplikace vyberte **aplikace Microsoftu** .

10. V části **řízení přístupu**  >  **udělení**přístupu vyberte **udělit přístup**, **vyžadovat vícefaktorové ověřování**a pak **Vyberte**.
11. V části relace **řízení přístupu**  >  **Session**vyberte **četnost přihlášení**, nastavte hodnotu na **1** a jednotku na **hodiny**a pak vyberte **Vybrat**.
12. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
13. Vyberte **vytvořit** a povolte tak zásady.

## <a name="next-steps"></a>Další kroky

- [Další informace o zásadách podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Další informace o četnosti přihlašování uživatelů](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
