---
title: Azure File Sync v místním nastavení brány firewall a proxy | Dokumentace Microsoftu
description: Azure File Sync místní konfigurace sítě
services: storage
documentationcenter: ''
author: fauhse
manager: aungoo
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: fauhse
ms.openlocfilehash: 39888772a257e9dc00e5a93736d8676ac6891a16
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161737"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Nastavení proxy a firewallu služby Azure File Sync
Azure File Sync se připojí k Azure Files umožňuje synchronizaci více webů a funkce vrstvení cloudu na místních serverech. V důsledku toho musí být na místním serveru připojený k Internetu. Správce IT je potřeba rozhodnout nejlepší cestu pro server k získání přístupu do cloudových služeb Azure.

Tento článek poskytuje přehled o konkrétní požadavky a možnosti dostupné pro úspěšně a bezpečného připojení serveru k Azure File Sync.

> [!Important]
> Azure File Sync zatím nepodporuje brány firewall a virtuální sítě pro účet úložiště.

## <a name="overview"></a>Přehled
Azure File Sync funguje jako službu Orchestrace mezi systému Windows Server, sdílené složky Azure a několik dalších služeb Azure, synchronizaci dat, jak je popsáno ve vaší skupině synchronizace. Pro Azure File Sync fungovala správně budete potřebovat ke konfiguraci serverů ke komunikaci s těmito službami Azure:

- Azure Storage
- Azure File Sync
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

Nastavení serveru proxy pro celý počítač jsou transparentní pro agenta Azure File Sync celý provoz serveru je směrován přes proxy server.

Nastavení proxy server pro konkrétní aplikaci povolit konfiguraci proxy serveru speciálně pro provoz Azure File Sync. Nastavení serveru proxy konkrétní aplikace jsou podporované ve verzi agenta 3.0.12.0 nebo novější a je možné konfigurovat během instalace agenta nebo pomocí rutiny prostředí PowerShell Set-StorageSyncProxyConfiguration.

Příkazy prostředí PowerShell a zadejte nastavení proxy server pro konkrétní aplikaci:
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```

## <a name="firewall"></a>Brána firewall
Jak je uvedeno v předchozí části, otevřít odchozí port 443 musí být. Na základě zásad v datovém centru, větvi nebo oblasti, další omezení provozu přes tento port na vybrané domény může být požadovaného nebo vyžaduje.

Následující tabulka popisuje požadovaných domén pro komunikace:

| Služba | Doména | Využití |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Jakékoli volání uživatele (jako je PowerShell) prochází přes tuto adresu URL, včetně volání registrace počáteční server. |
| **Azure Active Directory** | https://login.windows.net | Ověřený uživatel se musí provádět volání Azure Resource Manageru. Úspěšné, tato adresa URL slouží k ověřování uživatelů. |
| **Azure Active Directory** | https://graph.windows.net/ | Jako součást nasazení Azure File Sync se vytvoří instanční objekt služby ve službě Active Directory předplatného Azure. Pro, který se používá tuto adresu URL. Tento objekt se používá pro delegování minimální sadu práv ve službě Azure File Sync. Uživatel provádějící počáteční nastavení služby Azure File Sync musí být ověřený uživatel s oprávněními vlastníka předplatného. |
| **Azure Storage** | &ast;.core.windows.net | Pokud server stáhne soubor, pak server provede tento přesun dat efektivněji přímo s sdílené složky Azure v účtu úložiště. Server má klíč SAS, která povoluje jenom pro přístup ke sdílené složce cílového souboru. |
| **Azure File Sync** | &ast;.one.microsoft.com | Po registraci počáteční server přijímá na serveru místní adresu URL instance služby Azure File Sync v dané oblasti. Server může komunikovat přímo a efektivně s instancí zpracování synchronizace. použijte adresu URL. |

> [!Important]
> Při povolení provozu na &ast;. one.microsoft.com, provoz do více než jen synchronizační služby je možné ze serveru. Nejsou k dispozici v části subdomény mnoho další služby Microsoftu.

Pokud &ast;. one.microsoft.com je příliš široké, komunikaci serverem můžete omezit tím, že komunikaci pouze explicitní místní instance služby Azure File Sync. Které instancí zvolit závisí na oblasti mají nasazení a registraci serveru do služby synchronizace úložiště. Tuto oblast se nazývá "primární koncový bod adresy URL" v následující tabulce.

Pro provozní kontinuitu a po havárii (BCDR) obnovení důvodů jste zadali, že vaše sdílených složek Azure v účtu globálně redundantní úložiště (GRS). Pokud je to tento případ, pak sdílených složek Azure převezme spárované oblasti v případě výpadku oblasti trvalé zhodnocení. Azure File Sync používá stejné oblastní párování jako úložiště. Takže pokud používáte účty úložiště GRS, je potřeba povolit další adresy URL, aby váš server komunikovat s spárované oblasti pro Azure File Sync. Následující tabulka volá této "Paired oblasti". Kromě toho je adresa URL profilu Správce provozu, která musí být povolena také. Tím se zajistí síťový provoz můžete bezproblémově znovu směrovat do spárované oblasti v případě převzetí služeb při selhání a se nazývá "Zjišťování adresy URL" v následující tabulce.

| Oblast | Primární koncový bod adresy URL | Spárované oblasti | Adresa URL pro zjišťování | |---|---|| --------|| ---------------------------------------| | Austrálie – východ | https://kailani-aue.one.microsoft.com | Austrálie Souteast | https://kailani-aue.one.microsoft.com | | Austrálie – jihovýchod | https://kailani-aus.one.microsoft.com | Austrálie – východ | https://tm-kailani-aus.one.microsoft.com | | Kanada – střed | https://kailani-cac.one.microsoft.com | Kanada – východ | https://tm-kailani-cac.one.microsoft.com | | Kanada – východ | https://kailani-cae.one.microsoft.com | Kanada – střed | https://tm-kailani.cae.one.microsoft.com | | USA (střed) | https://kailani-cus.one.microsoft.com | USA – východ 2 | https://tm-kailani-cus.one.microsoft.com | | Východní Asie | https://kailani11.one.microsoft.com | Jihovýchodní Asie | https://tm-kailani11.one.microsoft.com | | USA – východ | https://kailani1.one.microsoft.com | USA – západ | https://tm-kailani1.one.microsoft.com | | USA – východ 2 | https://kailani-ess.one.microsoft.com | USA (střed) | https://tm-kailani-ess.one.microsoft.com | | Severní Evropa | https://kailani7.one.microsoft.com | Západní Evropa | https://tm-kailani7.one.microsoft.com | | Jihovýchodní Asie | https://kailani10.one.microsoft.com | Východní Asie | https://tm-kailani10.one.microsoft.com | | Velká Británie – jih | https://kailani-uks.one.microsoft.com | Velká Británie – západ | https://tm-kailani-uks.one.microsoft.com | | Velká Británie – západ | https://kailani-ukw.one.microsoft.com | Velká Británie – jih | https://tm-kailani-ukw.one.microsoft.com | | Západní Evropa | https://kailani6.one.microsoft.com | Severní Evropa | https://tm-kailani6.one.microsoft.com | | USA – západ | https://kailani.one.microsoft.com | USA – východ | https://tm-kailani.one.microsoft.com |

- Pokud používáte místně redundantní (LRS) a účty úložiště (ZRS) redundantní zóny, potřebujete jenom povolit adresu URL v části "primární koncový bod adresy URL".

- Pokud používáte účty globálně redundantní úložiště (GRS), povolte tři adresy URL.

**Příklad:** nasazení služby synchronizace úložiště v `"West US"` a zaregistrovat svůj server. Adresy URL, aby server ke komunikaci se pro tento případ jsou:

> - https://kailani.one.microsoft.com (primární koncový bod: USA – západ)
> - https://kailani1.one.microsoft.com (spárované oblasti převzetí služeb při selhání: USA – východ)
> - https://tm-kailani.one.microsoft.com (adresa URL pro zjišťování z primární oblasti)

## <a name="summary-and-risk-limitation"></a>Omezení rizika a souhrn
Seznamy dříve v tomto dokumentu obsahují adresy URL Azure File Sync aktuálně komunikuje s. Brány firewall musí být schopen přenosy odchozích těchto domén. Microsoft se snaží zachovat tento seznam se aktualizoval.

Nastavení domény omezení pravidel brány firewall může být míra pro vylepšení zabezpečení. Pokud se používají tyto konfigurace brány firewall, třeba Pamatujte, že adresy URL se přidají a může dokonce i v průběhu času měnit. Pravidelně ji kontrolujte, v tomto článku.

## <a name="next-steps"></a>Další postup
- [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- [Nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md)