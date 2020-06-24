---
title: Instalace rozšíření prohlížeče přístupového panelu aplikace – Azure AD
description: Oprava běžných chyb zjištěných při instalaci rozšíření prohlížeče přístupového panelu.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/04/2018
ms.author: kenwith
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2046c24220079a604792d07f3ebc3f6ef11e9c8a
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84761081"
---
# <a name="install-the-access-panel-browser-extension"></a>Nainstalovat rozšíření prohlížeče přístupového panelu

Přístupový panel je webový portál. Pokud máte pracovní nebo školní účet v Azure Active Directory (Azure AD), můžete pomocí přístupového panelu zobrazit a spustit cloudové aplikace, ke kterým vám správce Azure AD udělil přístup. 

Pokud používáte edice Azure AD, můžete pomocí přístupového panelu použít taky samoobslužnou skupinu a možnosti správy aplikací. 

Přístupový panel je oddělený od Azure Portal. Nevyžaduje, abyste měli předplatné Azure.

## <a name="web-browser-requirements"></a>Požadavky na webový prohlížeč

Přístupový panel vyžaduje minimálně prohlížeč, který podporuje jazyk JavaScript a je povolený pomocí šablon stylů CSS. Chcete-li být přihlášeni k aplikacím pomocí jednotného přihlašování založeného na heslech na přístupovém panelu, musíte mít v prohlížeči nainstalovanou příponu přístupového panelu. Rozšíření se automaticky stáhne při výběru aplikace, která je nakonfigurovaná pro jednotné přihlašování založené na heslech.

Pro jednotné přihlašování založené na heslech můžete použít některý z následujících prohlížečů:

- **Microsoft Edge**: ve Windows 10 výročí Edition nebo novějších verzích. 
- **Chrome**: ve Windows 7 nebo novějším a na MacOS X nebo novějším.
- **Firefox 26,0 nebo novější**: v systému Windows XP SP2 nebo novějším a v Mac OS X 10,6 nebo novějším.

## <a name="install-the-access-panel-browser-extension"></a>Nainstalovat rozšíření prohlížeče přístupového panelu

Chcete-li nainstalovat rozšíření prohlížeče přístupového panelu, postupujte následovně:

1.  V jednom z podporovaných prohlížečů otevřete [přístupový panel](https://myapps.microsoft.com)a pak se přihlaste jako uživatel ve svém účtu Azure AD.

2.  Vyberte aplikaci jednotného přihlašování založené na heslech.

3.  Po zobrazení výzvy vyberte **nainstalovat hned**.  
    Jste přesměrováni na odkaz ke stažení pro vybraný prohlížeč. 
    
4.  Vyberte možnost **Přidat**.

5.  Pokud se zobrazí výzva, buď **Povolte** nebo **Povolte** rozšíření.

6.  Po dokončení instalace restartujte prohlížeč.

7.  Přihlaste se k přístupovému panelu a zkontrolujte, zda můžete spustit aplikace jednotného přihlašování založené na heslech.

Rozšíření pro Chrome a Microsoft Edge si také můžete stáhnout přímo z následujících webů:

- [Rozšíření Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Rozšíření Microsoft Edge](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Rozšíření Firefox](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Použijte rozšíření pro zabezpečené přihlášení k aplikacím
* Pokud používáte jinou adresu URL moje aplikace než `https://myapps.microsoft.com` , nakonfigurujte výchozí adresu URL tak, že provedete následující kroky:
   1. Když *k rozšíření nejste přihlášení,* klikněte pravým tlačítkem myši na ikonu rozšíření.
   2. V nabídce vyberte **Moje aplikace adresa URL**.
   3. Vyberte výchozí adresu URL.
   4. Vyberte ikonu rozšíření.
   5. Pokud se chcete přihlásit k rozšíření, vyberte **Přihlásit se a**začněte.

* Pokud se chcete k aplikaci přihlásit přímo z prohlížeče, udělejte toto:
   1. Po instalaci rozšíření se k němu přihlaste výběrem možnosti **Přihlásit se a začněte**.
   2. Přihlaste se k aplikaci pomocí přihlašovací adresy URL.  
       Přihlašovací adresa URL je obvykle adresa URL aplikace, která zobrazuje přihlašovací formulář.
      Rozšíření by mělo změnit stav a dát vám jistotu, že je k dispozici heslo.
   3. Pokud se chcete přihlásit, vyberte ikonu rozšíření.

* Pokud chcete spustit aplikaci z rozšíření, udělejte toto:
   1. Po instalaci rozšíření se k němu přihlaste výběrem možnosti **Přihlásit se a začněte**.
   2. Vyberte ikonu rozšíření a otevřete její nabídku.
   3. Vyhledejte aplikaci, která je k dispozici na portálu moje aplikace.
   4. V seznamu výsledků hledání vyberte aplikaci.  
       Poslední tři aplikace, které jste použili, se zobrazí v seznamu **naposledy použitých** zástupců.
       
* Pokud chcete používat interní adresy URL společnosti při vzdálené službě, udělejte toto:
    1. [Konfigurace proxy aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) ve vašem tenantovi
    2. [Publikování aplikace](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) a adresy URL prostřednictvím proxy aplikací
    3. Nainstalujte rozšíření a přihlaste se k němu výběrem možnosti přihlásit se a začněte.
    4. Teď můžete přejít na interní adresu URL společnosti, i když je vzdálená.

> [!NOTE]
> Předchozí možnosti jsou k dispozici pouze pro Microsoft Edge, Chrome a Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Nastavení zásad skupiny pro Internet Explorer

Můžete nastavit zásady skupiny, které vám umožní vzdáleně nainstalovat rozšíření přístupového panelu pro Internet Explorer na počítačích uživatelů.

Před nastavením zásad skupiny se ujistěte, že:

-   Nastavili jste [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)a připojili jste počítače uživatelů k vaší doméně.

-   Chcete-li upravit objekt Zásady skupiny (GPO), musíte mít oprávnění *Upravit nastavení* . Ve výchozím nastavení je toto oprávnění uděleno členům těchto skupin zabezpečení: Domain Administrators, Enterprise Administrators a Group Policy Creator Owners.

Podrobné pokyny týkající se konfigurace zásad skupiny a jejich nasazení pro uživatele najdete v tématu [nasazení rozšíření přístupového panelu pro Internet Explorer pomocí zásad skupiny](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Řešení potíží s rozšířením přístupového panelu v aplikaci Internet Explorer

Přístup k nástroji pro diagnostiku a informace o konfiguraci rozšíření pro Internet Explorer najdete v tématu [Poradce při potížích s rozšířením přístupového panelu pro Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> Internet Explorer má omezené podpory a už nepřijímá nové aktualizace softwaru. Microsoft Edge je doporučený prohlížeč.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Pokud předchozí kroky problém nevyřeší

Otevřete lístek podpory s následujícími informacemi, pokud je k dispozici:

-   ID chyby korelace
-   UPN (uživatelská e-mailová adresa)
-   TenantID
-   Typ prohlížeče
-   Časové pásmo a čas nebo časový rámec, kdy došlo k chybě
-   Fiddler trasování

## <a name="next-steps"></a>Další kroky
[Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](what-is-single-sign-on.md)
