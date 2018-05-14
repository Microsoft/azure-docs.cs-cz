---
title: Synchronizace služby Azure soubor místní nastavení brány firewall a proxy | Microsoft Docs
description: Synchronizace služby Azure soubor místní konfigurace sítě
services: storage
documentationcenter: ''
author: fauhse
manager: klaasl
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 979897e3cb703b36a46e96848a9176d6d4c6cc6a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Nastavení proxy a firewallu služby Azure File Sync
Synchronizace služby Azure souboru se připojí k Azure Files, povolení synchronizace více lokalit a cloudu vrstvení funkce na místní servery. Místní server jako takový musí být připojen k Internetu. Správce IT potřebuje k rozhodování o nejlepší cestu pro server k získání přístupu do cloudových služeb Azure.

Tento článek se získat přehled o konkrétní požadavky a možnosti, které jsou k dispozici úspěšně a bezpečného připojení serveru pro synchronizaci souborů Azure.

> [!Important]
> Synchronizace služby Azure souboru zatím nepodporuje brány firewall a virtuální sítě pro účet úložiště. 

## <a name="overview"></a>Přehled
Synchronizace služby Azure souboru funguje jako služba orchestration mezi systému Windows Server, Azure sdílené složky a několika dalším službám Azure synchronizaci dat, jak je popsáno ve vaší skupině pro synchronizaci. Pro synchronizaci souborů Azure fungovala správně je nutné ke konfiguraci serverů ke komunikaci s následující služby Azure:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Ověřovací služby

> [!Note]  
> Agent Azure Sync souboru v systému Windows Server iniciuje všechny požadavky na cloudové služby, které má za následek pouze nutnosti zvažte odchozí provoz z hlediska brány firewall. <br /> Žádné služby Azure zahájí připojení k agentovi Azure synchronizace souboru.


## <a name="ports"></a>Porty
Synchronizace služby Azure soubor přesune data souborů a metadat výhradně prostřednictvím protokolu HTTPS a vyžaduje se otevřít odchozí port 443.
Výsledkem je zašifrován veškerý provoz.

## <a name="networks-and-special-connections-to-azure"></a>Sítě a speciální připojení k Azure
Agent Azure souboru Sync nemá žádné požadavky týkající Speciální kanály jako [ExpressRoute](../../expressroute/expressroute-introduction.md), atd. do Azure.

Synchronizace služby Azure soubor bude fungovat přes všechny dostupné znamená, že povolit reach do Azure, automaticky přizpůsobení do různých síť charakteristiky, jako jsou například šířky pásma, latence a také nabídky Správce řízení pro optimalizaci. Ne všechny funkce jsou dostupné v tuto chvíli. Pokud chcete nakonfigurovat konkrétní chování, dejte nám vědět prostřednictvím [Azure soubory UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy server
Synchronizace služby Azure souboru aktuálně podporuje nastavení proxy serveru celého systému. Tato nastavení proxy serveru je transparentní agentovi Azure synchronizace souborů, protože celý provoz serveru směrován přes tento proxy server.

Nastavení proxy serveru pro konkrétní aplikaci aktuálně jsou ve vývoji a bude podporovaný v budoucí verzi agenta synchronizace souboru Azure. To vám umožní konfigurace proxy speciálně pro provoz synchronizace souboru Azure.

## <a name="firewall"></a>Brána firewall
Jak je uvedeno v předchozí části, otevřete odchozí port 443, musí být. Na základě zásad v datovém centru, pobočky nebo oblast, další omezení provozu prostřednictvím tohoto portu na vybrané domény může být požadovaných nebo požadováno.

Následující tabulka popisuje požadované domén pro komunikace:

| Služba | Doména | Využití |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Volání žádné uživatele (například prostředí PowerShell) přejde prostřednictvím tuto adresu URL, včetně volání registrace počáteční server. |
| **Azure Active Directory** | https://login.windows.net | Ověřený uživatel musí být provedeny volání Azure Resource Manager. Úspěšné, je tato adresa URL použitá k ověření uživatele. |
| **Azure Active Directory** | https://graph.windows.net/ | Jako součást nasazení synchronizace služby Azure souboru bude vytvořen objekt služby v odběru Azure Active Directory. Tato adresa URL se používá pro tento. Tento objekt se používá pro delegování minimální sadu oprávnění ke službě Azure synchronizace souboru. Ověřený uživatel s oprávněními vlastníka předplatného musí být uživatel, provádí počáteční nastavení synchronizace souborů Azure. |
| **Azure Storage** | &ast;.core.windows.net | Pokud server stáhne soubor, pak server provádí tento přesun dat další efektivně při posuzování přímo do sdílené složky Azure v účtu úložiště. Server má SAS klíč, který umožňuje pouze pro přístup ke sdílené složce cílový soubor. |
| **Synchronizace Azure File** | &ast;.one.microsoft.com | Po registraci počáteční server server nahlásí místní adresu URL pro instanci služby synchronizace Azure soubor v této oblasti. Server můžete použít adresu URL komunikovat přímo a efektivně s instancí zpracování jeho synchronizace. |

> [!Important]
> Při povolování provoz &ast;. one.microsoft.com, provoz do více než jen synchronizační služby je možné ze serveru. Nejsou k dispozici v části subdomény mnoho další služby Microsoftu.

Pokud &ast;. one.microsoft.com je příliš široké, komunikace serveru můžete omezit tím, že pouze explicitní místní instance služby Azure soubory Sync. Instance, které do zvolit, závisí na oblasti služby Sync úložiště, ke kterému jste nasazení a registraci serveru. To je oblast, kterou je potřeba povolit pro server. Brzy bude existovat více adres URL můžete povolit nové funkce kontinuity obchodních. 

| Oblast | Adresa URL místní koncového bodu Azure synchronizace souboru |
|--------|---------------------------------------|
| Austrálie – východ | https://kailani-aue.one.microsoft.com |
| Střední Kanada | https://kailani-cac.one.microsoft.com |
| Východ USA | https://kailani1.one.microsoft.com |
| Jihovýchodní Asie | https://kailani10.one.microsoft.com |
| Spojené království – jih | https://kailani-uks.one.microsoft.com |
| Západní Evropa | https://kailani6.one.microsoft.com |
| Západní USA | https://kailani.one.microsoft.com |

> [!Important]
> Pokud definujete pravidla tyto podrobné brány firewall, často Zkontrolujte tento dokument a aktualizace vašich pravidlech brány firewall, aby se zabránilo přerušení poskytování služeb z důvodu zastaralé nebo jsou neúplné výpisy adresy URL v nastavení brány firewall.

## <a name="summary-and-risk-limitation"></a>Souhrn a riziko omezení
Seznam dříve v tomto dokumentu obsahovat synchronizace služby Azure souboru aktuálně komunikuje s adresy URL. Brány firewall musí být schopný umožňující přenos odchozí do těchto domén, jakož i odpovědí z nich. Společnost Microsoft usiluje ponechat tento seznam aktualizovat.

Nastavení domény omezení pravidla brány firewall může být míru pro zlepšení zabezpečení. Pokud se používají tyto konfigurace brány firewall, musí jeden mějte na paměti, že adresy URL bude přidána a časem změnit. Je proto třeba ji zkontrolovat míru ke kontrole tabulky v tomto dokumentu v rámci procesu správy změn z jedné verze agenta synchronizace souboru Azure do jiné na testovací nasazení nejnovější verzi agenta. Tímto způsobem můžete zajistit, že je nakonfigurována vaše brána firewall povolit provoz domény nejnovější agenta vyžaduje.

## <a name="next-steps"></a>Další postup
- [Plánování nasazení Azure souboru Sync](storage-sync-files-planning.md)
- [Nasadit soubor synchronizaci Azure (preview)](storage-sync-files-deployment-guide.md)
