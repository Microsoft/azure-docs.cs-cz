---
title: Instalovat aplikací rozšíření prohlížeče přístupového panelu – Azure | Dokumentace Microsoftu
description: Oprava běžných chyb při instalaci rozšíření prohlížeče přístupového panelu.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/4/18
ms.author: celested
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42b9668a68ed57273c6198dc2ecd34b9001811d3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209298"
---
# <a name="install-the-access-panel-browser-extension"></a>Instalace rozšíření prohlížeče přístupového panelu

Přístupový panel je webový portál. Pokud máte pracovní nebo školní účet v Azure Active Directory (Azure AD), můžete k zobrazení a spuštění cloudových aplikací, které správce Azure AD udělil přístup k přístupovému panelu. 

Pokud při použití edice Azure AD, můžete také použít samoobslužné skupiny a možnosti správy aplikací přes přístupový panel. 

Přístupový panel je nezávisle na webu Azure portal. Nevyžaduje, abyste měli předplatné Azure.

## <a name="web-browser-requirements"></a>Požadavky na webový prohlížeč

Minimálně na přístupovém panelu vyžaduje prohlížeč, který podporuje JavaScript a má povolený šablon stylů CSS. Být přihlášení k aplikacím přes jednotné přihlašování založené na heslo na přístupovém panelu, musíte mít rozšíření přístupového panelu nainstalovaný ve vašem prohlížeči. Rozšíření se automaticky stáhne při výběru, který je nakonfigurovaný pro jednotné přihlašování pomocí hesla aplikace.

Pro jednotné přihlašování pomocí hesla můžete použít některý z následujících prohlížečů:

- **Microsoft Edge**: Windows 10 Anniversary Edition nebo novější. 
- **Chrome**: ve Windows 7 nebo novější a v systému MacOS X nebo novější.
- **Firefox 26.0 nebo novější**: ve Windows XP SP2 nebo novější a Mac OS X 10.6 nebo novější.

## <a name="install-the-access-panel-browser-extension"></a>Instalace rozšíření prohlížeče přístupového panelu

Pokud chcete nainstalovat rozšíření prohlížeče přístupového panelu, postupujte takto:

1.  V jednom z podporovaných prohlížečů, otevřete [přístupového panelu](https://myapps.microsoft.com)a pak se přihlaste jako uživatel ve vašem účtu Azure AD.

2.  Vyberte aplikaci, jednotné přihlašování pomocí hesla.

3.  Po zobrazení výzvy vyberte **nainstalovat**.  
    Budete přesměrováni na odkaz ke stažení pro vybraný prohlížeč. 
    
4.  Vyberte **Přidat**.

5.  Pokud se zobrazí výzva, buď **povolit** nebo **povolit** rozšíření.

6.  Po dokončení instalace restartujte prohlížeč.

7.  Přihlaste se na přístupovém panelu a kontrola budete moct zobrazit, zda lze spustit jednotného přihlašování aplikace založené na heslech.

Můžete také stáhnout rozšíření pro Chrome a Microsoft Edgem přímo z následujících webů:

- [Rozšíření Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Rozšíření Microsoft Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Použití Moje aplikace zabezpečené přihlašování rozšíření
* Pokud používáte Moje adresy URL aplikací jiných než `https://myapps.microsoft.com`, nakonfigurujte adresu URL svého výchozí následujícím způsobem:
   1. Když jste *není* přihlášení k rozšíření, klikněte pravým tlačítkem na ikonu rozšíření.
   2. V nabídce vyberte **Moje aplikace URL**.
   3. Vyberte výchozí adresu URL.
   4. Vyberte ikonu rozšíření.
   5. Přihlaste se k rozšíření, vyberte **Přihlaste se k Začínáme**.

* Přihlásit se přímo k aplikaci v prohlížeči, postupujte takto:
   1. Po instalaci rozšíření, přihlaste se k němu tak, že vyberete **Přihlaste se k Začínáme**.
   2. Přihlaste se k aplikaci pomocí adresy URL přihlašování.  
       Adresa URL přihlašování je obvykle adresa URL aplikace, která se zobrazí přihlašovací formulář.
      Rozšíření by měl změny stavu a zjistíte, že heslo je k dispozici.
   3. Pro přihlášení, vyberte ikonu rozšíření.

* Chcete-li spustit aplikaci z rozšíření, postupujte takto:
   1. Po instalaci rozšíření, přihlaste se k němu tak, že vyberete **Přihlaste se k Začínáme**.
   2. Vyberte ikonu rozšíření a otevřete nabídku.
   3. Vyhledejte aplikaci, která je dostupná na portálu Moje aplikace.
   4. V seznamu výsledků hledání vyberte aplikaci.  
       Poslední tři aplikace využili jste se zobrazují v **naposledy použité** zástupce seznamu.
       
* Použití interní firemní adresy URL při vzdálené, postupujte takto:
    1. [Konfigurace Proxy aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) ve svém tenantovi
    2. [Publikování aplikace](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) a adresa URL, přes Proxy aplikací
    3. Instalace rozšíření a přihlaste se k němu tak, že vyberete přihlášení začít
    4. Teď můžete přejít na adresu URL interní společnosti i v průběhu vzdálené

> [!NOTE]
> Předchozí možnosti jsou dostupné jenom pro Microsoft Edge, Chrome a Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Nastavení zásad skupiny pro aplikaci Internet Explorer

Můžete nastavit zásady skupiny, který umožňuje vzdálenou instalaci rozšíření přístupového panelu pro aplikaci Internet Explorer v počítačích uživatelů.

Před nastavením zásad skupiny, zajistěte následující:

-   Nastavíte [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), a vaši uživatelé počítačů se připojili k vaší doméně.

-   Chcete-li upravit objekt zásad skupiny (GPO), musíte mít *upravit nastavení* oprávnění. Ve výchozím nastavení, je tato oprávnění udělena členům následující skupiny zabezpečení: Správci domény, enterprise administrators a skupiny policy creator owners.

Podrobné pokyny týkající se konfigurace zásad skupiny a jeho nasazení pro uživatele najdete v tématu [nasadit rozšíření přístupového panelu s využitím zásad skupiny pro aplikaci Internet Explorer](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Řešení potíží s rozšíření přístupového panelu v aplikaci Internet Explorer

Přístup k nástroje pro diagnostiku a informace o konfiguraci rozšíření pro aplikaci Internet Explorer, naleznete v tématu [řešení potíží s rozšířením panel přístupu pro aplikaci Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> Internet Explorer je na omezenou podporu a už nebude dostávat nové aktualizace softwaru. Microsoft Edge je doporučené prohlížeče.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Pokud problém nelze vyřešit pomocí předchozích kroků

Pokud je k dispozici, otevřete lístek podpory s následujícími informacemi:

-   Chyba ID korelace
-   Hlavní název uživatele (uživatel e-mailová adresa)
-   ID Tenanta
-   Typ prohlížeče
-   Časové pásmo a čas nebo časový rámec, kdy došlo k chybě
-   Trasování Fiddler

## <a name="next-steps"></a>Další postup
[Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](what-is-single-sign-on.md)
