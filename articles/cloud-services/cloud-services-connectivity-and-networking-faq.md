---
title: Problémy s připojením a sítí
titleSuffix: Azure Cloud Services
description: V tomto článku jsou uvedené Nejčastější dotazy týkající se připojení a sítě pro Microsoft Azure Cloud Services.
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
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 2a27161ca9a218b1f7c0e3fb51c9935438d9778e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533413"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problémy s připojením a sítí pro Azure Cloud Services: nejčastější dotazy

Tento článek obsahuje nejčastější dotazy týkající se potíží s připojením a sítí pro [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informace o velikosti najdete na [stránce Cloud Services velikosti virtuálního počítače](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Nejde si vyhradit IP adresu v cloudové službě s více VIP.
Nejdřív se ujistěte, že je zapnutá instance virtuálního počítače, pro kterou se pokoušíte rezervovat IP adresu. Za druhé se ujistěte, že používáte rezervované IP adresy pro produkční i provozní nasazení. *Neměňte nastavení* během upgradu nasazení.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Návody použít vzdálenou plochu, když mám NSG?
Přidejte do NSG pravidla, která povolují provoz na portech **3389** a **20000**. Vzdálená plocha používá port **3389**. Instance cloudových služeb jsou vyrovnávány zatížení, takže nemůžete přímo určovat, ke které instanci se má připojit. Agenti *RemoteForwarder* a *RemoteAccess* SPRAVUJÍ provoz protokol RDP (Remote Desktop Protocol) (RDP) a umožňují klientovi odeslat soubor cookie RDP a zadat samostatnou instanci pro připojení. *RemoteForwarder* a *RemoteAccess* agenti vyžadují, aby byl port **20000** otevřený, což může být blokované, pokud máte NSG.

## <a name="can-i-ping-a-cloud-service"></a>Můžu testovat cloudovou službu pomocí testu?

Ne, ne pomocí normálního protokolu "/ICMP" příkazu "test". Protokol ICMP není povolený prostřednictvím nástroje pro vyrovnávání zatížení Azure.

Pokud chcete otestovat připojení, doporučujeme, abyste provedli příkaz testování portu. I když Ping.exe používá protokol ICMP, můžete k otestování připojení k určitému portu TCP použít jiné nástroje, například PSPing, nmap a Telnet.

Další informace najdete v tématu [použití příkazů k testování připojení virtuálních počítačů Azure místo ICMP](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Návody zabránit příjmu tisíců přístupů z neznámých IP adres, které by mohly naznačovat škodlivý útok cloudové služby?
Azure implementuje zabezpečení sítě Multilayer a chrání své služby platforem před distribuovanými útoky DDoS (Denial of-Service). Systém ochrany Azure DDoS je součástí procesu nepřetržitého monitorování Azure, který se průběžně vylepšuje prostřednictvím testování průniku. Tento systém DDoS obrany je navržený tak, aby odolal nejen útokům zvenčí, ale i z jiných tenantů Azure. Další informace najdete v tématu [zabezpečení sítě Azure](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Můžete také vytvořit úlohu po spuštění, která selektivně zablokuje některé konkrétní IP adresy. Další informace najdete v tématu [blokování konkrétní IP adresy](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Když se pokouším RDP do instance cloudové služby, zobrazí se zpráva "platnost uživatelského účtu vypršela."
Když obdržíte datum vypršení platnosti, které je nakonfigurované v nastavení RDP, může se zobrazit chybová zpráva "Tento uživatelský účet vypršel." Datum vypršení platnosti můžete změnit z portálu pomocí následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com), otevřete cloudovou službu a vyberte kartu **Vzdálená plocha** .

2. Vyberte **produkční** nebo **pracovní** slot pro nasazení.

3. Změňte datum **vypršení platnosti** a pak konfiguraci uložte.

Nyní byste měli být schopni protokol RDP na váš počítač.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Proč nástroj Azure Load Balancer nevyrovnává zatížení rovnoměrně?
Informace o tom, jak funguje interní nástroj pro vyrovnávání zatížení, najdete v tématu [Azure Load Balancer nový distribuční režim](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Použitý distribuční algoritmus je 5-Tuple (zdrojová IP adresa, zdrojový port, cílová IP adresa, cílový port a typ protokolu) pro mapování provozu na dostupné servery. Poskytuje vytrvalost jenom v rámci relace přenosu. Pakety ve stejné relaci TCP nebo UDP se přesměrují na stejnou instanci datacentra IP (DIP) za koncovým bodem s vyrovnáváním zatížení. Když klient ukončí a znovu otevře připojení nebo spustí novou relaci ze stejné zdrojové IP adresy, změní se zdrojový port a způsobí, že provoz přejde na jiný koncový bod DIP.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Jak můžu přesměrovat příchozí provoz na výchozí adresu URL své cloudové služby na vlastní adresu URL?

Modul pro přepis adresy URL služby IIS se dá použít k přesměrování provozu, který se nachází na výchozí adrese URL cloudové služby (například \* . cloudapp.NET), do nějakého vlastního názvu nebo adresy URL. Vzhledem k tomu, že je modul pro přepis adres URL ve výchozím nastavení povolený a že jeho pravidla jsou nakonfigurovaná v web.config aplikace, je vždycky dostupné na virtuálním počítači bez ohledu na restartování a obnovení imagí. Další informace najdete v tématech:

- [Vytvořit pravidla pro přepsání pro modul URL pro přepis](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Odebrat výchozí odkaz](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Jak můžu blokovat nebo zakázat příchozí provoz na výchozí adresu URL mé cloudové služby?

Můžete zabránit příchozímu provozu na výchozí adresu URL nebo název vaší cloudové služby (například \* . cloudapp.NET). \.V části Konfigurace vazeb webu v souboru definice cloudové služby (*. csdef) nastavte hlavičku hostitele na vlastní název DNS (například www MyCloudService.com), jak je uvedeno níže:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Vzhledem k tomu, že je tato vazba hlavičky hostitele vynutila prostřednictvím souboru csdef, služba je přístupná pouze prostřednictvím vlastního názvu "www.MyCloudService.com". Všechny příchozí požadavky do domény "*. cloudapp.net" vždy selžou. Pokud ve službě použijete vlastní test služby SLB nebo interní nástroj pro vyrovnávání zatížení, blokování výchozí adresy URL nebo názvu služby může být v konfliktu s chováním při zjišťování.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Jak se dá zajistit, aby se veřejná IP adresa cloudové služby nikdy nezměnila?

Abyste se ujistili, že se veřejná IP adresa vaší cloudové služby (označovaná také jako VIP) nikdy nemění, aby mohla být běžně schválená několika konkrétními klienty, doporučujeme, abyste k ní měli přiřazenou rezervovanou IP adresu. V opačném případě se virtuální IP adresa poskytovaná Azure z vašeho předplatného oddělí, pokud nasazení odstraníte. Pro úspěšnou operaci odkládacích adres VIP potřebujete jednotlivé rezervované IP adresy pro produkční i přípravné sloty. Bez nich operace prohození neproběhne úspěšně. Pokud si chcete vyhradit IP adresu a přidružit ji ke cloudové službě, přečtěte si tyto články:

- [Vyhrazení IP adresy existující cloudové služby](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Přidružení rezervované IP adresy ke cloudové službě pomocí konfiguračního souboru služby](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Pokud máte pro své role více než jednu instanci, přidružením protokolu RIP ke cloudové službě by nemělo dojít k výpadku. Případně můžete přidat rozsah IP adres vašeho datacentra Azure do seznamu povolených. Všechny rozsahy IP adres Azure najdete na [webu služby Stažení softwaru](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Tento soubor obsahuje rozsahy IP adres (včetně výpočetních prostředků, SQL a rozsahů úložiště), které se používají v datových centrech Azure. Každý týden se účtuje aktualizovaný soubor, který odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres. Nové rozsahy, které se zobrazí v souboru, se v datových centrech nepoužijí aspoň na jeden týden. Každý týden si stáhněte nový soubor. XML a proveďte potřebné změny na svém webu, abyste správně identifikovali služby běžící v Azure. Uživatelé Azure ExpressRoute si můžou všimnout, že tento soubor se používá k aktualizaci inzerce protokolu BGP v Azure Space v první týden v měsíci.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Jak můžu použít Azure Resource Manager virtuální sítě s Cloud Services?

Cloud Services nejde umístit do Azure Resource Managerch virtuálních sítí. Správce prostředků virtuální sítě a virtuální sítě klasického nasazení se dají připojit prostřednictvím partnerského vztahu. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Jak získám seznam veřejných IP adres, které používá můj Cloud Services?

Pomocí následujícího skriptu PS můžete získat seznam veřejných IP adres pro Cloud Services v rámci vašeho předplatného.

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```
