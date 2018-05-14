---
title: Procesy služby Azure Automation Hybrid Runbook Worker
description: Tento článek obsahuje informace o instalaci a použití hybridní pracovní proces Runbooku, což je funkce Azure Automation, který umožňuje spuštění sad runbook na počítačích ve vaší místní datacenter nebo poskytovatele cloudových služeb.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 43a3427b05b8e4f1fbaf0f8f5e6b60da9e837a46
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatizaci prostředků v datovém centru nebo v cloudu s hybridní pracovní proces Runbooku

Runbooky ve službě Azure Automation se nemusí mít přístup k prostředkům v ostatních cloudů nebo v místním prostředí, protože spustit i v cloudu Azure. Funkce hybridní pracovní proces Runbooku automatizace Azure umožňuje spuštění sad runbook přímo na počítači, který hostuje roli a s prostředky v prostředí pro správu těchto místních prostředků. Sady Runbook jsou uložené a spravované ve službě Azure Automation a pak doručí jeden nebo více určenými počítači.

Tato funkce je znázorněno na následujícím obrázku:

![Přehled hybridních služby Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>Skupinám hybrid Runbook Worker

Každý hybridní pracovní proces Runbooku je členem skupiny hybridní pracovní proces Runbooku, který zadáte při instalaci agenta. Skupina může obsahovat jednoho agenta, ale můžete nainstalovat více agentů ve skupině pro vysokou dostupnost.

Při spuštění sady runbook pro Hybrid Runbook Worker, určete skupinu, která běží na. Každý pracovní ve skupině dotáže Azure Automation. Chcete-li zobrazit, pokud jsou k dispozici žádné úlohy. Pokud je k dispozici úlohu pak o první pracovní proces úlohy dojde ho. Nelze zadat konkrétní pracovního procesu.

## <a name="installing-a-hybrid-runbook-worker"></a>Instalace hybridní pracovní proces Runbooku

Proces instalace Hybrid Runbook worker se liší v závislosti na operačním systémem. Následující tabulka obsahuje odkazy na různé metody, které můžete použít k instalaci hybridní pracovní proces Runbooku. Instalace a konfigurace Windows Hybrid Runbook Worker, existují dvě metody k dispozici. Doporučený postup je použít runbook služby automatizace zcela automatizovat proces vyžadované ke konfiguraci počítači se systémem Windows. Druhý způsob je následující podrobný postup při ručně nainstalujte a nakonfigurujte roli. Pro počítače se systémem Linux spusťte skript v jazyce Python pro instalaci agenta na počítači

|Operační systém  |Typy nasazení  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Ruční](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Ke správě konfigurace serverů podpora roli hybridní pracovní proces Runbooku s potřeby konfigurace stavu (DSC), musíte je přidat jako uzly DSC. Další informace o připojování je pro správu s DSC, najdete v části [registrace počítačů pro správu Azure Automation DSC](automation-dsc-onboarding.md).
>
>Pokud povolíte [řešení pro správu aktualizací](automation-update-management.md), libovolného počítače připojeného k pracovní prostor analýzy protokolů je automaticky nakonfigurovaný jako hybridní pracovní proces Runbooku na podporu sady runbook, které jsou zahrnuté v tomto řešení. Však není registrován u žádné skupiny hybridní pracovní proces již definována v účtu Automation. Počítač lze přidat do skupiny Hybrid Runbook Worker ve vašem účtu Automation na podporu automatizace sady runbook, dokud používáte stejný účet pro řešení a členství ve skupině hybridní pracovní proces Runbooku. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

Zkontrolujte [informace pro plánování vaší sítě](#network-planning) než začnete nasazovat hybridní pracovní proces Runbooku. Po úspěšném nasazení služby runbook worker, zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.

## <a name="removing-hybrid-runbook-worker"></a>Odebrání hybridní pracovní proces Runbooku

Jeden nebo více procesy Hybrid Runbook Worker můžete odebrat ze skupiny nebo můžete odebrat skupiny, v závislosti na vaše požadavky. K odebrání hybridní pracovní proces Runbooku místním počítači, proveďte následující kroky:

1. Na portálu Azure přejděte na svůj účet Automation.
2. Z **nastavení** vyberte **klíče** a poznamenejte si hodnoty pro pole **URL** a **primární přístupový klíč**. Tyto informace jsou potřeba pro další krok.

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
> Microsoft Monitoring Agent nebude odstraněn z počítače, funkce a konfiguraci role hybridní pracovní proces Runbooku.

## <a name="remove-hybrid-worker-groups"></a>Odebrat skupiny hybridní pracovní proces

Odebrat skupinu, je nutné nejprve odebrat hybridní pracovní proces Runbooku z každý počítač, který je členem skupiny postupem uvedena výše a potom proveďte následující kroky k odebrání skupiny.

1. Otevřete účet Automation na portálu Azure.
1. V části **automatizace procesu**, vyberte **skupinám Hybrid worker**. Vyberte skupinu, kterou chcete odstranit. Po výběru konkrétní skupinu **skupinu hybridních pracovních procesů** zobrazí se stránka vlastností.

   ![Skupina hybridních pracovních procesů Runbook stránky](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Na stránce vlastnosti pro vybranou skupinu, klikněte na **odstranit**. Zobrazí se zpráva s žádostí o potvrzení této akce vyberte **Ano** Opravdu chcete pokračovat.

   ![Dialogové okno potvrzení odstranění skupiny](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Dokončení tohoto procesu může trvat několik sekund a průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

## <a name="network-planning"></a>Konfigurace sítě

### <a name="hybrid-worker-role"></a>Hybridní role pracovního procesu

Pro Runbook Worker hybridní připojení k a zaregistrovat u analýzy protokolů musí mít přístup k adresám URL, které jsou popsané v této části a číslo portu. Toto je kromě [portů a adres URL potřebných pro agenta Microsoft Monitoring Agent](../log-analytics/log-analytics-agent-windows.md) pro připojení k analýze protokolů.

Pokud používáte proxy server pro komunikaci mezi agentem a analýzy protokolů služby, zajistěte, aby byly dostupné s příslušnými prostředky. Pokud používáte bránu firewall k omezení přístupu k Internetu, musíte nakonfigurovat bránu firewall tak, aby povolovala přístup.

Následující portů a adres URL jsou vyžadovány pro roli hybridní pracovní proces Runbooku ke komunikaci s automatizace:

* Port: Pouze TCP 443 je vyžadována pro odchozí přístup k Internetu.
* Globální adresa URL: *.azure-automation.net
* Globální adresa URL USA verze pro státní správu Virginia: *.azure automation.us
* Služba agenta: https://\<workspaceId\>.agentsvc.azure-automation.net

Pokud máte účet Automation, který je definován pro určitou oblast, můžete omezit komunikaci s místní stejné datové centrum. Následující tabulka obsahuje záznam DNS pro každou oblast.

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
> Soubor XML adresu IP Datacentra Azure seznam rozsahů IP adres, které se používají v datových centrech Microsoft Azure. Výpočty, SQL a úložiště rozsahy jsou zahrnuty v souboru.
>
>Aktualizovaný soubor odeslání každý týden. Soubor odráží aktuálně nasazená rozsahy a žádné nadcházející změny rozsahy IP. Nové rozsahy, které se zobrazují v souboru nebudou používány v datacentru alespoň jeden týden.
>
> Je vhodné stáhnout nový soubor XML každý týden. Aktualizujte lokalitu se správně identifikují služby spuštěné v Azure. Azure ExpressRoute uživatelé Upozorňujeme, že tento soubor se používá k aktualizaci inzerování protokolu BGP (Border Gateway) Azure místa první týden v měsíci.

### <a name="update-management"></a>Update Management

Kromě standardní adresy a porty, které vyžaduje hybridní pracovní proces Runbooku že následující adresy se vyžaduje speciálně pro správu aktualizací. Komunikace na tyto adresy se provádí přes port 443.

|Veřejný Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>Řešení potíží

Hybridní pracovní proces Runbooku závisí na agenta Microsoft Monitoring Agent komunikovat s vaším účtem Automation k registraci pracovního procesu, přijímat úlohy sady runbook a zprávy o stavu. Pokud pracovní proces registrace selže, zde jsou některé možné příčiny chyby:

1. Hybridní pracovní proces je za proxy nebo brány firewall.

   Ověřte, zda že má počítač odchozí přístup k *.azure automation.net na portu 443.

2. Počítač, na kterém běží hybridní pracovní proces na má menší než minimální požadavky na hardware.

   Počítače se systémem hybridní pracovní proces Runbooku by měl splňovat minimální požadavky na hardware před označením ho k hostování této funkce. V závislosti na využití prostředků jiné procesy na pozadí a kolizí způsobené sady runbook během provádění, jinak počítač stane přetížen a způsobit zpoždění úlohy sady runbook nebo vypršení časových limitů.

   Potvrďte, že počítač určený pro spouštění funkce Hybrid Runbook Worker splňuje minimální požadavky na hardware. Pokud ano, sledujte využití procesoru a paměti k určení všech korelace mezi výkon procesů Hybrid Runbook Worker a Windows. Pokud je paměť nebo zatížení procesoru, může to znamenat potřeba upgradovat nebo přidáním dalších procesorů, případně zvyšte paměti adres problémové místo prostředků a opravte případné chyby. Nebo vyberte jiný výpočtový prostředek, který může podporovat minimální požadavky a škálování při vytížení indikovat, že se o zvýšení nezbytné.

3. Služba Microsoft Monitoring Agent není spuštěna.

   Jestliže není spuštěna služba Microsoft Monitoring Agent Windows, nebude hybridní pracovní proces Runbooku komunikaci se službou Azure Automation. Ověřte agenta běží tak, že zadáte následující příkaz prostředí PowerShell: `get-service healthservice`. Pokud je služba zastavená, zadejte následující příkaz v prostředí PowerShell spustit službu: `start-service healthservice`.

4. V **aplikace a Správce služby Logs\Operations** protokolu událostí se zobrazí události 4502 a EventMessage obsahující **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**s následující popis: *certifikát předložený službu \<wsid\>. oms.opinsights.azure.com nevydala certifikační autorita používaná pro služby společnosti Microsoft. Obraťte se na správce sítě a zjistěte, zda používají proxy server, který zabrání komunikace TLS/SSL. V článku KB3126513 obsahuje další informace o připojení k řešení potíží.*
    Příčinou může být proxy serveru nebo síťové brány firewall blokuje komunikaci s Microsoft Azure. Ověřte, zda že má počítač odchozí přístup k *.azure automation.net na porty 443.

Protokoly se ukládají místně na každém hybridní pracovní proces na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Můžete zkontrolovat, zda existují jakékoli upozornění nebo chybové události zapsané do **aplikace a služby Logs\Microsoft-SMA\Operations** a **aplikace a Správce služby Logs\Operations** protokolu událostí, které by signalizovat připojení nebo jiné problém ovlivňující registrace role Azure Automation nebo problém při provádění operací se Normální.

Další pokyny o tom, jak vyřešit problémy s správy aktualizací najdete v tématu [správy aktualizací – řešení potíží](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>Další postup

Zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.
