---
title: Integrace Azure protokolu – nejčastější dotazy | Microsoft Docs
description: Tento článek obsahuje odpovědi na otázky týkající se integrace se službou Azure protokolu.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 06/06/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 31524f24eea97082c8b148ba0a65bbeb24717039
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839316"
---
# <a name="azure-log-integration-faq"></a>Integrace Azure protokolu – nejčastější dotazy

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) informace o integraci Azure protokolu.

>[!IMPORTANT]
> Funkce integrace protokolu Azure bude 06/01/2019 zastaralá.  AzLog stahování bude ve 27. června 2018 zakázáno. Pokyny k tomu, co udělat přesunutí dopředného zkontrolujte v příspěvku [monitorování pomocí Azure, který chcete integrovat s nástroji SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/preview/?cdn=disable) 

Integrace se službou Azure protokolu je služba operačního systému Windows, která můžete integrovat do vaší místní zabezpečení informací a událostí (SIEM) systémy správy nezpracovaná protokoly z vašich prostředků Azure. Tato integrace poskytuje jednotný řídicí panel pro všechny vaše prostředky, místně nebo v cloudu. Můžete pak agregace, korelovat, analyzovat a výstrahy zabezpečení události související s vašimi aplikacemi.

Upřednostňovanou metodou pro integraci Azure protokoly se pomocí svého dodavatele SIEM Azure monitorování konektoru a následující tyto [pokyny](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Ale pokud dodavatele SIEM neposkytuje konektor k monitorování Azure, bude pravděpodobně možné použít protokol integrace se službou Azure jako dočasné řešení (je-li vašeho systému SIEM podporován protokol integrace se službou Azure) dokud takové connector je k dispozici.

## <a name="is-the-azure-log-integration-software-free"></a>Je software protokolu integrace se službou Azure volné?

Ano. Není nijak zpoplatněn pro software protokolu integrace se službou Azure.

## <a name="where-is-azure-log-integration-available"></a>Kde je k dispozici protokolu integrace se službou Azure?

Je aktuálně k dispozici v komerčních Azure a Azure Government a není k dispozici v Číně nebo v Německu.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Jak lze zobrazit účty úložiště, ze kterých je integrace se službou Azure protokolu stahování protokolů virtuálního počítače Azure?

Spusťte příkaz **AzLog zdrojového seznamu**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Jak poznám, které předplatné protokoly protokolu integrace se službou Azure jsou z?

V případě protokoly auditu, které jsou umístěny v **AzureResourcemanagerJson** adresáře, předplatné ID je v názvu protokolového souboru. To platí také pro protokoly v **AzureSecurityCenterJson** složky. Příklad:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Protokoly auditu Azure Active Directory zahrnují ID klienta v rámci názvu.

Diagnostické protokoly, které se načítají z centra událostí nebudou obsahovat ID předplatného v rámci názvu. Místo toho obsahují popisný název zadaný jako součást vytvoření zdroje události rozbočovače. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Jak můžete aktualizovat konfiguraci proxy serveru?

Pokud vaše nastavení proxy serveru přímo neumožňuje přístup k úložišti Azure, otevřete **AZLOG. SOUBOR EXE. KONFIGURACE** souboru v **c:\Program Files\Microsoft Azure protokolu integrace**. Aktualizace souboru **defaultProxy –** oddíl s adresu proxy serveru v organizaci. Po dokončení aktualizace zastavit a spustit službu pomocí příkazů **net stop AzLog** a **net start AzLog**.

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

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Jak lze zobrazit informace o předplatném v události systému Windows?

Při přidávání zdroj provést připojení k popisný název ID předplatného:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
Událost XML má následující metadata, včetně ID předplatného:

![Událost XML][1]

## <a name="error-messages"></a>Chybové zprávy
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Při spuštění příkazu ```AzLog createazureid```, proč se zobrazí chybová zpráva?

Chyba:

  *Nepodařilo se vytvořit aplikaci AAD - klienta 72f988bf-86f1-41af-91ab-2d7cd011db37-důvod = "Zakázáno" - zpráva = "Dostatečná oprávnění k dokončení operace."*

**Azlog createazureid** příkaz se pokouší vytvořit objekt služby v všechny klienty Azure AD pro předplatné, které má přístup k přihlášení k Azure. Pokud vaše Azure přihlášení je pouze uživatel guest v tomto klientovi Azure AD, příkaz selže a "Dostatečná oprávnění k dokončení operace." Požádejte správce klienta pro přidání účtu jako uživatel v klientovi.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Při spuštění příkazu **azlog Autorizovat**, proč se zobrazí chybová zpráva?

Chyba:

  *Upozornění vytvoření přiřazení Role - AuthorizationFailed: klient janedo@microsoft.com' s objektem id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2, nemá oprávnění k provedení akce 'Microsoft.Authorization/roleAssignments/write' v oboru, nebo odběry / 70d 95299-d689-4c 97-b971-0d8ff0000000'.*

**Azlog Autorizovat** příkaz přiřadí roli čtečky objekt služby Azure AD (vytvořené pomocí **azlog createazureid**) zadané předplatným. Pokud Azure přihlášení není společné správce nebo vlastníka předplatného, se nezdaří s chybovou zprávou "Autorizace se nezdařilo". Azure na základě rolí řízení přístupu (RBAC) spolusprávcem nebo vlastník je nutný k dokončení této akce.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Kde najdu definici vlastnosti v protokolu auditování?

Přečtěte si:

* [Operace auditu pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-audit.md)
* [Seznam událostí správy v rámci předplatného v monitorování REST API služby Azure](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Kde můžete najít podrobnosti o ve výstrahách Azure Security Center?

V tématu [Správa a zpracování výstrah zabezpečení v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Jak lze upravit co je shromažďováno s diagnostikou virtuálního počítače?

Podrobnosti o tom, jak získat, upravit a nastavit konfiguraci Azure Diagnostics najdete v části [pomocí Powershellu povolit v virtuálního počítače se systémem Windows Azure Diagnostics](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Následující příklad načte konfiguraci Azure Diagnostics:

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

Následující příklad změní konfiguraci Azure Diagnostics. V této konfiguraci pouze událost ID 4624 a událost ID 4625 se shromažďují z protokolu událostí zabezpečení. Antimalware od Microsoftu pro Azure události se shromažďují z protokolu událostí systému. Podrobnosti o používání výrazech XPath najdete v tématu [využívání události](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

Následující příklad nastaví konfiguraci Azure Diagnostics:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Po provedení změn, zkontrolujte účet úložiště zajistit, že jsou shromažďovány správné události.

Pokud máte problémy při instalaci a konfiguraci, otevřete prosím [žádost o podporu](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Vyberte **integrace protokolu** jako službu, pro kterou jsou žádosti o podporu.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Můžete použít protokol integrace se službou Azure integrovat do mé SIEM protokoly sledovací proces sítě?

Azure sledovací proces sítě generuje velká množství informací o protokolování. Tyto protokoly nejsou určeny k odeslání do server SIEM. Pouze cíl podporované protokoly sledovací proces sítě je účet úložiště. Integrace se službou Azure protokolu nepodporuje čtení tyto protokoly a zpřístupňuje je server SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
