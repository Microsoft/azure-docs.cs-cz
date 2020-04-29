---
title: Azure Automation Hybrid Runbook Worker
description: Tento článek poskytuje informace o instalaci a použití Hybrid Runbook Worker, což je funkce Azure Automation, kterou můžete použít ke spouštění Runbooků na počítačích v místním datovém centru nebo poskytovateli cloudu.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 861c7ffa647c8d2f37b32c359253ca991eeb314f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457701"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatizace prostředků ve vašem datovém centru nebo cloudu pomocí Hybrid Runbook Worker

Runbooky v Azure Automation nemusí mít přístup k prostředkům v jiných cloudech nebo v místním prostředí, protože běží na cloudové platformě Azure. Pomocí funkce Hybrid Runbook Worker služby Azure Automation můžete spouštět Runbooky přímo v počítači, který je hostitelem role, a k prostředkům v prostředí za účelem správy těchto místních prostředků. Sady Runbook jsou uloženy a spravovány v Azure Automation a poté dodány jednomu nebo více přiřazeným počítačům.

Tato funkce je znázorněna na následujícím obrázku:

![Přehled funkce Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Každý Hybrid Runbook Worker je členem skupiny Hybrid Runbook Worker, kterou zadáte při instalaci agenta. Skupina může obsahovat jediného agenta, ale můžete do skupiny nainstalovat více agentů pro zajištění vysoké dostupnosti. Každý počítač může hostovat jeden Hybrid Worker hlášení na jeden účet Automation.

Při spuštění sady Runbook na Hybrid Runbook Worker zadáte skupinu, ve které je spuštěna. Každý pracovní proces ve skupině se Azure Automation dotazuje, zda jsou k dispozici nějaké úlohy. Pokud je úloha k dispozici, první pracovní proces, který úlohu získá, ji vezme. Doba zpracování fronty úloh závisí na hardwarovém profilu a zatížení hybridního pracovního procesu. Nemůžete zadat konkrétního pracovního procesu. Hybridní pracovní procesy Runbooku nesdílejí spoustu omezení, která mají Azure sandboxs. Nemají stejná omezení místa na disku, paměti nebo síťových soketů. Procesy Hybrid Runbook Worker jsou omezeny pouze prostředky na samotném Hybrid Runbook Worker. Kromě toho hybridní pracovní procesy Runbooku nesdílejí limit 180-minutového [podílu pro reálný](automation-runbook-execution.md#fair-share) čas, který mají Azure sandboxy. Další informace o omezení služby pro izolované pracovní prostory Azure a procesy Hybrid Runbook Worker najdete v tématu [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)úloh.

## <a name="install-a-hybrid-runbook-worker"></a>Instalace Hybrid Runbook Worker

Proces instalace Hybrid Runbook Worker závisí na operačním systému. Následující tabulka definuje typy nasazení.

|Operační systém  |Typy nasazení  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Ruční](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

Doporučenou metodou instalace je použití Runbooku služby Automation k úplnému automatizaci procesu konfigurace počítače se systémem Windows. Druhou metodou je postupovat podle podrobných pokynů k ruční instalaci a konfiguraci role. Pro počítače se systémem Linux spustíte skript Pythonu, který nainstaluje agenta do počítače.

> [!NOTE]
> Chcete-li spravovat konfiguraci serverů, které podporují roli Hybrid Runbook Worker s požadovaným stavem konfigurace (DSC), je nutné přidat servery jako uzly DSC. Další informace o připojování pro správu s DSC najdete v tématu věnovaném [připojování počítačů pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md).
>
>Pokud povolíte [řešení Update Management](automation-update-management.md), všechny počítače, které jsou připojené k vašemu pracovnímu prostoru Azure Log Analytics, se automaticky nakonfigurují jako Hybrid Runbook Worker, aby podporovaly Runbooky zahrnuté v tomto řešení. Počítač ale není zaregistrovaný u žádné Hybrid Workeré skupiny, které už jsou ve vašem účtu Automation definované. Počítač může být přidán do skupiny Hybrid Runbook Worker ve vašem účtu Automation, aby podporoval Runbooky Automation, pokud používáte stejný účet pro řešení i pro členství v Hybrid Runbook Worker skupině. Tato funkce byla přidána do 7.2.12024.0 verze Hybrid Runbook Worker.

Než začnete s nasazováním Hybrid Runbook Worker, přečtěte si [informace pro plánování vaší sítě](#network-planning) . Po úspěšném nasazení pracovního procesu si přečtěte téma [spuštění runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md) , kde se dozvíte, jak nakonfigurovat Runbooky pro automatizaci procesů v místním datacentru nebo v jiném cloudovém prostředí.

Počítač může být přidán do skupiny Hybrid Runbook Worker ve vašem účtu Automation, aby podporoval Runbooky Automation, pokud používáte stejný účet pro řešení i pro členství v Hybrid Runbook Worker skupině. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

## <a name="a-nameremove-a-hybrid-runbook-workerremove-a-hybrid-runbook-worker-from-an-on-premises-computer"></a><a name="remove-a-hybrid-runbook-worker">Odebrání Hybrid Runbook Worker z místního počítače

Hybrid Runbook Worker můžete odebrat z místního počítače, jak je popsáno v této části pro systémy Windows a Linux.

### <a name="remove-the-worker-on-windows"></a>Odebrání pracovního procesu ve Windows

1. V Azure Portal přejdete do svého účtu Automation.
2. V části **Nastavení účtu**vyberte **klíče** a poznamenejte si hodnoty **adresy URL** a **primárního přístupového klíče**.

3. Otevřete relaci PowerShellu v režimu správce a spusťte následující příkaz s hodnotami adresy URL a primárního přístupového klíče. Použijte `Verbose` parametr pro podrobný protokol procesu odebrání. K odebrání zastaralých počítačů ze skupiny Hybrid Worker Použijte volitelný `machineName` parametr.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="remove-the-worker-on-linux"></a>Odebrání pracovního procesu v systému Linux

K získání ID pracovního prostoru `ls /var/opt/microsoft/omsagent` můžete použít příkaz na Hybrid Runbook Worker. Vytvoří se složka s názvem ID pracovního prostoru.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Tento kód neodebere agenta Log Analytics pro Linux z počítače. Odebírá jenom funkce a konfiguraci role Hybrid Runbook Worker.

## <a name="remove-a-hybrid-worker-group"></a>Odebrání skupiny Hybrid Worker

Chcete-li odebrat skupinu Hybrid Runbook Worker, musíte nejprve odebrat Hybrid Runbook Worker z každého počítače, který je členem skupiny. Pak odeberte skupinu pomocí následujících kroků:

1. Otevřete účet Automation v Azure Portal.
2. V části **Automatizace procesu**vyberte **skupiny hybridních pracovních procesů** . Vyberte skupinu, kterou chcete odstranit. Zobrazí se stránka Vlastnosti této skupiny.

   ![Stránka Vlastnosti](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na stránce vlastnosti vybrané skupiny vyberte **Odstranit**. Zobrazí se zpráva s výzvou k potvrzení této akce. Pokud jste si jisti, že chcete pokračovat, vyberte **Ano** .

   ![Potvrzovací zpráva](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Dokončení tohoto procesu může trvat několik sekund. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

## <a name="configure-your-network"></a><a name="network-planning"></a>Konfigurace sítě

### <a name="hybrid-worker-role"></a>Role Hybrid Worker

Aby bylo možné Hybrid Runbook Worker připojit k Azure Automation a zaregistrovat se v ní, musí mít přístup k číslu portu a adresám URL, které jsou popsány v této části. Tento přístup je zapnutý k [portům a adresám URL vyžadovaným pro Log Analytics agenta](../azure-monitor/platform/agent-windows.md) pro připojení k Azure Monitorm protokolům.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pokud používáte proxy server pro komunikaci mezi agentem a službou Azure Automation, ujistěte se, že jsou dostupné příslušné prostředky. Časový limit pro žádosti od Hybrid Runbook Worker a Automation Services je 30 sekund. Po 3 pokusech se požadavek nezdaří. Pokud používáte bránu firewall k omezení přístupu k Internetu, musíte bránu firewall nakonfigurovat tak, aby povolovala přístup. Pokud používáte bránu Log Analytics jako proxy, ujistěte se, že je nakonfigurovaná pro hybridní pracovní procesy. Pokyny k tomu, jak to provést, najdete v tématu [Konfigurace brány Log Analytics pro hybridní pracovní procesy automatizace](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

Následující porty a adresy URL jsou vyžadovány, aby Hybrid Runbook Worker role komunikovala se službou Automation:

* Port: odchozí internetový přístup vyžaduje jenom TCP 443.
* Globální adresa URL: *. azure-automation.net
* Globální adresa URL US Gov – Virginie: *.azure-automation.us
* Služba agenta: https://\<id pracovního prostoru\>. agentsvc.Azure-Automation.NET

Doporučuje se používat adresy uvedené při definování výjimek. Pro IP adresy můžete stáhnout [Microsoft Azure rozsahy IP adres datacentra](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Tento soubor se aktualizuje týdně a má aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres.

Pokud máte účet Automation, který je definovaný pro konkrétní oblast, můžete omezit komunikaci s tímto oblastním datacentrem. Následující tabulka uvádí záznam DNS pro jednotlivé oblasti:

| **Oblast** | **Záznam DNS** |
| --- | --- |
| USA – středozápad | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| USA – středojih |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA – východ 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| USA – západ 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Střední Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Západní Evropa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Severní Evropa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Jihovýchodní Asie |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indie – střed |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonsko – východ |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Austrálie – střed |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Austrálie – východ |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Austrálie – jihovýchod |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Spojené království – jih | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| USA (Gov) – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Seznam IP adres oblastí, nikoli názvy oblastí, si můžete stáhnout ze služby Stažení [softwaru ve službě](https://www.microsoft.com/download/details.aspx?id=41653) stažení softwaru.

> [!NOTE]
> Soubor XML IP adresy datacentra Azure obsahuje seznam rozsahů IP adres, které se používají v datových centrech Microsoft Azure. Soubor zahrnuje výpočetní prostředky, SQL a rozsahy úložiště.
>
>Aktualizovaný soubor je zveřejněný týdně. Tento soubor odráží aktuálně nasazené rozsahy a všechny nadcházející změny v rozsahu IP adres. Nové rozsahy, které se zobrazí v souboru, se v datových centrech nepoužijí aspoň na jeden týden.
>
> Každý týden je vhodné stáhnout nový soubor XML. Pak aktualizujte svůj web tak, aby správně identifikoval služby běžící v Azure. Uživatelé Azure ExpressRoute by si měli všimnout, že se tento soubor používá k aktualizaci inzerce protokolu BGP (Border Gateway Protocol) v Azure Space v první týden v měsíci.

### <a name="update-management"></a>Update Management

Na standardních adresách a portech, které Hybrid Runbook Worker vyžaduje, jsou pro Update Management vyžadovány následující adresy. Komunikace s těmito adresami se provádí přes port 443.

|Veřejný partnerský vztah Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí, najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Informace o řešení potíží s procesy Hybrid Runbook Worker najdete v tématu [řešení potíží s Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).