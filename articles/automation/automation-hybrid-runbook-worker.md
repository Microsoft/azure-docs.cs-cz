---
title: Azure Automation Hybrid Runbook Worker
description: Tento článek obsahuje informace o instalaci a používání hybridního runbook worker, což je funkce Azure Automation, kterou můžete použít ke spuštění runbooků na počítačích v místním datovém centru nebo poskytovateli cloudu.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 861c7ffa647c8d2f37b32c359253ca991eeb314f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457701"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatizace prostředků v datovém centru nebo cloudu pomocí hybridního runbookworkeru

Runbooky v Azure Automation nemusí mít přístup k prostředkům v jiných cloudech nebo ve vašem místním prostředí, protože běží na cloudové platformě Azure. Pomocí funkce Hybridní runbook worker azure automation můžete spustit runbooky přímo v počítači, který je hostitelem role a proti prostředkům v prostředí ke správě těchto místních prostředků. Sady Runbook se ukládají a spravují v Azure Automation a pak se doručují do jednoho nebo více přiřazených počítačů.

Následující obrázek ilustruje tuto funkci:

![Přehled funkce Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Každý pracovník hybridnísady Runbook je členem skupiny Hybridní pracovní proces sady Runbook, kterou zadáte při instalaci agenta. Skupina může obsahovat jednoho agenta, ale můžete nainstalovat více agentů ve skupině pro vysokou dostupnost. Každý počítač může hostovat jeden hybridní pracovník, který se hlásí k jednomu účtu automatizace.

Při spuštění runbooku v hybridním pracovníkovi runbooku určíte skupinu, ve které běží. Každý pracovník ve skupině dotazování Azure Automation zjistit, jestli jsou k dispozici nějaké úlohy. Pokud je úloha k dispozici, první pracovník, který ji získá, ji převezme. Doba zpracování fronty úloh závisí na hardwarovém profilu hybridního pracovního procesu a načtení. Nelze zadat konkrétního pracovníka. Hybridní runbook pracovníci nesdílejí mnoho omezení, které mají karantény zabezpečení Azure. Nemají stejné limity pro místo na disku, paměť nebo síťové sokety. Hybridní pracovníci runbooku jsou omezeni pouze prostředky na hybridní runbook worker sám. Kromě toho hybridní runbook pracovníci nesdílejí 180 minut [spravedlivého sdílení](automation-runbook-execution.md#fair-share) časový limit, který mají karantény zabezpečení Azure. Další informace o limitech služeb pro karantény zabezpečení Azure a hybridní pracovníky runbooku najdete v [tématu omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)úloh .

## <a name="install-a-hybrid-runbook-worker"></a>Instalace hybridního pracovníka sady Runbook

Proces instalace hybridního pracovníka sady Runbook závisí na operačním systému. Níže uvedená tabulka definuje typy nasazení.

|Operační systém  |Typy nasazení  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Ruční](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

Doporučenou metodou instalace je použití runbooku Automation k úplné automatizaci procesu konfigurace počítače se systémem Windows. Druhou metodou je postup krok za krokem k ruční instalaci a konfiguraci role. Pro počítače s Linuxem spustíte skript Pythonu pro instalaci agenta do počítače.

> [!NOTE]
> Chcete-li spravovat konfiguraci serverů, které podporují roli hybridního pracovníka runbooku s konfigurací požadovaného stavu (DSC), je třeba je přidat jako uzly DSC. Další informace o jejich připojení pro správu s DSC najdete [v tématu Onboarding machines for management by Azure Automation DSC](automation-dsc-onboarding.md).
>
>Pokud povolíte [řešení správy aktualizací](automation-update-management.md), každý počítač, který je připojený k pracovnímu prostoru Azure Log Analytics, se automaticky nakonfiguruje jako hybridní pracovník runbooku pro podporu runbooků zahrnutých v tomto řešení. Počítač však není registrován u žádné skupiny hybridních pracovníků, které jsou již definovány ve vašem účtu automatizace. Počítač lze přidat do skupiny Hybridní pracovní doba sady Runbook ve vašem účtu Automation pro podporu runbooků automatizace, pokud používáte stejný účet pro řešení i členství ve skupině Hybridní runbook Worker. Tato funkce byla přidána do verze 7.2.12024.0 hybridního pracovníka runbooku.

Než začnete nasazovat hybridní pracovník runbooku, zkontrolujte [informace pro plánování sítě.](#network-planning) Po úspěšném nasazení pracovníka zkontrolujte [runbooky na hybridním pracovníku runbooku,](automation-hrw-run-runbooks.md) abyste se dozvěděli, jak nakonfigurovat sady Runbook pro automatizaci procesů v místním datovém centru nebo jiném cloudovém prostředí.

Počítač lze přidat do skupiny Hybridní pracovní doba sady Runbook ve vašem účtu Automation pro podporu runbooků automatizace, pokud používáte stejný účet pro řešení i členství ve skupině Hybridní runbook Worker. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

## <a name="a-nameremove-a-hybrid-runbook-workerremove-a-hybrid-runbook-worker-from-an-on-premises-computer"></a><a name="remove-a-hybrid-runbook-worker">Odebrání hybridního pracovníka runbooku z místního počítače

Hybridní pracovník runbooku můžete odebrat z místního počítače, jak je popsáno v této části pro Windows a Linux.

### <a name="remove-the-worker-on-windows"></a>Odebrání pracovníka v systému Windows

1. Na webu Azure Portal přejděte na svůj účet Automation.
2. V části **Nastavení účtu**vyberte **Klíče** a poznamenejte si hodnoty **adresy URL** a **primárního přístupového klíče**.

3. Otevřete relaci Prostředí PowerShell v režimu správce a spusťte následující příkaz s adresou URL a hodnotami primárního přístupového klíče. `Verbose` Parametr použijte pro podrobný protokol procesu odebrání. Chcete-li odebrat zastaralé počítače ze skupiny Hybridní pracovní proces, použijte volitelný `machineName` parametr.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="remove-the-worker-on-linux"></a>Odebrání pracovníka na Linuxu

Pomocí příkazu `ls /var/opt/microsoft/omsagent` hybridního pracovníka runbooku můžete získat ID pracovního prostoru. Vytvoří se složka s názvem ID pracovního prostoru.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Tento kód neodebere agenta Analýzy protokolů pro Linux z počítače. Odebere pouze funkce a konfiguraci role hybridního pracovníka runbooku.

## <a name="remove-a-hybrid-worker-group"></a>Odebrání skupiny Hybrid Worker

Chcete-li odebrat skupinu hybridních pracovníků runbooku, musíte nejprve odebrat hybridní pracovník runbooku ze všech počítačů, které jsou členy skupiny. Potom skupinu odeberte pomocí následujících kroků:

1. Otevřete účet Automation na webu Azure Portal.
2. V části **Automatizace procesů**vyberte **Hybridní pracovní skupiny** . Vyberte skupinu, kterou chcete odstranit. Zobrazí se stránka vlastností této skupiny.

   ![Stránka Vlastnosti](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na stránce vlastností vybrané skupiny vyberte **Odstranit**. Zobrazí se zpráva s žádostí o potvrzení této akce. Pokud jste si jisti, že chcete pokračovat, vyberte **ano.**

   ![Potvrzovací zpráva](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Dokončení tohoto procesu může trvat několik sekund. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

## <a name="configure-your-network"></a><a name="network-planning"></a>Konfigurace sítě

### <a name="hybrid-worker-role"></a>Role hybridního pracovníka

Aby se pracovník hybridního runbooku mohl připojit a zaregistrovat se k Azure Automation a zaregistrovat se u něj, musí mít přístup k číslu portu a adresám URL, které jsou popsány v této části. Tento přístup je nahoře na [porty a adresy URL požadované pro agenta Log Analytics](../azure-monitor/platform/agent-windows.md) pro připojení k protokolům Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pokud používáte proxy server pro komunikaci mezi agentem a službou Azure Automation, ujistěte se, že jsou přístupné příslušné prostředky. Časový čas pro požadavky z hybridního pracovníka runbooku a automatizačních služeb je 30 sekund. Po 3 pokusech se požadavek nezdaří. Pokud k omezení přístupu k Internetu používáte bránu firewall, je nutné nakonfigurovat bránu firewall tak, aby umožňovala přístup. Pokud používáte bránu Log Analytics jako proxy server, ujistěte se, že je nakonfigurovaná pro hybridní pracovníky. Pokyny k tomu, jak to provést, naleznete [v tématu Konfigurace brány Log Analytics pro hybridní pracovníky automatizace](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

Pro komunikaci s automatizací je vyžadován následující port a adresy URL pro komunikaci s automatizací pro roli pracovníka hybridního runbooku:

* Port: Pro odchozí přístup k internetu je vyžadován pouze protokol TCP 443.
* Globální adresa URL: *.azure-automation.net
* Globální adresa URL US Gov – Virginie: *.azure-automation.us
* Služba agenta: https://\<\>workspaceId .agentsvc.azure-automation.net

Při definování výjimek doporučujeme používat uvedené adresy. Pro IP adresy si můžete stáhnout [rozsahy IP adres datového centra Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Tento soubor je aktualizován každý týden a má aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres.

Pokud máte účet Automation, který je definován pro konkrétní oblast, můžete omezit komunikaci na toto regionální datové centrum. V následující tabulce je uveden záznam DNS pro každou oblast:

| **Oblasti** | **Záznam DNS** |
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

Seznam adres IP oblasti namísto názvů oblastí si stáhněte soubor XML [IP adresy Datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653) ze služby Stažení softwaru.

> [!NOTE]
> V souboru XML ip adresy Datového centra Azure jsou uvedeny rozsahy IP adres, které se používají v datových centrech Microsoft Azure. Soubor obsahuje výpočetní, SQL a rozsahy úložiště.
>
>Aktualizovaný soubor je zaúčtován každý týden. Soubor odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres. Nové oblasti, které se zobrazí v souboru, se v datových centrech nepoužívají alespoň jeden týden.
>
> Je vhodné stáhnout nový soubor XML každý týden. Potom aktualizujte web, abyste správně identifikovali služby spuštěné v Azure. Uživatelé Azure ExpressRoute by si měli uvědomit, že tento soubor se používá k aktualizaci reklamy protokolu BGP (Border Gateway Protocol) na místo Azure v prvním týdnu každého měsíce.

### <a name="update-management"></a>Update Management

Kromě standardních adres a portů, které vyžaduje pracovník hybridního runbooku, jsou vyžadovány následující adresy speciálně pro správu aktualizací. Komunikace s těmito adresami se provádí přes port 443.

|Veřejný partnerský vztah Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci sad Runbook pro automatizaci procesů v místním datovém centru nebo jiném cloudovém prostředí najdete v [tématu Spuštění runbooků na hybridním pracovníkovi sady Runbook](automation-hrw-run-runbooks.md).
* Informace o řešení potíží s hybridními pracovníky runbooku naleznete [v tématu Poradce při potížích s hybridními pracovníky runbooku](troubleshoot/hybrid-runbook-worker.md#general).