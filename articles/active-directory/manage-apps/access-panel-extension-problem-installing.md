---
title: Instalace rozšíření prohlížeče přístupového panelu aplikace – Azure AD
description: Opravte běžné chyby, ke kterým došlo při instalaci rozšíření prohlížeče přístupového panelu.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 771ba79f067cbff1ab8bbfece64f4028b4ca50b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275861"
---
# <a name="install-the-access-panel-browser-extension"></a>Instalace rozšíření prohlížeče přístupového panelu

Přístupový panel je webový portál. Pokud máte pracovní nebo školní účet ve službě Azure Active Directory (Azure AD), můžete pomocí přístupového panelu zobrazit a spustit cloudové aplikace, ke kterým vám správce Azure AD udělil přístup. 

Pokud používáte edice Azure AD, můžete také použít možnosti samoobslužné skupiny a správy aplikací prostřednictvím přístupového panelu. 

Přístupový panel je oddělený od portálu Azure. Nevyžaduje, abyste měli předplatné Azure.

## <a name="web-browser-requirements"></a>Požadavky na webový prohlížeč

Přístupový panel vyžaduje minimálně prohlížeč, který podporuje JavaScript a má povoleno CSS. Chcete-li být přihlášeni k aplikacím prostřednictvím hesla založené přihlašování na přístupovém panelu, musíte mít v prohlížeči nainstalováno rozšíření přístupového panelu. Rozšíření se stáhne automaticky, když vyberete aplikaci, která je nakonfigurovaná pro zabezpečení založené na heslech.

Pro zabezpečení založené na heslech můžete použít libovolný z následujících prohlížečů:

- **Microsoft Edge**: ve Windows 10 Anniversary Edition nebo novějším. 
- **Chrome**: ve Windows 7 nebo novějším a v MacOS X nebo novějším.
- **Firefox 26.0 nebo novější**: v systému Windows XP SP2 nebo novějším a v systému Mac OS X 10.6 nebo novějším.

## <a name="install-the-access-panel-browser-extension"></a>Instalace rozšíření prohlížeče přístupového panelu

Chcete-li nainstalovat rozšíření prohlížeče přístupového panelu, postupujte takto:

1.  V jednom z podporovaných prohlížečů otevřete [přístupový panel](https://myapps.microsoft.com)a pak se přihlaste jako uživatel ve svém účtu Azure AD.

2.  Vyberte aplikaci s přihlašováným heslem.

3.  Po zobrazení výzvy vyberte **možnost Instalovat .**  
    Budete přesměrováni na odkaz ke stažení pro vybraný prohlížeč. 
    
4.  Vyberte **Přidat**.

5.  Pokud se zobrazí výzva, **povolit** nebo **povolit** rozšíření.

6.  Po dokončení instalace restartujte prohlížeč.

7.  Přihlaste se k přístupovému panelu a zkontrolujte, jestli můžete spustit aplikace s přihlašování pomocí hesla.

Rozšíření pro Chrome a Microsoft Edge si můžete stáhnout také přímo z následujících webů:

- [Rozšíření chromu](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Rozšíření Microsoft Edge](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Rozšíření Firefoxu](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Použití rozšíření Zabezpečené přihlášení moje aplikace
* Pokud používáte jinou adresu `https://myapps.microsoft.com`URL moje aplikace než , nakonfigurujte výchozí adresu URL následujícím způsobem:
   1. I když *nejste* přihlášeni k rozšíření, klikněte pravým tlačítkem myši na ikonu rozšíření.
   2. V nabídce vyberte **Moje adresa URL aplikace**.
   3. Vyberte výchozí adresu URL.
   4. Vyberte ikonu rozšíření.
   5. Pokud se chcete přihlásit k rozšíření, vyberte **Přihlásit se, abyste mohli začít**.

* Pokud se chcete přihlásit přímo k aplikaci z prohlížeče, postupujte takto:
   1. Po instalaci rozšíření se k němu přihlaste tak, že vyberete **Přihlásit se, abyste mohli začít**.
   2. Přihlaste se k aplikaci pomocí přihlašovací adresy URL.  
       Přihlašovací adresa URL je obvykle adresa URL aplikace, která zobrazuje přihlašovací formulář.
      Rozšíření by mělo změnit stav a dát vám vědět, že heslo je k dispozici.
   3. Chcete-li se přihlásit, vyberte ikonu rozšíření.

* Chcete-li spustit aplikaci z rozšíření, postupujte takto:
   1. Po instalaci rozšíření se k němu přihlaste tak, že vyberete **Přihlásit se, abyste mohli začít**.
   2. Vyberte ikonu rozšíření a otevřete její nabídku.
   3. Vyhledejte aplikaci, která je dostupná na portálu Moje aplikace.
   4. V seznamu výsledků hledání vyberte aplikaci.  
       Poslední tři aplikace, které jste použili, se zobrazí v seznamu **naposledy použitých** zkratek.
       
* Chcete-li používat interní adresy URL společnosti na dálku, postupujte takto:
    1. [Konfigurace proxy aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) v tenantovi
    2. [Publikování aplikace](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) a adresy URL prostřednictvím proxy aplikace
    3. Nainstalujte rozšíření a přihlaste se k němu tak, že vyberete Přihlásit se, abyste mohli začít
    4. Nyní můžete procházet interní adresu URL společnosti, i když je

> [!NOTE]
> Předchozí možnosti jsou k dispozici pouze pro Microsoft Edge, Chrome a Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Nastavení zásad skupiny pro Internet Explorer

Můžete nastavit zásady skupiny, které umožňují vzdáleně nainstalovat rozšíření přístupového panelu pro aplikaci Internet Explorer na počítačích uživatelů.

Před nastavením zásad skupiny se ujistěte, že:

-   Nastavili jste [službu Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)a připojili jste počítače uživatelů k doméně.

-   Chcete-li upravit objekt zásad skupiny (GPO), musíte mít oprávnění *k úpravám nastavení.* Ve výchozím nastavení je toto oprávnění uděleno členům následujících skupin zabezpečení: správcům domén, správcům rozlehlé sítě a vlastníkům tvůrců zásad skupiny.

Podrobné pokyny ke konfiguraci zásad skupiny a jejich nasazení uživatelům najdete v tématu [Nasazení rozšíření přístupového panelu pro aplikaci Internet Explorer pomocí zásad skupiny](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Poradce při potížích s rozšířením přístupového panelu v aplikaci Internet Explorer

Přístup k diagnostickému nástroji a informace o konfiguraci rozšíření pro aplikaci Internet Explorer naleznete [v tématu Poradce při potížích s rozšířením přístupového panelu pro aplikaci Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> Aplikace Internet Explorer má omezenou podporu a již nepřijímá nové aktualizace softwaru. Microsoft Edge je doporučený prohlížeč.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Pokud předchozí kroky problém nevyřeší

Otevřete lístek podpory s následujícími informacemi, pokud je k dispozici:

-   ID chyby korelace
-   UPN (e-mailová adresa uživatele)
-   ID tenanta
-   Typ prohlížeče
-   Časové pásmo a časový nebo časový rámec, kdy došlo k chybě
-   Fiddler stopy

## <a name="next-steps"></a>Další kroky
[Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](what-is-single-sign-on.md)
