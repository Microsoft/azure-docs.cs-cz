---
title: Azure File Sync místní bránu firewall a nastavení proxy serveru | Microsoft Docs
description: Konfigurace místní sítě Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 93681813c12f0df99909c849e57153e7a64c78fb
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299307"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Nastavení proxy a firewallu Synchronizace souborů Azure
Azure File Sync propojuje vaše místní servery se soubory Azure a povoluje funkce synchronizace více lokalit a vrstvení cloudu. V takovém případě musí být místní server připojený k Internetu. Správce IT musí určit nejlepší cestu pro server, který bude mít přístup k Azure Cloud Services.

Tento článek vám poskytne přehled o specifických požadavcích a možnostech, které jsou k dispozici pro úspěšné a bezpečné připojení serveru k Azure File Sync.

## <a name="overview"></a>Přehled
Azure File Sync slouží jako služba orchestrace mezi vaším Windows serverem, sdílenou složkou Azure a několika dalšími službami Azure pro synchronizaci dat, jak je popsáno ve skupině synchronizace. Aby Azure File Sync fungovalo správně, budete muset nakonfigurovat servery tak, aby komunikovaly s následujícími službami Azure:

- Azure Storage
- Synchronizace souborů Azure
- Azure Resource Manager
- Ověřovací služby

> [!Note]  
> Agent Azure File Sync na Windows serveru zahájí všechny požadavky na cloudové služby, které mají za následek jenom zvážit odchozí přenosy z perspektivy brány firewall. <br /> Žádná služba Azure neinicializuje připojení k agentovi Azure File Sync.

## <a name="ports"></a>Porty
Azure File Sync přesouvá data souborů a metadata exkluzivně přes protokol HTTPS a vyžaduje, aby byl port 443 otevřený odchozí.
V důsledku toho je veškerý provoz zašifrovaný.

## <a name="networks-and-special-connections-to-azure"></a>Sítě a zvláštní připojení k Azure
Agent Azure File Sync nemá žádné požadavky týkající se speciálních kanálů, jako je [ExpressRoute](../../expressroute/expressroute-introduction.md)atd. do Azure.

Azure File Sync budou fungovat s dostupnými prostředky, které umožňují dosahovat Azure, automaticky se přizpůsobí různým charakteristikám sítě, jako je šířka pásma, latence a nabízí administrativní ovládací prvky pro vyladění. V tuto chvíli nejsou k dispozici všechny funkce. Pokud chcete nakonfigurovat konkrétní chování, dejte nám informace prostřednictvím [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy server
Azure File Sync podporuje nastavení proxy serveru pro konkrétní aplikace a na úrovni počítače.

**Nastavení proxy serveru specifické pro aplikaci** umožňuje konfiguraci proxy serveru specificky pro Azure File Sync provoz. Nastavení proxy serveru specifické pro aplikaci jsou podporovaná v agentech verze 4.0.1.0 nebo novější a dají se nakonfigurovat během instalace agenta nebo pomocí rutiny Set-StorageSyncProxyConfiguration prostředí PowerShell.

Příkazy prostředí PowerShell pro konfiguraci nastavení proxy serveru specifických pro aplikaci:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Nastavení proxy serveru pro celý počítač** jsou transparentní pro agenta Azure File Sync, protože celý provoz serveru je směrován přes proxy server.

Pokud chcete nakonfigurovat nastavení proxy serveru na úrovni počítače, postupujte podle následujících kroků: 

1. Konfigurace nastavení proxy serveru pro aplikace .NET 

   - Upravte tyto dva soubory:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Do souborů Machine. config přidejte část < System. NET > (pod sekcí < System. serviceModel >).  Změňte 127.0.01:8888 na IP adresu a port proxy server. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Nastavení proxy serveru WinHTTP 

   - Spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními nebo PowerShellu zobrazte existující nastavení proxy serveru:   

     netsh WinHTTP zobrazit proxy

   - Spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními nebo PowerShellem nastavte nastavení proxy (Change 127.0.01:8888 na IP adresu a port proxy server):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Restartujte službu agenta synchronizace úložiště spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními nebo PowerShellu: 

      NET STOP filesyncsvc

      Poznámka: Služba agenta synchronizace úložiště (filesyncsvc) se po zastavení automaticky spustí.

## <a name="firewall"></a>Brána firewall
Jak je uvedeno v předchozí části, musí být port 443 otevřený odchozí. V závislosti na zásadách ve vašem datovém centru, větvi nebo oblasti se může vyžadovat i další omezení přenosů přes tento port na konkrétní domény.

V následující tabulce jsou popsány požadované domény pro komunikaci:

| Služba | Koncový bod veřejného cloudu | Azure Government koncový bod | Využití |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Jakékoli uživatelské volání (například prostředí PowerShell) odkazuje na tuto adresu URL, včetně počátečního volání registrace serveru. |
| **Azure Active Directory** | https://login.windows.net<br>https://login.microsoftonline.com | https://login.microsoftonline.us | Azure Resource Manager volání musí provádět ověřený uživatel. K úspěšnému použití se tato adresa URL používá pro ověřování uživatelů. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | V rámci nasazení Azure File Sync se vytvoří instanční objekt v Azure Active Directory předplatného. Tato adresa URL se pro tuto adresu používá. Tento objekt zabezpečení se používá k delegování minimální sady práv ke službě Azure File Sync. Uživatel, který provádí počáteční nastavení Azure File Sync, musí být ověřeným uživatelem s oprávněním vlastníka předplatného. |
| **Azure Storage** | &ast;. core.windows.net | &ast;. core.usgovcloudapi.net | Když server stáhne soubor, pak server při přímé komunikaci do sdílené složky Azure v účtu úložiště provede rychlejší přesun dat. Server má klíč SAS, který umožňuje jenom cílenému přístupu ke sdíleným složkám souborů. |
| **Synchronizace souborů Azure** | &ast;. one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | Po počáteční registraci serveru dostane Server místní adresu URL pro instanci služby Azure File Sync Service v této oblasti. Server může adresu URL použít ke komunikaci přímo a efektivně s instancí, která zpracovává její synchronizaci. |
| **Infrastruktura veřejných klíčů Microsoftu** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Po instalaci agenta Azure File Sync se adresa URL PKI používá ke stažení zprostředkujících certifikátů potřebných ke komunikaci se službou Azure File Sync a sdílenou složkou souborů Azure. Adresa URL protokolu OCSP slouží ke kontrole stavu certifikátu. |

> [!Important]
> Když se povolí provoz do &ast;. one.microsoft.com, může ze serveru docházet k více než pouze synchronizační službě. V rámci subdomén je k dispozici mnoho dalších služeb Microsoftu.

Pokud je &ast;. one.microsoft.com příliš široké, můžete omezit komunikaci serveru tím, že povolíte komunikaci jenom s explicitními místními instancemi služby Azure Files Sync. Které instance se mají zvolit, závisí na oblasti služby synchronizace úložiště, kterou jste nasadili a zaregistrovali na serveru. Tato oblast se nazývá "primární adresa URL koncového bodu" v následující tabulce.

V zájmu zajištění provozní kontinuity a zotavení po havárii (BCDR) jste pravděpodobně zadali sdílené složky Azure v globálním redundantním účtu úložiště (GRS). V takovém případě se sdílené složky Azure převezmou v spárované oblasti v případě trvajícího regionálního výpadku. Azure File Sync používá stejné místní párování jako úložiště. Takže pokud používáte účty úložiště GRS, je potřeba povolit další adresy URL, které umožní vašemu serveru komunikovat s spárovanými oblastmi pro Azure File Sync. Následující tabulka volá tuto "spárované oblasti". Kromě toho je k dispozici také adresa URL profilu Traffic Manageru, která musí být povolena. To zajistí plynulé přesměrování síťového provozu do spárované oblasti v případě převzetí služeb při selhání a v následující tabulce se nazývá "adresa URL zjišťování".

| Cloud  | Oblast | Adresa URL primárního koncového bodu | Spárovaná oblast | Adresa URL zjišťování |
|--------|--------|----------------------|---------------|---------------|
| Public |Austrálie – východ | https:\//kailani-aue.one.microsoft.com | Austrálie – jihovýchod | https:\//tm-kailani-aue.one.microsoft.com |
| Public |Austrálie – jihovýchod | https:\//kailani-aus.one.microsoft.com | Austrálie – východ | https:\//tm-kailani-aus.one.microsoft.com |
| Public | Brazílie – jih | https:\//brazilsouth01.afs.azure.net | Střed USA – jih | https:\//tm-brazilsouth01.afs.azure.net |
| Public | Kanada – střed | https:\//kailani-cac.one.microsoft.com | Kanada – východ | https:\//tm-kailani-cac.one.microsoft.com |
| Public | Kanada – východ | https:\//kailani-cae.one.microsoft.com | Kanada – střed | https:\//tm-kailani.cae.one.microsoft.com |
| Public | Střed Indie | https:\//kailani-cin.one.microsoft.com | Indie – jih | https:\//tm-kailani-cin.one.microsoft.com |
| Public | Střed USA | https:\//kailani-cus.one.microsoft.com | Východní USA 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Public | Východní Asie | https:\//kailani11.one.microsoft.com | Jihovýchodní Asie | https:\//tm-kailani11.one.microsoft.com |
| Public | USA – východ | https:\//kailani1.one.microsoft.com | USA – západ | https:\//tm-kailani1.one.microsoft.com |
| Public | Východní USA 2 | https:\//kailani-ess.one.microsoft.com | Střed USA | https:\//tm-kailani-ess.one.microsoft.com |
| Public | Japonsko – východ | https:\//japaneast01.afs.azure.net | Japonsko – západ | https:\//tm-japaneast01.afs.azure.net |
| Public | Japonsko – západ | https:\//japanwest01.afs.azure.net | Japonsko – východ | https:\//tm-japanwest01.afs.azure.net |
| Public | Jižní Korea – střed | https:\//koreacentral01.afs.azure.net/ | Jižní Korea – jih | https:\//tm-koreacentral01.afs.azure.net/ |
| Public | Jižní Korea – jih | https:\//koreasouth01.afs.azure.net/ | Jižní Korea – střed | https:\//tm-koreasouth01.afs.azure.net/ |
| Public | Střed USA – sever | https:\//northcentralus01.afs.azure.net | Střed USA – jih | https:\//tm-northcentralus01.afs.azure.net |
| Public | Severní Evropa | https:\//kailani7.one.microsoft.com | Západní Evropa | https:\//tm-kailani7.one.microsoft.com |
| Public | Střed USA – jih | https:\//southcentralus01.afs.azure.net | Střed USA – sever | https:\//tm-southcentralus01.afs.azure.net |
| Public | Indie – jih | https:\//kailani-sin.one.microsoft.com | Střed Indie | https:\//tm-kailani-sin.one.microsoft.com |
| Public | Jihovýchodní Asie | https:\//kailani10.one.microsoft.com | Východní Asie | https:\//tm-kailani10.one.microsoft.com |
| Public | Velká Británie – jih | https:\//kailani-uks.one.microsoft.com | Spojené království – západ | https:\//tm-kailani-uks.one.microsoft.com |
| Public | Spojené království – západ | https:\//kailani-ukw.one.microsoft.com | Velká Británie – jih | https:\//tm-kailani-ukw.one.microsoft.com |
| Public | Západní střed USA | https:\//westcentralus01.afs.azure.net | USA – západ 2 | https:\//tm-westcentralus01.afs.azure.net |
| Public | Západní Evropa | https:\//kailani6.one.microsoft.com | Severní Evropa | https:\//tm-kailani6.one.microsoft.com |
| Public | USA – západ | https:\//kailani.one.microsoft.com | USA – východ | https:\//tm-kailani.one.microsoft.com |
| Public | USA – západ 2 | https:\//westus201.afs.azure.net | Západní střed USA | https:\//tm-westus201.afs.azure.net |
| Státní správa | USA (Gov) – Arizona | https:\//usgovarizona01.afs.azure.us | USA (Gov) – Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Státní správa | USA (Gov) – Texas | https:\//usgovtexas01.afs.azure.us | USA (Gov) – Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Pokud používáte místně redundantní (LRS) nebo zóny úložiště ZRS (Zone redundantní) účty úložiště, stačí povolit adresu URL uvedenou v části "adresa URL primárního koncového bodu".

- Pokud používáte globálně redundantní účty úložiště (GRS), povolte tři adresy URL.

**Příklad:** Nasadíte službu synchronizace úložiště v `"West US"` a zaregistrujete Server s ním. Adresy URL, se kterými má server komunikovat pro tento případ:

> - https:\//kailani.one.microsoft.com (primární koncový bod: Západní USA)
> - https:\//kailani1.one.microsoft.com (spárovaná oblast převzetí služeb při selhání: Východní USA)
> - https:\//tm-kailani.one.microsoft.com (adresa URL zjišťování primární oblasti)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Seznam povolených pro Azure File Sync IP adres
Pokud místní brána firewall vyžaduje přidání konkrétních IP adres do seznamu povolených adres pro připojení k Azure File Sync, můžete přidat následující rozsahy IP adres na základě oblastí, ke kterým se připojujete.

| Oblast | Rozsahy IP adres |
|--------|-------------------|
| Střed USA | 52.176.149.179/32, 20.37.157.80/29 |
| Východní USA 2 | 40.123.47.110/32, 20.41.5.144/29 |
| USA – východ | 104.41.148.238/32, 20.42.4.248/29 |
| Střed USA – sever | 65.52.62.167/32, 40.80.188.24/29 |
| Střed USA – jih | 104.210.219.252/32, 13.73.248.112/29 |
| USA – západ 2 | 52.183.27.204/32, 20.42.131.224/29 |
| Západní střed USA | 52.161.25.233/32, 52.150.139.104/29 |
| USA – západ | 40.112.150.67/32, 40.82.253.192/29 |
| Kanada – střed | 52.228.42.41/32, 52.228.81.248/29 |
| Kanada – východ | 52.235.36.119/32, 40.89.17.232/29 |
| Brazílie – jih | 191.237.253.115/32, 191.235.225.216/29 |
| Severní Evropa | 40.113.94.67/32, 20.38.85.152/29 |
| Západní Evropa | 104.40.191.8/32, 20.50.1.0/29 |
| Francie – střed | 52.143.166.54/32, 20.43.42.8/29 |
| Francie – jih | 52.136.131.99/32, 51.105.88.248/29 |
| Velká Británie – jih | 51.140.67.72/32, 51.104.25.224/29 |
| Spojené království – západ | 51.140.202.34/32, 51.137.161.240/29 |
| Švýcarsko – sever | 51.107.48.224/29 |
| Švýcarsko – západ | 51.107.144.216/29 |
| Norsko – západ | 51.120.224.216/29 |
| Norsko – východ | 51.120.40.224/29 |
| Východní Asie | 23.102.225.54/32, 20.189.108.56/29 |
| Jihovýchodní Asie | 13.76.81.46/32, 20.43.131.40/29 |
| Austrálie – střed | 20.37.224.216/29 |
| Austrálie – střed 2 | 20.36.120.216/29 |
| Austrálie – východ | 13.75.153.240/32, 20.37.195.96/29 |
| Austrálie – jihovýchod | 13.70.176.196/32, 20.42.227.128/29 |
| Indie – jih | 104.211.231.18/32, 20.41.193.160/29 |
| Indie – západ | 52.136.48.216/29 |
| Japonsko – východ | 104.41.161.113/32, 20.43.66.0/29 |
| Japonsko – západ | 23.100.106.151/32, 40.80.57.192/29 |
| Jižní Korea – střed | 52.231.67.75/32, 20.41.65.184/29 |
| Jižní Korea – jih | 52.231.159.38/32, 40.80.169.176/29 |
| US DoD – východ | 20.140.72.152/29 |
| USA (Gov) – Arizona | 20.140.64.152/29 |
| USA (Gov) – Arizona | 52.244.75.224/32, 52.244.79.140/32 |
| US Gov – Iowa | 52.244.79.140/32, 52.244.75.224/32 |
| USA (Gov) – Texas | 52.238.166.107/32, 52.238.79.29/32 |
| USA (Gov) – Virginia | 13.72.17.152/32, 52.227.153.92/32 |
| Jižní Afrika – sever | 102.133.175.72/32 |
| Jižní Afrika – západ | 102.133.75.173/32, 102.133.56.128/29, 20.140.48.216/29 |
| Spojené arabské emiráty – střed | 20.45.71.151/32, 20.37.64.216/29, 20.140.48.216/29 |
| Spojené arabské emiráty sever | 40.123.216.130/32, 20.38.136.224/29, 20.140.56.136/29 |

## <a name="test-network-connectivity-to-service-endpoints"></a>Otestování připojení k síti ke koncovým bodům služby
Jakmile je server zaregistrován ve službě Azure File Sync, lze pomocí rutiny Test-StorageSyncNetworkConnectivity a ServerRegistration. exe testovat komunikaci se všemi koncovými body (URL) specifickými pro tento server. Tato rutina může pomoct řešit potíže, pokud nekompletní komunikace brání serveru plně pracovat s Azure File Sync a dá se použít k doladění konfigurací proxy a brány firewall.

Pokud chcete spustit test připojení k síti, nainstalujte agenta Azure File Sync verze 9,1 nebo novější a spusťte následující příkazy PowerShellu:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Souhrn a omezení rizik
Seznamy uvedené dříve v tomto dokumentu obsahují adresy URL Azure File Sync aktuálně komunikuje s. Brány firewall musí umožňovat přenos odchozího provozu do těchto domén. Microsoft usiluje o to, aby tento seznam byl aktualizovaný.

Nastavením omezení domény pravidla brány firewall může být míra, která zvyšuje zabezpečení. Pokud se tyto konfigurace brány firewall používají, je potřeba mít na paměti, že se přidají adresy URL a v průběhu času se můžou dokonce měnit. Pravidelně kontrolujte Tento článek.

## <a name="next-steps"></a>Další kroky
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
- [Monitorování Synchronizace souborů Azure](storage-sync-files-monitoring.md)
