---
title: Azure File Sync místní bránu firewall a nastavení proxy serveru | Microsoft Docs
description: Konfigurace místní sítě Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 69f4c767b9fc1da90db021ffb3eb8704983ca69b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699309"
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

## <a name="proxy"></a>Proxy
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

      Poznámka: Služba agenta synchronizace úložiště (filesyncsvc) se automaticky spustí po zastavení.

## <a name="firewall"></a>Brána firewall
Jak je uvedeno v předchozí části, musí být port 443 otevřený odchozí. V závislosti na zásadách ve vašem datovém centru, větvi nebo oblasti se může vyžadovat i další omezení přenosů přes tento port na konkrétní domény.

V následující tabulce jsou popsány požadované domény pro komunikaci:

| Služba | Koncový bod veřejného cloudu | Azure Government koncový bod | Použití |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Jakékoli uživatelské volání (například prostředí PowerShell) odkazuje na tuto adresu URL, včetně počátečního volání registrace serveru. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | Azure Resource Manager volání musí provádět ověřený uživatel. K úspěšnému použití se tato adresa URL používá pro ověřování uživatelů. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | V rámci nasazení Azure File Sync se vytvoří instanční objekt v Azure Active Directory předplatného. Tato adresa URL se pro tuto adresu používá. Tento objekt zabezpečení se používá k delegování minimální sady práv ke službě Azure File Sync. Uživatel, který provádí počáteční nastavení Azure File Sync, musí být ověřeným uživatelem s oprávněním vlastníka předplatného. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Když server stáhne soubor, pak server při přímé komunikaci do sdílené složky Azure v účtu úložiště provede rychlejší přesun dat. Server má klíč SAS, který umožňuje jenom cílenému přístupu ke sdíleným složkám souborů. |
| **Azure File Sync** | &ast;.one.microsoft.com | &ast;.afs.azure.us | Po počáteční registraci serveru dostane Server místní adresu URL pro instanci služby Azure File Sync Service v této oblasti. Server může adresu URL použít ke komunikaci přímo a efektivně s instancí, která zpracovává její synchronizaci. |
| **Infrastruktura veřejných klíčů Microsoftu** | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | Po instalaci agenta Azure File Sync se adresa URL PKI používá ke stažení zprostředkujících certifikátů potřebných ke komunikaci se službou Azure File Sync a sdílenou složkou souborů Azure. Adresa URL protokolu OCSP slouží ke kontrole stavu certifikátu. |

> [!Important]
> Při povolování provozu na &ast;. One.Microsoft.com je možné ze serveru použít přenos do více než jenom synchronizační služby. V rámci subdomén je k dispozici mnoho dalších služeb Microsoftu.

Pokud &ast;je. One.Microsoft.com moc široké, můžete komunikaci serveru omezit tím, že povolíte komunikaci jenom s explicitními místními instancemi služby synchronizace souborů Azure. Které instance se mají zvolit, závisí na oblasti služby synchronizace úložiště, kterou jste nasadili a zaregistrovali na serveru. Tato oblast se nazývá "primární adresa URL koncového bodu" v následující tabulce.

V zájmu zajištění provozní kontinuity a zotavení po havárii (BCDR) jste pravděpodobně zadali sdílené složky Azure v globálním redundantním účtu úložiště (GRS). V takovém případě se sdílené složky Azure převezmou v spárované oblasti v případě trvajícího regionálního výpadku. Azure File Sync používá stejné místní párování jako úložiště. Takže pokud používáte účty úložiště GRS, je potřeba povolit další adresy URL, které umožní vašemu serveru komunikovat s spárovanými oblastmi pro Azure File Sync. Následující tabulka volá tuto "spárované oblasti". Kromě toho je k dispozici také adresa URL profilu Traffic Manageru, která musí být povolena. To zajistí plynulé přesměrování síťového provozu do spárované oblasti v případě převzetí služeb při selhání a v následující tabulce se nazývá "adresa URL zjišťování".

| Cloud  | Oblast | Adresa URL primárního koncového bodu | Spárovaná oblast | Adresa URL pro zjišťování |
|--------|--------|----------------------|---------------|---------------|
| Public |Austrálie – východ | https:\//kailani-aue.one.microsoft.com | Austrálie – jihovýchod | https:\//tm-kailani-aue.one.microsoft.com |
| Public |Austrálie – jihovýchod | https:\//kailani-aus.one.microsoft.com | Austrálie – východ | https:\//TM-kailani-AUS.One.Microsoft.com |
| Public | Brazílie – jih | https:\//brazilsouth01.afs.azure.net | Střed USA – jih | https:\//tm-brazilsouth01.afs.azure.net |
| Public | Kanada – střed | https:\//kailani-cac.one.microsoft.com | Kanada – východ | https:\//tm-kailani-cac.one.microsoft.com |
| Public | Kanada – východ | https:\//kailani-cae.one.microsoft.com | Kanada – střed | https:\//tm-kailani.cae.one.microsoft.com |
| Public | Střed Indie | https:\//kailani-cin.one.microsoft.com | Jižní Indie | https:\//tm-kailani-cin.one.microsoft.com |
| Public | Střed USA | https:\//kailani-cus.one.microsoft.com | Východní USA 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Public | Východní Asie | https:\//kailani11.one.microsoft.com | Jihovýchodní Asie | https:\//tm-kailani11.one.microsoft.com |
| Public | East US | https:\//kailani1.one.microsoft.com | USA – západ | https:\//tm-kailani1.one.microsoft.com |
| Public | Východní USA 2 | https:\//kailani-ess.one.microsoft.com | Střed USA | https:\//tm-kailani-ess.one.microsoft.com |
| Public | Japonsko – východ | https:\//japaneast01.afs.azure.net | Japonsko – západ | https:\//tm-japaneast01.afs.azure.net |
| Public | Japonsko – západ | https:\//japanwest01.afs.azure.net | Japonsko – východ | https:\//tm-japanwest01.afs.azure.net |
| Public | Jižní Korea – střed | https:\//koreacentral01.afs.azure.net/ | Jižní Korea – jih | https:\//tm-koreacentral01.afs.azure.net/ |
| Public | Jižní Korea – jih | https:\//koreasouth01.afs.azure.net/ | Jižní Korea – střed | https:\//tm-koreasouth01.afs.azure.net/ |
| Public | Střed USA – sever | https:\//northcentralus01.afs.azure.net | Střed USA – jih | https:\//tm-northcentralus01.afs.azure.net |
| Public | Severní Evropa | https:\//kailani7.one.microsoft.com | Západní Evropa | https:\//tm-kailani7.one.microsoft.com |
| Public | Střed USA – jih | https:\//southcentralus01.afs.azure.net | Střed USA – sever | https:\//tm-southcentralus01.afs.azure.net |
| Public | Jižní Indie | https:\//kailani-sin.one.microsoft.com | Střed Indie | https:\//tm-kailani-sin.one.microsoft.com |
| Public | Jihovýchodní Asie | https:\//kailani10.one.microsoft.com | Východní Asie | https:\//tm-kailani10.one.microsoft.com |
| Public | Velká Británie – jih | https:\//kailani-uks.one.microsoft.com | Spojené království – západ | https:\//TM-kailani-UKS.One.Microsoft.com |
| Public | Spojené království – západ | https:\//kailani-ukw.one.microsoft.com | Velká Británie – jih | https:\//tm-kailani-ukw.one.microsoft.com |
| Public | Západní střed USA | https:\//westcentralus01.afs.azure.net | USA – západ 2 | https:\//tm-westcentralus01.afs.azure.net |
| Public | Západní Evropa | https:\//kailani6.one.microsoft.com | Severní Evropa | https:\//tm-kailani6.one.microsoft.com |
| Public | USA – západ | https:\//kailani.One.Microsoft.com | East US | https:\//TM-kailani.One.Microsoft.com |
| Public | USA – západ 2 | https:\//westus201.afs.azure.net | Západní střed USA | https:\//tm-westus201.afs.azure.net |
| Státní správa | USA (Gov) – Arizona | https:\//usgovarizona01.afs.azure.us | USA (Gov) – Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Státní správa | USA (Gov) – Texas | https:\//usgovtexas01.afs.azure.us | USA (Gov) – Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Pokud používáte místně redundantní (LRS) nebo zóny úložiště ZRS (Zone redundantní) účty úložiště, stačí povolit adresu URL uvedenou v části "adresa URL primárního koncového bodu".

- Pokud používáte globálně redundantní účty úložiště (GRS), povolte tři adresy URL.

**Příklad:** Nasadíte službu synchronizace úložiště v `"West US"` nástroji a zaregistrujete svůj server. Adresy URL, se kterými má server komunikovat pro tento případ:

> - https:\//kailani.One.Microsoft.com (primární koncový bod: Západní USA)
> - https:\//kailani1.One.Microsoft.com (oblast převzetí služeb při selhání se spárováním: Východní USA)
> - https:\//TM-kailani.One.Microsoft.com (adresa URL zjišťování primární oblasti)

## <a name="summary-and-risk-limitation"></a>Souhrn a omezení rizik
Seznamy uvedené dříve v tomto dokumentu obsahují adresy URL Azure File Sync aktuálně komunikuje s. Brány firewall musí umožňovat přenos odchozího provozu do těchto domén. Microsoft usiluje o to, aby tento seznam byl aktualizovaný.

Nastavením omezení domény pravidla brány firewall může být míra, která zvyšuje zabezpečení. Pokud se tyto konfigurace brány firewall používají, je potřeba mít na paměti, že se přidají adresy URL a v průběhu času se můžou dokonce měnit. Pravidelně kontrolujte Tento článek.

## <a name="next-steps"></a>Další postup
- [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
- [Monitorování Synchronizace souborů Azure](storage-sync-files-monitoring.md)
