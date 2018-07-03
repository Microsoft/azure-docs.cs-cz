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
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 7d86082abb6412072af44a6b2d794bcf536fa18d
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342722"
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

Pokud &ast;. one.microsoft.com je příliš široké, komunikaci serverem můžete omezit tím, že pouze explicitní místní instance služby Azure File Sync. Které instancí zvolit, závisí na oblasti služby synchronizace úložiště, ke kterému jste nasazení a registraci serveru. To je oblast, kterou je potřeba povolit pro server. Brzy bude existovat více adres URL umožňuje nové funkce pro provozní kontinuitu. 

| Oblast | Adresa URL místní koncového bodu Azure File Sync |
|--------|---------------------------------------|
| Austrálie – východ | https://kailani-aue.one.microsoft.com |
| Střední Kanada | https://kailani-cac.one.microsoft.com |
| Východ USA | https://kailani1.one.microsoft.com |
| Jihovýchodní Asie | https://kailani10.one.microsoft.com |
| Spojené království – jih | https://kailani-uks.one.microsoft.com |
| Západní Evropa | https://kailani6.one.microsoft.com |
| Západní USA | https://kailani.one.microsoft.com |

> [!Important]
> Pokud definujete tato pravidla brány firewall na podrobné, často Zkontrolujte tento dokument a aktualizovat pravidla brány firewall a vyhnout se přerušením služeb kvůli zastaralé nebo neúplné výpisy adresy URL v nastavení brány firewall.

## <a name="summary-and-risk-limitation"></a>Omezení rizika a souhrn
Seznamy dříve v tomto dokumentu obsahují adresy URL Azure File Sync aktuálně komunikuje s. Brány firewall musí být schopen přenosy odchozích těchto domén, jakož i odpovědi z nich. Microsoft se snaží zachovat tento seznam se aktualizoval.

Nastavení domény omezení pravidel brány firewall může být míra pro vylepšení zabezpečení. Pokud se používají tyto konfigurace brány firewall, jeden musí mít na paměti, že adresy URL přidat, který se mění v průběhu času. Proto je třeba ji zkontrolovat opatření ke kontrole tabulky v tomto dokumentu jako součást procesu správy změn z jedné verze agenta Azure File Sync do jiného testu nasazení nejnovější verzi agenta. Tímto způsobem můžete zajistit, že je vaše brána firewall nakonfigurována pro povolení provozu na domény nejnovější agent vyžaduje.

## <a name="next-steps"></a>Další postup
- [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- [Nasazení služby Azure File Sync (preview)](storage-sync-files-deployment-guide.md)
