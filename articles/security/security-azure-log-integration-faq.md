---
title: Integrace protokolů Azure – nejčastější dotazy | Dokumentace Microsoftu
description: Tento článek obsahuje odpovědi na otázky týkající se integrace protokolů Azure.
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
ms.date: 06/07/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 458b615ea872b62a412a71cbbb0b3827ccb4d9b8
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580922"
---
# <a name="azure-log-integration-faq"></a>Integrace protokolů Azure – nejčastější dotazy

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o integraci protokolů Azure.

>[!IMPORTANT]
> Funkce integrace protokolů Azure se přestanou používat podle 06/01/2019. Soubory ke stažení AzLog se deaktivuje 27. června 2018. Pokyny, jak postupovat přesun vpřed revizi, příspěvek [použití Azure monitoru k integraci s nástroji SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integrace protokolů Azure je služba operačního systému Windows, která vám umožní integrovat vaše místní zabezpečení informací a událostí (SIEM) systémy pro správu nezpracovaných protokolů z vašich prostředků Azure. Tato integrace poskytuje jednotný řídicí panel pro všechny vaše prostředky, místní nebo v cloudu. Můžete pak agregovat, korelovat, analyzovat a výstrahy pro události zabezpečení související s vašimi aplikacemi.

Upřednostňovanou metodou pro integraci protokolů Azure je pomocí konektoru SIEM dodavatele s Azure Monitor a podle těchto [pokyny](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Nicméně pokud t kódu dodavatele vašeho systému SIEM konektor poskytnout do Azure monitoru, bude pravděpodobně možné používat Azure Log Integration jako dočasné řešení (Pokud vašeho systému SIEM je podporovaný službou Azure Log Integration) dokud tyto connector je k dispozici.

## <a name="is-the-azure-log-integration-software-free"></a>Integrace protokolů Azure softwaru je zdarma?

Ano. Neplatí žádné poplatky za software integrace protokolů Azure.

## <a name="where-is-azure-log-integration-available"></a>Kde je integrace protokolů Azure dostupný?

Je aktuálně dostupné v komerčních Azure a Azure Government a není k dispozici v Číně a Německu.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Jak můžu zjistit, účty úložiště, ze kterých je integrace protokolů Azure stahování protokolů virtuálního počítače Azure?

Spusťte příkaz **AzLog zdrojového seznamu**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Jak zjistím, jaké předplatné Azure Log Integration protokoly jsou z?

V případě protokolů auditu, které jsou umístěny ve **AzureResourcemanagerJson** adresáře, předplatného, ID je v názvu souboru protokolu. To platí také pro protokoly **AzureSecurityCenterJson** složky. Příklad:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Protokoly auditování Azure Active Directory zahrnují ID tenanta jako část názvu.

Diagnostické protokoly, které se načítají z centra událostí neobsahují ID předplatného jako součást názvu. Místo toho obsahují zadaný jako součást vytvoření zdroje událostí centra popisný název. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Jak můžu aktualizovat konfiguraci proxy serveru?

Pokud vaše nastavení proxy serveru přímo neumožňuje přístup k úložišti Azure, otevřete **AZLOG. SOUBOR EXE. KONFIGURACE** ve **c:\Program Files\Microsoft Azure Log Integration**. Aktualizovat soubor zahrnout **defaultProxy** část vaší organizace s adresou proxy serveru. Po dokončení aktualizace zastavit a spustit službu pomocí příkazů **net stop AzLog** a **net start AzLog**.

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

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Jak lze zobrazit informace o předplatném v události Windows?

Při přidávání zdroje pro přidání ID předplatného na popisný název:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
Událost XML má následující metadata, včetně ID předplatného:

![Události XML][1]

## <a name="error-messages"></a>Chybové zprávy
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Kdy mohu spustit příkaz ```AzLog createazureid```, proč se zobrazí následující chyba?

Chyba:

  *Nepovedlo se vytvořit aplikaci AAD - Tenant 72f988bf-86f1-41af-91ab-2d7cd011db37 – z důvodu = "Zakázáno" - Message = "Dostatečná oprávnění k dokončení operace."*

**Azlog createazureid** příkaz se pokusí vytvořit instanční objekt ve všech tenantů Azure AD pro předplatné, které má přístup k Azure login. Pokud vaše přihlašovací jméno Azure je jenom uživatele typu Host v tomto tenantovi Azure AD, příkaz se nezdaří s "Dostatečná oprávnění k dokončení operace." Požádejte správce tenanta, který chcete přidat svůj účet jako uživatele v tenantovi.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Kdy mohu spustit příkaz **autorizovat azlog**, proč se zobrazí následující chyba?

Chyba:

  *Vytváří se přiřazení Role - AuthorizationFailed upozornění: Klient janedo@microsoft.com"s objektem id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' nemá oprávnění k provedení akce"Microsoft.Authorization/roleAssignments/write"rozsahu"/ subscriptions/70 d 95299-d689-4 c 97 – b971-0d8ff0000000".*

**Azlog Autorizovat** příkaz přiřadí roli Čtenář instančnímu objektu služby Azure AD (vytvořené pomocí **azlog createazureid**) pro zadaná předplatná. Pokud přihlášení k Azure není spolupracujícího správce nebo vlastníka předplatného, selže s chybovou zprávou "Ověření se nezdařilo". Azure na základě rolí řízení přístupu (RBAC) spolupracujícího správce nebo vlastníka je potřeba k dokončení této akce.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Kde najdu definici vlastnosti v protokolu auditu

Přečtěte si:

* [Audit operací pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-audit.md)
* [Seznam událostí správy v rámci předplatného v REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Kde najdete podrobnosti o na upozornění Azure Security Center?

Zobrazit [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Jak lze upravit co je shromažďováno s diagnostiku virtuálních počítačů?

Podrobnosti o tom, jak získat, upravit a nastavit konfiguraci diagnostiky Azure naleznete v tématu [použití Powershellu k povolení diagnostiky Azure v rámci virtuálního počítače se systémem Windows](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Následující příklad načte konfiguraci diagnostiky Azure:

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

Následující příklad změní konfiguraci diagnostiky Azure. V této konfiguraci se shromažďují jen události ID 4624 a událost ID 4625 z protokolu událostí zabezpečení. Microsoft Antimalware pro Azure události se shromažďují z protokolu událostí systému. Podrobnosti o použití výrazů XPath, naleznete v tématu [Consuming Events](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

Následující příklad nastaví konfiguraci diagnostiky Azure:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Když provedete změny, zkontrolujte účet úložiště k zajištění, že se shromažďují události správný.

Pokud máte problémy při instalaci a konfiguraci, otevřete [žádost o podporu](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Vyberte **integrace protokolů** jako službu, pro kterou jsou žádosti o podporu.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Můžete použít Azure Log Integration integrovat protokoly nástroje Network Watcher Moje SIEM?

Azure Network Watcher generuje velké množství informací o protokolování. Tyto protokoly nejsou určeny k odeslání na server SIEM. Jediný podporovaný cíl pro protokoly nástroje Network Watcher je účet úložiště. Integrace protokolů Azure nepodporuje čtení tyto protokoly a zpřístupňuje je pro server SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
