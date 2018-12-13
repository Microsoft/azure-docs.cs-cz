---
title: Problémy konfigurace a správa pro Microsoft Azure Cloud Services – nejčastější dotazy | Dokumentace Microsoftu
description: Tento článek obsahuje nejčastější dotazy týkající se konfigurace a správa pro Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: fe096efba5419b283981f27e16c3e30a791412da
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322666"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Konfigurace a správy problémů pro Azure Cloud Services: Nejčastější dotazy (FAQ)

Tento článek obsahuje nejčastější dotazy o konfiguraci a správě problémů pro [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Můžete také najdete [cloudové služby virtuálních počítačů velikosti stránky](cloud-services-sizes-specs.md) pro informace o velikosti.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certifikáty**

- [Proč není úplný řetěz certifikátů certifikát SSL cloudové služby?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [K čemu je "Windows Azure Tools šifrovací certifikát pro rozšíření"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Jak můžu vygenerovat certifikát Podepisování žádostí (CSR) bez "RDP-ing" v instanci?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Moje cloudové služby správy certifikátu vyprší platnost. Jak ho obnovit?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Jak automatizovat instalaci hlavní certificate(.pfx) SSL a zprostředkujících certificate(.p7b)?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- [Jaký je účel certifikátu "Microsoft Azure Service Management pro MachineKey"?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitorování a protokolování**

- [Jaké jsou možnosti nadcházející cloudové služby na webu Azure Portal, což může pomoci spravovat a monitorovat aplikace?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Proč služba IIS zastavili zápis na adresář protokolu?](#why-does-iis-stop-writing-to-the-log-directory)
- [Jak povolit protokolování WAD pro cloudové služby?](#how-do-i-enable-wad-logging-for-cloud-services)

**Konfigurace sítě**

- [Nastavení časového limitu nečinnosti u nástroje pro vyrovnávání zatížení Azure](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Jak přiřadit statickou IP adresu do svojí cloudové služby?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Jaké jsou funkce a možnosti, které poskytuje Azure základní IP adresy/ID a útoky DDOS?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Postup povolení protokolu HTTP/2 na virtuálním počítači cloudové služby?](#how-to-enable-http2-on-cloud-services-vm)

**Oprávnění**

- [Můžete vzdálené plochy interní techniky Microsoftu do instance cloudové služby bez oprávnění?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Můžu nelze vzdálené plochy k virtuálnímu počítači cloudové služby s použitím souboru RDP. Můžu získat následující chybě: Došlo k chybě ověřování (kód: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Škálování**

- [Můžu nelze škálovat na více než X instancí](#i-cannot-scale-beyond-x-instances)
- [Jak lze nakonfigurovat automatické škálování na základě metrik paměti?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Obecné**

- [Jak mohu přidat "nosniff" Můj web?](#how-do-i-add-nosniff-to-my-website)
- [Jak mohu přizpůsobit služby IIS pro webovou roli?](#how-do-i-customize-iis-for-a-web-role)
- [Co je kvótu pro moje cloudové služby?](#what-is-the-quota-limit-for-my-cloud-service)
- [Proč na jednotce na virtuální počítač cloudové služby zobrazit velmi málo volného místa na disku?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Jak mohu přidat Antimalwarové rozšíření pro moje cloudové služby v automatizovaný způsob, jak?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Jak povolit indikace názvu serveru (SNI) pro cloudové služby?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Jak mohu přidat značky na svojí cloudové služby Azure?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Na webu Azure portal nezobrazuje verze sady SDK svojí cloudové služby. Jakým způsobem, který získám?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Chci vypnout Cloudovou službu po dobu několika měsíců. Jak dosáhnout snížení fakturační náklady na cloudové služby, aniž by ztratily IP adresu?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certifikáty

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Proč není úplný řetěz certifikátů certifikát SSL cloudové služby?
    
Doporučujeme zákazníkům instalovat řetěz certifikátů úplné (listový certifikát zprostředkující certifikáty a kořenový certifikát) namísto pouze listový certifikát. Když instalujete pouze listový certifikát, využívají Windows se sestavit řetěz certifikátů procházením seznamu CTL. Pokud sítě nebo problémů s DNS se vyskytují v Azure nebo Windows Update při Windows k ověření certifikátu, certifikát může být považovány za neplatné. Řetěz certifikátů úplnou instalací, se můžete vyhnout tomuto problému. Blog na [postup instalace zřetězeného certifikátu SSL](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) ukazuje, jak to provést.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>K čemu je "Windows Azure Tools šifrovací certifikát pro rozšíření"?

Tyto certifikáty se automaticky vytvoří při každém přidání rozšíření do cloudové služby. Nejčastěji jde WAD rozšíření nebo rozšíření RDP, ale je možné, jiné, jako je například rozšíření Antimalware nebo Kolektoru protokolů. Tyto certifikáty slouží pouze k šifrování a dešifrování privátní konfigurace rozšíření. Datum vypršení platnosti je nikdy vráceny, takže nebude vadit, když platnost certifikátu vypršela. 

Tyto certifikáty, můžete ignorovat. Pokud chcete vyčistit certifikáty, můžete zkusit odstranit všechny. Azure vyvolá chybu, pokud se pokusíte odstranit certifikát, který se používá.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Jak můžu vygenerovat certifikát Podepisování žádostí (CSR) bez "RDP-ing" v instanci?

Najdete v následujících pokynech:

[Získání certifikátu pro použití s Windows Azure Web Sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Žádost o podepsání certifikátu je textový soubor. Nemá vytvářeného z počítače, kde bude certifikát nakonec použít. I když tento dokument je určené pro služby App Service, vytvoření žádosti o podepsání certifikátu je obecný a platí také pro cloudové služby.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Moje cloudové služby správy certifikátu vyprší platnost. Jak ho obnovit?

Následující příkazy prostředí PowerShell můžete použít k obnovení vašeho certifikáty pro správu:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

**Get-AzurePublishSettingsFile** vytvoří nový certifikát pro správu v **předplatné** > **certifikáty pro správu** na webu Azure Portal. Název nového certifikátu vypadá jako "YourSubscriptionNam]-[CurrentDate] - přihlašovací údaje".

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Jak automatizovat instalaci hlavní certificate(.pfx) SSL a zprostředkujících certificate(.p7b)?

Můžete tento úkol automatizovat pomocí skriptu při spuštění (batch/cmd/PowerShell) a zaregistrujte tento spouštěcí skript v definičním souboru služby. Přidáte spouštěcí skript i certifikát (soubor P7B) ve složce projektu stejného adresáře spouštěcího skriptu.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Jaký je účel certifikátu "Microsoft Azure Service Management pro MachineKey"?

Tento certifikát se používá k šifrování klíčů počítačů ve webových rolích Azure. Další informace, podívejte se na tento informační zpravodaj [https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731].

Další informace najdete v následujících článcích:
- [Jak nakonfigurovat a spustit úlohy po spuštění pro Cloudovou službu](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Běžné úlohy po spuštění cloudové služby](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitorování a protokolování

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Jaké jsou možnosti nadcházející cloudové služby na webu Azure Portal, což může pomoci spravovat a monitorovat aplikace?

Možnost Generovat nový certifikát pro protokol RDP (Remote Desktop) se připravuje. Alternativně můžete spustit tento skript:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Možnost výběru objektu blob nebo místní pro csdef a cscfg nahrát umístění je již brzy. Pomocí [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0), můžete nastavit hodnotu každého umístění.

Možnost monitorovat metriky na úrovni instance. Další možnosti monitorování jsou k dispozici v [postupy monitorování Cloud Services](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Proč služba IIS zastavili zápis na adresář protokolu?
Vyčerpání místního úložiště kvóty pro zápis k adresáři protokolů. To pokud chcete opravit, proveďte jednu z následujících způsobů:
* Povolit diagnostiku pro službu IIS a Diagnostika pravidelně přesunuli do úložiště objektů blob.
* Soubory protokolu ručně odeberte adresář protokolování.
* Zvyšte omezení kvóty pro místní prostředky.

Další informace najdete v tématu v následujících dokumentech:
* [Ukládání a zobrazení diagnostických dat v Azure Storage](cloud-services-dotnet-diagnostics-storage.md)
* [Protokoly služby IIS zastavení zápisu v cloudové službě](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Jak povolit protokolování WAD pro cloudové služby?
Můžete povolit protokolování Windows Azure Diagnostics (WAD), pomocí následujících možností:
1. [Povolit ze sady Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Povolit prostřednictvím kódu rozhraní .net](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Povolit pomocí Powershellu](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Pokud chcete získat aktuální nastavení WAD cloudové služby, můžete použít [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd, nebo můžete zobrazit přes portál v okně "Cloudové služby--> rozšíření".


## <a name="network-configuration"></a>Konfigurace sítě

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Nastavení časového limitu nečinnosti u nástroje pro vyrovnávání zatížení Azure
Můžete zadat časový limit v souboru definice (csdef) služby následujícím způsobem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Zobrazit [nové: Konfigurovatelné nečinnosti časový limit pro Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) Další informace.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Jak přiřadit statickou IP adresu do svojí cloudové služby?
Pokud chcete nastavit statickou IP adresu, je potřeba vytvořit vyhrazenou IP adresu. Tato vyhrazená IP adresa může být přidružené k nové cloudové službě nebo k existujícímu nasazení. Najdete v následujících dokumentech podrobnosti:
* [Jak vytvořit vyhrazenou IP adresu](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Vyhrazení IP adresy existující cloudové služby](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Přidružení vyhrazené IP adresy k nové cloudové služby](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Přidružení vyhrazené IP adresy na běžícím nasazení](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Přidružení vyhrazené IP adresy pro cloudové služby pomocí konfiguračního souboru služby](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Jaké jsou funkce a možnosti, které poskytuje Azure základní IP adresy/ID a útoky DDOS?
Azure obsahuje IP adresy/ID ve fyzických serverů datového centra se chránit proti hrozbám. Kromě toho zákazníci mohou nasadit řešení zabezpečení třetích stran, jako jsou brány firewall webových aplikací, síťové brány firewall, antimalwaru, zjišťování neoprávněných vniknutí, systémů ochrany před únikem informací (IDS/IPS) a další. Další informace najdete v tématu [chránit vaše data a prostředky a v souladu se standardy zabezpečení globální](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft neustále monitoruje servery, sítě a aplikace ke zjištění hrozeb. Azure multipronged řízení rizik přístupu používá vniknutí, distribuované s cílem odepření služeb (DDoS) zabránění útoku, průniku testování behaviorální analýzy, detekce anomálií a strojového učení neustále posílit její ochrany před mobilními a snížení rizik. Microsoft Antimalware pro Azure chrání Azure Cloud Services a virtual machines. Máte možnost nasadit řešení zabezpečení jiných výrobců navíc jako stěn fire webové aplikace, síťové brány firewall, antimalwaru, vniknutí detekce a ochrany před únikem informací systémy (IDS/IPS) a další.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Postup povolení protokolu HTTP/2 na virtuálním počítači cloudové služby?

Windows 10 a Windows serveru 2016 jsou dostupné podpora HTTP/2 na straně klienta i serveru. Pokud vašeho klienta (prohlížeč) se připojuje k serveru služby IIS přes protokol TLS, který vyjednává HTTP/2 prostřednictvím rozšíření protokolu TLS, pak není potřeba provádět změny na straně serveru. Důvodem je, že přes protokol TLS, je ve výchozím nastavení odesílají hlavičku h2-14 určující používání protokolu HTTP/2. Pokud na druhé straně váš klient zasílá hlavičku upgradu pro upgrade na HTTP/2, budete muset provést změnu níže na straně serveru k zajištění, že Upgrade funguje a můžete skončit s připojení HTTP/2. 

1. Spusťte regedit.exe.
2. Přejděte ke klíči registru: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Vytvořte novou hodnotu DWORD s názvem **DuoEnabled**.
4. Nastavte jej na hodnotu 1.
5. Restartujte server.
6. Přejděte k vaší **výchozí webový server** a v části **vazby**, vytvořte novou vazbu TLS pomocí certifikátu podepsaného svým držitelem, který je právě vytvořili. 

Další informace naleznete v tématu:

- [HTTP/2 ve službě IIS](https://blogs.iis.net/davidso/http2)
- [Video: HTTP/2 ve Windows 10: Prohlížeč, aplikace a webového serveru](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Tyto kroky může prostřednictvím úkolu po spuštění, automatizované, tak, aby pokaždé, když se vytvoří novou instanci PaaS, můžete provést změny výše v systémovém registru. Další informace najdete v tématu [jak nakonfigurovat a spustit úlohy po spuštění pro Cloudovou službu](cloud-services-startup-tasks.md).

 
Jakmile se to stalo, můžete ověřit, zda je povolen protokol HTTP/2 nebo není pomocí jedné z následujících metod:

- Povolení verze protokolu v protokolech služby IIS a podívejte se do protokolů IIS. HTTP/2 se zobrazí v protokolech. 
- Povolit nástroje pro vývojáře F12 v Internet Explorer nebo Edge a přepněte na kartu síť ověření protokolu. 

Další informace najdete v tématu [HTTP/2 ve službě IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Oprávnění

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Jak můžete implementovat přístup na základě rolí pro Cloud Services?
Cloud Services nepodporuje modelu řízení přístupu na základě role, protože není služby založené na Azure Resource Manageru.

Zobrazit [pochopit různé role v Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Vzdálená plocha

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Můžete vzdálené plochy interní techniky Microsoftu do instance cloudové služby bez oprávnění?
Microsoft postupuje od vlastníka nebo jejich zmocněnce striktní proces, který nedovolí interní techniky vzdálené plochy do cloudové služby bez předchozího písemného povolení (e-mailu nebo jiné písemné komunikace).

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Můžu nelze vzdálené plochy k virtuálnímu počítači cloudové služby s použitím souboru RDP. Můžu získat následující chybě: Došlo k chybě ověřování (kód: 0x80004005)

K této chybě může dojít, pokud používáte soubor protokolu RDP z počítače, který je připojený ke službě Azure Active Directory. Chcete-li vyřešit tento problém, postupujte podle těchto kroků:

1. Klikněte pravým tlačítkem na stažený soubor RDP a pak vyberte **upravit**.
2. Přidat "&#92;" jako předponu před uživatelské jméno. Například použít **. \username** místo **uživatelské jméno**.

## <a name="scaling"></a>Škálování

### <a name="i-cannot-scale-beyond-x-instances"></a>Můžu nelze škálovat na více než X instancí
Vaše předplatné Azure má omezený počet jader, které můžete použít. Škálování nebude fungovat, pokud jste už použili všechna dostupná jádra. Například pokud budete mít maximálně 100 jádrům, to znamená, že jste mohli 100 A1 velikostí instancí virtuálních počítačů pro Cloudovou službu nebo 50 A2 velikostí instancí virtuálních počítačů.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Jak lze nakonfigurovat automatické škálování na základě metrik paměti?

Automatické škálování podle metriky paměti pro Cloud Services se aktuálně nepodporuje. 

Chcete-li tento problém vyřešit, můžete použít Application Insights. Podporuje Application Insights jako zdroj metriky pro automatické škálování a můžete škálovat počet instancí role na základě hosta metriky, jako je "Paměti".  Budete muset nakonfigurovat Application Insights v balíčku souboru projektu cloudové služby (*.cspkg) a povolit rozšíření diagnostiky Azure na služby k implementaci této feat.

Další informace o tom, jak využívat vlastní metriky pomocí Application Insights a nakonfigurovat automatické škálování na Cloud Services, najdete v článku [zahájení práce se automatické škálování díky vlastní metrika v Azure](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)

Další informace o tom, jak integrovat Azure Diagnostics Application Insights pro služby Cloud Services, najdete v části [odeslat cloudové služby, virtuální počítač nebo Service Fabric diagnostická data do Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Další informace o povolení Application Insights pro služby Cloud Services najdete v tématu [Application Insights pro Azure Cloud Services](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Další informace o tom, jak povolit protokolování diagnostiky Azure pro cloudové služby, najdete v části [nastavení diagnostiky pro Azure Cloud Services a virtual machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Obecné

### <a name="how-do-i-add-nosniff-to-my-website"></a>Jak mohu přidat "nosniff" Můj web?
Pokud chcete pro analýzu sítě typy MIME klientům zabránit, přidejte nastavení ve vašich *web.config* souboru.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Můžete také přidat toto nastavení ve službě IIS. Použijte následující příkaz se [běžné úlohy po spuštění](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) článku.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Jak mohu přizpůsobit služby IIS pro webovou roli?
Použijte spouštěcí skript ze služby IIS [běžné úlohy po spuštění](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) článku.

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Co je kvótu pro moje cloudové služby?
Zobrazit [specifickou pro službu omezuje](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Proč na jednotce na virtuální počítač cloudové služby zobrazit velmi málo volného místa na disku?
Toto chování je očekávané, a to by nemělo způsobit jakýkoli problém do vaší aplikace. Pro jednotku % approot % ve virtuálních počítačích PaaS Azure, která v podstatě využívá double množství místa, které soubory obvykle zabírají zapnutý záznamu do deníku. Existují ale potřeba mít na paměti, která v podstatě několik věcí udělejte to není problém.

Velikost disku % approot % se vypočte takto: < velikost .cspkg + velikost deníku maximální > + okraj volného místa nebo 1,5 GB, která je větší. Velikost virtuálního počítače nemá žádný vliv na tento výpočet. (Velikost virtuálního počítače pouze ovlivňuje velikost dočasné jednotce C:.) 

Zápis na disk % approot % není podporováno. Pokud vytváříte virtuální počítač Azure, musíte to udělat v dočasné LocalStorage prostředku (nebo jiné možnosti, jako je například úložiště objektů Blob, soubory Azure, atd.). Proto není množství volného místa ve složce % approot % smysluplné. Pokud si nejste jistí, jestli vaše aplikace zapisuje do jednotku % approot %, můžete nechat vždy služby spustit několik dnů a porovnejte "před" a "po" velikosti. 

Azure nezapíše nic k jednotce % approot %. Jakmile virtuální pevný disk je vytvořena z vaší .cspkg a připojený k virtuálnímu počítači Azure, je to jediné, co, který může zapisovat na tuto jednotku vaše aplikace. 

Nastavení deníku jsou nekonfigurovatelnou, proto nejde ji vypnout.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Jak mohu přidat Antimalwarové rozšíření pro moje cloudové služby v automatizovaný způsob, jak?

Můžete povolit pomocí skriptu prostředí PowerShell v úloze po spuštění Antimalwarové rozšíření. Postupujte podle kroků v těchto článcích na jeho implementaci: 
 
- [Vytvoření úlohy po spuštění Powershellu](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Další informace o scénářích nasazení antimalwarových a jak ho povolit z portálu najdete v tématu [scénáře nasazení antimalwarových](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Jak povolit indikace názvu serveru (SNI) pro cloudové služby?

SNI v cloudových službách můžete povolit pomocí jedné z následujících metod:

**Metoda 1: Pomocí Powershellu**

Vazba SNI lze nastavit pomocí rutiny Powershellu **New-WebBinding** v úloze po spuštění pro instanci role cloudové služby, jak je uvedeno níže:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Jak je popsáno [tady](https://technet.microsoft.com/library/ee790567.aspx), $sslFlags může být jedna z hodnot následujícím způsobem:

|Hodnota|Význam|
------|------
|0|Žádné SNI|
|1|SNI povoleno |
|2 |Bez SNI vazby, který používá certifikát Store – střed|
|3|Ukládání SNI vazby, který používá centrálního úložiště certifikátů |
 
**Metoda 2: Použít kód**

SNI vazbu může také konfigurovat pomocí kódu ve spuštění role jak je popsáno v tomto [blogový příspěvek](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Jedním z přístupů výše, příslušné certifikátů (*.pfx) pro konkrétní názvy hostitelů nutné nejprve nainstalovat v instancích role pomocí úlohy po spuštění nebo prostřednictvím kódu v pořadí, vazba SNI platit.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Jak mohu přidat značky na svojí cloudové služby Azure? 

Cloudová služba je klasický prostředek. Pouze prostředkům vytvořeným prostřednictvím podpory značky Azure Resource Manageru. Značky nelze použít prostředky Classic, jako jsou cloudové služby. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Na webu Azure portal nezobrazuje verze sady SDK svojí cloudové služby. Jakým způsobem, který získám?

Pracujeme na tom, tato funkce na webu Azure portal. Mezitím můžete následující příkazy prostředí PowerShell se získat verzi sady SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Chci vypnout Cloudovou službu po dobu několika měsíců. Jak dosáhnout snížení fakturační náklady na cloudové služby, aniž by ztratily IP adresu?

Již nasazené cloudové služby se fakturují za výpočetní prostředky a úložiště používá. Takže i v případě, že vypnout virtuální počítač Azure vám bude stále účtovat úložiště. 

Zde je, co můžete dělat ke snížení fakturace bez ztráty IP adresu pro vaši službu:

1. [Vyhrazení IP adresy](../virtual-network/virtual-networks-reserved-public-ip.md) před odstraněním nasazení.  Vám bude účtováno pouze pro tuto IP adresu. Další informace o fakturaci IP adres najdete v části [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Odstraňte nasazení. Neodstraňovat xxx.cloudapp.net, takže ho můžete použít pro budoucnost.
3. Pokud chcete znovu nasadit Cloudovou službu s použitím stejnou IP Adresou rezervy, který je vyhrazen v rámci vašeho předplatného naleznete [rezervované IP adresy pro Cloud Services a Virtual Machines](https://azure.microsoft.com/blog/reserved-ip-addresses/).

