---
title: Konfigurace a správy problémy pro Microsoft Azure Cloud Services – nejčastější dotazy | Microsoft Docs
description: V tomto článku jsou uvedené nejčastější dotazy týkající se konfigurace a správa pro Microsoft Azure Cloud Services.
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
ms.date: 05/08/2018
ms.author: genli
ms.openlocfilehash: bbd817750d6c6d43ae6973eb38b866b76921f33b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problémy se správou a konfigurace pro Azure Cloud Services: Časté otázky (FAQ)

Tento článek obsahuje nejčastější dotazy o konfiguraci a správě problémů pro [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Můžete také obrátit [cloudové služby virtuálních počítačů velikost stránky](cloud-services-sizes-specs.md) velikost informace.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certifikáty**

- [Řetěz certifikátů my certifikátu SSL služby Cloud je nekompletní](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [Co je účelem "Windows Azure šifrovací certifikát pro rozšíření nástrojů"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Jak můžu vygenerovat žádost (Podepsání certifikátu) bez "RDP-ing" v instanci?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Moje cloudové služby správy certifikátu vyprší. Jak jej obnovit?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Jak automatizovat instalaci hlavní SSL certifikát (.pfx) a zprostředkující certificate(.p7b)?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- [Jaký je účel certifikátu "Microsoft Azure Service Management pro MachineKey"?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Sledování a protokolování**

- [Jaké jsou možnosti nadcházející cloudové služby na portálu Azure, která vám může pomoci spravovat a monitorovat aplikace?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Proč služba IIS přestali zapisovat do adresáře protokolu?](#why-does-iis-stop-writing-to-the-log-directory)

**Konfigurace sítě**

- [Jak nastavit časový limit nečinnosti pro vyrovnávání zatížení Azure?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Jak přiřadit statickou IP adresu do mé cloudové služby?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Jaké jsou funkce a možnosti, které poskytuje Azure základní IP adresy nebo ID a DDOS?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Postup povolení protokolu HTTP nebo 2 pro virtuální počítač cloudové služby?](#how-to-enable-http2-on-cloud-services-vm)

**Oprávnění**

- [Můžete Microsoft interní technici vzdálené plochy k instancím cloudové služby bez oprávnění?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Vzdálené plochy na virtuální počítač cloudové služby nelze provést I pomocí souboru RDP. Můžu získat následující chyba: došlo k chybě ověřování (kód: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Škálování**

- [Nelze nastavit mimo X instancí](#i-cannot-scale-beyond-x-instances)
- [Konfigurování automatického škálování, na základě paměti metriky](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Obecné**

- [Jak přidat "nosniff" my Web?](#how-do-i-add-nosniff-to-my-website)
- [Jak přizpůsobit služby IIS pro webovou roli?](#how-do-i-customize-iis-for-a-web-role)
- [Co je limit kvóty pro moje Cloudová služba?](#what-is-the-quota-limit-for-my-cloud-service)
- [Proč jednotku, na virtuální počítač cloudové služby zobrazit velmi málo volného místa na disku?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Jak můžu přidat rozšíření antimalwarových pro moje cloudové služby v automatizovaný způsob, jak?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Postup povolení indikace názvu serveru (SNI) pro cloudové služby?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Jak můžete přidat značky do mých cloudové služby Azure?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Portál Azure nezobrazuje verze sady SDK Moje cloudové služby. Načtení který?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Chci vypnout cloudové služby na několik měsíců. Jak snížit náklady na fakturační cloudové služby bez ztráty IP adresu?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certifikáty

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Řetěz certifikátů my certifikátu SSL služby Cloud je nekompletní
    
Doporučujeme zákazníkům nainstalovat úplnou řetězu certifikátů (cert listu, certifikátů zprostředkující a kořenový certifikát) namísto právě certifikát typu list. Když instalujete pouze certifikát listu, byste tedy spoléhat na systému Windows se sestavit řetěz certifikátů rámci seznamu CTL. Dojde-li DNS problémy se sítí přerušované nebo v Azure nebo Windows Update při Windows k ověření certifikátu, certifikát může být považovány za neplatné. Řetěz certifikátů úplnou instalaci, lze předejít tomuto problému. Blog na [jak nainstalovat certifikát SSL a zřetězené](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) ukazuje, jak to udělat.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Co je účelem "Windows Azure šifrovací certifikát pro rozšíření nástrojů"?

Tyto certifikáty se automaticky vytvoří při každém přidání rozšíření do cloudové služby. Nejčastěji je to rozšíření WAD nebo rozšíření protokolu RDP, ale může to být jiné, jako je například rozšíření antimalwarových nebo kolektor protokolů. Tyto certifikáty se používají jenom pro šifrování a dešifrování privátní konfigurace rozšíření. Datum vypršení platnosti je nikdy zaškrtnutá políčka, takže není důležité, pokud platnost certifikátu. 

Tyto certifikáty, můžete ignorovat. Pokud chcete vyčistit certifikáty, můžete zkusit všechny jejich odstranění. Azure vyvolá chybu, pokud se pokusíte odstranit certifikát, který je používán.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Jak můžu vygenerovat žádost (Podepsání certifikátu) bez "RDP-ing" v instanci?

Najdete v následujících dokumentech pokyny:

[Získání certifikátu pro použití s Windows Azure webů (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Zástupce je textový soubor. Nemá vytvořit z počítače, kde bude certifikát nakonec používat. I když tento dokument je napsán pro aplikační službu, je obecný vytvoření zástupce oddělení služeb zákazníkům a platí také pro cloudové služby.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Moje cloudové služby správy certifikátu vyprší. Jak jej obnovit?

Můžete použít následující příkazy prostředí PowerShell, obnovit certifikáty pro správu:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

**Get-AzurePublishSettingsFile** dojde k vytvoření nového certifikátu pro správu v **předplatné** > **certifikáty pro správu** na portálu Azure. Název nového certifikátu vypadá jako "YourSubscriptionNam]-[CurrentDate] - pověření".

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Jak automatizovat instalaci hlavní SSL certifikát (.pfx) a zprostředkující certificate(.p7b)?

Můžete automatizovat tato úloha pomocí spouštěcího skriptu (batch nebo cmd/PowerShell) a zaregistrujte spuštění skriptu v definičním souboru služby. Přidáte certifikát (soubor .p7b) i spouštěcí skript ve složce projektu ve stejném adresáři spouštěcího skriptu.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Jaký je účel certifikátu "Microsoft Azure Service Management pro MachineKey"?

Tento certifikát se používá k šifrování klíče počítače na webové role Azure. Další informace, podívejte se na tento informační zpravodaj [https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731].

Další informace najdete v následujících článcích:
- [Jak nakonfigurovat a spustit úlohy spuštění pro cloudové služby](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Běžné úlohy spuštění cloudové služby](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Sledování a protokolování

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Jaké jsou možnosti nadcházející cloudové služby na portálu Azure, která vám může pomoci spravovat a monitorovat aplikace?

Umožňuje vygenerovat nový certifikát pro protokol RDP (Remote Desktop) je již brzy. Alternativně můžete spustit tento skript:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Možnost zvolit objektů blob nebo místní pro csdef a cscfg nahrát umístění tu bude brzo dostupná. Pomocí [New-AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-4.0.0), můžete nastavit hodnotu každé umístění.

Umožňuje monitorovat metriky na úrovni instance. Další možnosti monitorování jsou k dispozici v [postup monitorování cloudové služby](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Proč služba IIS přestali zapisovat do adresáře protokolu?
Vyčerpáte místní úložiště kvóty pro zápis do adresáře protokolu. Chcete-li vyřešit tento problém, můžete provést jednu z následujících způsobů:
* Povolte diagnostiku pro službu IIS a mít k diagnostice pravidelně přesunout do úložiště objektů blob.
* Ručně odeberte soubory protokolu z adresáře protokolování.
* Zvýšit limit kvóty pro místní prostředky.

Další informace najdete v následujících dokumentech:
* [Ukládání a zobrazení diagnostických dat v Azure Storage](cloud-services-dotnet-diagnostics-storage.md)
* [Služba IIS protokoly se přestanou zápis v rámci cloudové služby](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="network-configuration"></a>Konfigurace sítě

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Jak nastavit časový limit nečinnosti pro vyrovnávání zatížení Azure?
Můžete zadat časový limit v souboru definice (csdef) služby takto:

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
V tématu [nový: konfigurovat nečinnosti časový limit pro vyrovnávání zatížení Azure](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) Další informace.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Jak přiřadit statickou IP adresu do mé cloudové služby?
Pokud chcete nastavit statickou IP adresu, musíte vytvořit vyhrazenou IP adresu. Tato vyhrazená IP adresa může být přidružené k novou Cloudovou službu nebo do stávajícího nasazení. Najdete v následujících dokumentech podrobnosti:
* [Jak vytvořit vyhrazenou IP adresu](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Rezervovat IP adresu existující cloudové služby](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Přidružení vyhrazené IP adresy na novou Cloudovou službu](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Přidružení vyhrazené IP adresy na spuštěného nasazení](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Pomocí konfigurační soubor služby přidružit vyhrazenou IP adresu do cloudové služby](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Jaké jsou funkce a možnosti, které poskytuje Azure základní IP adresy nebo ID a DDOS?
Azure má IP adresy nebo ID v datovém centru fyzické servery se chránit proti hrozbám. Kromě toho můžete zákazníkům nasadit řešení třetí strany zabezpečení, jako je například brány firewall webových aplikací, síťové brány firewall, proti malwaru, zjišťování neoprávněných vniknutí, prevence systémy (ID nebo IP adresy) a další. Další informace najdete v tématu [chránit vaše data a prostředky a v souladu s normami globálního zabezpečení](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft neustále monitoruje servery, sítě a aplikace k detekci hrozeb. Azure multipronged threat management přístup používá na detekci narušení, distributed denial of service (DDoS) prevence útoků, průnikům testování, chování analýza, detekce anomálií a strojového učení neustále posílit jeho obrany a snížení rizika. Antimalware od Microsoftu pro Azure chrání Azure Cloud Services a virtuálních počítačů. Máte možnost nasadit řešení třetí strany zabezpečení kromě, jako je například webové aplikace ještě efektivněji bariéry, síťové brány firewall, proti malwaru, vniknutí odhalování a prevence systémy (ID nebo IP adresy) a další.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Postup povolení protokolu HTTP nebo 2 pro virtuální počítač cloudové služby?

Windows 10 a Windows Server 2016 se na straně klienta a server s podporou protokolu HTTP nebo 2. Pokud vašeho klienta (prohlížeč) se připojuje k serveru služby IIS přes TLS, vyjedná HTTP/2 prostřednictvím rozšíření TLS, pak nepotřebujete, aby všechny změny na straně serveru. Důvodem je, že přes TLS, jsou ve výchozím nastavení odesílány hlavičku h2-14 zadání pomocí protokolu HTTP nebo 2. Pokud na druhé straně vašeho klienta odesílá hlavičku upgradu pro upgrade na HTTP/2, budete muset udělat změnu níže na straně serveru k zajištění, že Upgrade funguje a skončili s připojením k protokolu HTTP nebo 2. 

1. Spusťte regedit.exe.
2. Přejděte ke klíči registru: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Vytvořte novou hodnotu DWORD s názvem **DuoEnabled**.
4. Nastavte ho na hodnotu 1.
5. Restartujte server.
6. Přejděte na vaše **Default Web Site** a v části **vazby**, vytvořte novou vazbu TLS pomocí certifikátu podepsaného svým držitelem, které jsou právě vytvořili. 

Další informace naleznete v tématu:

- [HTTP/2 ve službě IIS](https://blogs.iis.net/davidso/http2)
- [Video: HTTP/2 ve Windows 10: prohlížeče, aplikace a Webový Server](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Tyto kroky může je možné automatizovat pomocí spuštění úlohy, tak, aby vždy, když se vytvoří novou instanci PaaS, provést změny výše v registru systému. Další informace najdete v tématu [postupy pro konfiguraci a spuštění úlohy spuštění pro cloudové služby](cloud-services-startup-tasks.md).

 
Po byla provedena, můžete ověřit, zda byl povolen protokol HTTP/2 nebo není pomocí jedné z následujících metod:

- Povolte verze protokolu v protokoly služby IIS a podívejte se do protokolů IIS. HTTP/2 se zobrazí v protokolech. 
- Povolit F12 Developer Tool v Internet Explorer nebo Edge a přepněte na kartu sítě k ověření protokol. 

Další informace najdete v tématu [HTTP/2 ve službě IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Oprávnění

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Jak můžete implementovat přístup na základě rolí pro cloudové služby?
Cloudové služby nepodporuje model řízení přístupu na základě Role (RBAC), protože se nejedná o službě Azure Resource Manager na základě.

V tématu [Azure RBAC oproti správci předplatného classic](../role-based-access-control/overview.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="remote-desktop"></a>Vzdálená plocha

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Můžete Microsoft interní technici vzdálené plochy k instancím cloudové služby bez oprávnění?
Microsoft vyplývá z vlastníka nebo jejich zmocněnci striktní proces, který nebude umožňují interní technikům vzdálené plochy do cloudové služby bez písemného souhlasu (e-mailu nebo jiných napsané komunikace).

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Vzdálené plochy na virtuální počítač cloudové služby nelze provést I pomocí souboru RDP. Můžu získat následující chyba: došlo k chybě ověřování (kód: 0x80004005)

Této chybě může dojít, pokud použijete soubor RDP z počítače, který je připojený ke službě Azure Active Directory. Chcete-li vyřešit tento problém, postupujte takto:

1. Klikněte pravým tlačítkem na soubor RDP, který jste stáhli a potom vyberte **upravit**.
2. Přidání "&#92;" jako předpony před uživatelské jméno. Například použít **. \username** místo **uživatelské jméno**.

## <a name="scaling"></a>Škálování

### <a name="i-cannot-scale-beyond-x-instances"></a>Nelze nastavit mimo X instancí
Vaše předplatné Azure limit je počet jader, které můžete použít. Škálování nebude fungovat, pokud jste použili všechna jádra, které jsou k dispozici. Například pokud máte limit 100 jader, znamená to může mít 100 instancí virtuální počítač A1 s nastavenou velikostí pro cloudové služby, nebo velikosti 50 A2 instancí virtuálního počítače.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Konfigurování automatického škálování, na základě paměti metriky

Automatické škálování, na základě paměti metriky pro cloudové služby není aktuálně podporován. 

Chcete-li tento problém obejít, můžete použít Application Insights. Automatické škálování podporuje Application Insights jako zdroj metriky a můžete škálovat podle metriky hosta jako "Paměti" počet instancí role.  Budete muset nakonfigurovat Application Insights v balíčku souboru projektu cloudové služby (*.cspkg) a povolit rozšíření diagnostiky Azure ve službě k implementaci této feat.

Další podrobnosti o tom, jak využívat vlastní metriku pomocí Application Insights ke konfiguraci automatického škálování na Cloud Services najdete v tématu [začít pracovat s automatické škálování podle vlastní metriky v Azure](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)

Další informace o tom, jak integrovat Azure Diagnostics Application Insights pro cloudové služby najdete v tématu [odeslat Cloudová služba, virtuální počítač nebo Service Fabric diagnostická data do služby Application Insights](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)

Další informace o povolení Application Insights pro cloudové služby najdete v tématu [Application Insights pro Azure Cloud Services](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Další informace o tom, jak povolit protokolování diagnostiky Azure pro cloudové služby najdete v tématu [nastavení diagnostiky pro Azure Cloud Services a virtuálních počítačů](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Obecné

### <a name="how-do-i-add-nosniff-to-my-website"></a>Jak přidat "nosniff" my Web?
Pokud chcete klientům zabránit sledování toku dat typy MIME, přidejte nastavení ve vaší *web.config* souboru.

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

To můžete také přidat jako nastavení ve službě IIS. Použijte následující příkaz se [běžné úlohy spuštění](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) článku.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Jak přizpůsobit služby IIS pro webovou roli?
Použijte spouštěcí skript služby IIS z [běžné úlohy spuštění](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) článku.

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Co je limit kvóty pro moje Cloudová služba?
V tématu [omezuje specifickou pro službu](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Proč jednotku, na virtuální počítač cloudové služby zobrazit velmi málo volného místa na disku?
Toto chování je očekávané a jeho by nemělo způsobit jakýkoli problém do vaší aplikace. Pro jednotku % approot % ve virtuálních počítačích PaaS Azure, která v podstatě využívá double množství místa, které soubory obvykle zabírají zapnutý deníku. Ale existuje několik věcí zajímat, v podstatě to změnit na jiný problém.

Velikost disku % approot % se počítá jako < .cspkg + velikost deníku maximální velikost > + okraj volného místa, nebo 1,5 GB, podle toho, která je větší. Velikost virtuálního počítače nemá žádný vliv na tohoto výpočtu. (Velikost virtuálního počítače pouze ovlivňuje velikost dočasné jednotce C:.) 

Ji není podporována pro zápis na disk % % approot. Pokud píšete do virtuálního počítače Azure, musíte jej v dočasných prostředků LocalStorage (nebo jiné možnosti, jako je například úložiště objektů Blob, soubory Azure, atd.). Množství volného místa ve složce % approot % tak není důležité. Pokud si nejste jistí, jestli vaše aplikace je zápis do jednotky % approot %, můžete je vždy nechat služby spustit několik dnů a potom porovnejte "před" a "po" velikosti. 

Azure nebude nic zapisovat do % jednotku % approot. Jakmile virtuální pevný disk je vytvořena z vaší .cspkg a připojené k virtuálnímu počítači Azure, jediné, co může zapisovat do této jednotky je vaše aplikace. 

Nastavení deníku jsou nekonfigurovatelnou, takže nelze ho vypnout.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Jak můžu přidat rozšíření antimalwarových pro moje cloudové služby v automatizovaný způsob, jak?

Můžete povolit pomocí skriptu prostředí PowerShell v úloze pro spuštění rozšíření proti malwaru. Postupujte podle kroků v těchto článcích pro implementaci: 
 
- [Vytvoření úlohy spuštění prostředí PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/Azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Další informace o scénářích nasazení antimalwarových a jak ji povolit z portálu najdete v tématu [scénáře nasazení antimalwarových](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Postup povolení indikace názvu serveru (SNI) pro cloudové služby?

SNI v cloudové služby můžete povolit pomocí jedné z následujících metod:

**Metoda 1: Použití prostředí PowerShell**

Vazba SNI lze nastavit pomocí rutiny prostředí PowerShell **New-WebBinding** spuštění úlohy pro instanci role cloudové služby jak je uvedeno níže:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Jak je popsáno [zde](https://technet.microsoft.com/library/ee790567.aspx), $sslFlags může být jedna z hodnot jako následující:

|Hodnota|Význam|
------|------
|0|Žádné SNI|
|1|SNI povoleno |
|2 |Bez SNI vazby, který používá centrálního úložiště certifikátů|
|3|Uložení SNI vazby, které používá Centrální certifikátu |
 
**Metoda 2: Použití kódu**

Vazba SNI lze nakonfigurovat také pomocí kódu v spuštění role popsané v tomto [příspěvku na blogu](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Pomocí kteréhokoli z přístupů výše, příslušných certifikátů (*.pfx) pro konkrétní názvy hostitelů musí být nejprve instalovány u instancí role pomocí spuštění úlohy nebo prostřednictvím kódu v pořadí pro vazbu SNI účinné.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Jak můžete přidat značky do mých cloudové služby Azure? 

Cloudová služba je klasický prostředek. Pouze prostředky vytvořené pomocí značek podpory Azure Resource Manager. Značky nemůže použít na klasické prostředky, například cloudové služby. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Portál Azure nezobrazuje verze sady SDK Moje cloudové služby. Načtení který?

Pracujeme na převedení této funkce na portálu Azure. Mezitím můžete provádět následující příkazy prostředí PowerShell získat verze sady SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Chci vypnout cloudové služby na několik měsíců. Jak snížit náklady na fakturační cloudové služby bez ztráty IP adresu?

Cloudové služby už nasazené získá účtovány poplatky za výpočty a úložiště používá. Takže i v případě, že vypnout virtuální počítač Azure, bude stále získat fakturuje pro úložiště. 

Zde je, co můžete dělat ke snížení vaší fakturace bez ztráty IP adresa služby:

1. [Rezervovat IP adresu](../virtual-network/virtual-networks-reserved-public-ip.md) před odstraněním nasazení.  Můžete pouze platit pro tuto IP adresu. Další informace o fakturaci IP adresu, najdete v části [IP adresy ceny](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Odstraňte nasazení. Neodstraňujte xxx.cloudapp.net, takže budete používat pro budoucí.
3. Pokud chcete znovu zavést cloudové služby s použitím stejnou IP Adresou rezervy, která je vyhrazena ve vašem předplatném naleznete v tématu [vyhrazené IP adresy pro cloudové služby a virtuální počítače](https://azure.microsoft.com/blog/reserved-ip-addresses/).

