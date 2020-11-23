---
title: Azure File Sync místní bránu firewall a nastavení proxy serveru | Microsoft Docs
description: Pochopte Azure File Sync místní proxy a nastavení brány firewall. Projděte si podrobnosti o konfiguraci portů, sítí a zvláštních připojení k Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/30/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cffa6b1200b7236b3c0a3e48b50c58275cf4c57b
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95316616"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Nastavení proxy a firewallu Synchronizace souborů Azure
Azure File Sync propojuje vaše místní servery se soubory Azure a povoluje funkce synchronizace více lokalit a vrstvení cloudu. V takovém případě musí být místní server připojený k Internetu. Správce IT musí určit nejlepší cestu pro server, který bude mít přístup k Azure Cloud Services.

Tento článek vám poskytne přehled o specifických požadavcích a možnostech, které jsou k dispozici pro úspěšné a bezpečné připojení serveru k Azure File Sync.

Před načtením tohoto průvodce doporučujeme přečíst si [informace o Azure File Syncch sítích](storage-sync-files-networking-overview.md) .

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

**Nastavení proxy serveru specifické pro aplikaci** umožňuje konfiguraci proxy serveru specificky pro Azure File Sync provoz. Nastavení proxy serveru specifické pro aplikaci jsou podporovaná v agentech verze 4.0.1.0 nebo novější a dají se nakonfigurovat během instalace agenta nebo pomocí rutiny Set-StorageSyncProxyConfiguration PowerShellu.

příkazů PowerShellu pro konfiguraci nastavení proxy serveru pro konkrétní aplikaci:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Nastavení proxy serveru pro celý počítač** jsou transparentní pro agenta Azure File Sync, protože celý provoz serveru je směrován přes proxy server.

Pokud chcete nakonfigurovat nastavení proxy serveru pro celý počítač, postupujte následovně: 

1. Konfigurace nastavení proxy serveru pro aplikace .NET 

   - Upravte tyto dva soubory:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Do souborů machine.config přidejte část <> system.net (pod oddílem <System. serviceModel>).  Změňte 127.0.01:8888 na IP adresu a port proxy server. 
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

## <a name="firewall"></a>Firewall
Jak je uvedeno v předchozí části, musí být port 443 otevřený odchozí. V závislosti na zásadách ve vašem datovém centru, větvi nebo oblasti se může vyžadovat i další omezení přenosů přes tento port na konkrétní domény.

V následující tabulce jsou popsány požadované domény pro komunikaci:

| Služba | Koncový bod veřejného cloudu | Azure Government koncový bod | Využití |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Jakékoli uživatelské volání (například prostředí PowerShell) odkazuje na tuto adresu URL, včetně počátečního volání registrace serveru. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager volání musí provádět ověřený uživatel. K úspěšnému použití se tato adresa URL používá pro ověřování uživatelů. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | V rámci nasazení Azure File Sync se vytvoří instanční objekt v Azure Active Directory předplatného. Tato adresa URL se pro tuto adresu používá. Tento objekt zabezpečení se používá k delegování minimální sady práv ke službě Azure File Sync. Uživatel, který provádí počáteční nastavení Azure File Sync, musí být ověřeným uživatelem s oprávněním vlastníka předplatného. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Použijte adresu URL veřejného koncového bodu. | K této adrese URL má přístup Knihovna ověřování Active Directory, kterou používá uživatelské rozhraní pro registraci Azure File Sync serveru pro přihlášení správce. |
| **Azure Storage** | &ast;. core.windows.net | &ast;. core.usgovcloudapi.net | Když server stáhne soubor, pak server při přímé komunikaci do sdílené složky Azure v účtu úložiště provede rychlejší přesun dat. Server má klíč SAS, který umožňuje jenom cílenému přístupu ke sdíleným složkám souborů. |
| **Synchronizace souborů Azure** | &ast;. one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | Po počáteční registraci serveru dostane Server místní adresu URL pro instanci služby Azure File Sync Service v této oblasti. Server může adresu URL použít ke komunikaci přímo a efektivně s instancí, která zpracovává její synchronizaci. |
| **Infrastruktura veřejných klíčů Microsoftu** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Po instalaci agenta Azure File Sync se adresa URL PKI používá ke stažení zprostředkujících certifikátů potřebných ke komunikaci se službou Azure File Sync a sdílenou složkou souborů Azure. Adresa URL protokolu OCSP slouží ke kontrole stavu certifikátu. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;. dl.delivery.mp.microsoft.com<br>&ast;. emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;. dl.delivery.mp.microsoft.com<br>&ast;. emdl.ws.microsoft.com | Po instalaci agenta Azure File Sync se Microsoft Update adresy URL používají ke stažení Azure File Sync aktualizace agenta. |

> [!Important]
> Při povolování provozu do &ast; . AFS.Azure.NET je přenos možné pouze do synchronizační služby. Tuto doménu nepoužívají žádné další služby společnosti Microsoft.
> Při povolování provozu na &ast; . One.Microsoft.com je možné ze serveru použít přenos do více než jenom synchronizační služby. V rámci subdomén je k dispozici mnoho dalších služeb Microsoftu.

Pokud &ast; je. AFS.Azure.NET nebo &ast; . One.Microsoft.com příliš široké, můžete omezit komunikaci serveru tím, že povolíte komunikaci jenom s explicitními místními instancemi služby Azure Files Sync. Které instance se mají zvolit, závisí na oblasti služby synchronizace úložiště, kterou jste nasadili a zaregistrovali na serveru. Tato oblast se nazývá "primární adresa URL koncového bodu" v následující tabulce.

V zájmu zajištění provozní kontinuity a zotavení po havárii (BCDR) jste pravděpodobně zadali sdílené složky Azure v globálním redundantním účtu úložiště (GRS). V takovém případě se sdílené složky Azure převezmou v spárované oblasti v případě trvajícího regionálního výpadku. Azure File Sync používá stejné místní párování jako úložiště. Takže pokud používáte účty úložiště GRS, je potřeba povolit další adresy URL, které umožní vašemu serveru komunikovat s spárovanými oblastmi pro Azure File Sync. Následující tabulka volá tuto "spárované oblasti". Kromě toho je k dispozici také adresa URL profilu Traffic Manageru, která musí být povolena. To zajistí plynulé přesměrování síťového provozu do spárované oblasti v případě převzetí služeb při selhání a v následující tabulce se nazývá "adresa URL zjišťování".

| Cloud  | Oblast | Adresa URL primárního koncového bodu | Spárovaná oblast | Adresa URL zjišťování |
|--------|--------|----------------------|---------------|---------------|
| Veřejná |Austrálie – východ | https: \/ /australiaeast01.AFS.Azure.NET<br>https: \/ /kailani-Aue.One.Microsoft.com | Australia Southeast | https: \/ /TM-australiaeast01.AFS.Azure.NET<br>https: \/ /TM-kailani-Aue.One.Microsoft.com |
| Veřejná |Australia Southeast | https: \/ /australiasoutheast01.AFS.Azure.NET<br>https: \/ /kailani-AUS.One.Microsoft.com | Austrálie – východ | https: \/ /TM-australiasoutheast01.AFS.Azure.NET<br>https: \/ /TM-kailani-AUS.One.Microsoft.com |
| Veřejná | Brazil South | https: \/ /brazilsouth01.AFS.Azure.NET | Středojižní USA | https: \/ /TM-brazilsouth01.AFS.Azure.NET |
| Veřejná | Střední Kanada | https: \/ /canadacentral01.AFS.Azure.NET<br>https: \/ /kailani-CAC.One.Microsoft.com | Kanada – východ | https: \/ /TM-canadacentral01.AFS.Azure.NET<br>https: \/ /TM-kailani-CAC.One.Microsoft.com |
| Veřejná | Kanada – východ | https: \/ /canadaeast01.AFS.Azure.NET<br>https: \/ /kailani-CAE.One.Microsoft.com | Střední Kanada | https: \/ /TM-canadaeast01.AFS.Azure.NET<br>https: \/ /TM-kailani.CAE.One.Microsoft.com |
| Veřejná | Indie – střed | https: \/ /centralindia01.AFS.Azure.NET<br>https: \/ /kailani-CIN.One.Microsoft.com | Indie – jih | https: \/ /TM-centralindia01.AFS.Azure.NET<br>https: \/ /TM-kailani-CIN.One.Microsoft.com |
| Veřejná | Střední USA | https: \/ /centralus01.AFS.Azure.NET<br>https: \/ /kailani-CUS.One.Microsoft.com | USA – východ 2 | https: \/ /TM-centralus01.AFS.Azure.NET<br>https: \/ /TM-kailani-CUS.One.Microsoft.com |
| Veřejná | Východní Asie | https: \/ /eastasia01.AFS.Azure.NET<br>https: \/ /kailani11.One.Microsoft.com | Southeast Asia | https: \/ /TM-eastasia01.AFS.Azure.NET<br>https: \/ /TM-kailani11.One.Microsoft.com |
| Veřejná | East US | https: \/ /eastus01.AFS.Azure.NET<br>https: \/ /kailani1.One.Microsoft.com | USA – západ | https: \/ /TM-eastus01.AFS.Azure.NET<br>https: \/ /TM-kailani1.One.Microsoft.com |
| Veřejná | USA – východ 2 | https: \/ /eastus201.AFS.Azure.NET<br>https: \/ /kailani-ESS.One.Microsoft.com | Střední USA | https: \/ /TM-eastus201.AFS.Azure.NET<br>https: \/ /TM-kailani-ESS.One.Microsoft.com |
| Veřejná | Německo – sever | https: \/ /germanynorth01.AFS.Azure.NET | Německo – středozápad | https: \/ /TM-germanywestcentral01.AFS.Azure.NET |
| Veřejná | Německo – středozápad | https: \/ /germanywestcentral01.AFS.Azure.NET | Německo – sever | https: \/ /TM-germanynorth01.AFS.Azure.NET |
| Veřejná | Japan East | https: \/ /japaneast01.AFS.Azure.NET | Japonsko – západ | https: \/ /TM-japaneast01.AFS.Azure.NET |
| Veřejná | Japonsko – západ | https: \/ /japanwest01.AFS.Azure.NET | Japan East | https: \/ /TM-japanwest01.AFS.Azure.NET |
| Veřejná | Jižní Korea – střed | https: \/ /koreacentral01.AFS.Azure.NET/ | Jižní Korea – jih | https: \/ /TM-koreacentral01.AFS.Azure.NET/ |
| Veřejná | Jižní Korea – jih | https: \/ /koreasouth01.AFS.Azure.NET/ | Jižní Korea – střed | https: \/ /TM-koreasouth01.AFS.Azure.NET/ |
| Veřejná | USA – středosever | https: \/ /northcentralus01.AFS.Azure.NET | Středojižní USA | https: \/ /TM-northcentralus01.AFS.Azure.NET |
| Veřejná | Severní Evropa | https: \/ /northeurope01.AFS.Azure.NET<br>https: \/ /kailani7.One.Microsoft.com | West Europe | https: \/ /TM-northeurope01.AFS.Azure.NET<br>https: \/ /TM-kailani7.One.Microsoft.com |
| Veřejná | Středojižní USA | https: \/ /southcentralus01.AFS.Azure.NET | USA – středosever | https: \/ /TM-southcentralus01.AFS.Azure.NET |
| Veřejná | Indie – jih | https: \/ /southindia01.AFS.Azure.NET<br>https: \/ /kailani-Sin.One.Microsoft.com | Indie – střed | https: \/ /TM-southindia01.AFS.Azure.NET<br>https: \/ /TM-kailani-Sin.One.Microsoft.com |
| Veřejná | Southeast Asia | https: \/ /southeastasia01.AFS.Azure.NET<br>https: \/ /kailani10.One.Microsoft.com | Východní Asie | https: \/ /TM-southeastasia01.AFS.Azure.NET<br>https: \/ /TM-kailani10.One.Microsoft.com |
| Veřejná | Spojené království – jih | https: \/ /uksouth01.AFS.Azure.NET<br>https: \/ /kailani-UKS.One.Microsoft.com | Spojené království – západ | https: \/ /TM-uksouth01.AFS.Azure.NET<br>https: \/ /TM-kailani-UKS.One.Microsoft.com |
| Veřejná | Spojené království – západ | https: \/ /ukwest01.AFS.Azure.NET<br>https: \/ /kailani-UKW.One.Microsoft.com | Spojené království – jih | https: \/ /TM-ukwest01.AFS.Azure.NET<br>https: \/ /TM-kailani-UKW.One.Microsoft.com |
| Veřejná | USA – středozápad | https: \/ /westcentralus01.AFS.Azure.NET | Západní USA 2 | https: \/ /TM-westcentralus01.AFS.Azure.NET |
| Veřejná | West Europe | https: \/ /westeurope01.AFS.Azure.NET<br>https: \/ /kailani6.One.Microsoft.com | Severní Evropa | https: \/ /TM-westeurope01.AFS.Azure.NET<br>https: \/ /TM-kailani6.One.Microsoft.com |
| Veřejná | USA – západ | https: \/ /westus01.AFS.Azure.NET<br>https: \/ /kailani.One.Microsoft.com | East US | https: \/ /TM-westus01.AFS.Azure.NET<br>https: \/ /TM-kailani.One.Microsoft.com |
| Veřejná | Západní USA 2 | https: \/ /westus201.AFS.Azure.NET | USA – středozápad | https: \/ /TM-westus201.AFS.Azure.NET |
| Státní správa | USA (Gov) – Arizona | https: \/ /usgovarizona01.AFS.Azure.us | USA (Gov) – Texas | https: \/ /TM-usgovarizona01.AFS.Azure.us |
| Státní správa | USA (Gov) – Texas | https: \/ /usgovtexas01.AFS.Azure.us | USA (Gov) – Arizona | https: \/ /TM-usgovtexas01.AFS.Azure.us |

- Pokud používáte místně redundantní (LRS) nebo zóny úložiště ZRS (Zone redundantní) účty úložiště, stačí povolit adresu URL uvedenou v části "adresa URL primárního koncového bodu".

- Pokud používáte globálně redundantní účty úložiště (GRS), povolte tři adresy URL.

**Příklad:** Nasadíte službu synchronizace úložiště v `"West US"` nástroji a zaregistrujete svůj server. Adresy URL, se kterými má server komunikovat pro tento případ:

> - https: \/ /westus01.AFS.Azure.NET (primární koncový bod: západní USA)
> - https: \/ /eastus01.AFS.Azure.NET (oblast převzetí služeb při selhání, která se spáruje: východní USA)
> - https: \/ /TM-westus01.AFS.Azure.NET (adresa URL zjišťování primární oblasti)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Seznam povolených pro Azure File Sync IP adres
Azure File Sync podporuje použití [značek služeb](../../virtual-network/service-tags-overview.md), které reprezentují skupinu předpon IP adres pro danou službu Azure. Pomocí značek služby můžete vytvořit pravidla brány firewall, která umožňují komunikaci s Azure File Sync službou. Označení služby pro Azure File Sync je `StorageSyncService` .

Pokud používáte Azure File Sync v rámci Azure, můžete pro povolení provozu použít ve skupině zabezpečení sítě přímo název značky služby. Další informace o tom, jak to provést, najdete v tématu [skupiny zabezpečení sítě](../../virtual-network/network-security-groups-overview.md).

Pokud používáte Azure File Sync v místním prostředí, můžete k získání specifických rozsahů IP adres pro seznam povolených bran firewall použít rozhraní API tag služby. Existují dvě metody pro získání těchto informací:

- Aktuální seznam rozsahů IP adres pro všechny služby Azure, které podporují značky služby, se každý týden zveřejňuje na webu služby Stažení softwaru ve formě dokumentu JSON. Každý cloud Azure má svůj vlastní dokument JSON s rozsahy IP adres, které jsou relevantní pro tento Cloud:
    - [Veřejné Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure pro vládu USA](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure (Čína)](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure (Německo)](https://www.microsoft.com/download/details.aspx?id=57064)
- Rozhraní API zjišťování značek služby (Preview) umožňuje programové načtení aktuálního seznamu značek služeb. Ve verzi Preview může rozhraní API zjišťování značek služby vracet informace, které jsou méně aktuální než informace vrácené z dokumentů JSON publikovaných na webu Microsoft Download Center. Plochu rozhraní API můžete použít na základě preference automatizace:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az-network-list-service-tags)

Vzhledem k tomu, že se rozhraní API zjišťování značek služeb neaktualizuje tak často, jako dokumenty JSON publikované na webu Microsoft Download Center, doporučujeme k aktualizaci seznamu povolených místních bran firewall použít dokument JSON. Můžete to udělat takto:

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

Pak můžete použít rozsahy IP adres v nástroji `$ipAddressRanges` k aktualizaci brány firewall. Informace o tom, jak aktualizovat bránu firewall, najdete na webu brány firewall/síťového zařízení.

## <a name="test-network-connectivity-to-service-endpoints"></a>Otestování připojení k síti ke koncovým bodům služby
Jakmile je server zaregistrovaný ve službě Azure File Sync, můžete použít rutinu Test-StorageSyncNetworkConnectivity a ServerRegistration.exe k otestování komunikace se všemi koncovými body (URL) specifickými pro tento server. Tato rutina může pomoct řešit potíže, pokud nekompletní komunikace brání serveru plně pracovat s Azure File Sync a dá se použít k doladění konfigurací proxy a brány firewall.

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
- [Sledování služby Synchronizace souborů Azure](storage-sync-files-monitoring.md)
