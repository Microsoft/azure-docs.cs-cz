---
title: Nejčastější dotazy týkající se konfigurace a správy
titleSuffix: Azure Cloud Services
description: V tomto článku jsou uvedeny nejčastější dotazy týkající se konfigurace a správy cloudových služeb Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 5821c72ae1be4759cf5aa76ff1f5af43337749c0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668583"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problémy s konfigurací a správou cloudových služeb Azure: Nejčastější dotazy (nejčastější dotazy)

Tento článek obsahuje nejčastější dotazy týkající se problémů s konfigurací a správou [cloudových služeb Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Informace o velikosti najdete také na [stránce Velikost virtuálního počítače cloudových služeb.](cloud-services-sizes-specs.md)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certifikáty**

- [Proč je řetězec certifikátů mého certifikátu TLS/SSL služby Cloud Service neúplný?](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [Jaký je účel "Šifrovací certifikát nástrojů Windows Azure pro rozšíření"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Jak lze vygenerovat žádost o podpis certifikátu (CSR) bez "RDP-ing" v instanci?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Platnost certifikátu správy cloudových služeb vyprší. Jak ji obnovit?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Jak automatizovat instalaci hlavního certifikátu TLS/SSL(.pfx) a zprostředkujícího certifikátu(.p7b)?](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [Jaký je účel certifikátu Microsoft Azure Service Management for MachineKey?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitorování a protokolování**

- [Jaké jsou nadcházející funkce cloudové služby na webu Azure Portal, které můžou pomoct spravovat a monitorovat aplikace?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Proč služba IIS přestane zapisovat do adresáře protokolu?](#why-does-iis-stop-writing-to-the-log-directory)
- [Jak povolím protokolování wadu pro cloudové služby?](#how-do-i-enable-wad-logging-for-cloud-services)

**Konfigurace sítě**

- [Jak nastavím časový limit nečinnosti pro Azure balancer?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Jak přidručím statickou IP adresu ke své cloudové službě?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Jaké jsou funkce a možnosti, které poskytuje Základní IPS/IDS a DDOS Azure?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Jak povolit HTTP/2 na virtuálním počítači cloudových služeb?](#how-to-enable-http2-on-cloud-services-vm)

**Oprávnění**

- [Může microsoft interní technici vzdálenou plochu cloudové služby instance bez povolení?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Pomocí souboru RDP nelze vzdálenou plochu virtuálnímu počítači cloudové služby. Zoňou následující chyba: Došlo k chybě ověřování (Kód: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Škálování**

- [Nelze škálovat nad rámec x instancí](#i-cannot-scale-beyond-x-instances)
- [Jak lze nakonfigurovat automatické škálování na základě metrik paměti?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Obecné**

- [Jak mohu přidat "nosniff" na mých webových stránkách?](#how-do-i-add-nosniff-to-my-website)
- [Jak lze přizpůsobit službu IIS pro webovou roli?](#how-do-i-customize-iis-for-a-web-role)
- [Jaký je limit kvóty pro moji cloudovou službu?](#what-is-the-quota-limit-for-my-cloud-service)
- [Proč jednotka na mém virtuálním počítači cloudové služby zobrazuje velmi málo volného místa na disku?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Jak mohu automatické přidání antimalwarového rozšíření pro cloudové služby?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Jak povolit označení názvu serveru (SNI) pro cloudové služby?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Jak můžu do cloudové služby Azure přidat značky?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Na webu Azure Portal se nezobrazuje verze sady SDK mé cloudové služby. Jak to můžu získat?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Chci na několik měsíců vypnout cloudovou službu. Jak snížit náklady na fakturaci cloudové služby bez ztráty IP adresy?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certifikáty

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>Proč je řetězec certifikátů mého certifikátu TLS/SSL služby Cloud Service neúplný?
    
Zákazníkům doporučujeme nainstalovat úplný řetěz certifikátů (listový certifikát, zprostředkující certifikáty a kořenový certifikát) namísto pouze listu. Při instalaci pouze listový certifikát, spoléháte se na systém Windows k vytvoření řetězu certifikátů procházkou ctl. Pokud při pokusu systému Windows o ověření certifikátu dochází k občasným problémům se sítí nebo službou DNS v Azure nebo Windows Update, může být certifikát považován za neplatný. Instalací úplného řetězu certifikátů se tomuto problému lze vyhnout. Blog na [Jak nainstalovat zřetězený SSL certifikát](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) ukazuje, jak to udělat.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Jaký je účel "Šifrovací certifikát nástrojů Windows Azure pro rozšíření"?

Tyto certifikáty se automaticky vytvářejí při každém přidání rozšíření do cloudové služby. Nejčastěji se jedná o rozšíření WAD nebo rozšíření RDP, ale může to být jiné, například rozšíření Antimalware nebo Log Collector. Tyto certifikáty se používají pouze pro šifrování a dešifrování privátní konfigurace rozšíření. Datum vypršení platnosti není nikdy zaškrtnuto, takže nezáleží na tom, zda platnost certifikátu vypršela. 

Tyto certifikáty můžete ignorovat. Pokud chcete certifikáty vyčistit, můžete je zkusit všechny smažit. Azure vyvolá chybu, pokud se pokusíte odstranit certifikát, který se používá.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Jak lze vygenerovat žádost o podpis certifikátu (CSR) bez "RDP-ing" v instanci?

Viz následující pokyny:

[Získání certifikátu pro použití s weby Windows Azure (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Zástupce je pouze textový soubor. Nemusí být vytvořen ze stroje, kde bude certifikát nakonec použit.I když je tento dokument napsán pro službu App Service, vytvoření zástupce je obecné a platí také pro cloudové služby.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Platnost certifikátu správy cloudových služeb vyprší. Jak ji obnovit?

K obnovení certifikátů správy můžete použít následující příkazy prostředí PowerShell:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

**Get-AzurePublishSettingsFile** vytvoří nový certifikát správy v**certifikátech správy** **předplatného** > na webu Azure Portal. Název nového certifikátu vypadá jako "YourSubscriptionNam]-[CurrentDate]-credentials".

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>Jak automatizovat instalaci hlavního certifikátu TLS/SSL(.pfx) a zprostředkujícího certifikátu(.p7b)?

Tuto úlohu můžete automatizovat pomocí spouštěcího skriptu (batch/cmd/PowerShell) a zaregistrovat tento spouštěcí skript v souboru definice služby. Přidejte spouštěcí skript i soubor certifikátu (.p7b) do složky projektu ve stejném adresáři spouštěcího skriptu.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Jaký je účel certifikátu Microsoft Azure Service Management for MachineKey?

Tento certifikát se používá k šifrování klíčů počítače na webových rolích Azure. Další informace naleznete v [tomto informačním zpravodaji](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731).

Další informace najdete v těchto článcích:
- [Konfigurace a spuštění úloh při spuštění pro cloudovou službu](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Běžné úlohy spuštění cloudové služby](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitorování a protokolování

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Jaké jsou nadcházející funkce cloudové služby na webu Azure Portal, které můžou pomoct spravovat a monitorovat aplikace?

Možnost generovat nový certifikát pro protokol RDP (RdP) je již brzy. Případně můžete spustit tento skript:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Možnost vybrat blob nebo místní pro vaše csdef a cscfg upload umístění se blíží. Pomocí [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0)můžete nastavit každou hodnotu umístění.

Možnost sledovat metriky na úrovni instance. Další možnosti monitorování jsou k dispozici v [aplikaci How to Monitor Cloud Services](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Proč služba IIS přestane zapisovat do adresáře protokolu?
Vyčerpali jste kvótu místního úložiště pro zápis do adresáře protokolu.Chcete-li tento problém opravit, můžete provést jednu ze tří věcí:
* Povolte diagnostiku služby IIS a nechat diagnostiku pravidelně přesouvat do úložiště objektů blob.
* Ručně odeberte soubory protokolu z adresáře protokolování.
* Zvyšte limit kvóty pro místní prostředky.

Další informace najdete v následujících dokumentech:
* [Ukládání a zobrazení diagnostických dat ve službě Azure Storage](/azure/storage/common/storage-introduction)
* [Protokoly služby IIS přestanou psát ve službě Cloud Service](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Jak povolím protokolování wadu pro cloudové služby?
Protokolování diagnostiky Windows Azure (WAD) můžete povolit pomocí následujících možností:
1. [Povolit z Visual Studia](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Povolit prostřednictvím kódu .NET](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Povolení prostřednictvím prostředí Powershell](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Chcete-li získat aktuální nastavení WAD vaší cloudové služby, můžete použít [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd nebo můžete zobrazit prostřednictvím portálu z okna "Cloud Services --> Extensions".


## <a name="network-configuration"></a>Konfigurace sítě

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Jak nastavím časový limit nečinnosti pro Azure balancer?
Časový čas můžete zadat v souboru definice služby (csdef) takto:

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
Další informace najdete [v tématu Novinka: Konfigurovatelný časový limit nečinnosti pro Azure Load Balancer.](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/)

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Jak přidručím statickou IP adresu ke své cloudové službě?
Chcete-li nastavit statickou adresu IP, musíte vytvořit vyhrazenou adresu IP. Tato vyhrazená ADRESA IP může být přidružena k nové cloudové službě nebo k existujícímu nasazení. Podrobnosti naleznete v následujících dokumentech:
* [Jak vytvořit vyhrazenou IP adresu](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Rezervovat IP adresu stávající cloudové služby](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Přidružení vyhrazené IP adresy k nové cloudové službě](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Přidružení vyhrazené ip adresy ke spuštěnému nasazení](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Přidružení vyhrazené ip adresy ke cloudové službě pomocí konfiguračního souboru služby](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Jaké jsou funkce a možnosti, které poskytuje Základní IPS/IDS a DDOS Azure?
Azure má IPS/IDS na fyzických serverech datového centra, které chrání před hrozbami. Kromě toho mohou zákazníci nasadit řešení zabezpečení třetích stran, jako jsou brány firewall webových aplikací, síťové brány firewall, antimalwar, detekce vniknutí, systémy prevence (IDS/IPS) a další. Další informace naleznete v [tématu Ochrana dat a datových zdrojů a dodržování globálních standardů zabezpečení](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Společnost Microsoft průběžně monitoruje servery, sítě a aplikace, aby zjišťovala hrozby. Přístup Azure pro správu multiprnged hrozeb využívá detekci vniknutí, distribuovanou prevenci útoků denial-of-service (DDoS), penetrační testování, analýzy chování, detekci anomálií a strojové učení k neustálému posilování obrany a snižování rizik. Microsoft Antimalware pro Azure chrání Cloudové služby Azure a virtuální počítače. Máte možnost nasadit řešení zabezpečení třetích stran, jako jsou požární stěny webových aplikací, síťové brány firewall, antimalwarové systémy, systémy detekce a prevence vniknutí (IDS/IPS) a další.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Jak povolit HTTP/2 na virtuálním počítači cloudových služeb?

Windows 10 a Windows Server 2016 jsou dodávány s podporou http/2 na straně klienta i serveru. Pokud se váš klient (prohlížeč) připojuje k serveru IIS přes protokol TLS, který vyjednává protokol HTTP/2 prostřednictvím rozšíření TLS, není nutné provádět žádné změny na straně serveru. Důvodem je, že přes TLS hlavička h2-14 určující použití PROTOKOLU HTTP/2 je odeslána ve výchozím nastavení. Pokud na druhé straně váš klient odesílá hlavičku upgradu pro upgrade na HTTP/2, musíte provést níže uvedené změny na straně serveru, abyste zajistili, že upgrade funguje a skončíte s připojením HTTP/2. 

1. Spusťte program regedit.exe.
2. Přejděte ke klíči registru: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Vytvořte novou hodnotu DWORD s názvem **DuoEnabled**.
4. Nastavte jeho hodnotu na 1.
5. Restartujte server.
6. Přejděte na **výchozí web** a v části **Vazby**vytvořte novou vazbu TLS s právě vytvořeným certifikátem podepsaným svým držitelem. 

Další informace naleznete v tématu:

- [HTTP/2 ve službě IIS](https://blogs.iis.net/davidso/http2)
- [Video: HTTP/2 ve Windows 10: Prohlížeč, aplikace a webový server](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Tyto kroky mohou být automatizovány prostřednictvím úlohy při spuštění, takže kdykoli se vytvoří nová instance PaaS, může provést výše uvedené změny v systémovém registru. Další informace naleznete v tématu [Konfigurace a spuštění úloh při spuštění služby Cloud Service](cloud-services-startup-tasks.md).

 
Jakmile to provedete, můžete ověřit, zda byl protokol HTTP/2 povolen nebo nebyl, pomocí jedné z následujících metod:

- Povolte verzi protokolu protokolu iis a prohlédněte si protokoly iis. Zobrazí protokoly HTTP/2. 
- Povolte vývojářský nástroj F12 v aplikaci Internet Explorer nebo Microsoft Edge a přepněte na kartu Síť a ověřte protokol. 

Další informace naleznete [v tématu HTTP/2 ve službě IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Oprávnění

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Jak můžu implementovat přístup založený na rolích pro cloudové služby?
Cloudové služby nepodporuje model řízení přístupu na základě rolí (RBAC), protože se nejedná o službu založenou na Azure Resource Manageru.

Viz [Principy různých rolí v Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Vzdálená plocha

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Může microsoft interní technici vzdálenou plochu cloudové služby instance bez povolení?
Společnost Microsoft dodržuje přísný proces, který interním technikům neumožní vzdálené plochy do cloudové služby bez písemného souhlasu (e-mailu nebo jiné písemné komunikace) od vlastníka nebo jeho zmocněnce.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Pomocí souboru RDP nelze vzdálenou plochu virtuálnímu počítači cloudové služby. Zoňou následující chyba: Došlo k chybě ověřování (Kód: 0x80004005)

K této chybě může dojít, pokud použijete soubor RDP z počítače, který je připojen ke službě Azure Active Directory. Pokud chcete tento problém vyřešit, postupujte následovně:

1. Klepněte pravým tlačítkem myši na stažený soubor RDP a vyberte příkaz **Upravit**.
2. Před uživatelské jméno přidejte jako předponu "&#92;". Místo **uživatelského jména**použijte například **uživatelské jméno .\.**

## <a name="scaling"></a>Škálování

### <a name="i-cannot-scale-beyond-x-instances"></a>Nelze škálovat nad rámec x instancí
Vaše předplatné Azure má limit na počet jader, které můžete použít. Škálování nebude fungovat, pokud jste použili všechna jádra k dispozici. Například pokud máte limit 100 jader, to znamená, že můžete mít 100 a1 velikosti virtuálních strojů instance pro vaši cloudovou službu, nebo 50 A2 velikosti virtuálních počítačů instance.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Jak lze nakonfigurovat automatické škálování na základě metrik paměti?

Automatické škálování založené na metrikách paměti pro cloudové služby není aktuálně podporováno. 

Chcete-li tento problém vyřešit, můžete použít Application Insights. Automatické škálování podporuje Application Insights jako zdroj metrik a může škálovat počet instancí role na základě metriky hosta, jako je "Paměť".  Musíte nakonfigurovat Application Insights v souboru balíčku projektu cloudové služby (*.cspkg) a povolit rozšíření Diagnostika Azure ve službě k implementaci tohoto výkonu.

Další podrobnosti o tom, jak využít vlastní metriku pomocí Application Insights ke konfiguraci automatickéškálování v cloudových službách, najdete [v tématu Začínáme s automatickým škálování podle vlastní metriky v Azure](../azure-monitor/platform/autoscale-custom-metric.md)

Další informace o tom, jak integrovat Diagnostika Azure s Application Insights pro cloudové služby, najdete v [tématu Odesílání cloudových služeb, virtuálních strojů nebo diagnostických dat Fabric služby do Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Další informace o povolení přehledů aplikací pro cloudové služby najdete v [tématu Application Insights for Azure Cloud Services](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Další informace o povolení protokolování diagnostiky Azure pro cloudové služby najdete v tématu [Nastavení diagnostiky pro Cloudové služby Azure a virtuální počítače.](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Obecné

### <a name="how-do-i-add-nosniff-to-my-website"></a>Jak mohu přidat "nosniff" na mých webových stránkách?
Chcete-li klientům zabránit v čichání typů MIME, přidejte do souboru *web.config* nastavení.

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

Můžete také přidat jako nastavení ve správě IIS. Pomocí následujícího příkazu postupujte v článku [běžné úlohy při spuštění.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Jak lze přizpůsobit službu IIS pro webovou roli?
Použijte spouštěcí skript služby IIS z článku [běžných úloh při spuštění.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Jaký je limit kvóty pro moji cloudovou službu?
Viz [Omezení specifická pro službu](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Proč jednotka na mém virtuálním počítači cloudové služby zobrazuje velmi málo volného místa na disku?
Toto je očekávané chování a nemělo by způsobit žádný problém pro vaši aplikaci. Ukládání do deníku je zapnuto pro jednotku %approot% ve virtuálních počítačích Azure PaaS, která v podstatě spotřebovává dvojnásobek množství místa, které soubory obvykle zabírají. Existuje však několik věcí, které je třeba si uvědomit, že v podstatě to promění v neproblém.

Velikost jednotky %approot% se \<vypočítá jako velikost .cspkg + max velikost deníku + okraj volného místa> nebo 1,5 GB, podle toho, která hodnota je větší. Velikost virtuálního počítače nemá žádný vliv na tento výpočet. (Velikost virtuálního počítače ovlivňuje jenom velikost dočasné jednotky C:.) 

Zápis na jednotku %approot% není podporován. Pokud píšete do virtuálního počítače Azure, musíte tak učinit v dočasném prostředku LocalStorage (nebo jiné možnosti, jako je například úložiště objektů blob, soubory Azure atd.). Velikost volného místa ve složce %approot% tedy nemá smysl. Pokud si nejste jisti, zda vaše aplikace zapisuje na jednotku %approot%, můžete službu nechat běžet několik dní a pak porovnat velikosti "před" a "po". 

Azure nebude nic zapisovat na jednotku %approot%. Jakmile virtuální pevný disk je vytvořen z .cspkg a připojené do virtuálního počítače Azure, jediná věc, která může zapisovat na tuto jednotku je vaše aplikace. 

Nastavení deníku nelze konfigurovat, takže jej nelze vypnout.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Jak mohu automatické přidání antimalwarového rozšíření pro cloudové služby?

Rozšíření antimalwaru můžete povolit pomocí skriptu Prostředí PowerShell v úloze po spuštění. Postupujte podle kroků v těchto článcích k jeho implementaci: 
 
- [Vytvoření úlohy spuštění Prostředí PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Další informace o scénářích nasazení antimalwaru a o tom, jak je povolit z portálu, naleznete [v tématu Scénáře nasazení antimalwaru](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Jak povolit označení názvu serveru (SNI) pro cloudové služby?

SNI můžete povolit v cloudových službách pomocí jedné z následujících metod:

**Metoda 1: Použití prostředí PowerShell**

Vazbu SNI lze nakonfigurovat pomocí rutiny PowerShell **New-WebBinding** v úloze spuštění instance role cloudové služby, jak je uvedeno níže:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Jak [je popsáno zde](https://technet.microsoft.com/library/ee790567.aspx), $sslFlags může být jednou z hodnot, jako je následující:

|Hodnota|Význam|
------|------
|0|Žádné SNI|
|1|SNI povoleno|
|2|Nesni vázací, která používá centrální úložiště certifikátů|
|3|SNI vazba, která používá centrální úložiště certifikátů|
 
**Metoda 2: Použití kódu**

Vazba SNI by také mohla být nakonfigurována pomocí kódu při spuštění role, jak je popsáno na tomto [blogu](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                    binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Pomocí některého z výše uvedených přístupů musí být příslušné certifikáty (*.pfx) pro konkrétní názvy hostitelů nejprve nainstalovány v instancích rolí pomocí úlohy při spuštění nebo pomocí kódu, aby byla vazba SNI účinná.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Jak můžu do cloudové služby Azure přidat značky? 

Cloudová služba je klasický prostředek. Jenom prostředky vytvořené pomocí značek podpory Azure Resource Manager. Značky nelze použít na klasické prostředky, jako je cloudová služba. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Na webu Azure Portal se nezobrazuje verze sady SDK mé cloudové služby. Jak to můžu získat?

Pracujeme na uvedení této funkce na portálazure. Mezitím můžete použít následující příkazy prostředí PowerShell získat verzi sady SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Chci na několik měsíců vypnout cloudovou službu. Jak snížit náklady na fakturaci cloudové služby bez ztráty IP adresy?

Již nasazená cloudová služba se účtuje za výpočetní prostředky a úložiště, které používá. Takže i když vypnete virtuální počítač Azure, bude se vám pořád účtovat úložiště. 

Zde je to, co můžete udělat pro snížení fakturace bez ztráty IP adresy pro vaši službu:

1. [Před odstraněním nasazení si vyhraďte adresu IP.](../virtual-network/virtual-networks-reserved-public-ip.md)  Bude se vám účtovat pouze tato IP adresa. Další informace o fakturaci IP adres naleznete v [tématu Ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Odstraňte nasazení. Neodstraňujte xxx.cloudapp.net, abyste je mohli použít pro budoucnost.
3. Pokud chcete službu Cloud znovu nasadit pomocí stejné záložní IP adresy, kterou jste si rezervovali ve svém předplatném, přečtěte [si informace o vyhrazených IP adresách pro cloudové služby a virtuální počítače](https://azure.microsoft.com/blog/reserved-ip-addresses/).

