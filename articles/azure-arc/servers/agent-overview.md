---
title: Přehled agenta připojeného počítače systému Windows
description: Tento článek poskytuje podrobný přehled dostupného agenta serverů s podporou ARC Azure, který podporuje monitorování virtuálních počítačů hostovaných v hybridních prostředích.
ms.date: 02/18/2021
ms.topic: conceptual
ms.openlocfilehash: ebd9412849b4a0b3081e892d7472e598ca6e8365
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651089"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Přehled agenta serverů s podporou ARC Azure

Agent počítače připojené k serverům s podporou Azure ARC vám umožní spravovat počítače se systémem Windows a Linux hostované mimo Azure ve vaší podnikové síti nebo jiném poskytovateli cloudu. Tento článek poskytuje podrobný přehled požadavků na agenty, systém a síť a různé metody nasazení.

>[!NOTE]
>Počínaje obecným vydáním serverů s podporou Azure ARC v září 2020 budou všechny předběžné verze agenta připojeného počítače Azure (agenti s verzemi nižšími než 1,0) od **2. února 2021** **zastaralé** .  Tento časový rámec vám umožní upgradovat na verzi 1,0 nebo vyšší, než předem vypraví agenti nebudou moct komunikovat se službou servery s podporou Azure ARC.

## <a name="agent-component-details"></a>Podrobnosti komponenty agenta

Balíček agenta připojeného počítače Azure obsahuje několik logických součástí, které jsou společné pro dohromady.

* Služba metadat hybridní instance (HIMDS) spravuje připojení k Azure a identitu Azure připojeného počítače.

* Agent konfigurace hosta poskytuje funkce In-Guest zásady a konfigurace hostů, jako je například vyhodnocení toho, jestli počítač vyhovuje požadovaným zásadám.

    Všimněte si následujícího chování s Azure Policy [konfigurací hostů](../../governance/policy/concepts/guest-configuration.md) pro odpojený počítač:

    * Přiřazení zásad konfigurace hosta, které cílí na odpojené počítače, není nijak ovlivněno.
    * Přiřazení hosta je uloženo místně po dobu 14 dnů. Pokud se agent připojeného počítače připojí ke službě za 14 dní, znovu se použije přiřazení zásad.
    * Přiřazení se odstraní po 14 dnech a po 14 dnech se znovu nepřiřazují k počítači.

* Agent rozšíření spravuje rozšíření virtuálních počítačů, včetně instalace, odinstalace a upgradu. Rozšíření se stáhnou z Azure a zkopírují se do `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` složky ve Windows a pro Linux do `/opt/GC_Ext/downloads` . V systému Windows je rozšíření nainstalováno v následující cestě `%SystemDrive%\Packages\Plugins\<extension>` a v systému Linux je rozšíření nainstalováno do nástroje `/var/lib/waagent/<extension>` .

## <a name="instance-metadata"></a>Metadata instance

Informace o metadatech připojeného počítače jsou shromažďovány po registraci agenta připojeného počítače s povolenými servery ARC. Konkrétně se jedná o tyto:

* Název, typ a verze operačního systému
* Název počítače
* Plně kvalifikovaný název domény počítače (FQDN)
* Verze agenta připojeného počítače
* Plně kvalifikovaný název domény služby Active Directory a DNS (FQDN)
* UUID (ID SYSTÉMU BIOS)
* Prezenční signál agenta připojeného počítače
* Verze agenta připojeného počítače
* Veřejný klíč pro spravovanou identitu
* Stav dodržování zásad a podrobnosti (Pokud se používá Azure Policy zásady konfigurace hostů)

Agent z Azure požaduje následující informace metadat:

* Umístění prostředku (oblast)
* ID virtuálního počítače
* Značky
* Azure Active Directory certifikát spravované identity
* Přiřazení zásad konfigurace hostů
* Požadavky na rozšíření – Instalace, aktualizace a odstranění.

## <a name="download-agents"></a>Stáhnout agenty

Balíček agenta připojeného počítače Azure pro Windows a Linux si můžete stáhnout z níže uvedených umístění.

* [Balíček Windows agent Instalační služba systému Windows](https://aka.ms/AzureConnectedMachineAgent) z webu Microsoft Download Center.

* Balíček agenta pro Linux se distribuuje z [úložiště balíčků](https://packages.microsoft.com/) Microsoftu pomocí preferovaného formátu balíčku pro distribuci (. Ot./min. nebo. DEB).

Agenta připojeného počítače Azure pro Windows a Linux se dá upgradovat na nejnovější verzi ručně nebo automaticky v závislosti na vašich požadavcích. Další informace najdete [tady](manage-agent.md).

## <a name="prerequisites"></a>Požadavky

### <a name="supported-environments"></a>Podporovaná prostředí

Servery s podporou ARC podporují instalaci agenta připojeného počítače na jakýkoli fyzický server a virtuální počítač hostovaný *mimo* Azure. To zahrnuje virtuální počítače běžící na platformách, jako je VMware, Azure Stack HCI a další cloudová prostředí. Servery s podporou ARC nepodporují instalaci agenta na virtuálních počítačích, které běží v Azure, nebo virtuálních počítačů běžících na Azure Stack hub nebo na Azure Stack Edge, protože už jsou modelované jako virtuální počítače Azure.

### <a name="supported-operating-systems"></a>Podporované operační systémy

Pro agenta připojeného počítače Azure jsou oficiálně podporované následující verze operačního systému Windows a Linux:

- Windows Server 2008 R2, Windows Server 2012 R2 a vyšší (včetně jádra serveru)
- Ubuntu 16,04 a 18,04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)
- Oracle Linux 7

> [!WARNING]
> Název hostitele nebo počítač se systémem Linux nemůže v názvu použít jedno z vyhrazených slov nebo ochranných známek, jinak se pokus o registraci připojeného počítače k Azure nezdaří. Seznam rezervovaných slov najdete v tématu [řešení chyb rezervovaných názvů prostředků](../../azure-resource-manager/templates/error-reserved-resource-name.md) .

### <a name="required-permissions"></a>Požadovaná oprávnění

* Pokud chcete připojit počítače, jste členem role **připojení počítače připojeného k Azure** .

* Pokud chcete číst, upravovat a odstraňovat počítač, jste členem role **Správce prostředků počítače připojeného k Azure** . 

### <a name="azure-subscription-and-service-limits"></a>Limity předplatného a služeb Azure

Před konfigurací počítačů se servery s podporou ARC Azure zkontrolujte [omezení Azure Resource Manager předplatného](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) a [omezení skupiny prostředků](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) , abyste naplánovali počet počítačů, které se mají připojit.

Servery s podporou ARC Azure podporují až 5 000 instancí počítačů ve skupině prostředků.

### <a name="transport-layer-security-12-protocol"></a>Protokol TLS (Transport Layer Security 1,2)

Abychom zajistili zabezpečení dat při přenosu do Azure, důrazně doporučujeme nakonfigurovat počítač tak, aby používal protokol TLS (Transport Layer Security) 1,2. Zjistili jsme, že starší verze TLS/SSL (Secure Sockets Layer) (SSL) jsou zranitelné a i když stále fungují k tomu, aby se zajistila zpětná kompatibilita, **nedoporučuje** se.

|Platforma/jazyk | Podpora | Další informace |
| --- | --- | --- |
|Linux | Distribuce systému Linux se obvykle spoléhají na [OpenSSL](https://www.openssl.org) pro podporu TLS 1,2. | Zkontrolujte [OpenSSL protokolu změn](https://www.openssl.org/news/changelog.html) a potvrďte, že je podporovaná vaše verze OpenSSL.|
| Windows Server 2012 R2 a vyšší | Podporované a povolené ve výchozím nastavení. | Potvrďte, že stále používáte [výchozí nastavení](/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Konfigurace sítě

Agent připojeného počítače pro systémy Linux a Windows komunikuje zabezpečeným způsobem přes Azure ARC přes port TCP 443. Pokud se počítač připojí prostřednictvím brány firewall nebo proxy server komunikovat přes Internet, Projděte si následující informace, abyste pochopili požadavky na konfiguraci sítě.

> [!NOTE]
> Servery s podporou ARC nepodporují použití [Log Analytics brány](../../azure-monitor/agents/gateway.md) jako proxy pro agenta připojeného počítače.
>

Pokud je odchozí připojení omezeno bránou firewall nebo proxy server, ujistěte se, že níže uvedené adresy URL nejsou blokované. Pokud povolíte pouze rozsahy IP adres nebo názvy domén, které musí agent komunikovat se službou, budete potřebovat přístup k následujícím značkám služby a adresám URL.

Značky služby:

* Azureactivedirectory selhala
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure

Adrese

| Prostředek agenta | Popis |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Konfigurace hosta|
|`*.his.arc.azure.com`|Služba hybridní identity|
|`www.office.com`|Office 365|

Agenti Preview (verze 0,11 a nižší) také vyžadují přístup k následujícím adresám URL:

| Prostředek agenta | Popis |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Konfigurace hosta|
|`*-agentservice-prod-1.azure-automation.net`|Konfigurace hosta|

Seznam IP adres pro jednotlivé značky nebo oblasti služby najdete v souboru JSON – [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publikuje týdenní aktualizace obsahující každou službu Azure a rozsahy IP adres, které používá. Tyto informace v souboru JSON představují aktuální časový Seznam rozsahů IP adres, které odpovídají jednotlivým značkám služby. Tato IP adresa se může změnit. Pokud se pro konfiguraci brány firewall vyžadují rozsahy IP adres, měla by se tato značka služby **AzureCloud** použít k povolení přístupu ke všem službám Azure. Nepovolujte monitorování zabezpečení ani kontrolu těchto adres URL. Povolte je stejně jako jiný internetový provoz.

Další informace najdete v [přehledu značek služeb](../../virtual-network/service-tags-overview.md).

### <a name="register-azure-resource-providers"></a>Registrovat poskytovatele prostředků Azure

Servery s podporou ARC Azure jsou závislé na následujících poskytovatelích prostředků Azure ve vašem předplatném, aby bylo možné tuto službu používat:

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

Pokud nejsou registrovány, můžete je zaregistrovat pomocí následujících příkazů:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Rozhraní příkazového řádku Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Poskytovatele prostředků můžete také zaregistrovat v Azure Portal podle kroků v části [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="installation-and-configuration"></a>Instalace a konfigurace

Propojení počítačů ve vašem hybridním prostředí s Azure je možné dosáhnout pomocí různých metod v závislosti na vašich požadavcích. Následující tabulka zvýrazňuje jednotlivé metody, abyste zjistili, které funkce jsou pro vaši organizaci nejvhodnější.

> [!IMPORTANT]
> Agenta připojeného počítače nejde nainstalovat na virtuální počítač Azure s Windows. Pokud se pokusíte, instalace ji zjistí a vrátí zpět.

| Metoda | Popis |
|--------|-------------|
| Interaktivně | Ručně nainstalujte agenta na jeden nebo malý počet počítačů podle postupu v části [připojení počítačů od Azure Portal](onboard-portal.md).<br> Z Azure Portal můžete vygenerovat skript a spustit ho na počítači, abyste mohli automatizovat kroky instalace a konfigurace agenta.|
| Ve velkém měřítku | Nainstalujte a nakonfigurujte agenta pro více počítačů, které následují po [připojení počítačů pomocí instančního objektu](onboard-service-principal.md).<br> Tato metoda vytvoří instanční objekt pro připojení počítačů, které nejsou interaktivně.|
| Ve velkém měřítku | Nainstalujte a nakonfigurujte agenta pro více počítačů, které následují za metodou [pomocí Windows POWERSHELL DSC](onboard-dsc.md).<br> Tato metoda používá instanční objekt k propojení počítačů bez interaktivního připojení k prostředí PowerShell DSC. |

## <a name="connected-machine-agent-technical-overview"></a>Technický přehled agenta připojeného počítače

### <a name="windows-agent-installation-details"></a>Podrobnosti o instalaci agenta Windows

Agenta připojeného počítače pro systém Windows lze nainstalovat pomocí jedné z následujících tří metod:

* Dvakrát klikněte na soubor `AzureConnectedMachineAgent.msi` .
* Ručně spuštěním Instalační služba systému Windows balíčku `AzureConnectedMachineAgent.msi` z příkazového prostředí.
* Z relace PowerShellu pomocí skriptované metody.

Po instalaci agenta připojeného počítače pro systém Windows jsou aplikovány následující změny konfigurace v rámci systému.

* Během instalace se vytvoří následující instalační složky.

    |Složka |Popis |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Výchozí instalační cesta obsahující soubory podpory agenta.|
    |%ProgramData%\AzureConnectedMachineAgent |Obsahuje konfigurační soubory agenta.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Obsahuje získané tokeny.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Obsahuje konfigurační soubor agenta, který ve `agentconfig.json` službě zaznamenává informace o registraci.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | Instalační cesta obsahující soubory agenta konfigurace hosta |
    |%ProgramData%\GuestConfig |Obsahuje zásady (použité) z Azure.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | Rozšíření se stáhnou z Azure a zkopírují se sem.|

* Následující služby systému Windows se v cílovém počítači vytvoří během instalace agenta.

    |Název služby |Zobrazované jméno |Název procesu |Popis |
    |-------------|-------------|-------------|------------|
    |himds |Hybridní Instance Metadata Service Azure |himds |Tato služba implementuje službu metadat Azure instance (IMDS) pro správu připojení k Azure a identitě Azure připojeného počítače.|
    |GCArcService |Služba ARC konfigurace hosta |gc_service |Monitoruje konfiguraci požadovaného stavu počítače.|
    |ExtensionService |Služba rozšíření konfigurace hosta | gc_service |Nainstaluje požadovaná rozšíření, která cílí na daný počítač.|

* Během instalace agenta se vytvoří následující proměnné prostředí.

    |Název |Výchozí hodnota |Popis |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Pro řešení potíží je k dispozici několik souborů protokolu. Jsou popsány v následující tabulce.

    |Protokol |Popis |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Zaznamenává údaje služby agenti (HIMDS) a interakce s Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Obsahuje výstup příkazů nástroje azcmagent, pokud je použit argument verbose (-v).|
    |%ProgramData%\GuestConfig\ gc_agent_logs \ gc_agent. log |Zaznamenává údaje o aktivitě služby DSC.<br> zejména připojení mezi službou HIMDS a Azure Policy.|
    |% Složka ProgramData% \GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |Zaznamenává údaje týkající se telemetrie služby DSC a podrobného protokolování.|
    |%ProgramData%\GuestConfig\ ext_mgr_logs|Zaznamenává údaje o komponentě agenta rozšíření.|
    |%ProgramData%\GuestConfig\ extension_logs\<Extension>|Zaznamenává údaje z nainstalovaného rozšíření.|

* Vytvoří se **aplikace rozšíření hybridního agenta** pro místní skupinu zabezpečení.

* Během odinstalace agenta nejsou odebrány následující artefakty.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent a podadresáře
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Podrobnosti o instalaci agenta pro Linux

Agent připojeného počítače pro Linux je k dispozici v preferovaném formátu balíčku pro distribuci (. Ot./min. nebo. DEB), která je hostovaná v [úložišti balíčků](https://packages.microsoft.com/)Microsoftu. Agent se instaluje a konfiguruje pomocí sady skriptů prostředí [Install_linux_azcmagent. sh](https://aka.ms/azcmagent).

Po instalaci agenta připojeného počítače pro Linux se aplikují následující změny konfigurace v rámci systému.

* Během instalace se vytvoří následující instalační složky.

    |Složka |Popis |
    |-------|------------|
    |/var/opt/azcmagent/ |Výchozí instalační cesta obsahující soubory podpory agenta.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | Instalační cesta obsahující soubory agenta konfigurace hosta|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Obsahuje získané tokeny.|
    |/var/lib/GuestConfig |Obsahuje zásady (použité) z Azure.|
    |/opt/GC_Ext/downloads|Rozšíření se stáhnou z Azure a zkopírují se sem.|

* Následující procesy démon jsou vytvořeny v cílovém počítači během instalace agenta.

    |Název služby |Zobrazované jméno |Název procesu |Popis |
    |-------------|-------------|-------------|------------|
    |himdsd. Service |Služba agenta připojeného počítače Azure |himds |Tato služba implementuje službu metadat Azure instance (IMDS) pro správu připojení k Azure a identitě Azure připojeného počítače.|
    |gcad.servce |Služba oblouku GC |gc_linux_service |Monitoruje konfiguraci požadovaného stavu počítače. |
    |ExtD. Service |Služba rozšíření |gc_linux_service | Nainstaluje požadovaná rozšíření, která cílí na daný počítač.|

* Pro řešení potíží je k dispozici několik souborů protokolu. Jsou popsány v následující tabulce.

    |Protokol |Popis |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Zaznamenává údaje služby agenti (HIMDS) a interakce s Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Obsahuje výstup příkazů nástroje azcmagent, pokud je použit argument verbose (-v).|
    |/opt/logs/dsc.log |Zaznamenává údaje o aktivitě služby DSC.<br> zejména připojení mezi službou himds a Azure Policy.|
    |/opt/logs/dsc.telemetry.txt |Zaznamenává údaje týkající se telemetrie služby DSC a podrobného protokolování.|
    |/var/lib/GuestConfig/ext_mgr_logs |Zaznamenává údaje o komponentě agenta rozšíření.|
    |/var/lib/GuestConfig/extension_logs|Zaznamenává údaje z nainstalovaného rozšíření.|

* Během instalace agenta se vytvoří následující proměnné prostředí. Tyto proměnné jsou nastaveny v `/lib/systemd/system.conf.d/azcmagent.conf` .

    |Název |Výchozí hodnota |Popis |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Během odinstalace agenta nejsou odebrány následující artefakty.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít vyhodnocovat servery s podporou ARC Azure, postupujte podle článku [připojení Hybrid Machines k Azure z Azure Portal](onboard-portal.md).

* Informace o řešení potíží najdete v části [Poradce při potížích s agentem připojeného počítače](troubleshoot-agent-onboard.md).
