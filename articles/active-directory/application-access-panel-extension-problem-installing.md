---
title: Nainstalujte panely prohlížeče aplikace přístup rozšíření - Azure | Microsoft Docs
description: Opravte běžné chyby při instalaci rozšíření přístup k panelu prohlížeče.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 3903e0f55e996d2ff793f17fb710843c5c64127f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="install-the-access-panel-browser-extension"></a>Instalace rozšíření přístup k panelu prohlížeče

Přístupový panel je webový portál. Pokud máte pracovní nebo školní účet ve službě Azure Active Directory (Azure AD), můžete zobrazit a spustit cloudové aplikace, které správce Azure AD udělil přístup k přístupovému panelu. 

Pokud používáte edice Azure AD, můžete také použít skupiny samoobslužné služby a možnosti správy aplikace prostřednictvím přístupového panelu. 

Přístupový panel je nezávislý na portálu Azure. Nevyžaduje, abyste měli předplatné Azure.

## <a name="web-browser-requirements"></a>Požadavky na webový prohlížeč

Minimálně přístupový panel vyžaduje prohlížeč, který podporuje jazyk JavaScript a aktivoval šablon stylů CSS. Být přihlášeni k aplikacím prostřednictvím založené na heslech jednotného přihlašování na přístupovém panelu, musí mít příponu panel přístupu nainstalovaný v prohlížeči. Rozšíření se stáhne automaticky, když vyberete aplikaci, která je nakonfigurována pro jednotné přihlašování založené na heslech.

Pro jednotné přihlašování založené na heslech můžete použít některý z následujících prohlížečů:

- **Hraniční**: v systému Windows 10 Anniversary Edition nebo novějším. 
- **Chrome**: v systému Windows 7 nebo novější a v systému MacOS X nebo novější.
- **Firefox 26.0 nebo novější**: v systému Windows XP SP2 nebo novější a Mac OS X 10.6 nebo novější.

## <a name="install-the-access-panel-browser-extension"></a>Instalace rozšíření přístup k panelu prohlížeče

K instalaci rozšíření přístup k panelu prohlížeče, postupujte takto:

1.  V jednom z podporovaných prohlížečích, otevřete [přístupový panel](https://myapps.microsoft.com)a potom se přihlaste jako uživatel ve vašem účtu Azure AD.

2.  Vyberte aplikaci jednotného přihlašování založené na heslech.

3.  Po zobrazení výzvy vyberte **instalovat nyní**.  
    Budete přesměrováni odkaz ke stažení pro vybrané prohlížeč. 
    
4.  Vyberte **Přidat**.

5.  Pokud budete vyzváni, buď **povolit** nebo **povolit** rozšíření.

6.  Po dokončení instalace restartujte prohlížeč.

7.  Přihlaste se k přístupového panelu a zkontrolujte zobrazíte, zda můžete spustit aplikace založené na heslech jednotné přihlašování.

Můžete také stáhnout rozšíření pro Chrome a hraniční přímo z následujících lokalit:

- [Rozšíření Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Rozšíření Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Použití v mé aplikace zabezpečené přihlašování rozšíření
* Pokud používáte Moje adresa URL aplikace jiné než `https://myapps.microsoft.com`, nakonfigurujte výchozí adresu URL pomocí těchto kroků:
   1. Když jste *není* přihlášení k rozšíření, klikněte pravým tlačítkem na ikonu rozšíření.
   2. V nabídce vyberte **Moje adresa URL aplikace**.
   3. Vyberte výchozí adresa URL.
   4. Vyberte ikonu pro rozšíření.
   5. Přihlaste se k rozšíření, vyberte **Přihlaste se k Začínáme**.

* Přihlaste se k aplikaci přímo z prohlížeče, takto:
   1. Po instalaci rozšíření, přihlaste se k němu výběrem **Přihlaste se k Začínáme**.
   2. Přihlaste se k aplikaci pomocí adresy URL přihlašování.  
       Adresa URL přihlašování je obvykle adresu URL aplikace, které zobrazí formulář přihlášení.
      Rozšíření by měl změnit stav a vám oznamuje, že heslo je k dispozici.
   3. K přihlášení, vyberte ikonu rozšíření.

* Spuštění aplikace z rozšíření, postupujte takto:
   1. Po instalaci rozšíření, přihlaste se k němu výběrem **Přihlaste se k Začínáme**.
   2. Vyberte ikonu rozšíření otevřete nabídku.
   3. Vyhledejte aplikaci, která je k dispozici na portálu Moje aplikace.
   4. V seznamu výsledků hledání vyberte aplikaci.  
       Poslední tři aplikace poprvé, zobrazí se zobrazují v **nedávno použité** zástupce seznamu.

> [!NOTE]
> Předchozí možnosti jsou dostupné pouze pro okraj a Chrome, Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Nastavení zásad skupiny pro Internet Explorer

Můžete nastavit zásady skupiny, které vám umožní vzdáleně nainstalovat rozšíření panely přístup pro prohlížeč Internet Explorer na počítačích uživatelů.

Před nastavením zásad skupiny, zajistěte, aby:

-   Jste nastavili [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), a vaši uživatelé počítačů se připojili k vaší doméně.

-   Chcete-li upravit objekt zásad skupiny (GPO), musíte mít *upravit nastavení* oprávnění. Ve výchozím nastavení, je tato oprávnění udělit členy z těchto skupin zabezpečení: domain administrators, enterprise administrators a skupiny group policy creator owners.

Podrobné informace o konfiguraci zásad skupiny a jeho nasazení na uživatele, najdete v části [nasazení rozšíření panel přístupu pro Internet Explorer pomocí zásad skupiny](active-directory-saas-ie-group-policy.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Řešení potíží s příponou přístup k panelu v aplikaci Internet Explorer

Přístup k Diagnostika a informace o konfiguraci rozšíření pro Internet Explorer, najdete v části [řešení potíží s příponou panel přístupu pro Internet Explorer](active-directory-saas-ie-troubleshooting.md).

> [!NOTE]
> Internet Explorer je na omezenou podporu a už obdrží nové aktualizace softwaru. Okraj je doporučené prohlížeče.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Pokud předchozí kroky není problém vyřešit

Pokud je k dispozici, otevřete lístek podpory s následujícími informacemi:

-   ID chyby korelace
-   Hlavní název uživatele (e-mailovou adresu uživatele)
-   TenantID
-   Typ prohlížeče
-   Časové pásmo a čas nebo časový rámec, když došlo k chybě
-   Fiddler trasování

## <a name="next-steps"></a>Další postup
[Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)
