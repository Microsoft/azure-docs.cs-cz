---
title: Publikování vzdálené plochy s Proxy aplikace Azure AD | Microsoft Docs
description: Popisuje základní informace o Azure AD Application Proxy konektory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: barbkess
ms.custom: it-pro
ms.reviewer: harshja
ms.openlocfilehash: 61ff91709b52f7378bbc320524781dca7faf2204
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248873"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publikování vzdálené plochy s proxy aplikace služby Azure AD

Vzdálenou plochu a Azure AD Application Proxy vzájemně spolupracují a zvýšit produktivitu zaměstnanců, kteří jsou mimo podnikovou síť. 

Předpokládanou cílovou skupinou pro tento článek je:
- Aktuální Proxy aplikace zákazníci, kteří mají nabízejí víc aplikací k jejich koncoví uživatelé s publikování místní aplikací prostřednictvím služby Vzdálená plocha.
- Aktuální služby Vzdálená plocha zákazníkům, kteří chtějí omezit možnost útoku jejich nasazení pomocí proxy aplikace služby Azure AD. Tento scénář nabízí omezenou sadu dvoustupňové ověřování a řízení podmíněného přístupu pro vzdálené plochy

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Jak vyhovuje Proxy aplikace v standardní nasazení vzdálené plochy

Standardní nasazení vzdálené plochy zahrnuje různé služby role Vzdálená plocha systémem Windows Server. Prohlížení [architektura služby Vzdálená plocha](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture), existuje několik možností nasazení. Na rozdíl od jiné možnosti nasazení vzdálené plochy [nasazení vzdálené plochy s Azure AD Application Proxy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (viz následující diagram) má trvalé odchozí připojení ze serveru se spuštěnou službou konektoru. Ostatní nasazení ponechat otevřené příchozí připojení přes nástroj pro vyrovnávání zatížení.

![Proxy aplikací nachází mezi virtuálních počítačů vzdálené plochy a veřejného Internetu](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

V nasazení služby Vzdálená plocha VP webovou roli a roli služby Brána VP spouštět na internetových počítačích. Tyto koncové body jsou viditelné z následujících důvodů:
- RD Web poskytuje veřejný koncový bod přihlásit a zobrazit různé místními aplikacemi a stolních počítačů, které získají přístup k uživateli. Po výběru prostředek, se vytvoří připojení ke vzdálené ploše pomocí nativní aplikaci na operačního systému.
- Brána VP na obrázku stává, když uživatel spustí připojení RDP. Brána VP zpracovává šifrovaný provoz protokolu RDP procházející přes internet a převede jej na místním serveru, který uživatel se připojuje k. V tomto scénáři provoz, který přijímá Brána VP pochází z Azure AD Application Proxy.

>[!TIP]
>Pokud jste nenasadili RDS před nebo chcete další informace, než začnete, přečtěte si postup [bezproblémově nasazení vzdálené plochy s Azure Resource Manageru a webu Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Požadavky

- Koncové body Web VP a Brána VP musí být umístěné ve stejném počítači a s společný kořen. Web VP a Brána VP jsou publikovány jako jednu aplikaci pomocí Proxy aplikace tak, že můžete mít jednom přihlašování mezi těmito dvěma aplikacemi.

- Byste již měli mít [nasazení vzdálené plochy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure), a [povolit Proxy aplikace](application-proxy-enable.md).

- Tento scénář předpokládá, že vaši koncoví uživatelé projít Internet Exploreru na stolní počítače Windows 7 nebo Windows 10, které se připojují prostřednictvím vzdálené plochy webové stránky. Pokud potřebujete podporovat jiné operační systémy, přečtěte si téma [podporu pro ostatní konfigurace klienta](#support-for-other-client-configurations).

- Při publikování s RD Web, je nejvhodnější použít stejné interních a externích FQDN. Pokud se plně kvalifikované názvy domény interních a externích liší měli byste zakázat žádosti o překlad záhlaví, aby se zabránilo přijímání neplatné odkazy. 

- V Internet Exploreru povolte rozšíření ActiveX vzdálené plochy.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Nasazení společné scénáře vzdálené plochy a Proxy aplikací

Po nastavení vzdálené plochy a Azure AD Application Proxy pro vaše prostředí, postupujte podle kroků kombinovat dvě řešení. Tyto kroky provede publikování dva webové směřujících RDS koncových bodů (RD Web a Brána VP) jako aplikace a pak směrují přenosy na vaše RDS projít Proxy aplikace.

### <a name="publish-the-rd-host-endpoint"></a>Publikování koncový bod hostiteli vzdálené plochy

1. [Publikovat nové aplikace Proxy aplikace](application-proxy-publish-azure-portal.md) s následujícími hodnotami:
   - Interní adresa URL: https://\<rdhost\>.com /, kde \<rdhost\> je společný kořen, Web VP a Brána VP sdílet.
   - Externí adresu URL: Toto pole se vyplní automaticky na základě názvu aplikace, ale můžete ho upravit. Vaši uživatelé budou moct tuto adresu URL, když přistupují k vzdálené plochy
   - Metoda předběžného ověření: Azure Active Directory
   - Převede adresu URL hlavičky: Ne
2. Přiřazení uživatelů k publikované aplikaci VP. Ujistěte se, že všechny mají přístup k VP, příliš.
3. Ponechejte jedné metody přihlašování pro aplikace jako **Azure AD jednotné přihlašování zakázáno**. Vaši uživatelé vyzváni k ověření jednou do služby Azure AD a jednou pro RD Web, ale mají jednotného přihlašování k bráně VP.
4. Přejděte na **Azure Active Directory** > **registrace aplikace** > *aplikace* > **nastavení**.
5. Vyberte **vlastnosti** a aktualizovat **adresa URL domovské stránky** pole tak, aby odkazovaly na váš koncový bod RD Web (například https://\<rdhost\>.com nebo RDWeb).

### <a name="direct-rds-traffic-to-application-proxy"></a>Přímé přenosy vzdálené plochy na Proxy aplikace

Připojit k nasazení vzdálené plochy jako správce a změňte název serveru služby Brána VP pro nasazení. Tato konfigurace zajistí, že připojení procházejí přes službu Azure AD Application Proxy.

1. Připojte k serveru vzdálené plochy rolí Zprostředkovatel připojení k VP.
2. Spusťte **správce serveru**.
3. Vyberte **služby Vzdálená plocha** z podokna na levé straně.
4. Vyberte **Přehled**.
5. V části Přehled nasazení vyberte v rozevírací nabídce a zvolte **upravit vlastnosti nasazení**.
6. Na kartě Brána VP změnit **název serveru** pole na externí adresu URL, který nastavíte pro koncový bod VP hostitele proxy serveru aplikace.
7. Změna **přihlášení metoda** do **ověřování hesla**.

  ![Vlastnosti obrazovky nasazení na vzdálené plochy](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Spusťte tento příkaz pro každou kolekci. Nahraďte *\<yourcollectionname\>* a *\<proxyfrontendurl\>* nahraďte svými vlastními informacemi. Tento příkaz umožňuje jednotné přihlašování mezi Web VP a Brána VP a je optimalizován výkon:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Například:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```

9. Ověřte úpravy vlastních vlastností RDP a také zobrazit obsah souboru RDP, které budou staženy z RDWeb pro tuto kolekci, spusťte následující příkaz:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Nyní, když jste nakonfigurovali vzdálené plochy, Azure AD Application Proxy trvá jako součást internetové brány vzdálené plochy Na vaše webové VP a Brána VP počítače můžete odebrat jiné veřejné internetové koncové body.

## <a name="test-the-scenario"></a>Otestování tohoto scénáře

Otestování tohoto scénáře s aplikací Internet Explorer na počítači 10 nebo Windows 7.

1. Přejít na externí adresu URL nastavíte nebo najít svoji aplikaci v [MyApps panel](https://myapps.microsoft.com).
2. Zobrazí se výzva k ověření služby Azure Active Directory. Použijte účet, který jste přiřadili k aplikaci.
3. Zobrazí se výzva k ověření RD Web.
4. Po úspěšné ověřování vzdálené plochy, můžete vybrat plocha nebo aplikace, kterou chcete a začátek práce.

## <a name="support-for-other-client-configurations"></a>Podpora pro ostatní konfigurace klienta

Konfigurace uvedených v tomto článku je pro uživatele Windows 7 nebo Windows 10 se aplikace Internet Explorer a rozšíření ActiveX vzdálené plochy. Pokud potřebujete, může podporovat jiné operační systémy nebo prohlížeče. Rozdíl je v metodu ověřování, který používáte.

| Metoda ověřování | Podporované klientské konfigurace |
| --------------------- | ------------------------------ |
| Předběžné ověření    | Windows 7 nebo 10 pomocí Internet Exploreru + rozšíření ActiveX vzdálené plochy |
| Průchod | Všechny ostatní operační systém, který podporuje aplikace Vzdálená plocha od Microsoftu |

Tok předběžného ověření nabízí další výhody zabezpečení než průchozí toku. V předběžné ověřování můžete použít funkce ověřování Azure AD jako jednotné přihlašování, podmíněného přístupu a dvoustupňové ověřování pro vaše místní prostředky. Můžete také zajistěte, aby který pouze ověřené přenosy dosáhnou vaší sítě.

Pokud chcete používat předávací ověřování, jsou právě dva úpravy kroků uvedených v tomto článku:
1. V [publikovat koncový bod hostitele VP](#publish-the-rd-host-endpoint) kroku 1, nastavte jako metodu předběžného ověření **průchozí**.
2. V [přímé RDS provoz Proxy aplikace](#direct-rds-traffic-to-application-proxy), zcela přeskočit krok 8.

## <a name="next-steps"></a>Další postup

[Povolte vzdálený přístup na SharePoint s proxy aplikace služby Azure AD](application-proxy-integrate-with-sharepoint-server.md)  
[Důležité informace o zabezpečení pro vzdálený přístup k aplikací pomocí proxy aplikace služby Azure AD](application-proxy-security.md)
