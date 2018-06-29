---
title: Azure Automation Hybrid Runbook Worker
description: Tento článek obsahuje informace o instalaci a použití Hybrid Runbook Worker, což je funkce Azure Automation, který můžete použít ke spuštění sady runbook na počítačích ve vaší místní datacenter nebo poskytovatele cloudových služeb.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be79f0111cb569509cb05b24c99f86d4ca9534b0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063789"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatizaci prostředků v datovém centru nebo v cloudu pomocí hybridní pracovní proces Runbooku

Runbooky ve službě Azure Automation nemusí být možné získat přístup k prostředkům v ostatních cloudů nebo v místním prostředí, protože běží na platformě Azure cloud. Ke spuštění sady runbook přímo na počítači, který je hostitelem role a s prostředky v prostředí pro správu těchto místních prostředků, můžete použít funkci hybridní pracovní proces Runbooku automatizace Azure. Sady Runbook jsou uložené a spravované ve službě Azure Automation a pak doručí jeden nebo více určenými počítači.

Následující obrázek znázorňuje tuto funkci:

![Přehled funkce Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Každý hybridní pracovní proces Runbooku je členem skupiny hybridní pracovní proces Runbooku, který zadáte při instalaci agenta. Skupina může obsahovat jednoho agenta, ale můžete nainstalovat více agentů ve skupině pro vysokou dostupnost.

Při spuštění sady runbook pro Hybrid Runbook Worker, určete skupinu, která běží na. Každý pracovní ve skupině dotáže Azure Automation. Chcete-li zobrazit, pokud jsou k dispozici žádné úlohy. Pokud úloha je k dispozici, má první pracovní proces úlohy ho. Nelze zadat konkrétní pracovního procesu.

## <a name="install-a-hybrid-runbook-worker"></a>Instalace nástroje Hybrid Runbook Worker

Proces instalace hybridní pracovní proces Runbooku závisí na operačním systémem. Následující tabulka obsahuje odkazy na metody, které můžete použít pro instalaci. 

Instalace a konfigurace Windows Hybrid Runbook Worker, můžete použít dvě metody. Doporučený postup je použít runbook služby automatizace zcela automatizovat proces konfigurace počítači se systémem Windows. Druhý způsob je následující podrobný postup při ručně nainstalujte a nakonfigurujte roli. Pro počítače se systémem Linux spusťte skript v jazyce Python pro instalaci agenta na počítači.

|Operační systém  |Typy nasazení  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Ruční](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Pokud chcete spravovat konfiguraci serverů, které podporují roli hybridní pracovní proces Runbooku s potřeby konfigurace stavu (DSC), musíte je přidat jako uzly DSC. Další informace o připojování je pro správu s DSC, najdete v části [registrace počítačů pro správu Azure Automation DSC](automation-dsc-onboarding.md).
>
>Pokud povolíte [řešení pro správu aktualizací](automation-update-management.md), jakýkoli počítač, který je připojen k pracovní prostor analýzy protokolů Azure je automaticky nakonfigurovaný jako hybridní pracovní proces Runbooku na podporu sady runbook, které jsou zahrnuté v tomto řešení. Počítač se však není registrován u žádné skupiny hybridní pracovní proces již definována v účtu Automation. Počítač lze přidat do skupiny Hybrid Runbook Worker ve vašem účtu Automation na podporu automatizace sady runbook, dokud používáte stejný účet pro řešení a členství ve skupině hybridní pracovní proces Runbooku. Tato funkce se přidal 7.2.12024.0 verzi hybridní pracovní proces Runbooku.

Zkontrolujte [informace pro plánování vaší sítě](#network-planning) než začnete nasazovat hybridní pracovní proces Runbooku. Poté, co nasadíte úspěšně pracovního procesu, zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.

## <a name="remove-a-hybrid-runbook-worker"></a>Odebrat hybridní pracovní proces Runbooku

Jeden nebo více procesy Hybrid Runbook Worker můžete odebrat ze skupiny, nebo můžete odebrat skupiny, v závislosti na požadavcích. K odebrání hybridní pracovní proces Runbooku místním počítači, proveďte následující kroky:

1. Na portálu Azure přejděte na svůj účet Automation.
2. V části **nastavení**, vyberte **klíče** a poznamenejte si hodnoty pro **URL** a **primární přístupový klíč**. Tyto informace jsou potřeba pro další krok.

### <a name="windows"></a>Windows

Otevřete relaci prostředí PowerShell v režimu správce a spusťte následující příkaz. Použití **-Verbose** přepínač pro podrobný protokol proces odebrání.

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Pokud chcete odstranit zastaralé počítače z vaší skupinu hybridních pracovních procesů, použijte nepovinný `machineName` parametr.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Tento kód neodebere agenta Microsoft Monitoring Agent z počítače, funkce a konfiguraci role hybridní pracovní proces Runbooku.

## <a name="remove-a-hybrid-worker-group"></a>Odebrat skupinu hybridních pracovních procesů

Chcete-li odebrat skupinu, musíte nejprve odebrat hybridní pracovní proces Runbooku z každý počítač, který je členem skupiny pomocí postupu uvedena výše. Potom proveďte následující kroky k odebrání skupiny:

1. Otevřete účet Automation na portálu Azure.
1. V části **automatizace procesu**, vyberte **skupinám Hybrid worker**. Vyberte skupinu, kterou chcete odstranit. Zobrazí se stránka Vlastnosti pro tuto skupinu.

   ![Stránka Vlastnosti](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Na stránce vlastnosti pro vybranou skupinu, vyberte možnost **odstranit**. Výzva k potvrzení této akce. Vyberte **Ano** Pokud jste si jisti, že chcete pokračovat.

   ![Potvrzovací zpráva](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Tento proces může trvat několik sekund. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

## <a name="network-planning"></a>Konfigurace sítě

### <a name="hybrid-worker-role"></a>Hybridní role pracovního procesu

Pro Runbook Worker hybridní připojení k a zaregistrovat u analýzy protokolů musí mít přístup k adresám URL, které jsou popsané v této části a číslo portu. Tento přístup je kromě [portů a adres URL potřebných pro agenta Microsoft Monitoring Agent](../log-analytics/log-analytics-agent-windows.md) pro připojení k analýze protokolů.

Pokud používáte proxy server pro komunikaci mezi agentem a analýzy protokolů služby, zajistěte, aby byly dostupné s příslušnými prostředky. Pokud používáte bránu firewall k omezení přístupu k Internetu, musíte nakonfigurovat bránu firewall tak, aby povolovala přístup.

Následující portů a adres URL jsou vyžadovány pro roli hybridní pracovní proces Runbooku ke komunikaci s automatizace:

* Port: Pouze TCP 443 je vyžadována pro odchozí přístup k Internetu.
* Globální adresa URL: *.azure-automation.net
* Globální adresa URL USA verze pro státní správu Virginia: *.azure automation.us
* Služba agenta: https://\<workspaceId\>.agentsvc.azure-automation.net

Pokud máte účet Automation, který je definován pro určitou oblast, můžete omezit komunikaci s místní stejné datové centrum. Následující tabulka obsahuje záznam DNS pro každou oblast:

| **Oblast** | **Záznam DNS** |
| --- | --- |
| Západní střed USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice produkčnímu 1.azure-automation.net |
| Střed USA – jih |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Východní USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Střední Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Západní Evropa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Severní Evropa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Jihovýchodní Asie |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Střed Indie |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonsko – východ |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Austrálie – jihovýchod |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Spojené království – jih | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice produkčnímu 1.azure-automation.net |
| USA (Gov) – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice produkčnímu 1.azure-automation.us |

Seznam IP adres oblasti namísto názvů oblast, stáhněte si [Azure Datacenter IP adresu](https://www.microsoft.com/download/details.aspx?id=41653) souboru XML z webu Microsoft Download Center.

> [!NOTE]
> Soubor XML adresu IP Datacentra Azure seznam rozsahů IP adres, které se používají v datových centrech Microsoft Azure. Soubor obsahuje rozsahy výpočty, SQL a úložiště.
>
>Aktualizovaný soubor odeslání každý týden. Soubor odráží aktuálně nasazená rozsahy a žádné nadcházející změny rozsahy IP. Nové rozsahy, které se zobrazují v souboru nebudou používány v datacentru alespoň jeden týden.
>
> Je vhodné stáhnout nový soubor XML každý týden. Aktualizujte lokalitu se správně identifikují služby spuštěné v Azure. Azure ExpressRoute uživatelé Upozorňujeme, že tento soubor se používá k aktualizaci inzerování protokolu BGP (Border Gateway) Azure místa v první týden v měsíci.

### <a name="update-management"></a>Update Management

Kromě standardní adresy a porty, které vyžaduje hybridní pracovní proces Runbooku že následující adresy se vyžaduje speciálně pro správu aktualizací. Komunikace na tyto adresy se provádí přes port 443.

|Veřejný Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="troubleshoot"></a>Řešení potíží

Další informace o řešení potíží s procesy Hybrid Runbook Worker naleznete v tématu [řešení potíží s procesy Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general)

## <a name="next-steps"></a>Další postup

Naučte se konfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí, najdete v tématu [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md).