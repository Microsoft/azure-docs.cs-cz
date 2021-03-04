---
title: Podrobnosti konfigurace Azure Automation sítě
description: Tento článek poskytuje podrobnosti o síťových informacích vyžadovaných konfigurací stavu Azure Automation, Azure Automation Hybrid Runbook Worker, Update Management a Change Tracking a inventáře.
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 36331e9c07926d4d3ffff136aefa2f9a77d47cb4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708879"
---
# <a name="azure-automation-network-configuration-details"></a>Podrobnosti konfigurace Azure Automation sítě

Tato stránka poskytuje informace o síti, které jsou vyžadovány pro [Hybrid Runbook Worker a konfiguraci stavu](#hybrid-runbook-worker-and-state-configuration), a pro [Update Management a Change Tracking a inventář](#update-management-and-change-tracking-and-inventory).

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Konfigurace Hybrid Runbook Worker a stavu

Následující porty a adresy URL jsou vyžadovány pro Hybrid Runbook Worker a pro [konfiguraci stavu automatizace](automation-dsc-overview.md) ke komunikaci s Azure Automation.

* Port: pro odchozí přístup k Internetu se vyžaduje jenom 443.
* Globální adresa URL: `*.azure-automation.net`
* Globální adresa URL US Gov – Virginie: `*.azure-automation.us`
* Služba agenta: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Plánování sítě pro Hybrid Runbook Worker

Aby se mohl systém nebo uživatel Hybrid Runbook Worker připojit k Azure Automation a zaregistrovat se v ní, musí mít přístup k číslu portu a adresám URL popsaným v této části. Pracovní proces musí mít také přístup k [portům a adresám URL vyžadovaným pro Log Analytics agenta](../azure-monitor/agents/agent-windows.md) , aby se připojil k pracovnímu prostoru Azure monitor Log Analytics.

Pokud máte účet Automation, který je definovaný pro konkrétní oblast, můžete Hybrid Runbook Worker komunikaci omezit na příslušné oblastní datové centrum. Zkontrolujte [záznamy DNS používané Azure Automation](how-to/automation-region-dns-records.md) pro požadované záznamy DNS.

### <a name="configuration-of-private-networks-for-state-configuration"></a>Konfigurace privátních sítí pro konfiguraci stavu

Pokud jsou uzly umístěny v privátní síti, je nutné zadat výše uvedené porty a adresy URL. Tyto prostředky poskytují připojení k síti pro spravovaný uzel a umožňují, aby DSC komunikovala s Azure Automation.

Pokud používáte prostředky DSC, které komunikují mezi uzly, například s [prostředky WAITFOR *](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), musíte taky u těchto uzlů povolený přenos. Pochopte tyto požadavky na síť v dokumentaci ke každému prostředku DSC.

Informace o požadavcích klienta na TLS 1,2 najdete v tématu [vynucení TLS 1,2 pro Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="update-management-and-change-tracking-and-inventory"></a>Update Management a Change Tracking a inventář

Adresy v této tabulce jsou vyžadovány pro Update Management i pro Change Tracking a inventář. V obou případech platí i odstavec následující po tabulce.

Komunikace s těmito adresami používá **port 443**.

|Veřejný partnerský vztah Azure  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*. ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*. oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*. azure-automation.us|

Když vytvoříte pravidla zabezpečení skupiny sítě nebo nakonfigurujete Azure Firewall, aby povolovala přenosy do služby Automation Service a do pracovního prostoru Log Analytics, použijte [značky služby](../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** a **AzureMonitor**. Tím se zjednoduší průběžná správa pravidel zabezpečení sítě. Pokud se chcete připojit ke službě Automation z vašich virtuálních počítačů Azure bezpečně a soukromě, přečtěte si téma [použití privátního odkazu Azure](./how-to/private-link-security.md). Pokud chcete získat aktuální informace o značce služby a rozsahu, které mají být zahrnuty v rámci místních konfigurací brány firewall, přečtěte si téma [Stažení souborů JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="next-steps"></a>Další kroky

* Seznamte se s [přehledem služby Automation Update Management](update-management\overview.md).
* Přečtěte si o [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Přečtěte si o [Change Tracking a inventáři](change-tracking\overview.md).
* Přečtěte si o [konfiguraci stavu automatizace](automation-dsc-overview.md).