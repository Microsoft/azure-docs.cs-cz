---
title: Azure Automation Hybrid Runbook Worker
description: Tento článek obsahuje informace o instalaci a použití procesu Hybrid Runbook Worker, což je funkce služby Azure Automation, který používáte ke spuštění sady runbook na počítačích v místním datovém centru nebo poskytovatele cloudu.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d42a9458afa6244e0b6d8e7deb420a8ac49a130f
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634162"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatizace prostředky v datovém centru nebo v cloudu s využitím procesu Hybrid Runbook Worker

Runbooky ve službě Azure Automation nebudete mít přístup k prostředkům v jiných cloudech nebo v místním prostředí vzhledem k tomu, že jsou spuštěné na cloudovou platformu Azure. Funkce Hybrid Runbook Worker služby Azure Automation můžete použít ke spuštění sady runbook přímo na počítači, který je hostitelem role a s prostředky v prostředí ke správě místních prostředků. Sady Runbook jsou uloženy a spravovaná ve službě Azure Automation a pak doručí jeden nebo více počítačů přiřazených.

Následující obrázek ukazuje tuto funkci:

![Přehled funkce Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Každá funkce Hybrid Runbook Worker je členem skupiny Hybrid Runbook Worker, který zadáváte při instalaci agenta. Skupina může obsahovat jeden agent, ale můžete nainstalovat více agentů ve skupině pro zajištění vysoké dostupnosti.

Při spuštění sady runbook v procesu Hybrid Runbook Worker, určete skupinu, která se spouští. Každému pracovnímu procesu ve skupině se dotazuje služby Azure Automation, jestli jsou k dispozici žádné úlohy. Pokud úloha je k dispozici, má první pracovního procesu k dosažení ho. Doba zpracování do fronty úloh závisí na Hybrid worker hardwarový profil a zatížení. Nelze zadat konkrétní pracovního procesu. Procesy hybrid Runbook Worker Nesdílejte mnoho omezení, které mají Azure karantény. Nemají stejné omezení místa na disku, paměti nebo síťové sokety. Procesy hybrid Runbook Worker jsou omezené jenom k prostředkům v procesu Hybrid Runbook Worker, samotného. Kromě toho Hybrid Runbook Worker nesdílejí 180 minutu [spravedlivé sdílení](automation-runbook-execution.md#fair-share) časový limit, kterou Azure sandboxy provádět. Další informace o omezení služby Azure karantény a procesy Hybrid Runbook Worker, najdete v úloze [omezení](../azure-subscription-service-limits.md#automation-limits) stránky.

## <a name="install-a-hybrid-runbook-worker"></a>Instalace procesu Hybrid Runbook Worker

Proces instalace procesu Hybrid Runbook Worker závisí na operační systém. Následující tabulka obsahuje odkazy na metody, které můžete použít k instalaci.

Instalace a konfigurace Windows Hybrid Runbook Worker, můžete použít dvě metody. Doporučený postup je použít runbook služby Automation zcela automatizovat proces konfigurace počítače Windows. Druhá metoda sleduje podrobný postup, jak ručně nainstalovat a nakonfigurovat role. Pro počítače s Linuxem spustili skript Pythonu pro instalaci agenta na počítači.

|Operační systém  |Typy nasazení  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Ruční](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Ke správě konfigurace serverů, které podporují role Hybrid Runbook Worker s Desired State Configuration (DSC), musíte je přidat jako uzly DSC. Další informace o připojování je pro správu pomocí DSC, naleznete v tématu [připojování počítačů pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md).
>
>Pokud povolíte [řešení Update Management](automation-update-management.md), jakýkoli počítač, který je připojený k pracovnímu prostoru Azure Log Analytics se automaticky nakonfiguruje jako Hybrid Runbook Worker pro podporu runbooků, které jsou zahrnuté v tomto řešení. Ale počítač není zaregistrovaný u žádných skupin Hybrid Worker, který je již definován ve vašem účtu Automation. Počítač je přidat do skupiny Hybrid Runbook Worker ve vašem účtu Automation pro podporu runbooků Automation za předpokladu, že používáte stejný účet pro řešení a členství ve skupině Hybrid Runbook Worker. Tato funkce byla přidána na verzi 7.2.12024.0 Hybrid Runbook Worker.

Zkontrolujte [informace pro plánování sítě](#network-planning) před zahájením nasazení funkce Hybrid Runbook Worker. Po úspěšně nasazení pracovního procesu, zkontrolujte [spouštění runbooků v procesu Hybrid Runbook Worker](automation-hrw-run-runbooks.md) na další informace o konfiguraci runbooky pro automatizaci procesů do vašeho místního datového centra nebo jiné cloudové prostředí.

## <a name="remove-a-hybrid-runbook-worker"></a>Odebrání funkce Hybrid Runbook Worker

Jeden nebo více procesy Hybrid Runbook Worker můžete odebrat ze skupiny, nebo můžete odebrat skupinu, v závislosti na vašich požadavcích. Odebrání funkce Hybrid Runbook Worker v místním počítači, použijte následující postup:

1. Na webu Azure Portal přejděte na svůj účet Automation.
2. V části **nastavení**vyberte **klíče** a poznamenejte si hodnoty pro **URL** a **primární přístupový klíč**. Tyto informace budete potřebovat další krok.

### <a name="windows"></a>Windows

Otevřete relaci Powershellu v režimu správce a spusťte následující příkaz. Použití **-Verbose** přepnout podrobný protokol odebrání.

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Chcete-li odebrat zastaralé počítače ze skupiny Hybrid Worker, použijte volitelný `machineName` parametru.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Tento kód neodebere agenta Microsoft Monitoring Agent z počítače, funkce a konfigurace role Hybrid Runbook Worker.

## <a name="remove-a-hybrid-worker-group"></a>Odebrat skupiny Hybrid Worker

Můžete odebrat skupinu, budete nejdřív muset odebrat funkce Hybrid Runbook Worker v každém počítači, který je členem skupiny postupem, jak je uvedeno výše. Potom použijte následující kroky k odebrání skupiny:

1. Na webu Azure Portal otevřete účet Automation.
1. V části **automatizace procesů**vyberte **skupiny hybridních pracovních procesů**. Vyberte skupinu, kterou chcete odstranit. Zobrazí se stránka Vlastnosti pro tuto skupinu.

   ![Stránka Vlastnosti](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Na stránce vlastnosti pro vybranou skupinu, vyberte **odstranit**. Výzva k potvrzení této akce. Vyberte **Ano** Pokud si nejste jisti, že chcete pokračovat.

   ![Potvrzovací zpráva](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Tento proces může trvat několik sekund na dokončení. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

## <a name="network-planning"></a>Konfigurace sítě

### <a name="hybrid-worker-role"></a>Role pracovního procesu hybrid

Pro Hybrid Runbook Worker mohl připojit a zaregistrovat pomocí Log Analytics musí mít přístup k portu a adresám URL, které jsou popsány v této části. Tento přístup je v horní části [portů a adresám URL požadovaným pro agenta Microsoft Monitoring Agent](../azure-monitor/platform/agent-windows.md) pro připojení ke službě Log Analytics.

Pokud používáte proxy server pro komunikaci mezi agentem a službou Log Analytics, ujistěte se, že jsou dostupné příslušné prostředky. Pokud používáte k omezení přístupu k Internetu bránu firewall, musíte nakonfigurovat bránu firewall, aby povolovala přístup. Pokud používáte bránu Log Analytics jako proxy server, ujistěte se, že je nakonfigurovaný pro proces hybrid Worker. Pokyny, jak to udělat, najdete v části [konfigurace brány Log Analytics pro Automation Hybrid Worker](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway#configure-for-automation-hybrid-workers).

Následující portů a adres URL jsou požadovány pro role Hybrid Runbook Worker ke komunikaci se službou Automation:

* Port: Se jenom TCP 443 pro odchozí přístup k Internetu vyžaduje.
* Globální adresa URL: *.azure-automation.net
* Globální adresa URL US Gov Virginie: *.azure-automation.us
* Služba agenta: https://\<ID pracovního prostoru\>.agentsvc.azure-automation.net

Doporučuje se použít adresy, které uvedete při definování výjimky. Pro IP adresy, které si můžete stáhnout [Microsoft Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor se každý týden aktualizuje a má aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres.

Pokud máte účet Automation, který je definován pro konkrétní oblasti, můžete omezit komunikaci s místní stejné datové centrum. Následující tabulka obsahuje záznam DNS pro každou oblast:

| **Oblast** | **Záznam DNS** |
| --- | --- |
| Západní střed USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice nepodařilo prod-1.azure-automation.net |
| Středojižní USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Východní USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kanada – střed |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Západní Evropa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Severní Evropa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Jihovýchodní Asie |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Střed Indie |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonsko – východ |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Austrálie – jihovýchod |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Velká Británie – jih | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice nepodařilo prod-1.azure-automation.net |
| USA (Gov) – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice nepodařilo prod-1.azure-automation.us |

Seznam oblastí IP adres místo názvů oblast, stáhněte si [IP adresy Datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653) XML soubor z webu Microsoft Download Center.

> [!NOTE]
> Soubor XML adres Azure Datacenter IP obsahuje rozsahy IP adres, které se používají v datacentrech Microsoft Azure. Soubor obsahuje rozsahy compute, SQL a úložiště.
>
>Aktualizovaný soubor každý týden se zveřejňuje. Soubor odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres. Nové rozsahy, které se zobrazují v souboru nejsou používány v datových centrech alespoň jeden týden.
>
> Je vhodné Stáhněte nový soubor XML každý týden. Potom aktualizujte váš web pro zajištění správné identifikace služeb spuštěných v Azure. Uživatelé Azure ExpressRoute upozorňujeme ale, že tento soubor se používá k aktualizaci inzerování protokolu BGP (Border Gateway) prostoru Azure probíhá první týden každého měsíce.

### <a name="update-management"></a>Update Management

Nad rámec standardních adresy a porty, které vyžaduje, aby Hybrid Runbook Worker jsou požadovány speciálně pro správu aktualizací těchto adres. Komunikace na tyto adresy se provádí přes port 443.

|Veřejné Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="troubleshoot"></a>Řešení potíží

Další řešení potíží s procesy Hybrid Runbook Worker, najdete v článku [řešení potíží s procesy Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general)

## <a name="next-steps"></a>Další postup

Další informace o konfiguraci runbooky pro automatizaci procesů do vašeho místního datového centra nebo jiné cloudové prostředí, najdete v článku [spouštění runbooků v procesu Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
