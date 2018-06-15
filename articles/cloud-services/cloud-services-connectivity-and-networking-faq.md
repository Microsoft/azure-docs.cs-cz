---
title: Problémy s připojením a sítě pro Microsoft Azure Cloud Services – nejčastější dotazy | Microsoft Docs
description: V tomto článku jsou uvedené nejčastější dotazy o připojení a sítě pro Microsoft Azure Cloud Services.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: ab0fa22e9ba776db3d4af301499545f6e0822478
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070160"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problémy s připojením a sítě pro Azure Cloud Services: Časté otázky (FAQ)

Tento článek obsahuje nejčastější dotazy týkající se problémů s připojením a sítě pro [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Velikost informace najdete v tématu [cloudové služby virtuálních počítačů velikost stránky](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>I nelze rezervovat IP adresy v cloudové službě více virtuálních IP adres.
Zkontrolujte, jestli je zapnutá, kterou chcete rezervovat IP adresu pro instanci virtuálního počítače. Druhý Ujistěte se, že používáte vyhrazené IP adresy pro pracovní a provozní nasazení. *Nechcete* změnit nastavení při nasazení je upgradu.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Jak se při skupinu NSG je nutné použít vzdálené plochy?
Přidat pravidla k této skupině, která povolí komunikaci na portech **3389** a **20000**. Vzdálená plocha používá port **3389**. Instance cloudové služby jsou Vyrovnávané, takže nemůže přímo řídit kterou instanci pro připojení k. *RemoteForwarder* a *RemoteAccess* agenty spravovat provoz protokolu RDP (Remote Desktop) a umožňují klientu odesílat soubor cookie s RDP a zadejte jednotlivé instance pro připojení k. *RemoteForwarder* a *RemoteAccess* agentů vyžadují port **20000** být otevřená, které mohou být blokovány Pokud máte skupinu NSG.

## <a name="can-i-ping-a-cloud-service"></a>Můžete příkaz ping Cloudová služba?

Ne, nikoli pomocí normální "ping" / protokolu ICMP. Protokol ICMP, nemá oprávnění prostřednictvím nástroje pro vyrovnávání zatížení Azure.

K testování připojení, doporučujeme, abyste provedli port ping. Zatímco Ping.exe používá ICMP, můžete dalších nástrojů, jako je například Pspingu, Nmap a telnet, abyste otestovali připojení ke specifického portu TCP.

Další informace najdete v tématu [používat příkazy ping portu místo ICMP k testování připojení virtuálního počítače Azure](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Jak zabráním přijetí tisíc přístupů z neznámého IP adres, které můžou uvádět napadením se zlými úmysly do cloudové služby?
Azure implementuje zabezpečení s více vrstvami sítě k ochraně jeho služby platformy proti útokům (DDoS) distribuované denial of service. V systému Azure DDoS obrany je součástí Azure nepřetržité monitorování procesu, který je neustále zlepšit prostřednictvím průnikům testování. Tento systém obrany DDoS je určena pro odolat nejen útokům zvenku, ale taky od ostatních klientů Azure. Další informace najdete v tématu [zabezpečení sítě Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Také můžete vytvořit úlohu spuštění pro selektivní blokování některé konkrétní IP adresy. Další informace najdete v tématu [blokovat konkrétní IP adresu](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Při pokusu protokolu RDP pro instance Moje cloudové služby se zobrazí zpráva "vypršela platnost uživatelského účtu."
Chybová zpráva "Tento uživatelský účet vypršela" může docházet, při vyřazení datum vypršení platnosti, který je nakonfigurovaný v nastavení protokolu RDP. Datum vypršení platnosti z portálu můžete změnit pomocí následujících kroků:

1. Přihlaste se k [portál Azure](https://portal.azure.com), přejděte do cloudové služby a vyberte **vzdálené plochy** kartě.

2. Vyberte **produkční** nebo **pracovní** nasazovací slot.

3. Změna **vyprší dne** data a pak konfiguraci uložte.

Nyní nyní byste měli mít pro připojení RDP k vašemu počítači.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Proč není nástroj pro vyrovnávání zatížení Azure vyrovnávání přenosů stejně?
Informace o fungování pro vyrovnávání zatížení interní najdete v tématu [nový režim distribuce Vyrovnávání zatížení Azure](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Jako distribuční algoritmus používá je 5-n-tice (zdrojové IP adresy, zdrojového portu, cílové adresy IP, cílový port a typ protokolu) hodnotu hash pro mapování provoz na dostupné servery. Poskytuje věrnosti pouze v rámci relace přenosu. Pakety ve stejné relaci TCP nebo UDP jsou směrované na stejnou instanci datacenter IP (DIP) za vyrovnáváním zatížení koncového bodu. Když klient zavře a znovu otevře připojení nebo spustí novou relaci ze stejné zdrojové IP adresy, zdrojového portu změny a způsobí, že provoz přejít k jinému koncovému bodu vyhrazené IP adresy.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Jak můžete přesměrovat příchozí přenosy na výchozí adresa URL Moje cloudové služby na vlastní adresu URL? 

Modul přepisování adres URL služby IIS umožňuje přesměrovat provoz, který přichází do výchozí adresa URL pro cloudové služby (například \*. cloudapp.net) pro některé vlastní název nebo adresa URL. Vzhledem k tomu, že je modul přepisování adres URL ve výchozím nastavení zapnuta webové role a její pravidla jsou nakonfigurovány v souboru web.config aplikace, je vždy k dispozici pro virtuální počítač bez ohledu na to restartování počítače nebo reimages. Další informace naleznete v tématu:

- [Vytvoření pravidel přepisování pro modul přepisování adres URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Odebrání výchozí odkaz](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Jak můžete I bloku nebo zakázat příchozí přenosy na adresu URL výchozí Moje cloudové služby? 

Výchozí adresa URL nebo název cloudové služby můžete zabránit příchozí přenosy (například \*. cloudapp.net). Nastavte hlavičku hostitele pro vlastní název DNS (například www.MyCloudService.com) v části Konfigurace vazeb webu v souboru definice (*.csdef) cloudové služby, jak je uvedeno: 
 

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
 
Protože tato vazba hlavičky hostitele se vynucuje prostřednictvím soubor csdef, služba je dostupné jenom přes vlastní název "www.MyCloudService.com." Všechny příchozí požadavky na "*. cloudapp.net" domény vždy selže. Pokud používáte vlastní test paměti SLB nebo interní nástroj ve službě, blokování výchozí adresa URL nebo název služby může narušovat testování chování. 

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Jak se může Ujistěte se, že nikdy změní veřejnou IP adresu cloudové služby?

Pokud chcete mít jistotu, že veřejnou IP adresu vaší cloudové služby (také označované jako VIP) nikdy změní tak, že může být infrastruktura seznam povolených adres několik konkrétní klienty, doporučujeme, abyste měli vyhrazená IP adresa s ním spojená. Virtuální IP adresy, které poskytuje Azure, jinak hodnota je navrácena ze svého předplatného, pokud odstraníte nasazení. Úspěšná operace prohození virtuální IP adresy musíte jednotlivé vyhrazené IP adresy pro produkční a pracovní sloty. Bez, operace prohození se nezdaří. Rezervovat IP adresu a přidružte ji k cloudové služby, přečtěte si tyto články:
 
- [Rezervovat IP adresu existující cloudové služby](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Pomocí konfigurační soubor služby přidružit vyhrazenou IP adresu do cloudové služby](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Pokud máte více než jedna instance pro role, přidružení RIP s cloudovou službou by nemělo způsobit výpadek. Alternativně můžete povolených rozsah IP adres datového centra Azure. Můžete najít všechny rozsahy Azure IP na [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Tento soubor obsahuje rozsahy IP adres (včetně rozsahů výpočty, SQL a úložiště) použít v datových centrech Azure. Aktualizovaný soubor odeslání týdně, který aktuálně nasazená rozsahy a žádné nadcházející změny rozsahy IP. Nové rozsahy, které se zobrazují v souboru nebudou používány v datacentru alespoň jeden týden. Stáhněte si nový soubor .xml každý týden a proveďte potřebné změny na svém webu správně identifikovat služby spuštěné v Azure. Azure ExpressRoute uživatelé si všimnout, že tento soubor se používá k aktualizaci inzerování protokolu BGP Azure místa v první týden v měsíci. 

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Jak můžete použít virtuální sítě Azure Resource Manager s cloudovými službami? 

Cloudové služby nelze umístit do virtuální sítě Azure Resource Manager. Virtuální sítě Resource Manager a classic nasazení virtuální sítě může být připojena prostřednictvím partnerského vztahu. Další informace najdete v tématu [partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md).
