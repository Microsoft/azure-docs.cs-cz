---
title: Nejčastější dotazy týkající se konfigurace a správy
description: V tomto článku jsou uvedeny nejčastější dotazy týkající se konfigurace a správy pro Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 31659f4e8e4f9e25a997be54223b8856edfa8abe
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612979"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problémy s konfigurací a správou pro Azure Cloud Services (Classic): nejčastější dotazy

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Tento článek obsahuje nejčastější dotazy týkající se problémů s konfigurací a správou pro [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informace o velikosti najdete také na [stránce Cloud Services velikosti virtuálního počítače](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certifikáty**

- [Proč je neúplný řetěz certifikátů pro certifikát TLS/SSL cloudové služby?](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [Jaký je účel "certifikát šifrování nástrojů Windows Azure pro rozšíření"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Jak můžu vygenerovat žádost o podepsání certifikátu (CSR) bez "RDP-in" do instance?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Platnost certifikátu pro správu cloudové služby brzy vyprší. Jak ji obnovit?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Jak automatizovat instalaci hlavního certifikátu TLS/SSL (. pfx) a zprostředkujícího certifikátu (. P7B)?](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [Jaký je účel certifikátu "Microsoft Azure Service Management for MachineKey"?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitorování a protokolování**

- [Jaké jsou nadcházející možnosti cloudové služby ve Azure Portal, které vám pomůžou se správou a monitorováním aplikací?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Proč služba IIS zastaví zápis do adresáře protokolů?](#why-does-iis-stop-writing-to-the-log-directory)
- [Návody povolit protokolování WAD pro Cloud Services?](#how-do-i-enable-wad-logging-for-cloud-services)

**Konfigurace sítě**

- [Návody nastavit časový limit nečinnosti pro Azure Load Balancer?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Návody přidružit ke cloudové službě statickou IP adresu?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Jaké jsou funkce a možnosti, které Azure Basic IP/IDS a DDOS poskytuje?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Jak povolit HTTP/2 na Cloud Servicesm virtuálním počítači?](#how-to-enable-http2-on-cloud-services-vm)

**Oprávnění**

- [Můžou interní technici z Microsoftu do cloudových instancí služby Microsoft Desktop bez oprávnění?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Nejde vzdáleně připojit k VIRTUÁLNÍmu počítači cloudové služby pomocí souboru RDP. Zobrazí se následující chyba: došlo k chybě ověřování (kód: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Škálování**

- [Nejde škálovat na více než X instancí](#i-cannot-scale-beyond-x-instances)
- [Jak můžu nakonfigurovat automatické škálování na základě metrik paměti?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Obecné**

- [Návody přidat `nosniff` na můj web?](#how-do-i-add-nosniff-to-my-website)
- [Návody přizpůsobení služby IIS pro webovou roli?](#how-do-i-customize-iis-for-a-web-role)
- [Jaká je maximální kvóta pro cloudovou službu?](#what-is-the-quota-limit-for-my-cloud-service)
- [Proč jednotka na virtuálním počítači cloudové služby zobrazuje hodně volného místa na disku?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Jak můžu automatizovaným způsobem přidat antimalwarové rozšíření pro moji Cloud Services?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Jak povolit Indikace názvu serveru (SNI) pro Cloud Services?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Jak můžu přidat značky do cloudové služby Azure?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Azure Portal nezobrazuje verzi sady SDK cloudové služby. Jak to mohu získat?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Chci po několik měsíců vypnout cloudovou službu. Jak snížit náklady na vyúčtování cloudové služby bez ztráty IP adresy?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certifikáty

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>Proč je neúplný řetěz certifikátů pro certifikát TLS/SSL cloudové služby?
    
Doporučujeme, aby si zákazníci nainstalují úplný řetěz certifikátů (list Certificate, zprostředkující certifikáty a kořenový certifikát) místo jenom na listový certifikát. Když instalujete jenom listový certifikát, spoléháte se na Windows, aby se vytvořil řetěz certifikátů, a to procházením seznamu CTL. Pokud se v Azure nebo při pokusu o ověření certifikátu vyskytnou občasné problémy se sítí nebo službou DNS nebo web Windows Update, může se certifikát považovat za neplatný. Instalací úplného řetězu certifikátů se můžete vyhnout tomuto problému. To, jak to udělat, najdete v blogu [Postup instalace zřetězeného certifikátu SSL](/archive/blogs/azuredevsupport/how-to-install-a-chained-ssl-certificate) .

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Jaký je účel "certifikát šifrování nástrojů Windows Azure pro rozšíření"?

Tyto certifikáty se automaticky vytvoří pokaždé, když se do cloudové služby přidá rozšíření. Nejčastěji se jedná o rozšíření WAD nebo rozšíření RDP, ale může to být jiné, jako je třeba rozšíření antimalwaru nebo kolektoru protokolů. Tyto certifikáty se používají pouze k šifrování a dešifrování privátní konfigurace pro rozšíření. Datum vypršení platnosti není nikdy zaškrtnuto, takže nezáleží na tom, jestli vypršela platnost certifikátu. 

Tyto certifikáty můžete ignorovat. Pokud chcete certifikáty vyčistit, můžete je zkusit odstranit. Pokud se pokusíte odstranit certifikát, který se používá, Azure vyvolá chybu.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Jak můžu vygenerovat žádost o podepsání certifikátu (CSR) bez "RDP-in" do instance?

Podívejte se na následující dokument s pokyny:

[Získání certifikátu pro použití s weby Windows Azure (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

CSR je jenom textový soubor. Nemusí být vytvořen z počítače, ve kterém bude certifikát nakonec použit.I když je tento dokument napsán pro App Service, je vytvoření CSR obecné a platí také pro Cloud Services.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Platnost certifikátu pro správu cloudové služby brzy vyprší. Jak ji obnovit?

K obnovení certifikátů pro správu můžete použít následující příkazy PowerShellu:

```powershell
Add-AzureAccount
Select-AzureSubscription -Current -SubscriptionName <your subscription name>
Get-AzurePublishSettingsFile
```

**Příkaz Get-AzurePublishSettingsFile** vytvoří nový certifikát pro správu v certifikátech správy **předplatných**  >   v Azure Portal. Název nového certifikátu vypadá jako "YourSubscriptionNam]-[CurrentDate]-přihlašovací údaje".

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>Jak automatizovat instalaci hlavního certifikátu TLS/SSL (. pfx) a zprostředkujícího certifikátu (. P7B)?

Tuto úlohu můžete automatizovat pomocí spouštěcího skriptu (Batch/cmd/PowerShell) a zaregistrovat spouštěcí skript v definičním souboru služby. Do složky projekt ve stejném adresáři spouštěcího skriptu přidejte spouštěcí skript i certifikát (soubor. P7B).

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Jaký je účel certifikátu "Microsoft Azure Service Management for MachineKey"?

Tento certifikát se používá k šifrování klíčů počítačů ve webových rolích Azure. Pokud se chcete dozvědět víc, podívejte se na [Tento informační zpravodaj](/security-updates/securityadvisories/2018/4092731).

Další informace najdete v následujících článcích:
- [Jak nakonfigurovat a spustit úlohy po spuštění pro cloudovou službu](./cloud-services-startup-tasks.md)
- [Běžné úlohy po spuštění cloudové služby](./cloud-services-startup-tasks-common.md)

## <a name="monitoring-and-logging"></a>Monitorování a protokolování

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Jaké jsou nadcházející možnosti cloudové služby ve Azure Portal, které vám pomůžou se správou a monitorováním aplikací?

Už brzy bude možné vygenerovat nový certifikát pro protokol RDP (Remote Desktop Protocol) (RDP). Případně můžete spustit tento skript:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
K dispozici je možnost zvolit si objekt BLOB nebo místní pro umístění odeslaných souborů csdef a cscfg. Pomocí [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment)můžete nastavit každou hodnotu umístění.

Možnost monitorovat metriky na úrovni instance. Další možnosti monitorování jsou k dispozici v tématu [jak monitorovat Cloud Services](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Proč služba IIS zastaví zápis do adresáře protokolů?
Vyčerpali jste kvótu místního úložiště pro zápis do adresáře protokolů.Pokud to chcete opravit, můžete udělat jednu ze tří věcí:
* Povolte diagnostiku služby IIS a pravidelně se přesouvají do úložiště objektů BLOB.
* Soubory protokolu odstraňte ručně z adresáře protokolování.
* Zvyšte limit kvóty pro místní prostředky.

Další informace najdete v následujících dokumentech:
* [Ukládání a zobrazení diagnostických dat v Azure Storage](../storage/common/storage-introduction.md)
* [Protokoly IIS zastaví zápis v cloudové službě.](/archive/blogs/cie/iis-logs-stops-writing-in-cloud-service)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Návody povolit protokolování WAD pro Cloud Services?
Můžete povolit protokolování Windows Azure Diagnostics (WAD) pomocí následujících možností:
1. [Povolit ze sady Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Povolit prostřednictvím kódu .NET](./cloud-services-dotnet-diagnostics.md)
3. [Povolit prostřednictvím PowerShellu](./cloud-services-diagnostics-powershell.md)

Chcete-li získat aktuální nastavení WAD vaší cloudové služby, můžete použít příkaz [Get-AzureServiceDiagnosticsExtensions](./cloud-services-diagnostics-powershell.md#get-current-diagnostics-extension-configuration) PowerShell cmd nebo ho můžete zobrazit prostřednictvím portálu z okna rozšíření Cloud Services-->.


## <a name="network-configuration"></a>Konfigurace sítě

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Návody nastavit časový limit nečinnosti pro Azure Load Balancer?
Můžete zadat časový limit v souboru definice služby (csdef) takto:

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
Další informace najdete v tématu [New: konfigurovatelný časový limit nečinnosti pro Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) .

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Návody přidružit ke cloudové službě statickou IP adresu?
Chcete-li nastavit statickou IP adresu, je nutné vytvořit vyhrazenou IP adresu. Tato vyhrazená IP adresa může být přidružená k nové cloudové službě nebo ke stávajícímu nasazení. Podrobnosti najdete v následujících dokumentech:
* [Postup vytvoření rezervované IP adresy](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Vyhrazení IP adresy existující cloudové služby](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Přidružení rezervované IP adresy k nové cloudové službě](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Přidružení rezervované IP adresy k běžícímu nasazení](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Přidružení rezervované IP adresy ke cloudové službě pomocí konfiguračního souboru služby](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Jaké jsou funkce a možnosti, které Azure Basic IP/IDS a DDOS poskytuje?
Azure má na fyzických serverech Datacenter IP/ID, aby se mohly chránit před hrozbami. Kromě toho můžou zákazníci nasazovat řešení zabezpečení třetích stran, například brány firewall webových aplikací, síťové brány firewall, antimalwarové funkce, zjišťování neoprávněných vniknutí, systémy prevence (ID/IP adresy) a další. Další informace najdete v tématech [Ochrana vašich dat a prostředků a dodržování globálních standardů zabezpečení](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft nepřetržitě monitoruje servery, sítě a aplikace a detekuje hrozby. Přístup pro správu s více kombinovanými hrozbami Azure používá zjišťování neoprávněných vniknutí (DDoS) prevence útoků proti útokům, testování průniku, analýzu chování, detekci anomálií a strojové učení za účelem neustálého zvyšování obrany a snížení rizik. Microsoft Antimalware pro Azure chrání Azure Cloud Services a virtuální počítače. Máte možnost kromě toho nasadit řešení zabezpečení od jiných výrobců, například brány fire wall webové aplikace, brány firewall sítě, antimalwarové, odhalování vniknutí a systémy prevence (ID/IP adresy) a další.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Jak povolit HTTP/2 na Cloud Servicesm virtuálním počítači?

Systémy Windows 10 a Windows Server 2016 obsahují podporu protokolu HTTP/2 na straně klienta i serveru. Pokud se klient (prohlížeč) připojuje k serveru IIS přes TLS, který vyjednává HTTP/2 přes rozšíření TLS, nemusíte provádět žádnou změnu na straně serveru. Důvodem je, že přes TLS se ve výchozím nastavení pošle hlavička H2-14 určující použití HTTP/2. Pokud na druhé straně váš klient posílá hlavičku upgradu pro upgrade na HTTP/2, pak je potřeba provést změnu níže na straně serveru, aby se zajistilo, že upgrade funguje a že skončíte s připojením HTTP/2. 

1. Spusťte regedit.exe.
2. Přejděte do klíče registru: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Vytvořte novou hodnotu DWORD s názvem **DuoEnabled**.
4. Nastavte jeho hodnotu na 1.
5. Restartujte server.
6. Ve svém **výchozím** webu a v části **Bindings (vazby**) vytvořte novou vazbu TLS s právě vytvořeným certifikátem podepsaným svým držitelem. 

Další informace naleznete v tématu:

- [HTTP/2 ve službě IIS](https://blogs.iis.net/davidso/http2)
- [Video: HTTP/2 ve Windows 10: prohlížeč, aplikace a webový server](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Tyto kroky může být automatizované prostřednictvím úlohy po spuštění, takže když se vytvoří nová instance PaaS, může to provést výše uvedené změny v registru systému. Další informace najdete v tématu [Konfigurace a spouštění úloh po spuštění pro cloudovou službu](cloud-services-startup-tasks.md).

 
Až to uděláte, můžete ověřit, jestli je protokol HTTP/2 povolený, nebo ne, a to pomocí jedné z následujících metod:

- Povolte verzi protokolu v protokolech služby IIS a Prohlédněte si protokoly služby IIS. V protokolech se zobrazí protokol HTTP/2. 
- Povolte vývojářský nástroj F12 v aplikaci Internet Explorer nebo Microsoft Edge a přepněte na kartu síť a ověřte protokol. 

Další informace najdete v tématu [http/2 ve službě IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Oprávnění

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Jak mohu implementovat přístup na základě rolí pro Cloud Services?
Cloud Services nepodporuje model řízení přístupu na základě role Azure (Azure RBAC), protože to není služba Azure Resource Manager založená na službě.

Přečtěte si téma [porozumění různým rolím v Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Vzdálená plocha

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Můžou interní technici z Microsoftu do cloudových instancí služby Microsoft Desktop bez oprávnění?
Společnost Microsoft se skládá z striktního procesu, který neumožní interním technikům na vzdálenou plochu do vaší cloudové služby bez písemného svolení (e-mailu nebo jiné písemné komunikace) od vlastníka nebo jeho zmocnění.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Nejde vzdáleně připojit k VIRTUÁLNÍmu počítači cloudové služby pomocí souboru RDP. Zobrazí se následující chyba: došlo k chybě ověřování (kód: 0x80004005)

K této chybě může dojít, pokud použijete soubor RDP z počítače, který je připojený k Azure Active Directory. Pokud chcete tento problém vyřešit, postupujte následovně:

1. Klikněte pravým tlačítkem na soubor RDP, který jste stáhli, a pak vyberte **Upravit**.
2. Přidat "&#92;" jako předponu před uživatelské jméno. Použijte například **.\username** místo  **uživatelského jména**.

## <a name="scaling"></a>Škálování

### <a name="i-cannot-scale-beyond-x-instances"></a>Nejde škálovat na více než X instancí
Vaše předplatné Azure má omezený počet jader, které můžete použít. Pokud jste použili všechny dostupné jádra, škálování nebude fungovat. Například pokud máte omezení 100 jader, znamená to, že byste měli mít 100 instancí virtuálních počítačů pro vaši cloudovou službu, nebo 50 instancí virtuálních počítačů a2 velikosti a2.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Jak můžu nakonfigurovat automatické škálování na základě metrik paměti?

Automatické škálování na základě metriky paměti pro Cloud Services se momentálně nepodporuje. 

Pokud chcete tento problém obejít, můžete použít Application Insights. Automatické škálování podporuje Application Insights jako zdroj metrik a může škálovat počet instancí role na základě metriky hosta, jako je "paměť".  Je nutné nakonfigurovat Application Insights v souboru balíčku projektu cloudové služby (*. cspkg) a povolit rozšíření Azure Diagnostics na službě pro implementaci tohoto feat.

Další podrobnosti o využití vlastní metriky prostřednictvím Application Insights ke konfiguraci automatického škálování pro Cloud Services najdete v tématu [Začínáme s automatickým škálováním podle vlastní metriky v Azure](../azure-monitor/autoscale/autoscale-custom-metric.md) .

Další informace o tom, jak integrovat Azure Diagnostics s Application Insights pro Cloud Services, najdete v tématu [odeslání cloudové služby, virtuálního počítače nebo Service Fabric diagnostických dat do Application Insights](../azure-monitor/agents/diagnostics-extension-to-application-insights.md)

Další informace o povolení Application Insights pro Cloud Services najdete v tématu [Application Insights pro Azure Cloud Services](../azure-monitor/app/cloudservices.md)

Další informace o tom, jak povolit protokolování Azure Diagnostics pro Cloud Services, najdete v tématu [nastavení diagnostiky pro Azure Cloud Services a virtuální počítače](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them) .

## <a name="generic"></a>Obecné

### <a name="how-do-i-add-nosniff-to-my-website"></a>Návody přidat `nosniff` na můj web?
Chcete-li klientům zabránit v sledování typů MIME, přidejte do souboru *web.config* nastavení.

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

Můžete ho také přidat jako nastavení služby IIS. Použijte následující příkaz s článkem [běžné úlohy po spuštění](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) .

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Návody přizpůsobení služby IIS pro webovou roli?
Použijte spouštěcí skript služby IIS v článku [běžné úlohy po spuštění](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) .

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Jaká je maximální kvóta pro cloudovou službu?
Viz [omezení pro konkrétní služby](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Proč jednotka na virtuálním počítači cloudové služby zobrazuje hodně volného místa na disku?
Jedná se o očekávané chování, které by nemělo způsobovat žádné potíže s vaší aplikací. Deník je zapnutý pro jednotku% AppRoot% ve virtuálních počítačích Azure PaaS, která v podstatě spotřebovává dvojnásobek velikosti místa, které soubory obvykle zabírají. Existuje však několik věcí, které je třeba si uvědomit, že v podstatě tuto chybu zapínají na neproblém.

Velikost jednotky% AppRoot% se počítá jako velikost <. cspkg + maximální velikost deníku + okraj volného místa> nebo 1,5 GB, podle toho, co je větší. Velikost virtuálního počítače nemá žádný vliv na tento výpočet. (Velikost virtuálního počítače ovlivňuje pouze velikost dočasné jednotky C:.) 

Zápis na jednotku% AppRoot% není podporován. Pokud píšete do virtuálního počítače Azure, musíte to udělat v dočasném prostředku LocalStorage (nebo jiné možnosti, jako je BLOB Storage, soubory Azure atd.). Takže množství volného místa ve složce% AppRoot% není smysluplné. Pokud si nejste jistí, jestli vaše aplikace zapisuje na jednotku% AppRoot%, můžete vždycky nechat službu běžet po dobu několika dní a pak porovnat velikosti před a po. 

Azure nebude zapisovat do jednotky% AppRoot% vůbec. Až se virtuální pevný disk vytvoří z počítače `.cspkg` připojeného k virtuálnímu počítači Azure, stačí, když na tuto jednotku zapíšete svou aplikaci. 

Nastavení deníku nejde konfigurovat, takže je nemůžete vypnout.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Jak můžu automatizovaným způsobem přidat antimalwarové rozšíření pro moji Cloud Services?

V úloze po spuštění můžete povolit antimalwarové rozšíření pomocí skriptu PowerShellu. K implementaci použijte postup v těchto článcích: 
 
- [Vytvoření spouštěcí úlohy PowerShellu](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](/powershell/module/servicemanagement/azure.service/Set-AzureServiceAntimalwareExtension)

Další informace o scénářích antimalwarového nasazení a o tom, jak je povolit z portálu, najdete v tématu [scénáře nasazení antimalwaru](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Jak povolit Indikace názvu serveru (SNI) pro Cloud Services?

SNI můžete povolit v Cloud Services pomocí jedné z následujících metod:

**Metoda 1: použití PowerShellu**

Vazbu SNI můžete nakonfigurovat pomocí rutiny prostředí PowerShell **New-webbinding** v úloze po spuštění pro instanci role cloudové služby, jak je uvedeno níže:

```powershell
New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags
```

Jak je popsáno [zde](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee790567(v=technet.10)), $sslFlags může být jednou z následujících hodnot:

|Hodnota|Význam|
------|------
|0|Žádné SNI|
|1|SNI povolen|
|2|SNI vazba, která používá centrální úložiště certifikátů|
|3|SNI vazba, která používá centrální úložiště certifikátů|
 
**Metoda 2: použití kódu**

Vazbu SNI můžete také nakonfigurovat prostřednictvím kódu ve spuštění role, jak je popsáno v tomto [blogovém příspěvku](/archive/blogs/jianwu/expose-ssl-service-to-multi-domains-from-the-same-cloud-service):

```csharp
//<code snip> 
                var serverManager = new ServerManager(); 
                var site = serverManager.Sites[0]; 
                var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                serverManager.CommitChanges(); 
    //</code snip>
```
    
Pomocí některého z výše uvedených přístupů musí být příslušné certifikáty (*. pfx) pro konkrétní názvy hostitelů napřed v instancích role nainstalovaná pomocí úlohy po spuštění nebo prostřednictvím kódu, aby vazba SNI mohla platit.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Jak můžu přidat značky do cloudové služby Azure? 

Cloudová služba je klasický prostředek. Značky podporují jenom prostředky vytvořené prostřednictvím Azure Resource Manager. Tagy nemůžete použít u klasických prostředků, jako je třeba cloudová služba. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Azure Portal nezobrazuje verzi sady SDK cloudové služby. Jak to mohu získat?

Pracujeme na uvedení této funkce na Azure Portal. Mezitím můžete použít následující příkazy PowerShellu k získání verze sady SDK:

```powershell
Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot
```

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Chci po několik měsíců vypnout cloudovou službu. Jak snížit náklady na vyúčtování cloudové služby bez ztráty IP adresy?

Už nasazená cloudová služba se účtuje za výpočetní výkon a úložiště, které používá. Takže i když virtuální počítač Azure vypnete, bude se vám pořád účtovat úložiště. 

Tady je postup, ve kterém můžete snížit svou fakturaci, aniž byste ztratili IP adresu pro vaši službu:

1. Před odstraněním nasazení [si IP adresu vyhradit](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) .  Bude se vám účtovat jenom tato IP adresa. Další informace o fakturaci IP adres najdete v tématu [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Odstraňte nasazení. Neodstraňujte xxx.cloudapp.net, abyste ho mohli použít pro budoucnost.
3. Pokud chcete cloudovou službu znovu nasadit pomocí stejné rezervované IP adresy, kterou jste rezervovali ve vašem předplatném, přečtěte si téma [vyhrazená IP adresa adres Cloud Services a Virtual Machines](https://azure.microsoft.com/blog/reserved-ip-addresses/).