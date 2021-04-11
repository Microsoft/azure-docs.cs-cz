---
title: Záznamy DNS datového centra Azure, které používá Azure Automation | Microsoft Docs
description: Tento článek obsahuje záznamy DNS vyžadované funkcemi Azure Automation, když omezíte komunikaci s konkrétní oblastí Azure hostující tento účet Automation.
services: automation
ms.subservice: process-automation
ms.date: 11/25/2020
ms.topic: conceptual
ms.openlocfilehash: d41d825c7bc33e05815c7528b436c85873859928
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168922"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>Záznamy DNS pro oblasti Azure používané Azure Automation

Služba [Azure Automation](../automation-intro.md) používá několik záznamů DNS pro funkce, které se připojují ke službě. Pokud máte účet Automation, který je definovaný pro konkrétní oblast, můžete omezit komunikaci s tímto oblastním datacentrem. Tyto záznamy možná budete muset znát, chcete-li, aby následující funkce automatizace fungovaly, když je hostována za bránou firewall:

* Hybrid Runbook Worker
* Konfigurace stavu
* Webhooky

>[!NOTE]
>Pokud se nejedná o 1.6.10.2 nebo vyšší verzi, registrace pro Hybrid Runbook Worker Linux se nezdaří s novými záznamy. Aby počítač mohl získat aktualizovanou verzi role pracovního procesu a používat tyto nové záznamy, musíte upgradovat na novější verzi [agenta Log Analytics pro Linux](../../azure-monitor/agents/agent-linux.md) . Stávající počítače budou dál fungovat bez problémů.  

## <a name="dns-records-per-region"></a>Záznamy DNS na oblast

Následující tabulka uvádí záznam DNS pro každou oblast.

>[!NOTE]
>I když seznam záznamů DNS služby Automation, které jsou zde uvedené, jsou vyřazené, stále funkční, aby bylo možné migrovat na nové záznamy uvedené v části [Podpora privátního propojení](#support-for-private-link) a zabránit chybám v procesech automatizace.

| **Oblast** | **Záznam DNS** |
| --- | --- |
| Austrálie – střed |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Austrálie – východ |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Austrálie – jihovýchod |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Střední Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Indie – střed |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| USA – východ 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Japonsko – východ |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Severní Evropa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Středojižní USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Jihovýchodní Asie |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Spojené království – jih | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| USA (Gov) – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| USA – středozápad | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| West Europe |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Západní USA 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Podpora pro privátní propojení

Pro podporu [privátního odkazu](../../private-link/private-link-overview.md) v Azure Automation byly aktualizovány záznamy DNS pro všechna podporovaná datacentra. Místo adres URL specifických pro jednotlivé oblasti jsou adresy URL specifické pro účet Automation.

| **Oblast** | **Záznam DNS** |
| --- | --- |
| USA – středozápad |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| USA – západ |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| Západní USA 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| USA – střed |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| Středojižní USA |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| USA – středosever |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| East US |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| USA – východ 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| Střední Kanada |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| West Europe |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| Severní Evropa |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| Jihovýchodní Asie |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| Východní Asie |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| Indie – střed |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| Japonsko – východ |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| Jižní Korea – střed |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| Austrálie – jihovýchod |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| Austrálie – východ |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| Austrálie – střed |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| Spojené království – jih |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| Francie – střed |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| Jižní Afrika – sever |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| Brazílie – jih |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| Čína – sever |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| Čína – sever 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| Čína – východ 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| USA (Gov) – Virginia |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| USA (Gov) – Texas |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| USA (Gov) – Arizona |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

`<accountId>`V záznamu DNS nahraďte identifikátorem GUID, který představuje ID vašeho účtu Automation z hodnoty **URL**. ID požadované z **klíčového** okna můžete získat pomocí položky **nastavení účtu** v Azure Portal.

![Stránka primárního klíče účtu Automation](./media/automation-region-dns-records/automation-account-keys.png)

Zkopírujte hodnotu po *účtech/* z pole **Adresa URL** – `https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`

> [!NOTE]
> Všechny záznamy DNS Webhooku a AgentService se aktualizovaly na nové styly DNS pro podporu privátního odkazu. U záznamů DNS JRDS se podporují staré i nové styly DNS. Pokud nepoužíváte privátní odkaz, zobrazí se původní styl záznamů DNS, zatímco na základě privátního odkazu se zobrazí nový styl záznamů DNS.

Doporučujeme používat adresy uvedené při definování [výjimek](../automation-runbook-execution.md#exceptions). Seznam IP adres oblastí a nikoli názvů oblastí získáte stažením souboru JSON z webu Microsoft Download Center pro následující cloudová prostředí:

* [Rozsahy IP adres Azure a značky služeb – Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
* [Rozsahy IP adres Azure a značky služby – Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
* [Rozsahy IP adres Azure a značky služeb – Azure Německo](https://www.microsoft.com/download/details.aspx?id=57064)
* [Rozsahy IP adres a značky služeb Azure – Azure Čína Vianet 21](https://www.microsoft.com/download/details.aspx?id=57062)

V souboru IP adresy jsou uvedené rozsahy IP adres, které se používají v datových centrech Microsoft Azure. Zahrnuje výpočetní prostředí, SQL a rozsahy úložiště a odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres. Nové rozsahy, které se zobrazí v souboru, se v datových centrech nepoužijí aspoň na jeden týden.

Je vhodné stáhnout nový soubor s IP adresou každý týden. Pak aktualizujte svůj web tak, aby správně identifikoval služby běžící v Azure.

> [!NOTE]
> Pokud používáte Azure ExpressRoute, pamatujte na to, že soubor IP adresy se používá k aktualizaci inzerce protokolu BGP (Border Gateway Protocol) v Azure Space v prvním týdnu v měsíci.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak řešit potíže s procesy Hybrid Runbook Worker, najdete v tématu [řešení potíží s Hybrid Runbook Worker](../troubleshoot/hybrid-runbook-worker.md#general).

* Informace o řešení potíží s konfigurací stavu najdete v tématu řešení potíží s [konfigurací stavu](../troubleshoot/desired-state-configuration.md).