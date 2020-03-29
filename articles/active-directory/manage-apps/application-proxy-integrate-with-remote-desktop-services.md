---
title: Publikování vzdálené plochy pomocí proxy aplikace Azure AD | Dokumenty společnosti Microsoft
description: Popisuje základy konektorů proxy aplikací Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6ca64e2de5734c567173fc735776074f4c87fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108470"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publikování vzdálené plochy pomocí proxy aplikace Azure AD

Vzdálená plocha a proxy aplikace Azure AD spolupracují na zlepšení produktivity pracovníků, kteří jsou mimo podnikovou síť. 

Zamýšlené publikum pro tento článek je:
- Aktuální zákazníci proxy aplikací, kteří chtějí svým koncovým uživatelům nabízet více aplikací publikováním místních aplikací prostřednictvím služby Vzdálená plocha.
- Aktuální zákazníci služby Vzdálená plocha, kteří chtějí snížit prostor pro útok na jejich nasazení pomocí proxy aplikací Azure AD. Tento scénář poskytuje omezenou sadu dvoustupňové ověření a podmíněný přístup ovládacíprvky RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Jak proxy aplikace zapadá do standardního nasazení RDS

Standardní nasazení služby RdS zahrnuje různé služby role Vzdálená plocha spuštěné v systému Windows Server. Při pohledu na [architekturu služby Vzdálená plocha](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)existuje několik možností nasazení. Na rozdíl od jiných možností nasazení RDS má [nasazení RDS s proxy aplikací Azure AD](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (znázorněno na následujícím diagramu) trvalé odchozí připojení ze serveru se službou konektoru. Ostatní nasazení ponechat otevřené příchozí připojení prostřednictvím vyrovnávání zatížení.

![Aplikační proxy server je mezi virtuálním virtuálním operátorem RDS a veřejným internetem](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

V nasazení rds se role Webu v y PV a role brány VP spouštějí na počítačích orientovaných na Internet. Tyto koncové body jsou vystaveny z následujících důvodů:
- Rd Web poskytuje uživateli veřejný koncový bod pro přihlášení a zobrazení různých místních aplikací a ploch, ke kterým má přístup. Při výběru prostředku se vytvoří připojení RDP pomocí nativní aplikace v os.
- Rd Gateway přichází do obrazu, jakmile uživatel spustí připojení RDP. Brána VP zpracovává šifrovaný provoz RDP přicházející přes Internet a překládá jej na místní server, ke kterému se uživatel připojuje. V tomto scénáři provoz brány VP přijímá pochází z proxy aplikace Azure AD.

>[!TIP]
>Pokud jste rds ještě nenasadili nebo chcete před zahájením získat další informace, přečtěte si, jak [bezproblémově nasadit RDS pomocí Azure Resource Manageru a Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Požadavky

- Použijte jiného klienta než webového klienta vzdálené plochy, protože webový klient nepodporuje proxy aplikace.

- Koncové body služby Rd Web i Rd Gateway musí být umístěny ve stejném počítači a se společným kořenem. Web VP a Brána VP jsou publikovány jako jedna aplikace s proxy aplikací, takže mezi těmito dvěma aplikacemi můžete mít jednotné přihlašování.

- Měli jste již [nasadit rds](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)a [povolit proxy aplikace](application-proxy-add-on-premises-application.md).

- Tento scénář předpokládá, že koncoví uživatelé procházejí aplikací Internet Explorer na stolních počítačích se systémem Windows 7 nebo Windows 10, které se připojují prostřednictvím webové stránky VP. Pokud potřebujete podporovat jiné operační systémy, přečtěte si [informace o podpoře dalších konfigurací klientů](#support-for-other-client-configurations).

- Při publikování webu v oblasti VP se doporučuje používat stejný interní a externí vícestranný název sítě. Pokud se interní a externí vícenežnové oblasti se liší, měli byste zakázat překlad hlavičky požadavku, abyste zabránili tomu, aby klient obdržel neplatné odkazy. 

- V aplikaci Internet Explorer povolte doplněk RdS ActiveX.

- Pro tok předběžného ověřování Azure AD se uživatelé můžou připojit jenom k prostředkům, které jsou k nim publikovány v podokně **RemoteApp a Plochy.** Uživatelé se nemohou připojit ke stolní ploše pomocí **podokna Připojit ke vzdálenému počítači.**

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Nasazení scénáře společného protokolu RDS a proxy aplikace

Po nastavení rds a Proxy aplikace Azure AD pro vaše prostředí, postupujte podle pokynů kombinovat dvě řešení. Tyto kroky procházejí publikováním dvou koncových bodů RDS směřujících k webu (Rd Web a Rd Gateway) jako aplikací a následným přesměrováním provozu na rds, aby procházely proxy aplikace.

### <a name="publish-the-rd-host-endpoint"></a>Publikování koncového bodu hostitele VP

1. [Publikujte novou aplikaci Proxy aplikace](application-proxy-add-on-premises-application.md) s následujícími hodnotami:
   - Interní adresa `https://\<rdhost\>.com/`URL: , kde `\<rdhost\>` je společný kořen, který sdílejí web VP a brána VP.
   - Externí adresa URL: Toto pole je automaticky vyplněno na základě názvu aplikace, ale můžete jej upravit. Uživatelé přejdou na tuto adresu URL při přístupu k rds.
   - Metoda předběžného ověřování: Služba Azure Active Directory
   - Přeložit záhlaví adres URL: Ne
2. Přiřaďte uživatele k publikované aplikaci VP. Ujistěte se, že všichni mají přístup k RDS, taky.
3. Ponechte metodu jednotného přihlašování pro aplikaci jako **azure ad jednotné přihlašování zakázáno**. Vaši uživatelé jsou požádáni o ověření jednou azure a d. a jednou na webu VP, ale mají jednotné přihlášení k bráně VP.
4. Vyberte **Službu Azure Active Directory**a potom **registraci aplikací**. Vyberte si aplikaci ze seznamu.
5. V části **Správa**vyberte **Branding**.
6. Aktualizujte pole **URL domovské stránky** tak, aby `https://\<rdhost\>.com/RDWeb`ukazovalo na koncový bod webu VP (například).

### <a name="direct-rds-traffic-to-application-proxy"></a>Přímý provoz rds na proxy aplikace

Připojte se k nasazení služby RdS jako správce a změňte název serveru služby Brána VP pro nasazení. Tato konfigurace zajišťuje, že připojení procházejí službou Proxy aplikací Azure AD.

1. Připojte se k serveru RDS, na který je spuštěna role Zprostředkovatel připojení k VP.
2. Spusťte **Správce serveru**.
3. V podokně vlevo vyberte **Vzdálená plocha.**
4. Vyberte **Přehled**.
5. V části Přehled nasazení vyberte rozevírací nabídku a zvolte **Upravit vlastnosti nasazení**.
6. Na kartě Brána VP změňte pole **Název serveru** na externí adresu URL nastavenou pro koncový bod hostitele VP v proxy aplikaci.
7. Změňte pole **Metody přihlášení** na **Ověřování hesla**.

   ![Obrazovka Vlastnosti nasazení v rds](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Spusťte tento příkaz pro každou kolekci. Nahraďte * \<\> název collectionname* a * \<proxyfrontendurl\> * vlastními informacemi. Tento příkaz povoluje jednotné přihlašování mezi službou Rd Web a bránou VP a optimalizuje výkon:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Například:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Výše uvedený příkaz používá backtick v "'nrequire".

9. Chcete-li ověřit změnu vlastních vlastností protokolu RDP a zobrazit obsah souboru PROTOKOLU RDP, který bude stažen z webu RDWeb pro tuto kolekci, spusťte následující příkaz:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Teď, když jste nakonfigurovali vzdálenou plochu, proxy aplikace Azure AD převzal a to jako součást RDS směřující k internetu. Ostatní veřejné koncové body směřující k Internetu můžete odebrat na počítačích rd web a rd gateway.

## <a name="test-the-scenario"></a>Otestujte scénář

Otestujte scénář s aplikací Internet Explorer v počítači se systémem Windows 7 nebo 10.

1. Přejděte na externí adresu URL, kterou jste nastavili, nebo vyhledejte aplikaci v [panelu MyApps](https://myapps.microsoft.com).
2. Budete vyzváni k ověření služby Azure Active Directory. Použijte účet, který jste přiřadili k aplikaci.
3. Budete vyzváni k ověření na webu RD.
4. Jakmile bude ověření rds úspěšné, můžete vybrat požadovanou plochu nebo aplikaci a začít pracovat.

## <a name="support-for-other-client-configurations"></a>Podpora pro ostatní konfigurace klientů

Konfigurace uvedená v tomto článku je pro uživatele v systému Windows 7 nebo 10, s aplikací Internet Explorer a doplňkem RdS ActiveX. Pokud však potřebujete, můžete podporovat jiné operační systémy nebo prohlížeče. Rozdíl je v metodě ověřování, kterou používáte.

| Metoda ověřování | Podporovaná konfigurace klienta |
| --------------------- | ------------------------------ |
| Předběžné ověřování    | Windows 7/10 pomocí doplňku Internet Explorer + RDS ActiveX |
| Passthrough | Jakýkoli jiný operační systém, který podporuje aplikaci Vzdálená plocha společnosti Microsoft |

Tok předběžného ověřování nabízí více výhod zabezpečení než tok průchodu. S předběžným ověřováním můžete pro místní prostředky používat funkce ověřování Azure AD, jako je jednotné přihlašování, podmíněný přístup a dvoustupňové ověřování. Zajistíte také, aby se do vaší sítě dostal pouze ověřený provoz.

Chcete-li použít passthrough ověřování, existují pouze dvě změny kroků uvedených v tomto článku:
1. V [části Publikovat koncový bod hostitele VP](#publish-the-rd-host-endpoint) krok 1 nastavte metodu předběžného ověřování na **passthrough**.
2. V [přímém provozu RDS na proxy aplikace](#direct-rds-traffic-to-application-proxy), přeskočit krok 8 úplně.

## <a name="next-steps"></a>Další kroky

[Povolení vzdáleného přístupu k SharePointu s využitím Proxy aplikací služby Azure AD](application-proxy-integrate-with-sharepoint-server.md)  
[Důležité informace o zabezpečení pro vzdálený přístup k aplikacím pomocí proxy aplikací Azure AD](application-proxy-security.md)
