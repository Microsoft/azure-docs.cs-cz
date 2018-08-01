---
title: Publikování vzdálené plochy pomocí Proxy aplikace Azure AD | Dokumentace Microsoftu
description: Popisuje základní informace o konektory Proxy aplikací Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: barbkess
ms.custom: it-pro
ms.reviewer: harshja
ms.openlocfilehash: 61ac0d823322b919952b7ea426c447e070a09fc1
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363192"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publikování vzdálené plochy s Azure AD Application Proxy

Služba Vzdálená plocha a Proxy aplikací Azure AD spolupracují a zvyšují produktivitu zaměstnanců, kteří jsou mimo firemní síť. 

Jeho zamýšlenou cílovou skupinou pro účely tohoto článku je:
- Aktuální zákazníci Proxy aplikací, kteří chtějí nabídnout víc aplikací koncovým uživatelům, a publikujte místní aplikace prostřednictvím služby Vzdálená plocha.
- Aktuální zákazníci služby Vzdálená plocha, kteří chtějí omezení možností útoku na jejich nasazení pomocí Azure AD Application Proxy. V tomto scénáři poskytuje omezenou sadu dvoustupňové ověřování a řízení podmíněného přístupu pro vzdálené plochy

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Jak zapadá Proxy aplikace ve standardní nasazení vzdálené plochy

Standardní nasazení vzdálené plochy obsahuje různé služby role Vzdálená plocha běžící na Windows serveru. Podíváme [architektury služby Vzdálená plocha](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture), více možností nasazení. Na rozdíl od dalších možností nasazení vzdálené plochy [nasazení služby Vzdálená plocha s Azure AD Application Proxy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (viz následující diagram) má trvalé odchozí připojení ze serveru se spuštěnou službou konektoru. Jiná nasazení ponechte otevřené příchozí připojení přes nástroj pro vyrovnávání zatížení.

![Proxy aplikací je umístěna mezi vzdálené ploše virtuálního počítače a veřejným Internetem.](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

V nasazení vzdálené plochy VP webové role a role Brána vzdálené plochy spustit na počítačích s přístupem k Internetu. Tyto koncové body jsou přístupné z následujících důvodů:
- Veřejný koncový bod se přihlásit a zobrazit různé u místních aplikací a stolní počítače, který bude mít přístup k uživateli poskytuje webu. Po výběru prostředku se vytvoří připojení ke vzdálené ploše pomocí nativní aplikace v operačním systému.
- Brána VP obrázek stává, když uživatel spustí připojení RDP. Brána VP zpracovává šifrovaný provoz protokolu RDP procházející přes internet a převede jej na místním serveru, který uživatel se připojuje k. V tomto scénáři provoz, který přijímá Brána VP pochází z Azure AD Application Proxy.

>[!TIP]
>Pokud nenasadili RDS před, nebo přečtěte si další informace než začnete, zjistěte, jak [bezproblémové nasazení vzdálené plochy s Azure Resource Manageru a webu Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Požadavky

- Používání klienta než webový klient vzdálené plochy, protože webový klient nepodporuje Proxy aplikací.

- Koncové body webu a brány VP musí být umístěn ve stejném počítači a s společný kořen. Webu a brány VP jsou publikovány jako jednu aplikaci pomocí Proxy aplikace tak, že máte jednotné přihlašování mezi těmito dvěma aplikacemi.

- Byste už měli mít [nasazení vzdálené plochy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure), a [povolenou Proxy aplikaci](application-proxy-enable.md).

- Tento scénář předpokládá, že koncoví uživatelé procházejí aplikace Internet Explorer ve Windows 7 nebo Windows 10 stolních počítačů, které se připojují prostřednictvím vzdálené plochy webové stránky. Pokud budete potřebovat pro podporu jiných operačních systémech, přečtěte si téma [podporu pro další konfigurace klienta](#support-for-other-client-configurations).

- Při publikování webu, se doporučuje použít stejné interní a externí plně kvalifikovaný název. Pokud jsou různých plně kvalifikované názvy domény interních a externích byste měli zakázat požádat o překlad hlaviček Chcete-li klientovi příjem neplatné odkazy. 

- V Internet Exploreru povolte doplněk ActiveX vzdálené plochy.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Nasazení společné scénáře vzdálené plochy a Proxy aplikací

Po nastavení vzdálené plochy a Azure AD Application Proxy pro vaše prostředí, postupujte podle kroků ke sloučení dvou řešení. Tyto kroky se provedou v rámci publikování dvěma body přístupem k webové vzdálené plochy (RD Web a služby Brána VP) jako aplikace a pak směrovat provoz na vaše vzdálené plochy a absolvovat Proxy aplikací.

### <a name="publish-the-rd-host-endpoint"></a>Publikování koncových bodů hostitele vzdálené plochy

1. [Publikujte novou aplikaci Proxy aplikací](application-proxy-publish-azure-portal.md) s použitím následujících hodnot:
   - Interní adresa URL: https://\<rdhost\>.com /, kde \<rdhost\> je společný kořen, webu a brány VP sdílet.
   - Externí adresa URL: Toto pole se vyplní automaticky na základě názvu aplikace, ale můžete ho upravit. Vaši uživatelé budou moct tuto adresu URL, když přistupují k vzdálené plochy
   - Metoda předběžného ověření: Azure Active Directory
   - Přeložit hlavička adresy URL: Ne
2. Přiřadíte uživatele k publikované aplikaci VP. Zajistěte, aby že všichni mají přístup k vzdálené ploše, příliš.
3. Nechte jedinou metodu přihlašování pro aplikaci jako **Azure AD jednotné přihlašování zakázáno**. Uživatelům se výzva k ověření jednou do služby Azure AD a jednou na webu, ale mají jednotné přihlašování ve službě Brána VP.
4. Přejděte na **Azure Active Directory** > **registrace aplikací** > *aplikace* > **nastavení**.
5. Vyberte **vlastnosti** a aktualizovat **adresa URL domovské stránky** pole tak, aby odkazovala na váš koncový bod webu (třeba https://\<rdhost\>.com/RDWeb).

### <a name="direct-rds-traffic-to-application-proxy"></a>Směrovat přenos dat vzdálené plochy na Proxy aplikací

Připojení k VP k nasazení jako správce a změňte název serveru služby Brána VP pro nasazení. Tato konfigurace zajistí, že připojení procházejí službou Azure AD Application Proxy.

1. Připojení k serveru vzdálené plochy s rolí Zprostředkovatel připojení k VP.
2. Spuštění **správce serveru**.
3. Vyberte **služby Vzdálená plocha** z podokna na levé straně.
4. Vyberte **Přehled**.
5. V části Přehled nasazení vyberte rozevírací nabídku a zvolte **upravit vlastnosti nasazení**.
6. Na kartě brány VP, změnit **název serveru** zadejte externí adresu URL, kterou jste nastavili pro koncový bod vzdálené plochy hostiteli v Proxy aplikací.
7. Změnit **přihlašovací metoda** pole **ověřování hesla**.

  ![Obrazovky Vlastnosti nasazení na vzdálené plochy](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Spusťte tento příkaz pro každou kolekci. Nahraďte *\<yourcollectionname\>* a *\<proxyfrontendurl\>* nahraďte svými vlastními informacemi. Tento příkaz umožňuje jednotné přihlašování mezi webu a brány VP a optimalizuje výkon:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Příklad:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```

9. Ověření úprav vlastních vlastností RDP také zobrazit obsah souboru RDP, které budou staženy z RDWeb pro tuto kolekci, spusťte následující příkaz:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Teď, když jste nakonfigurovali vzdálené plochy, Azure AD Application Proxy převzalo jako součást přístupem k Internetu vzdálené plochy Na počítačích, webu a brány VP můžete odebrat další veřejné internetové koncové body.

## <a name="test-the-scenario"></a>Otestování tohoto scénáře

Otestování tohoto scénáře pomocí Internet Exploreru na počítači 10 nebo Windows 7.

1. Přejděte na externí adresu URL můžete nastavit nebo najít svoji aplikaci [MyApps panel](https://myapps.microsoft.com).
2. Zobrazí se výzva k ověření do služby Azure Active Directory. Použijte účet, který jste přiřadili k aplikaci.
3. Zobrazí se výzva k ověření na webu.
4. Po úspěšném ověření vzdálené plochy, můžete vybrat na ploše nebo aplikace, kterou chcete a začít pracovat.

## <a name="support-for-other-client-configurations"></a>Podpora pro další konfigurace klienta

Konfigurace popsané v tomto článku je pro uživatele na Windows 7 nebo 10 se aplikace Internet Explorer a doplněk ActiveX vzdálené plochy. Pokud je potřeba, může podporovat jiné operační systémy nebo prohlížeče. Rozdíl je v metodě ověřování, který používáte.

| Metoda ověřování | Podporované klientské konfigurace |
| --------------------- | ------------------------------ |
| Předběžné ověření    | Windows 7/10 pomocí aplikace Internet Explorer + doplněk ActiveX vzdálené plochy |
| Průchod | Libovolný operační systém, který podporuje aplikace Vzdálená plocha od Microsoftu |

Tok předběžného ověření nabízí další výhody zabezpečení než průchozí toku. Předběžné ověření pomocí můžete funkce ověřování Azure AD jako jednotné přihlašování, podmíněného přístupu a dvoustupňové ověřování pro vaše místní prostředky. Můžete také zajistěte, aby, která jenom ověřené přenosy dosáhne vaší sítě.

Pokud chcete používat předávací ověřování, jsou jen dva způsobem upravit kroky uvedené v tomto článku:
1. V [publikovat koncový bod vzdálené plochy hostiteli](#publish-the-rd-host-endpoint) kroku 1, nastavte jako metodu předběžného ověření **průchozí**.
2. V [provozu vzdálené plochy s přímým přístupem na Proxy aplikací](#direct-rds-traffic-to-application-proxy), zcela přeskočit krok 8.

## <a name="next-steps"></a>Další postup

[Povolit vzdálený přístup k Sharepointu pomocí Azure AD Application Proxy](application-proxy-integrate-with-sharepoint-server.md)  
[Informace o zabezpečení pro vzdálený přístup k aplikací s využitím Azure AD Application Proxy](application-proxy-security.md)
