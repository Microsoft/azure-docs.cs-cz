---
title: Nejčastější dotazy k Azure Log Integration | Microsoft Docs
description: Tento článek obsahuje odpovědi na otázky týkající se Azure Log Integration.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 7ed63364a9dc4b96470a23af3a4bff832d42621b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727606"
---
# <a name="azure-log-integration-faq"></a>Nejčastější dotazy k Azure Log Integration

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Log Integration.

>[!IMPORTANT]
> Funkce integrace protokolu Azure bude zastaralá od 06/15/2019. Stahování AzLog bylo zakázáno 27. června 2018. Další informace o tom, co dělat, najdete v příspěvku [pomocí Azure monitoru pro integraci s nástroji Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) . 

Azure Log Integration je služba operačního systému Windows, kterou můžete použít k integraci nezpracovaných protokolů z prostředků Azure do místních systémů správy informací a zabezpečení SIEM (Security Information and Event Management). Tato integrace poskytuje jednotný řídicí panel pro všechny vaše prostředky, místně i v cloudu. Pak můžete agregovat, sladit, analyzovat a upozorňovat na události zabezpečení přidružené k vašim aplikacím.

Upřednostňovanou metodou pro integraci protokolů Azure je použití konektoru Azure Monitor dodavatele SIEM a podle těchto [pokynů](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). Pokud však dodavatel SIEM neposkytuje konektor pro Azure Monitor, můžete Azure Log Integration použít jako dočasné řešení (Pokud SIEM podporuje Azure Log Integration), dokud nebude takový konektor k dispozici.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="is-the-azure-log-integration-software-free"></a>Je Azure Log Integration software zdarma?

Ano. Azure Log Integration softwaru se neúčtují žádné poplatky.

## <a name="where-is-azure-log-integration-available"></a>Kde je Azure Log Integration k dispozici?

Je aktuálně k dispozici v Azure Commercial a Azure Government a není k dispozici v Číně ani v Německu.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Jak si můžu zobrazit účty úložiště, ze kterých Azure Log Integration nachází protokoly virtuálních počítačů Azure?

Spusťte **seznam AzLog Command source**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Jak můžu zjistit, ze kterého předplatného Azure Log Integration protokoly?

V případě protokolů auditu, které jsou umístěné v adresářích **AzureResourcemanagerJson** , se ID předplatného nachází v názvu souboru protokolu. To platí také pro protokoly ve složce **AzureSecurityCenterJson** . Příklad:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Protokoly auditu Azure Active Directory zahrnují ID tenanta jako součást názvu.

Diagnostické protokoly, které se čtou z centra událostí, nezahrnují jako součást názvu ID předplatného. Místo toho obsahují popisný název zadaný jako součást vytvoření zdroje centra událostí. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Jak můžu aktualizovat konfiguraci proxy serveru?

Pokud vaše nastavení proxy nepovoluje přímý přístup k úložišti Azure, otevřete **AZLOG. Programu. KONFIGURAČNÍ** soubor ve **složce c:\Program Files\Microsoft Azure log Integration**. Aktualizujte soubor tak, aby obsahoval oddíl **defaultProxy** s adresou proxy vaší organizace. Po dokončení aktualizace zastavte a spusťte službu pomocí příkazů **net stop AzLog** a **net start AzLog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress="http://127.0.0.1:8888"
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Jak si můžu zobrazit informace o předplatném v událostech Windows?

Připojit ID předplatného k popisnému názvu při přidávání zdroje:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
KÓD XML události má následující metadata, včetně ID předplatného:

![XML události](media/azure-log-integration-faq/event-xml.png)

## <a name="error-messages"></a>Chybové zprávy
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Proč se při spuštění příkazu ```AzLog createazureid```zobrazí následující chyba?

Chyba:

  *Nepovedlo se vytvořit aplikaci AAD-tenant 72f988bf-86f1-41af-91ab-2d7cd011db37-důvod = zakázané-zpráva = nedostatečná oprávnění k dokončení operace.*

Příkaz **azlog createazureid** se pokusí vytvořit instanční objekt ve všech klientech služby Azure AD pro předplatná, ke kterým má přihlášení Azure přístup. Pokud je vaše přihlášení Azure v tenantovi Azure AD jenom uživatelem typu Host, příkaz se nepovede a "nedostatečná oprávnění k dokončení operace". Požádejte správce tenanta, aby přidal váš účet jako uživatele v tenantovi.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Proč se při spuštění příkazu **azlog autorizuje**, proč se zobrazí následující chyba?

Chyba:

  *Upozornění při vytváření přiřazení role – AuthorizationFailed: Klient janedo\@Microsoft.com s ID objektu fe9e03e4-4dad-4328-910f-fd24a9660bd2 nemá autorizaci k provedení akce "Microsoft. Authorization/roleAssignments/Write" nad oborem "/Subscriptions/". 70d95299-d689-4c97-b971-0d8ff0000000'.*

Příkaz **azlog autorizovat** přiřadí roli čtenářů k instančnímu objektu služby Azure AD (vytvořenému pomocí **azlog createazureid**) k uvedeným předplatným. Pokud přihlášení Azure není spolusprávcem nebo vlastníkem předplatného, dojde k jeho selhání s chybovou zprávou "autorizace se nepovedla". K dokončení této akce je potřeba Access Control na základě rolí v Azure (RBAC) spolusprávce nebo vlastníka.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Kde najdu definici vlastností v protokolu auditu?

Další informace:

* [Operace auditu s Azure Resource Manager](/azure/azure-resource-manager/resource-group-audit)
* [Výpis událostí správy v rámci předplatného v Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Kde najdu podrobnosti o výstrahách Azure Security Center?

Přečtěte si téma [Správa a reakce na výstrahy zabezpečení v Azure Security Center](/azure/security-center/security-center-managing-and-responding-alerts).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Jak můžu upravit, co se shromažďuje s diagnostikou virtuálních počítačů?

Podrobnosti o tom, jak získat, upravit a nastavit konfiguraci Azure Diagnostics, najdete v tématu [použití PowerShellu k povolení Azure Diagnostics na virtuálním počítači s Windows](/azure/virtual-machines/windows/ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Následující příklad získá konfiguraci Azure Diagnostics:

    Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

Následující příklad upraví konfiguraci Azure Diagnostics. V této konfiguraci jsou z protokolu událostí zabezpečení shromažďována pouze ID události 4624 a ID události 4625. Události Microsoft Antimalware pro Azure se shromažďují z protokolu událostí systému. Podrobnosti o použití výrazů XPath naleznete v tématu spotřebovávající [události](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

Následující příklad nastaví konfiguraci Azure Diagnostics:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Po provedení změn zkontrolujte účet úložiště, abyste měli jistotu, že jsou shromažďovány správné události.

Pokud máte v průběhu instalace a konfigurace nějaké problémy, otevřete prosím [žádost o podporu](/azure/azure-supportability/how-to-create-azure-support-request). Jako službu, pro kterou požadujete podporu, vyberte **integrace protokolu** .

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Můžu použít Azure Log Integration k integraci protokolů Network Watcher do SIEM?

Azure Network Watcher generuje velké množství informací o protokolování. Tyto protokoly nejsou určeny k odeslání do SIEM. Jediným podporovaným cílem pro protokoly Network Watcher je účet úložiště. Azure Log Integration nepodporuje čtení těchto protokolů a jejich zpřístupnění pro SIEM.


