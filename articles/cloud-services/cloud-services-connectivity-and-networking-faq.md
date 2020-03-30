---
title: Problémy s připojením a sítí
titleSuffix: Azure Cloud Services
description: V tomto článku jsou uvedeny nejčastější dotazy týkající se připojení a sítí pro cloudové služby Microsoft Azure.
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
ms.openlocfilehash: 7caeba0e88f63106eae80f7142b5d65463f8d7a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019396"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problémy s připojením a sítí pro Cloudové služby Azure: Nejčastější dotazy (nejčastější dotazy)

Tento článek obsahuje nejčastější dotazy týkající se problémů s připojením a sítí pro [cloudové služby Azure](https://azure.microsoft.com/services/cloud-services). Informace o velikosti najdete na [stránce velikost virtuálního počítače cloudových služeb](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Nemohu si rezervovat IP adresu v cloudové službě s více virtuálními IP adresami.
Nejprve se ujistěte, že je zapnutá instance virtuálního počítače, pro kterou se pokoušíte rezervovat ip adresu. Za druhé, ujistěte se, že používáte vyhrazené IP adresy pro pracovní i produkční nasazení. *Při* inovaci nasazení neměňte nastavení.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Jak se používá vzdálená plocha, když mám nsg?
Přidejte do souboru služeb dispečerů pro přenos, která umožňují provoz na portech **3389** a **20000**. Vzdálená plocha používá port **3389**. Instance cloudových služeb jsou vyvažované, takže nemůžete přímo řídit, ke které instanci se chcete připojit. Agenti *RemoteForwarder* a *RemoteAccess* spravují přenosy protokolu RDP (Remote Desktop Protocol) a umožňují klientovi odeslat soubor cookie PROTOKOLU RDP a zadat jednotlivé instance, ke které se chcete připojit. Agenti *RemoteForwarder* a *RemoteAccess* vyžadují otevření portu **20000,** který může být blokován, pokud máte soubor nsg.

## <a name="can-i-ping-a-cloud-service"></a>Mohu ping cloudové služby?

Ne, ne pomocí normálního protokolu "ping"/ICMP. Protokol ICMP není povolen prostřednictvím služby Azure pro vyrovnávání zatížení.

Chcete-li otestovat připojení, doporučujeme provést příkaz ping portu. Zatímco nástroj Ping.exe používá protokol ICMP, můžete k testování připojení k určitému portu TCP použít jiné nástroje, například PSPing, Nmap a telnet.

Další informace najdete [v tématu Použití příkazu ping portu namísto ICMP k testování připojení virtuálního počítače Azure](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Jak zabráním přijímání tisíců přístupů z neznámých IP adres, které by mohly naznačovat škodlivý útok na cloudovou službu?
Azure implementuje vícevrstvé zabezpečení sítě k ochraně svých služeb platformy před distribuovanými útoky odmítnutí služby (DDoS). Systém ochrany DDoS Azure je součástí procesu průběžného monitorování Azure, který se neustále vylepšuje prostřednictvím testování průniku. Tento obranný systém DDoS je navržen tak, aby odolává nejen útokům zvenčí, ale i od jiných klientů Azure. Další informace najdete v tématu [Zabezpečení sítě Azure](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Můžete také vytvořit úlohu spuštění selektivně blokovat některé konkrétní ADRESY IP. Další informace naleznete [v tématu Blokování konkrétní adresy IP](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Při pokusu o RDP na mé instanci cloudové služby se zobrazí zpráva "Uživatelský účet vypršel."
Při obejití data vypršení platnosti, které je nakonfigurováno v nastavení protokolu RDP, se může zobrazit chybová zpráva "Platnost tohoto uživatelského účtu vypršela". Datum vypršení platnosti z portálu můžete změnit takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com), přejděte do cloudové služby a vyberte kartu **Vzdálená plocha.**

2. Vyberte slot **pro produkční** nebo **pracovní** nasazení.

3. Změňte datum **vypršení platnosti** a uložte konfiguraci.

Nyní byste měli být schopni RDP do vašeho počítače.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Proč nástroj Azure Load Balancer nevyrovnává zatížení rovnoměrně?
Informace o tom, jak funguje interní systém vyrovnávání zatížení, naleznete v [tématu Nový distribuční režim služby Azure Load Balancer](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Použitý distribuční algoritmus je 5-n-tice (zdrojová IP, zdrojový port, cílová IP adresa, cílový port a typ protokolu) hash pro mapování provozu na dostupné servery. Poskytuje lepivost pouze v rámci relace přenosu. Pakety ve stejné relaci TCP nebo UDP jsou směrovány na stejnou instanci IP (DIP) datového centra za koncovým bodem s vyrovnáváním zatížení. Když klient zavře a znovu otevře připojení nebo spustí novou relaci ze stejné zdrojové IP adresy, zdrojový port se změní a způsobí, že provoz přejde do jiného koncového bodu DIP.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Jak lze přesměrovat příchozí provoz na výchozí adresu URL cloudové služby na vlastní adresu URL?

Modul přepisování adres URL služby IIS lze použít k přesměrování přenosů, které \*přicházejí na výchozí adresu URL pro cloudovou službu (například .cloudapp.net) na nějaký vlastní název/adresu URL. Vzhledem k tomu, že modul přepisování adres URL je ve výchozím nastavení povolen na webových rolích a jeho pravidla jsou nakonfigurována v souboru web.config aplikace, je vždy k dispozici na virtuálním počítači bez ohledu na restartování nebo reimages. Další informace naleznete v tématu:

- [Vytvořit pravidla přepisu pro modul Přepis adresy URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Odebrání výchozího odkazu](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Jak mohu blokovat/zakázat příchozí provoz na výchozí adresu URL mé cloudové služby?

Příchozíprovoz můžete zabránit výchozí adrese URL/názvu cloudové služby \*(například .cloudapp.net). Nastavte hlavičku hostitele na vlastní název DNS\.(například www MyCloudService.com) v konfiguraci vazby webu v souboru definice cloudové služby (*.csdef), jak je uvedeno:

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

Vzhledem k tomu, že tato vazba záhlaví hostitele je vynucena prostřednictvím souboru csdef, služba je přístupná pouze prostřednictvím vlastního názvu "www.MyCloudService.com". Všechny příchozí požadavky do domény "*.cloudapp.net" vždy nezdaří. Pokud ve službě používáte vlastní sondu SLB nebo interní systém vyrovnávání zatížení, blokování výchozí adresy URL nebo názvu služby může narušit chování zjišťování.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Jak se můžu ujistit, že se veřejná IP adresa cloudové služby nikdy nezmění?

Chcete-li se ujistit, že veřejná IP adresa vaší cloudové služby (označovaná také jako VIP) se nikdy nezmění, aby mohla být obvykle zapsána na seznamu povolených několika konkrétních klientů, doporučujeme, abyste k ní byli přidruženi vyhrazená ADRESA IP. V opačném případě virtuální IP poskytované Azure se zvašeho předplatného zmocní, pokud nasazení odstraníte. Pro úspěšnou operaci výměny VIP potřebujete jednotlivé vyhrazené IP adresy pro produkční i pracovní sloty. Bez nich se operace prohození nezdaří. Pokud chcete rezervovat IP adresu a přidružit ji ke cloudové službě, přečtěte si tyto články:

- [Rezervace IP adresy existující cloudové služby](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Přidružení vyhrazené ip adresy ke cloudové službě pomocí konfiguračního souboru služby](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Pokud máte více než jednu instanci pro vaše role, přisuzování RIP s cloudovou službou by nemělo způsobit žádné prostoje. Případně můžete přidat rozsah IP adres vašeho datového centra Azure do seznamu povolených adres. Všechny rozsahy IP adres Azure najdete na webu [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Tento soubor obsahuje rozsahy IP adres (včetně výpočetních, SQL a rozsahů úložišť) používané v datových centrech Azure. Aktualizovaný soubor je zaúčtován týdně, který odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres. Nové oblasti, které se zobrazí v souboru, se v datových centrech nepoužívají alespoň jeden týden. Stáhněte si každý týden nový soubor XML a proveďte potřebné změny na webu, abyste správně identifikovali služby spuštěné v Azure. Uživatelé Azure ExpressRoute si mohou všimnout, že tento soubor slouží k aktualizaci reklamy Protokolu BGP na místo Azure v prvním týdnu každého měsíce.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Jak můžu používat virtuální sítě Azure Resource Manageru s cloudovými službami?

Cloudové služby nelze umístit do virtuálních sítí Azure Resource Manager. Virtuální sítě Resource Manageru a klasické virtuální sítě nasazení lze připojit prostřednictvím partnerského vztahu. Další informace naleznete v [tématu Partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Jak získám seznam veřejných IP služeb používaných mými cloudovými službami?

Pomocí následujícího skriptu ps můžete získat seznam veřejných IP služeb pro cloudové služby v rámci předplatného.

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
