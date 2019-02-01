---
title: Azure File Sync v místním nastavení brány firewall a proxy | Dokumentace Microsoftu
description: Azure File Sync místní konfigurace sítě
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 11/26/2018
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: c0a1d2bf1d7a103ad473cadb1528bd9b9a4c90de
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488012"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Nastavení proxy a firewallu Synchronizace souborů Azure
Azure File Sync se připojí k Azure Files umožňuje synchronizaci více webů a funkce vrstvení cloudu na místních serverech. V důsledku toho musí být na místním serveru připojený k Internetu. Správce IT je potřeba rozhodnout nejlepší cestu pro server k získání přístupu do cloudových služeb Azure.

Tento článek poskytuje přehled o konkrétní požadavky a možnosti dostupné pro úspěšně a bezpečného připojení serveru k Azure File Sync.

> [!Important]
> Azure File Sync zatím nepodporuje brány firewall a virtuální sítě pro účet úložiště.

## <a name="overview"></a>Přehled
Azure File Sync funguje jako službu Orchestrace mezi systému Windows Server, sdílené složky Azure a několik dalších služeb Azure, synchronizaci dat, jak je popsáno ve vaší skupině synchronizace. Pro Azure File Sync fungovala správně budete potřebovat ke konfiguraci serverů ke komunikaci s těmito službami Azure:

- Azure Storage
- Synchronizace souborů Azure
- Azure Resource Manager
- Ověřovací služby

> [!Note]  
> Agenta Azure File Sync v systému Windows Server spustí všechny požadavky na cloudové služby, čímž vznikne pouze by bylo třeba řešit odchozího provozu z hlediska brány firewall. <br /> Žádné služby Azure zahájí připojení agenta Azure File Sync.

## <a name="ports"></a>Porty
Azure File Sync přesune data souborů a metadat výhradně prostřednictvím protokolu HTTPS a vyžaduje se otevřít odchozí port 443.
V důsledku veškerý provoz se šifruje.

## <a name="networks-and-special-connections-to-azure"></a>Speciální připojení k Azure a sítě
Agenta Azure File Sync nemá žádné požadavky týkající se speciálních kanály, jako jsou [ExpressRoute](../../expressroute/expressroute-introduction.md), atd. a Azure.

Na všechny prostředky k dispozici, díky kterým můžou dosah do Azure, automaticky přizpůsobuje různým charakteristiky sítě jako šířky pásma, latence stejně jako nabídka řízení správy pro optimalizaci exportovatelného bude spolupracovat Azure File Sync. Ne všechny funkce jsou v tuto chvíli k dispozici. Pokud chcete nakonfigurovat konkrétní chování, dejte nám vědět prostřednictvím [UserVoice soubory Azure](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy server
Azure File Sync podporuje nastavení proxy serveru pro konkrétní aplikace a celý počítač.

**Nastavení proxy serveru pro konkrétní aplikace** povolit konfiguraci proxy serveru speciálně pro provoz Azure File Sync. Nastavení serveru proxy konkrétní aplikace jsou podporované ve verzi agenta 4.0.1.0 nebo novější a je možné konfigurovat během instalace agenta nebo pomocí rutiny prostředí PowerShell Set-StorageSyncProxyConfiguration.

Příkazy prostředí PowerShell a zadejte nastavení proxy server pro konkrétní aplikaci:
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Nastavení serveru proxy pro celý počítač** jsou transparentní pro agenta Azure File Sync celý provoz serveru je směrován přes proxy server.

Konfigurace nastavení proxy pro celý počítač, použijte následující postup: 

1. Konfigurace nastavení proxy serveru pro aplikace .NET 

  - Upravte tyto dva soubory:  
    C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
    C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

  - Přidání oddílu < system.net > v souboru machine.config (níže v části < system.serviceModel >).  Změňte 127.0.01:8888 na IP adresu a port proxy serveru. 
  ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
  ```

2. Nastavení konfigurace proxy serveru WinHTTP 

  - Spusťte následující příkaz z příkazového řádku se zvýšenými oprávněními nebo z Powershellu, pokud chcete zobrazit stávající nastavení proxy serveru:   

    netsh winhttp zobrazit proxy

  - Spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními nebo z prostředí PowerShell nastavit nastavení proxy serveru (změnit 127.0.01:8888 na IP adresu a port proxy serveru):  

    netsh winhttp nastavit proxy 127.0.0.1:8888

3. Spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními nebo z Powershellu restartujte službu agenta synchronizace úložiště: 

      filesyncsvc net stop

      Poznámka: Služba agenta synchronizace úložiště (filesyncsvc) se automaticky spouštěná jednou zastavena.

## <a name="firewall"></a>Brána firewall
Jak je uvedeno v předchozí části, otevřít odchozí port 443 musí být. Na základě zásad v datovém centru, větvi nebo oblasti, další omezení provozu přes tento port na vybrané domény může být požadovaného nebo vyžaduje.

Následující tabulka popisuje požadovaných domén pro komunikace:

| Služba | Domain (Doména) | Využití |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Jakékoli volání uživatele (jako je PowerShell) prochází přes tuto adresu URL, včetně volání registrace počáteční server. |
| **Azure Active Directory** | https://login.windows.net | Ověřený uživatel se musí provádět volání Azure Resource Manageru. Úspěšné, tato adresa URL slouží k ověřování uživatelů. |
| **Azure Active Directory** | https://graph.windows.net/ | Jako součást nasazení Azure File Sync se vytvoří instanční objekt služby ve službě Active Directory předplatného Azure. Pro, který se používá tuto adresu URL. Tento objekt se používá pro delegování minimální sadu práv ve službě Azure File Sync. Uživatel provádějící počáteční nastavení služby Azure File Sync musí být ověřený uživatel s oprávněními vlastníka předplatného. |
| **Azure Storage** | &ast;.core.windows.net | Pokud server stáhne soubor, pak server provede tento přesun dat efektivněji přímo s sdílené složky Azure v účtu úložiště. Server má klíč SAS, která povoluje jenom pro přístup ke sdílené složce cílového souboru. |
| **Azure File Sync** | &ast;.one.microsoft.com | Po registraci počáteční server přijímá na serveru místní adresu URL instance služby Azure File Sync v dané oblasti. Server může komunikovat přímo a efektivně s instancí zpracování synchronizace. použijte adresu URL. |
| **Microsoft PKI** | http://ocsp.msocsp.com | Po instalaci agenta Azure File Sync se adresa URL infrastruktury veřejných KLÍČŮ se používá ke stahování zprostředkující certifikáty vyžadované pro komunikaci se službou Azure File Sync a sdílené složky Azure. Adresa URL protokolu OCSP se používá ke kontrole stavu certifikátu. |

> [!Important]
> Při povolení provozu na &ast;. one.microsoft.com, provoz do více než jen synchronizační služby je možné ze serveru. Nejsou k dispozici v části subdomény mnoho další služby Microsoftu.

Pokud &ast;. one.microsoft.com je příliš široké, komunikaci serverem můžete omezit tím, že komunikaci pouze explicitní místní instance služby Azure File Sync. Které instancí zvolit závisí na oblasti mají nasazení a registraci serveru do služby synchronizace úložiště. Tuto oblast se nazývá "primární koncový bod adresy URL" v následující tabulce.

Pro provozní kontinuitu a po havárii (BCDR) obnovení důvodů jste zadali, že vaše sdílených složek Azure v účtu globálně redundantní úložiště (GRS). Pokud je to tento případ, pak sdílených složek Azure převezme spárované oblasti v případě výpadku oblasti trvalé zhodnocení. Azure File Sync používá stejné oblastní párování jako úložiště. Takže pokud používáte účty úložiště GRS, je potřeba povolit další adresy URL, aby váš server komunikovat s spárované oblasti pro Azure File Sync. Následující tabulka volá této "Paired oblasti". Kromě toho je adresa URL profilu Správce provozu, která musí být povolena také. Tím se zajistí síťový provoz můžete bezproblémově znovu směrovat do spárované oblasti v případě převzetí služeb při selhání a se nazývá "Zjišťování adresy URL" v následující tabulce.

| Oblast | Primární koncový bod adresy URL | Spárovaná oblast | Adresa URL pro zjišťování |
|--------|---------------------------------------|--------|---------------------------------------|
| Austrálie – východ | https://kailani-aue.one.microsoft.com | Austrálie – jihovýchod | https://kailani-aue.one.microsoft.com |
| Austrálie – jihovýchod | https://kailani-aus.one.microsoft.com | Austrálie – východ | https://tm-kailani-aus.one.microsoft.com |
| Kanada – střed | https://kailani-cac.one.microsoft.com | Kanada – východ | https://tm-kailani-cac.one.microsoft.com |
| Kanada – východ | https://kailani-cae.one.microsoft.com | Kanada – střed | https://tm-kailani.cae.one.microsoft.com |
| USA – střed | https://kailani-cus.one.microsoft.com | Východní USA 2 | https://tm-kailani-cus.one.microsoft.com |
| Východní Asie | https://kailani11.one.microsoft.com | Jihovýchodní Asie | https://tm-kailani11.one.microsoft.com |
| USA – východ | https://kailani1.one.microsoft.com | Západní USA | https://tm-kailani1.one.microsoft.com |
| Východ USA 2 | https://kailani-ess.one.microsoft.com | USA – střed | https://tm-kailani-ess.one.microsoft.com |
| Severní Evropa | https://kailani7.one.microsoft.com | Západní Evropa | https://tm-kailani7.one.microsoft.com |
| Jihovýchodní Asie | https://kailani10.one.microsoft.com | Východní Asie | https://tm-kailani10.one.microsoft.com |
| Velká Británie – jih | https://kailani-uks.one.microsoft.com | Spojené království – západ | https://tm-kailani-uks.one.microsoft.com |
| Spojené království – západ | https://kailani-ukw.one.microsoft.com | Velká Británie – jih | https://tm-kailani-ukw.one.microsoft.com |
| Západní Evropa | https://kailani6.one.microsoft.com | Severní Evropa | https://tm-kailani6.one.microsoft.com |
| Západní USA | https://kailani.one.microsoft.com | USA – východ | https://tm-kailani.one.microsoft.com |

- Pokud používáte místně redundantní (LRS) a účty úložiště (ZRS) redundantní zóny, potřebujete jenom povolit adresu URL v části "primární koncový bod adresy URL".

- Pokud používáte účty globálně redundantní úložiště (GRS), povolte tři adresy URL.

**Příklad:** Nasazení služby synchronizace úložiště v `"West US"` a zaregistrovat svůj server. Adresy URL, aby server ke komunikaci se pro tento případ jsou:

> - https://kailani.one.microsoft.com (primární koncový bod: USA – západ)
> - https://kailani1.one.microsoft.com (spárované oblasti převzetí služeb při selhání: USA – východ)
> - https://tm-kailani.one.microsoft.com (adresa URL pro zjišťování z primární oblasti)

## <a name="summary-and-risk-limitation"></a>Omezení rizika a souhrn
Seznamy dříve v tomto dokumentu obsahují adresy URL Azure File Sync aktuálně komunikuje s. Brány firewall musí být schopen přenosy odchozích těchto domén. Microsoft se snaží zachovat tento seznam se aktualizoval.

Nastavení domény omezení pravidel brány firewall může být míra pro vylepšení zabezpečení. Pokud se používají tyto konfigurace brány firewall, třeba Pamatujte, že adresy URL se přidají a může dokonce i v průběhu času měnit. Pravidelně ji kontrolujte, v tomto článku.

## <a name="next-steps"></a>Další postup
- [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- [Nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorování Azure File Sync](storage-sync-files-monitoring.md)
