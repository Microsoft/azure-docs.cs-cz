---
title: Možnosti připojení a sítě problémy pro Microsoft Azure Cloud Services – nejčastější dotazy | Dokumentace Microsoftu
description: Tento článek obsahuje nejčastější dotazy o připojení a sítě pro Microsoft Azure Cloud Services.
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
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: defd623eff76a4e37a9d88c4f59d2edaa71e34e0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227446"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Potíže s připojení a sítě pro Azure Cloud Services: Nejčastější dotazy (FAQ)

Tento článek obsahuje nejčastější dotazy o připojení a sítě problémy [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informace o velikosti najdete v článku [stránky velikost virtuálních počítačů cloudových služeb](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Můžu si rezervovat IP adresy v cloudové službě s více virtuálních IP adres.
Nejprve se ujistěte, jestli je zapnutá, kterou chcete rezervovat IP adresu pro instanci virtuálního počítače. Za druhé Ujistěte se, že pomocí vyhrazené IP adresy pro nasazení pracovního a produkčního prostředí. *Ne* změnit nastavení při upgradu nasazení.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Použití vzdálené plochy když mám skupinu zabezpečení sítě?
Přidání pravidel k NSG, která umožňují přenosy na portech **3389** a **20000**. Vzdálené plochy používá port **3389**. Instance cloudové služby jsou Vyrovnávané, takže nemůže přímo řídí kterou instanci chcete připojit k. *RemoteForwarder* a *RemoteAccess* agenty spravovat provoz protokolu RDP (Remote Desktop) a povolit klienta k odeslání do souboru cookie protokolu RDP a zadejte jednotlivé instance pro připojení k. *RemoteForwarder* a *RemoteAccess* agentů vyžadují port **20000** byly otevřené, což může být blokovaný Pokud máte skupinu zabezpečení sítě.

## <a name="can-i-ping-a-cloud-service"></a>Může příkaz ping cloudové služby?

Ne, nikoli pomocí normální "příkaz ping" / protokol ICMP. Prostřednictvím služby Azure load balancer není povolený protokol ICMP.

K otestování připojení, doporučujeme, abyste provedli příkaz ping portu. Zatímco Ping.exe využívá protokol ICMP, můžete použít jiné nástroje, jako je například PSPing Nmap a telnet, k otestování připojení na určitém portu TCP.

Další informace najdete v tématu [nahrazujícím port za příkazy ping ICMP k otestování připojení k virtuálnímu počítači Azure](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Jak zabránit přijímají tisíc přístupů z neznámé IP adresy, které by mohly poukazovat napadením se zlými úmysly do cloudové služby?
Implementace sítě s více vrstvami zabezpečení k ochraně svých služeb platformy před distribuovanými útoky s cílem odepření služeb (DDoS) v Azure. Systém ochrany Azure DDoS je součástí Azure průběžné monitorování procesu, který nástroj neustále vylepšujeme tak prostřednictvím testování průniku. Tento systém ochrany před útoky DDoS slouží k odolat nejen útokům zvenku, ale také z jiných tenantů Azure. Další informace najdete v tématu [zabezpečení sítě Azure](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Také můžete vytvořit úlohy po spuštění pro selektivní blokování některé konkrétní IP adresy. Další informace najdete v tématu [blokovat konkrétní IP adresu](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Při pokusu o protokolu RDP k instanci své cloudové služby, zobrazí zpráva "uživatelský účet vypršela."
Když obcházíte datum vypršení platnosti, který je nakonfigurovaný v nastavení protokolu RDP, může se zobrazit chybová zpráva "Tento uživatelský účet vypršela". Datum vypršení platnosti z portálu můžete změnit pomocí následujících kroků:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com), přejděte ke cloudové službě a vyberte **vzdálené plochy** kartu.

2. Vyberte **produkční** nebo **pracovní** slot pro nasazení.

3. Změnit **vyprší dne** data a pak konfiguraci uložte.

Teď by měl budete moci RDP k vašemu počítači.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Proč se Azure Load Balancer nevyrovnává zatížení rovnoměrně?
Informace o fungování interního nástroje load balancer, naleznete v tématu [nový režim distribuce Azure Load Balancer](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

5-řazené kolekce členů (Zdrojová IP adresa, zdrojový port, cílová IP adresa, cílový port a typ protokolu) je distribuční algoritmus používá hodnotu hash mapovat datové přenosy do dostupných serverů. Poskytuje věrnosti pouze v rámci relace přenosu. Pakety ve stejné relaci TCP nebo UDP přesměrováni na stejnou instanci datacenter IP (DIP) za koncový bod s vyrovnáváním zatížení. Když klient ukončí a znovu otevře připojení nebo spustí novou relaci ze stejné Zdrojová IP adresa, zdrojový port změní a způsobí, že provoz přejít k jinému koncovému bodu vyhrazené IP adresy.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Jak můžete přesměrovat příchozí provoz na výchozí adresa URL svojí cloudové služby na vlastní adresu URL? 

Modul přepisování adres URL služby IIS je možné přesměrovat provoz, který přichází do výchozí adresa URL pro cloudovou službu (například \*. cloudapp.net) na některé vlastní název nebo adresu URL. Protože modulem přepisu adresy URL je ve výchozím nastavení zapnutá webové role a jeho pravidlech jsou nakonfigurované v souboru web.config vaší aplikace, je vždy k dispozici na virtuálním počítači bez ohledu na to restartování počítače nebo obnoví. Další informace naleznete v tématu:

- [Vytvoření pravidla pro přepis pro modul přepisování adres URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Odebrat výchozí odkaz](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Jak lze I bloku nebo zakázat příchozí provoz na výchozí adresa URL svojí cloudové služby? 

Mohli byste znemožnit příchozí provoz k výchozí adresu URL nebo název cloudové služby (například \*. cloudapp.net). Nastavte hlavičku hostitele na vlastní název DNS (například www.MyCloudService.com) v části Konfigurace vazby webu v souboru definice (*.csdef) cloudové služby, jak je uvedeno: 
 

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
 
Protože tato vazba hlavičky hostitele se vynucuje prostřednictvím souboru csdef, služba je dostupná jenom prostřednictvím vlastní název "www.MyCloudService.com." Všechny příchozí požadavky "*. cloudapp.net" domény vždy selže. Pokud používáte vlastní test paměti SLB nebo interní load balancer ve službě, blokování výchozí adresu URL nebo název služby může narušit zjišťování chování. 

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Jak se můžete Ujistěte se, že nikdy změní veřejná IP adresa cloudové služby?

Pokud chcete mít jistotu, že se že nikdy nemění veřejnou IP adresu vaší cloudové služby (označované také jako virtuální IP adresu) tak, aby může být zamezeno přidat na seznam povolených několik konkrétní klienty, doporučujeme, abyste měli vyhrazené IP adresy s ním spojená. V opačném případě virtuální IP adresy, které poskytuje Azure zrušeno přidělení k předplatnému při odstranění nasazení. Pro úspěšné operaci prohození virtuálních IP adres musíte jednotlivé rezervované IP adresy pro produkční a přípravné sloty. Bez nich operace prohození se nezdaří. Rezervovat IP adresu a přidružte jej k vaší cloudové služby, najdete v těchto článcích:
 
- [Vyhrazení IP adresy existující cloudové služby](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Přidružení vyhrazené IP adresy pro cloudové služby pomocí konfiguračního souboru služby](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Pokud máte více než jednu instanci pro svoje role, přidružení RIP s cloudovou službou by nemělo způsobit žádné výpadky. Alternativně můžete seznam povolených IP rozsah vašeho datového centra Azure. Můžete najít všechny rozsahy IP adres Azure na [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Tento soubor obsahuje rozsahy IP adres (včetně rozsahů pro compute, SQL a úložiště) použít v datových centrech Azure. Aktualizovaný soubor nový tweet každý týden, který zobrazuje aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres. Nové rozsahy, které se zobrazují v souboru nejsou používány v datových centrech alespoň jeden týden. Stáhněte si nový soubor .xml každý týden a proveďte potřebné změny ve vaší lokalitě pro zajištění správné identifikace služeb spuštěných v Azure. Uživatelé Azure ExpressRoute si všimnout, že tento soubor používá k aktualizaci inzerování protokolu BGP Azure místa v první týden každého měsíce. 

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Jak můžu použít virtuální sítí Azure Resource Manageru s cloudovými službami? 

Cloudové služby nelze umístit do virtuálních sítí Azure Resource Manageru. Virtuální sítě Resource Manageru a klasického nasazení virtuální sítě můžete připojit prostřednictvím partnerského vztahu. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Jak lze získat seznam veřejných IP adres, které používá své cloudové služby?

Můžete použít následující skript PS se získat seznam veřejných IP adres pro cloudové služby v rámci vašeho předplatného

    $services = Get-AzureService  | Group-Object -Property ServiceName

    foreach ($service in $services) 
    {
        "Cloud Service '$($service.Name)'"

        $deployment = Get-AzureDeployment -ServiceName $service.Name 
        "VIP - " +  $deployment.VirtualIPs[0].Address
        "================================="
    }
