---
title: Nastavení brány firewall azure synchronizace souborů a serveru proxy | Dokumenty společnosti Microsoft
description: Konfigurace místní sítě Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f398012edc25ba6a04e230fa8049e7264f857bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294530"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Nastavení proxy a firewallu Synchronizace souborů Azure
Azure File Sync propojuje vaše místní servery se soubory Azure a umožňuje synchronizaci na více pracovištích a funkce cloudového vrstvení. Jako takový musí být místní server připojen k internetu. Správce IT musí rozhodnout o nejlepší cestě pro server, která se dostane do cloudových služeb Azure.

Tento článek vám poskytne přehled o konkrétních požadavcích a možnostech, které jsou k dispozici pro úspěšné a bezpečné připojení serveru k Azure File Sync.

## <a name="overview"></a>Přehled
Azure File Sync funguje jako služba orchestrace mezi Windows Serverem, sdílenou složkou Azure a několika dalšími službami Azure pro synchronizaci dat, jak je popsáno ve vaší synchronizační skupině. Aby Azure File Sync fungovalsprávně, budete muset nakonfigurovat servery tak, aby komunikovaly s následujícími službami Azure:

- Azure Storage
- Synchronizace souborů Azure
- Azure Resource Manager
- Ověřovací služby

> [!Note]  
> Agent Azure File Sync na Windows Serveru iniciuje všechny požadavky na cloudové služby, což má za následek pouze nutnost zvážit odchozí provoz z hlediska brány firewall. <br /> Žádná služba Azure neinicializuje připojení k agentovi Azure File Sync.

## <a name="ports"></a>Porty
Azure File Sync přesune data souboru a metadata výhradně přes HTTPS a vyžaduje port 443 otevřít odchozí.
V důsledku toho je veškerý provoz šifrován.

## <a name="networks-and-special-connections-to-azure"></a>Sítě a speciální připojení k Azure
Agent Azure File Sync nemá žádné požadavky týkající se speciálních kanálů, jako je [ExpressRoute](../../expressroute/expressroute-introduction.md)atd.

Azure File Sync bude fungovat všemi dostupnými prostředky, které umožní dosáhnout do Azure, automaticky se přizpůsobí různým síťovým charakteristikám, jako je šířka pásma, latence, a nabídne ovládací prvek správce pro jemné doladění. V současné době nejsou k dispozici všechny funkce. Pokud chcete nakonfigurovat konkrétní chování, dejte nám vědět prostřednictvím [Služby Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy server
Azure File Sync podporuje nastavení proxy serveru pro konkrétní aplikace a celý počítač.

**Nastavení proxy serveru specifické pro aplikaci** umožňují konfiguraci proxy serveru speciálně pro provoz Azure File Sync. Nastavení proxy serveru specifického pro aplikaci jsou podporována u agenta verze 4.0.1.0 nebo novějšího a lze ji konfigurovat během instalace agenta nebo pomocí rutiny prostředí Nastavení-StorageSyncProxyConfiguration PowerShell.

Příkazy Prostředí PowerShell pro konfiguraci nastavení proxy serveru specifické pro aplikaci:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Nastavení serveru proxy pro celý počítač** je transparentní pro agenta Azure File Sync, protože celý provoz serveru je směrován přes proxy server.

Chcete-li konfigurovat nastavení proxy serveru pro celý počítač, postupujte podle následujících kroků: 

1. Konfigurace nastavení serveru proxy pro aplikace .NET 

   - Upravte tyto dva soubory:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Přidejte oddíl <system.net> do souborů machine.config (pod oddíl <system.serviceModel>).  Změňte 127.0.01:8888 na IP adresu a port proxy serveru. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Nastavení serveru proxy WinHTTP 

   - Spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními nebo prostředí PowerShell zobrazíte existující nastavení proxy serveru:   

     netsh winhttp zobrazit proxy

   - Spusťte následující příkaz z příkazového řádku se zvýšenými oprávněními nebo prostředí PowerShell a nastavte nastavení proxy serveru (změna 127.0.01:8888 na IP adresu a port proxy serveru):  

     netsh winhttp nastavit proxy 127.0.0.1:8888

3. Restartujte službu Storage Sync Agent spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními nebo prostředí PowerShell: 

      net stop filesyncsvc

      Poznámka: Služba Storage Sync Agent (filesyncsvc) se po zastavení automaticky spustí.

## <a name="firewall"></a>Brána firewall
Jak již bylo zmíněno v předchozí části, port 443 musí být otevřen odchozí. Na základě zásad ve vašem datovém centru, pobočce nebo oblasti může být žádoucí nebo vyžadováno další omezení provozu přes tento port na konkrétní domény.

Následující tabulka popisuje požadované domény pro komunikaci:

| Služba | Koncový bod veřejného cloudu | Koncový bod Azure Government | Využití |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Jakékoli volání uživatele (například PowerShell) přejde na nebo prostřednictvím této adresy URL, včetně počátečního volání registrace serveru. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager volání musí být provedeno ověřeným uživatelem. Chcete-li uspět, tato adresa URL se používá pro ověřování uživatelů. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | V rámci nasazení Azure File Sync se vytvoří instanční objekt ve službě Azure Active Directory předplatného. Tato adresa URL se k tomu používá. Tento objekt zabezpečení se používá pro delegování minimální sadu práv na službu Azure File Sync. Uživatel provádějící počáteční nastavení Azure File Sync musí být ověřený uživatel s oprávněními vlastníka předplatného. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Když server stáhne soubor, pak server provede tento přesun dat efektivněji, když mluví přímo do sdílené složky Azure v účtu úložiště. Server má klíč SAS, který umožňuje pouze cílený přístup ke sdílené složce. |
| **Synchronizace souborů Azure** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | Po počáteční registraci serveru server obdrží místní adresu URL pro instanci služby Synchronizace souborů Azure v této oblasti. Server může použít adresu URL ke komunikaci přímo a efektivně s instancí zpracovávající jeho synchronizaci. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Po instalaci agenta Synchronizace souborů Azure se adresa URL PKI používá ke stažení zprostředkujících certifikátů potřebných ke komunikaci se službou Azure File Sync a sdílenou složkou Azure. Adresa URL obcé poznamované z ozeten slouží ke kontrole stavu certifikátu. |

> [!Important]
> Při povolení provozu &ast;na .one.microsoft.com, provoz na více než jen synchronizační služby je možné ze serveru. V rámci subdomén je k dispozici mnoho dalších služeb společnosti Microsoft.

Pokud &ast;je .one.microsoft.com příliš široká, můžete omezit komunikaci serveru povolením komunikace pouze explicitním regionálním instancím služby Azure Files Sync. Které instance(s) zvolit závisí na oblasti služby synchronizace úložiště, které jste nasadili a zaregistrovali server. Tato oblast se nazývá "Primární adresa URL koncového bodu" v následující tabulce.

Z důvodů kontinuity provozu a zotavení po havárii (BCDR) jste možná zadali sdílené složky Azure v globálně redundantním účtu úložiště (GRS). Pokud tomu tak je, pak sdílené složky Azure převezme služby při selhání do spárované oblasti v případě trvalého regionálního výpadku. Azure File Sync používá stejné místní párování jako úložiště. Takže pokud používáte účty úložiště GRS, musíte povolit další adresy URL, aby váš server mohl mluvit s paired region pro Azure File Sync. Níže uvedená tabulka volá tuto "Spárované oblasti". Kromě toho je adresa URL profilu správce provozu, která musí být také povolena. Tím zajistíte, že síťový provoz může být bezproblémově přesměrován do spárované oblasti v případě převzetí služeb při selhání a nazývá se "Adresa URL zjišťování" v následující tabulce.

| Cloud  | Region (Oblast) | Adresa URL primárního koncového bodu | Spárovaná oblast | Adresa URL zjišťování |
|--------|--------|----------------------|---------------|---------------|
| Public |Austrálie – východ | https:\//kailani-aue.one.microsoft.com | Austrálie – jihovýchod | https:\//tm-kailani-aue.one.microsoft.com |
| Public |Austrálie – jihovýchod | https:\//kailani-aus.one.microsoft.com | Austrálie – východ | https:\//tm-kailani-aus.one.microsoft.com |
| Public | Brazílie – jih | https:\//brazilsouth01.afs.azure.net | USA – středojih | https:\//tm-brazilsouth01.afs.azure.net |
| Public | Střední Kanada | https:\//kailani-cac.one.microsoft.com | Kanada – východ | https:\//tm-kailani-cac.one.microsoft.com |
| Public | Kanada – východ | https:\//kailani-cae.one.microsoft.com | Střední Kanada | https:\//tm-kailani.cae.one.microsoft.com |
| Public | Indie – střed | https:\//kailani-cin.one.microsoft.com | Indie – jih | https:\//tm-kailani-cin.one.microsoft.com |
| Public | USA – střed | https:\//kailani-cus.one.microsoft.com | USA – východ 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Public | Východní Asie | https:\//kailani11.one.microsoft.com | Jihovýchodní Asie | https:\//tm-kailani11.one.microsoft.com |
| Public | USA – východ | https:\//kailani1.one.microsoft.com | USA – západ | https:\//tm-kailani1.one.microsoft.com |
| Public | USA – východ 2 | https:\//kailani-ess.one.microsoft.com | USA – střed | https:\//tm-kailani-ess.one.microsoft.com |
| Public | Japonsko – východ | https:\//japaneast01.afs.azure.net | Japonsko – západ | https:\//tm-japaneast01.afs.azure.net |
| Public | Japonsko – západ | https:\//japanwest01.afs.azure.net | Japonsko – východ | https:\//tm-japanwest01.afs.azure.net |
| Public | Jižní Korea – střed | https:\//koreacentral01.afs.azure.net/ | Jižní Korea – jih | https:\//tm-koreacentral01.afs.azure.net/ |
| Public | Jižní Korea – jih | https:\//koreasouth01.afs.azure.net/ | Jižní Korea – střed | https:\//tm-koreasouth01.afs.azure.net/ |
| Public | USA – středosever | https:\//northcentralus01.afs.azure.net | USA – středojih | https:\//tm-northcentralus01.afs.azure.net |
| Public | Severní Evropa | https:\//kailani7.one.microsoft.com | Západní Evropa | https:\//tm-kailani7.one.microsoft.com |
| Public | USA – středojih | https:\//southcentralus01.afs.azure.net | USA – středosever | https:\//tm-southcentralus01.afs.azure.net |
| Public | Indie – jih | https:\//kailani-sin.one.microsoft.com | Indie – střed | https:\//tm-kailani-sin.one.microsoft.com |
| Public | Jihovýchodní Asie | https:\//kailani10.one.microsoft.com | Východní Asie | https:\//tm-kailani10.one.microsoft.com |
| Public | Spojené království – jih | https:\//kailani-uks.one.microsoft.com | Spojené království – západ | https:\//tm-kailani-uks.one.microsoft.com |
| Public | Spojené království – západ | https:\//kailani-ukw.one.microsoft.com | Spojené království – jih | https:\//tm-kailani-ukw.one.microsoft.com |
| Public | USA – středozápad | https:\//westcentralus01.afs.azure.net | USA – západ 2 | https:\//tm-westcentralus01.afs.azure.net |
| Public | Západní Evropa | https:\//kailani6.one.microsoft.com | Severní Evropa | https:\//tm-kailani6.one.microsoft.com |
| Public | USA – západ | https:\//kailani.one.microsoft.com | USA – východ | https:\//tm-kailani.one.microsoft.com |
| Public | USA – západ 2 | https:\//westus201.afs.azure.net | USA – středozápad | https:\//tm-westus201.afs.azure.net |
| Státní správa | USA (Gov) – Arizona | https:\//usgovarizona01.afs.azure.us | USA (Gov) – Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Státní správa | USA (Gov) – Texas | https:\//usgovtexas01.afs.azure.us | USA (Gov) – Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Pokud používáte místně redundantní (LRS) nebo zónově redundantní (ZRS) účty úložiště, stačí povolit adresu URL uvedenou v části "Primární adresa URL koncového bodu".

- Pokud používáte globálně redundantní účty úložiště (GRS), povolte tři adresy URL.

**Příklad:** Nasadíte službu `"West US"` synchronizace úložiště a zaregistrujete s ní server. Adresy URL, které serveru umožňují komunikovat v tomto případě, jsou:

> - https:\//kailani.one.microsoft.com (primární koncový bod: Západní USA)
> - https:\//kailani1.one.microsoft.com (spárovaná oblast převzetí služeb při selhání: Východní USA)
> - https:\//tm-kailani.one.microsoft.com (discovery URL primární oblasti)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Seznam povolených adres IP synchronizace souborů Azure
Azure File Sync podporuje použití [značek služeb](../../virtual-network/service-tags-overview.md), které představují skupinu předpon IP adres pro danou službu Azure. Pomocí značek služeb můžete vytvořit pravidla brány firewall, která umožňují komunikaci se službou Azure File Sync. Značka služby pro Azure `StorageSyncService`File Sync je .

Pokud používáte Azure File Sync v rámci Azure, můžete použít název značky služby přímo ve skupině zabezpečení sítě k povolení provozu. Další informace o tom, jak to provést, naleznete v [tématu Skupiny zabezpečení sítě](../../virtual-network/security-overview.md).

Pokud používáte Azure File Sync místně, můžete použít rozhraní API pro značku služby k získání konkrétních rozsahů IP adres pro seznam povolených adres brány firewall. Existují dva způsoby, jak získat tyto informace:

- Aktuální seznam rozsahů IP adres pro všechny služby Azure podporující značky služeb se každý týden publikuje na webu Služby pro stahování Microsoft Download Center ve formě dokumentu JSON. Každý cloud Azure má svůj vlastní dokument JSON s rozsahy IP adres relevantní pro tento cloud:
    - [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure americká vláda](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure (Čína)](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure (Německo)](https://www.microsoft.com/download/details.aspx?id=57064)
- Rozhraní API pro zjišťování výrobních značek (preview) umožňuje programové načítání aktuálního seznamu značek služeb. Ve verzi Preview může rozhraní API pro zjišťování výrobních značek vracet informace, které jsou méně aktuální než informace vrácené z dokumentů JSON publikovaných na webu služby Stažení softwaru. Povrch rozhraní API můžete použít na základě předvoleb automatizace:
    - [ROZHRANÍ API PRO ODPOČINEK](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

Vzhledem k tomu, že rozhraní API pro zjišťování výrobních značek není aktualizováno tak často jako dokumenty JSON publikované na webu stažení softwaru, doporučujeme k aktualizaci seznamu povolených místních bran firewall použít dokument JSON. To lze provést následujícím způsobem:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

K aktualizaci brány firewall pak `$ipAddressRanges` můžete použít rozsahy IP adres. Informace o tom, jak aktualizovat bránu firewall, naleznete na webu zařízení firewall/síťového zařízení.

## <a name="test-network-connectivity-to-service-endpoints"></a>Testování připojení k síti ke koncovým bodům služby
Jakmile je server zaregistrován službou Azure File Sync, můžete rutinu Test-StorageSyncNetworkConnectivity a ServerRegistration.exe použít k testování komunikace se všemi koncovými body (URL) specifickými pro tento server. Tato rutina může pomoci při řešení potíží, když neúplná komunikace zabrání serveru plně pracovat s Azure File Sync a lze ji použít k jemnému doladění konfigurací proxy a brány firewall.

Chcete-li spustit test připojení k síti, nainstalujte agenta Azure File Sync verze 9.1 nebo novějšího a spusťte následující příkazy prostředí PowerShell:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Shrnutí a omezení rizik
Seznamy dříve v tomto dokumentu obsahují adresy URL Azure File Sync aktuálně komunikuje s. Brány firewall musí být schopny povolit přenosy odchozí do těchto domén. Společnost Microsoft se snaží aktualizovat tento seznam.

Nastavení domény omezující pravidla brány firewall může být opatření ke zlepšení zabezpečení. Pokud se tyto konfigurace brány firewall používají, je třeba mít na paměti, že adresy URL budou přidány a mohou se v průběhu času dokonce měnit. Pravidelně kontrolujte tento článek.

## <a name="next-steps"></a>Další kroky
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
- [Monitorování Synchronizace souborů Azure](storage-sync-files-monitoring.md)
