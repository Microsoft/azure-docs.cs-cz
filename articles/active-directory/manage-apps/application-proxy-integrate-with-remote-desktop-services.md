---
title: Publikování vzdálené plochy pomocí Proxy aplikací služby Azure Active Directory
description: Popisuje, jak nakonfigurovat proxy aplikace pomocí služby Vzdálená plocha (RDS).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 83d7ed6c937d515520058819636bc23c8de173fd
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015272"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publikování Vzdálené plochy pomocí Proxy aplikací služby Azure AD

Služba Vzdálená plocha a Azure Proxy aplikací služby AD společně spolupracují na zlepšení produktivity zaměstnanců, kteří jsou mimo podnikovou síť. 

Zamýšlenou cílovou skupinu pro tento článek:
- Aktuální zákazníci proxy aplikací, kteří chtějí koncovým uživatelům nabídnout další aplikace publikováním místních aplikací prostřednictvím služby Vzdálená plocha.
- Aktuální zákazníci se vzdálenou plochou, kteří chtějí snížit plochu pro útok na nasazení pomocí služby Azure Proxy aplikací služby AD. Tento scénář obsahuje sadu dvou kroků ověřování a řízení podmíněného přístupu pro RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Způsob, jakým se proxy aplikace vejde do standardního nasazení služby RDS

Standardní nasazení služby RDS zahrnuje různé služby role Vzdálená plocha spuštěné v systému Windows Server. Při prohlížení [architektury vzdálené plochy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)je k dispozici několik možností nasazení. Na rozdíl od jiných možností nasazení RDS má [nasazení RDS s Azure proxy aplikací služby AD](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (zobrazené v následujícím diagramu) trvalé odchozí připojení ze serveru, na kterém je spuštěná služba konektoru. Další nasazení ponechají otevřená příchozí připojení prostřednictvím nástroje pro vyrovnávání zatížení.

![Proxy aplikace se nachází mezi virtuálním počítačem RDS a veřejným internetem.](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

V nasazení služby Vzdálená plocha se webová role RD a role Brána VP spouští na počítačích s přístupem k Internetu. Tyto koncové body se zveřejňují z následujících důvodů:
- Web VP poskytuje uživateli veřejný koncový bod pro přihlášení a zobrazení různých místních aplikací a stolních počítačů, ke kterým mají přístup. Po výběru prostředku se připojení RDP vytvoří pomocí nativní aplikace v operačním systému.
- Jakmile uživatel spustí připojení RDP, Brána VP do tohoto obrázku vloží. Brána VP zpracovává šifrovaný provoz protokolu RDP přicházející přes Internet a překládá ho na místní server, ke kterému se uživatel připojuje. V tomto scénáři provoz, který Brána VP přijímá, pochází z Azure Proxy aplikací služby AD.

>[!TIP]
>Pokud jste službu Vzdálená plocha ještě neimplementovali nebo chcete další informace před zahájením práce, přečtěte si, jak [plynule nasadit službu Vzdálená plocha pomocí Azure Resource Manager a Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Požadavky

- Webové a Brána VP koncové body webu VP musí být umístěny ve stejném počítači a se společným kořenem. Web a Brána VP VP jsou publikovány jako jediná aplikace s proxy aplikací, takže můžete mít k dispozici jednotné přihlašování mezi oběma aplikacemi.

- Už byste měli mít [nasazený](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure) [proxy aplikace](application-proxy-add-on-premises-application.md)RDS a Enabled.

- Koncoví uživatelé musí použít kompatibilní prohlížeč pro připojení k webu RD Web nebo webovému klientovi RD. Další podrobnosti najdete v článku [Podpora pro konfigurace klientů](#support-for-other-client-configurations).

- Při publikování webu RD Web se doporučuje použít stejný interní a externí plně kvalifikovaný název domény. Pokud se vnitřní a vnější plně kvalifikovaný název domény liší, měli byste zakázat překlad hlaviček požadavku, aby klient nepřijal neplatné odkazy.

- Pokud používáte web RD v aplikaci Internet Explorer, bude nutné povolit doplněk RDS ActiveX.

- Pokud používáte webového klienta služby Vzdálená plocha, budete muset použít konektor proxy aplikace [verze 1.5.1975 nebo novější](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-release-version-history).

- Pro tok předběžného ověřování Azure AD se uživatelé můžou k publikovaným prostředkům připojit jenom v podokně **aplikace RemoteApp a plochy** . Uživatelé se nemohou připojit k ploše pomocí podokna **připojit k VZDÁLENÉMU počítači** .

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Nasazení společného scénáře proxy serveru RDS a aplikace

Po nastavení služby RDS a Azure Proxy aplikací služby AD pro vaše prostředí postupujte podle kroků a kombinací těchto dvou řešení. Tento postup vás provede publikováním dvou webových koncových bodů vzdálené plochy (RD Web a Brána VP) jako aplikací a následným směrováním provozu v rámci služby Vzdálená plocha (RDS) prostřednictvím proxy aplikací.

### <a name="publish-the-rd-host-endpoint"></a>Publikování koncového bodu hostitele vzdálené plochy

1. [Publikujte novou aplikaci proxy aplikací](application-proxy-add-on-premises-application.md) s následujícími hodnotami:
   - Interní adresa URL: `https://\<rdhost\>.com/` , kde `\<rdhost\>` je společná kořenová složka RD Web a Brána VP.
   - Externí adresa URL: Toto pole se vyplní automaticky na základě názvu aplikace, ale můžete ho upravit. Uživatelé budou při přístupu k VP přejít na tuto adresu URL.
   - Metoda předběžného ověření: Azure Active Directory
   - Přeložit hlavičky adresy URL: ne
2. Přiřaďte uživatele k publikované aplikaci RD. Ujistěte se, že všichni mají přístup ke službě RDS.
3. Ponechte pro aplikaci metodu jednotného přihlašování, jako je **zakázané jednotné přihlašování Azure AD**.

   >[!Note]
   >Uživatelům se zobrazí výzva k ověření jednou pro Azure AD a jednorázově na webu RD Web, ale mají k Brána VP jednotné přihlašování.

4. Vyberte **Azure Active Directory**a pak **Registrace aplikací**. Vyberte aplikaci ze seznamu.
5. V části **Spravovat**vyberte **branding**.
6. Aktualizujte pole **Adresa URL domovské stránky** tak, aby odkazovalo na webový koncový bod služby Vzdálená plocha (například `https://\<rdhost\>.com/RDWeb` ).

### <a name="direct-rds-traffic-to-application-proxy"></a>Přímý provoz RDS do proxy aplikace

Připojte se k nasazení služby Vzdálená plocha jako správce a změňte název Brána VPho serveru pro nasazení. Tato konfigurace zajišťuje, aby připojení procházela prostřednictvím služby Azure Proxy aplikací služby AD.

1. Připojte se k serveru RDS, na kterém běží role Zprostředkovatel připojení k VP.
2. Spusťte **Správce serveru**.
3. V levém podokně vyberte **Služba Vzdálená plocha** .
4. Vyberte **Přehled**.
5. V části Přehled nasazení vyberte rozevírací nabídku a zvolte možnost **Upravit vlastnosti nasazení**.
6. Na kartě Brána VP změňte pole **název serveru** na externí adresu URL, kterou jste nastavili pro koncový bod hostitele vzdálené plochy v proxy aplikaci.
7. Změňte pole **metoda přihlášení** na **ověřování hesla**.

   ![Obrazovka vlastností nasazení v VP](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Spusťte tento příkaz pro každou kolekci. Nahraďte *\<yourcollectionname\>* a *\<proxyfrontendurl\>* vlastními informacemi. Tento příkaz umožňuje jednotné přihlašování mezi webovými a Brána VP VP a optimalizuje výkon:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Příklad:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Výše uvedený příkaz používá v "" nrequire ".

9. Chcete-li ověřit úpravu vlastních vlastností protokolu RDP a zobrazit obsah souboru RDP, který bude stažen z RDWeb pro tuto kolekci, spusťte následující příkaz:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Teď, když jste nakonfigurovali vzdálenou plochu, Azure Proxy aplikací služby AD převzal jako internetovou komponentu služby RDS. Ostatní veřejné koncové body pro Internet můžete odebrat na webu a na počítačích s Brána VP.

### <a name="enable-the-rd-web-client"></a>Povolení webového klienta služby Vzdálená plocha
Pokud chcete, aby uživatelé mohli použít webový klient služby Vzdálená plocha, postupujte podle kroků v [části nastavení webového klienta vzdálené plochy pro vaše uživatele](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin) , abyste mohli tuto možnost povolit.

Webový klient vzdálené plochy umožňuje uživatelům přístup k infrastruktuře vzdálené plochy vaší organizace prostřednictvím webového prohlížeče kompatibilního s HTML5, jako je Microsoft Edge, Internet Explorer 11, Google Chrome, Safari nebo Mozilla Firefox (v 55.0 a novější).

## <a name="test-the-scenario"></a>Testování scénáře

Otestujte scénář pomocí aplikace Internet Explorer v počítači se systémem Windows 7 nebo 10.

1. Přejít na externí adresu URL, kterou jste nastavili, nebo Najděte aplikaci na [panelu MyApp](https://myapps.microsoft.com).
2. Zobrazí se výzva k ověření Azure Active Directory. Použijte účet, který jste přiřadili k aplikaci.
3. Zobrazí se výzva k ověření na webu RD Web.
4. Po úspěšném ověření služby Vzdálená plocha můžete vybrat plochu nebo aplikaci, kterou chcete, a začít pracovat.

## <a name="support-for-other-client-configurations"></a>Podpora pro jiné konfigurace klientů

Konfigurace, která je popsaný v tomto článku, je určena pro přístup k VP prostřednictvím webu RD Web nebo webového klienta RD. Pokud ale potřebujete, můžete podporovat jiné operační systémy nebo prohlížeče. Rozdíl je v metodě ověřování, kterou používáte.

| Metoda ověřování | Podporovaná konfigurace klienta |
| --------------------- | ------------------------------ |
| Předběžné ověřování    | RD Web-Windows 7/10 použití aplikace Internet Explorer nebo [Edge chrom v režimu IE](https://docs.microsoft.com/deployedge/edge-ie-mode) + doplněk RDS ActiveX |
| Předběžné ověřování    | Webový klient služby Vzdálená plocha – webový prohlížeč kompatibilní s HTML5, jako je Microsoft Edge, Internet Explorer 11, Google Chrome, Safari nebo Mozilla Firefox (v 55.0 a novější) |
| Předávací | Jakýkoli jiný operační systém, který podporuje aplikaci Vzdálená plocha Microsoft |

Tok předběžného ověřování nabízí více výhod zabezpečení než tok průchodu. S předběžným ověřením můžete použít funkce ověřování Azure AD, jako je jednotné přihlašování, podmíněný přístup a dvoustupňové ověřování pro vaše místní prostředky. Zajistěte také, aby vaše síť dosáhla pouze ověřeného provozu.

Chcete-li použít předávací ověřování, existují pouze dvě úpravy kroků uvedených v tomto článku:
1. V [části publikovat koncový bod hostitele VP](#publish-the-rd-host-endpoint) krok 1 nastavte metodu předběžného ověřování na **Passthrough**.
2. V [přímém přenosu RDS do proxy aplikace](#direct-rds-traffic-to-application-proxy)vynechejte úplně krok 8.

## <a name="next-steps"></a>Další kroky
- [Povolení vzdáleného přístupu k SharePointu s využitím Proxy aplikací služby Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Požadavky na zabezpečení při vzdáleném přístupu k aplikacím pomocí Azure Proxy aplikací služby AD](application-proxy-security.md)
- [Osvědčené postupy pro vyrovnávání zatížení několika aplikačních serverů](application-proxy-high-availability-load-balancing.md#best-practices-for-load-balancing-among-multiple-app-servers)
