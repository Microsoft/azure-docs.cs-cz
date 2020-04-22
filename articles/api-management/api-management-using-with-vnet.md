---
title: Použití služby Azure API Management s virtuálními sítěmi
description: Zjistěte, jak nastavit připojení k virtuální síti ve správě rozhraní Azure API a získat přes něj přístup k webovým službám.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2020
ms.author: apimpm
ms.openlocfilehash: 0ecb7ee7f5c7c0ebaa87eb6b32eee1926d9e294d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768955"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Použití služby Azure API Management s virtuálními sítěmi
Virtuální sítě Azure umožňují umístit jakékoli prostředky Azure do jiné než internetové sítě podporující směrování, ke které můžete řídit přístup. Tyto sítě pak můžete připojit k místním sítím pomocí různých technologií VPN. Další informace o virtuálních sítích Azure nazačátku s informacemi zde: [Azure Virtual Network Overview](../virtual-network/virtual-networks-overview.md).

Azure API Management lze nasadit uvnitř virtuální sítě (VNET), takže má přístup k back-endovým službám v rámci sítě. Portál pro vývojáře a brána rozhraní API lze nakonfigurovat tak, aby byly přístupné z Internetu nebo pouze v rámci virtuální sítě.

> [!NOTE]
> Adresa URL importního dokumentu rozhraní API musí být hostována na veřejně přístupné internetové adrese.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky popsané v tomto článku, musíte mít:

+ Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Instance APIM. Další informace najdete [v tématu Vytvoření instance Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>Povolení připojení virtuální sítě

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Povolení připojení virtuální sítě pomocí portálu Azure

1. Přejděte na [portál Azure](https://portal.azure.com) a najděte instanci správy rozhraní API. Vyhledejte a vyberte **služby správy rozhraní API**.

2. Zvolte instanci správy rozhraní API.

3. Vyberte **Virtuální síť**.
4. Nakonfigurujte instanci správy rozhraní API, která má být nasazena uvnitř virtuální sítě.

    ![Nabídka virtuální sítě api managementu][api-management-using-vnet-menu]
5. Vyberte požadovaný typ přístupu:

    * **Vypnuto**: Toto je výchozí nastavení. Správa rozhraní API není nasazena do virtuální sítě.

    * **Externí**: Brána pro správu rozhraní API a portál pro vývojáře jsou přístupné z veřejného internetu prostřednictvím externího vykladače zatížení. Brána může přistupovat k prostředkům v rámci virtuální sítě.

        ![Veřejný partnerský vztah][api-management-vnet-public]

    * **Interní**: Brána pro správu rozhraní API a portál pro vývojáře jsou přístupné pouze z virtuální sítě prostřednictvím interního nástroje pro vyrovnávání zatížení. Brána může přistupovat k prostředkům v rámci virtuální sítě.

        ![Soukromý partnerský vztah][api-management-vnet-private]

6. Pokud jste vybrali **externí** nebo **interní**, zobrazí se seznam všech oblastí, kde je zřízena vaše služba správy rozhraní API. Zvolte **umístění**a pak vyberte jeho **virtuální síť** a **podsíť**. Seznam virtuálních sítí je naplněn klasickými virtuálními sítěmi a virtuálními sítěmi Resource Manager, které jsou dostupné ve vašich předplatných Azure, které jsou nastavené v oblasti, kterou konfigurujete.

    > [!IMPORTANT]
    > Při nasazování instance Azure API Management do virtuální sítě Správce prostředků musí být služba ve vyhrazené podsíti, která neobsahuje žádné další prostředky s výjimkou instancí Azure API Management. Pokud se pokusíte nasadit instanci Azure API Management do podsítě virtuální sítě Správce prostředků, která obsahuje další prostředky, nasazení se nezdaří.

    Potom vyberte **Použít**. Stránka **Virtuální síť** instance Správy rozhraní API se aktualizuje pomocí nových možností virtuální sítě a podsítě.

    ![Vybrat VPN][api-management-setup-vpn-select]

7. Na horním navigačním panelu vyberte **Uložit**a pak **vyberte Použít konfiguraci sítě**.

> [!NOTE]
> Vip adresa instance správy rozhraní API se změní pokaždé, když je virtuální síť povolena nebo zakázána.
> VIP adresa se také změní, když se správa rozhraní API přesune z **externí** na **interní**nebo naopak.
>

> [!IMPORTANT]
> Pokud odeberete správu rozhraní API z virtuální sítě nebo změníte tu, ve které je nasazená, dříve použitá virtuální síť může zůstat uzamčena až šest hodin. Během tohoto období nebude možné odstranit virtuální síť nebo nasadit nový prostředek do něj. Toto chování platí pro klienty, kteří používají rozhraní API verze 2018-01-01 a starší. Klienti pomocí rozhraní api verze 2019-01-01 a novější, virtuální síť je uvolněna, jakmile je odstraněna přidružená služba api management.

## <a name="enable-vnet-connection-using-powershell-cmdlets"></a><a name="enable-vnet-powershell"> </a>Povolení připojení virtuální sítě pomocí rutin prostředí PowerShell
Připojení virtuální sítě můžete také povolit pomocí rutin prostředí PowerShell.

* **Vytvoření služby správy rozhraní API uvnitř virtuální sítě**: Pomocí rutiny [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) vytvořte službu Azure API Management uvnitř virtuální sítě.

* **Nasazení existující služby správy rozhraní API uvnitř virtuální sítě**: Pomocí rutiny [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) přesuňte existující službu Azure API Management uvnitř virtuální sítě.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Připojení k webové službě hostované ve virtuální síti
Po připojení služby API Management k virtuální síti se přístup k back-endovým službám v ní neliší od přístupu k veřejným službám. Stačí zadat místní IP adresu nebo název hostitele (pokud je server DNS nakonfigurován pro virtuální síť) webové služby do pole **URL webové služby** při vytváření nového rozhraní API nebo při úpravě existujícího rozhraní API.

![Přidání rozhraní API z VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Běžné problémy s konfigurací sítě
Následuje seznam běžných problémů s chybnou konfigurací, ke kterým může dojít při nasazování služby API Management do virtuální sítě.

* **Vlastní nastavení serveru DNS**: Služba api management závisí na několika službách Azure. Když je správa rozhraní API hostovaná ve virtuální síti s vlastním serverem DNS, musí vyřešit názvy hostitelů těchto služeb Azure. Postupujte podle [těchto](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) pokynů k vlastnímu nastavení DNS. Podívejte se na níže uvedenou tabulku portů a další požadavky na síť.

> [!IMPORTANT]
> Pokud plánujete použít vlastní DNS server (y) pro virtuální síť, měli byste ji nastavit **před** nasazením služby api management do něj. V opačném případě je třeba aktualizovat službu API Management při každé změně serveru DNS spuštěním [operace Použít konfiguraci sítě](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Porty potřebné pro správu rozhraní API**: Příchozí a odchozí provoz do podsítě, ve které je nasazena správa rozhraní API, lze řídit pomocí [skupiny zabezpečení sítě][Network Security Group]. Pokud některý z těchto portů nejsou k dispozici, správa rozhraní API nemusí fungovat správně a může být nepřístupný. S jeden nebo více z těchto portů blokovánje je další běžný problém s chybnou konfigurací při použití správy rozhraní API s virtuální sítí.

<a name="required-ports"> </a> Když je instance služby API Management hostována ve virtuální síti, používají se porty v následující tabulce.

| Zdrojový / cílový port (porty) | Směr          | Transportní protokol |   [Značky služeb](../virtual-network/security-overview.md#service-tags) <br> Zdroj / cíl   | Účel\*( )                                                 | Typ virtuální sítě |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Příchozí            | TCP                | INTERNET / VIRTUAL_NETWORK            | Klientská komunikace se správou API                      | Externí             |
| * / 3443                     | Příchozí            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Koncový bod správy pro Portál Azure a Powershell         | Externí & interní  |
| * / 443                  | Odchozí           | TCP                | VIRTUAL_NETWORK / Skladování             | **Závislost na úložišti Azure**                             | Externí & interní  |
| * / 443                  | Odchozí           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) (pokud je to možné)                   | Externí & interní  |
| * / 1433                     | Odchozí           | TCP                | VIRTUAL_NETWORK / SQL                 | **Přístup ke koncovým bodům Azure SQL**                           | Externí & interní  |
| * / 5671, 5672, 443          | Odchozí           | TCP                | VIRTUAL_NETWORK / EventHub            | Závislost pro [zásady a](api-management-howto-log-event-hubs.md) agenta centra událostí protokolu do centra událostí | Externí & interní  |
| * / 445                      | Odchozí           | TCP                | VIRTUAL_NETWORK / Skladování             | Závislost na sdílené složce Azure pro [GIT](api-management-configuration-repository-git.md)                      | Externí & interní  |
| * / 1886                     | Odchozí           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Potřeba publikovat stav stavu do stavu zdrojů          | Externí & interní  |
| * / 443                     | Odchozí           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publikovat [protokoly diagnostiky a metriky](api-management-howto-use-azure-monitor.md)                       | Externí & interní  |
| * / 25                       | Odchozí           | TCP                | VIRTUAL_NETWORK / INTERNET            | Připojení k přenosu SMTP pro odesílání e-mailů                    | Externí & interní  |
| * / 587                      | Odchozí           | TCP                | VIRTUAL_NETWORK / INTERNET            | Připojení k přenosu SMTP pro odesílání e-mailů                    | Externí & interní  |
| * / 25028                    | Odchozí           | TCP                | VIRTUAL_NETWORK / INTERNET            | Připojení k přenosu SMTP pro odesílání e-mailů                    | Externí & interní  |
| * / 6381 - 6383              | Odchozí & | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Přístup k službě Redis pro zásady [omezení sazeb](api-management-access-restriction-policies.md#LimitCallRateByKey) mezi počítači         | Externí & interní  |
| * / *                        | Příchozí            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure Infrastructure Load Balancer                          | Externí & interní  |

>[!IMPORTANT]
> Porty, pro které je **tučně** *Purpose,* jsou vyžadovány pro úspěšné nasazení služby API Management. Blokování ostatních portů však způsobí snížení schopnosti používat a monitorovat spuštěnou službu.

+ **Funkce TLS**: Chcete-li povolit vytváření a ověřování řetězu certifikátů TLS/SSL, potřebuje služba API Management odchozí připojení k síti k ocsp.msocsp.com, mscrl.microsoft.com a crl.microsoft.com. Tato závislost není vyžadována, pokud jakýkoli certifikát, který nahrajete do správy rozhraní API, obsahuje celý řetězec do kořenového adresáře certifikační autority.

+ **Přístup k DNS**: Pro komunikaci se servery DNS je vyžadován odchozí přístup na portu 53. Pokud na druhém konci brány VPN existuje vlastní server DNS, musí být server DNS dostupný ze správy rozhraní API pro hostování podsítě.

+ **Metriky a monitorování stavu**: Odchozí síťové připojení ke koncovým bodům Azure Monitoring, které se řeší v následujících doménách. Jak je znázorněno v tabulce, tyto adresy URL jsou reprezentovány pod značkou služby AzureMonitor pro použití se skupinami zabezpečení sítě.

    | Prostředí Azure | Koncové body                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Veřejný partnerský vztah Azure      | <ul><li>gcs.prod.monitoring.core.windows.net(**nové**)</li><li>prod.warmpath.msftcloudes.com(**zastaralá**)</li><li>shoebox2.metrics.microsoftmetrics.com(**nové**)</li><li>shoebox2.metrics.nsatc.net(**zastaralá**)</li><li>prod3.metrics.microsoftmetrics.com(**nové**)</li><li>prod3.metrics.nsatc.net(**zastaralá**)</li><li>prod3-black.prod3.metrics.microsoftmetrics.com(**nové**)</li><li>prod3-black.prod3.metrics.nsatc.net(**zastaralá**)</li><li>prod3-red.prod3.metrics.microsoftmetrics.com(**nové**)</li><li>prod3-red.prod3.metrics.nsatc.net(**zastaralá**)</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com `East US 2` kde je eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.microsoftmetrics.com(**nové**)</li><li>shoebox2.metrics.nsatc.net(**zastaralá**)</li><li>prod3.metrics.microsoftmetrics.com(**nové**)</li><li>prod3.metrics.nsatc.net(**zastaralá**)</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.microsoftmetrics.com(**nové**)</li><li>shoebox2.metrics.nsatc.net(**zastaralá**)</li><li>prod3.metrics.microsoftmetrics.com(**nové**)</li><li>prod3.metrics.nsatc.net(**zastaralá**)</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > Změna clusterů výše s dns zone **.nsatc.net** na **.microsoftmetrics.com** je většinou DNS Change. Adresa IP clusteru se nezmění.

+ **Místní značky služeb**: Pravidla nsg umožňující odchozí připojení ke značkám služby Storage, SQL a Event Hubs mohou používat místní verze těchto značek odpovídající oblasti obsahující instanci správy rozhraní API (například Storage.WestUS pro instanci správy rozhraní API v oblasti Západní USA). V nasazení s více oblastmi by skupina nsg v každé oblasti měla povolit provoz na značky služeb pro tuto oblast a primární oblast.

+ **SMTP Relay**: Odchozí síťové připojení pro přenos SMTP, `smtpi-co1.msn.com` `smtpi-ch1.msn.com`které `smtpi-db3.msn.com` `smtpi-sin.msn.com` řeší pod hostitelem , , a`ies.global.microsoft.com`

+ **Vývojářský portál CAPTCHA**: Odchozí síťové připojení pro captcha portálu `client.hip.live.com` pro `partner.hip.live.com`vývojáře, který řeší pod hostiteli a .

+ **Diagnostika portálu Azure**: Chcete-li povolit tok diagnostických protokolů z portálu Azure `dc.services.visualstudio.com` při použití rozšíření api management z vnitřní virtuální sítě, je vyžadován odchozí přístup na portu 443. To pomáhá při řešení problémů, kterým můžete čelit při použití rozšíření.

+ **Vynutit tunelování provozu na místní bránu firewall pomocí expresní trasy nebo síťové virtuální zařízení**: Běžná konfigurace zákazníka je definovat vlastní výchozí trasu (0.0.0.0/0), která vynutí veškerý provoz z delegované podsítě api správy toku přes místní bránu firewall nebo síťové virtuální zařízení. Tento tok provozu vždy přeruší připojení se správou rozhraní Azure API, protože odchozí provoz je blokován místně nebo na nanesené na adresu na nerozpoznatelnou sadu adres, které už nefungují s různými koncovými body Azure. Řešení vyžaduje, abyste udělali pár věcí:

  * Povolte koncové body služby v podsíti, ve které je nasazena služba API Management. [Koncové body služby][ServiceEndpoints] je potřeba povolit pro Azure Sql, Azure Storage, Azure EventHub a Azure ServiceBus. Povolení koncových bodů přímo z delegované podsítě API pro tyto služby jim umožňuje používat páteřní síť Microsoft Azure poskytující optimální směrování pro provoz služeb. Pokud používáte koncové body služby s vynucenou tunelovou správou rozhraní Api, výše uvedený provoz služeb Azure není vynuceně tunelové propojení. Ostatní provoz závislostí služby správy rozhraní API je vynuceně tunelové propojení a nelze je ztratit nebo služba api management nebude fungovat správně.
    
  * Veškerý provoz roviny řízení z Internetu do koncového bodu správy služby API Management jsou směrovány prostřednictvím konkrétní sady příchozích IP adresy hostovaných službou API Management. Při přenosu je vynuceno tunelové propojení odpovědi nebude symetricky mapovat zpět na tyto příchozí zdrojové IP adresy. Chcete-li překonat omezení, musíme přidat následující uživatelem definované trasy[(UDR)][UDRs]řídit provoz zpět do Azure nastavením cíle těchto hostitelských tras na "Internet". Sada příchozích IP adres pro řízení rovinného provozu je dokumentována [adresaMI IP roviny řízení](#control-plane-ips)

  * Pro ostatní závislosti služby správy rozhraní API, které jsou vynuceně tunelové propojení, by měl existovat způsob, jak přeložit název hostitele a oslovit koncový bod. Patří mezi ně
      - Metriky a monitorování stavu
      - Diagnostika portálu Azure
      - SMTP relé
      - Vývojářský portál CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Poradce při potížích
* **Počáteční instalace**: Pokud počáteční nasazení služby API Management do podsítě neproběhne úspěšně, doporučujeme nejprve nasadit virtuální počítač do stejné podsítě. Další vzdálená plocha do virtuálního počítače a ověření, že je připojení k jednomu z každého prostředku níže ve vašem předplatném Azure
    * Objekt blob úložiště Azure
    * Azure SQL Database
    * Tabulka úložišť Azure

  > [!IMPORTANT]
  > Po ověření připojení nezapomeňte před nasazením správy rozhraní API do podsítě odebrat všechny prostředky nasazené v podsíti.

* **Přírůstkové aktualizace**: Při provádění změn v síti, odkazovat na [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/networkstatus), chcete-li ověřit, že služba api management neztratil přístup k žádné z kritických prostředků, které závisí na. Stav připojení by měl být aktualizován každých 15 minut.

* **Odkazy na navigaci prostředků**: Při nasazování do podsítě stylu Správce prostředků správa rozhraní API rezervuje podsíť vytvořením odkazu navigace prostředků. Pokud podsíť již obsahuje prostředek od jiného zprostředkovatele, nasazení **se nezdaří**. Podobně když přesunete službu správy rozhraní API do jiné podsítě nebo ji odstraníte, odebereme toto navigační propojení prostředků.

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> Požadavek na velikost podsítě
Azure si rezervuje některé IP adresy v rámci každé podsítě a tyto adresy nelze použít. První a poslední IP adresy podsítí jsou vyhrazeny pro shodu protokolu spolu s dalšími třemi adresami používanými pro služby Azure. Další informace naleznete v tématu [Existují nějaká omezení pro používání IP adres v těchto podsítích?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Kromě IP adres používaných infrastrukturou virtuální sítě Azure používá každá instance správy rozhraní Api v podsíti dvě IP adresy na jednotku sku Premium nebo jednu IP adresu pro skladovou položku vývojáře. Každá instance si vyhrazuje další IP adresu pro externí vyrovnávání zatížení. Při nasazování do interní virtuální sítě vyžaduje další IP adresu pro interní nástroj pro vyrovnávání zatížení.

Vzhledem k tomu, výpočet nad minimální velikost podsítě, ve kterém api management lze nasadit je /29, který poskytuje tři použitelné IP adresy.

Každá další škálovací jednotka správy rozhraní API vyžaduje další dvě IP adresy.

## <a name="routing"></a><a name="routing"> </a> Směrování
+ Veřejná IP adresa (VIP) s vyrovnáváním zatížení bude vyhrazena pro poskytování přístupu ke všem koncovým bodům služby.
+ Ip adresa z rozsahu IP podsítě (DIP) bude použita pro přístup k prostředkům v rámci virtuální sítě a veřejná IP adresa (VIP) bude použita pro přístup k prostředkům mimo virtuální síť.
+ Veřejná IP adresa s vyrovnáváním zatížení najdete na okně Přehled/Essentials na webu Azure Portal.

## <a name="limitations"></a><a name="limitations"> </a>Omezení
* Podsíť obsahující instance správy rozhraní API nemůže obsahovat žádné jiné typy prostředků Azure.
* Podsíť a služba api management musí být ve stejném předplatném.
* Podsíť obsahující instance správy rozhraní API nelze přesunout mezi předplatnými.
* Pro nasazení správy rozhraní API s více oblastmi nakonfigurovaných v režimu interní virtuální sítě jsou uživatelé zodpovědní za správu vyrovnávání zatížení ve více oblastech, protože vlastní směrování.
* Připojení z prostředku v globálně partnerské virtuální síti v jiné oblasti ke službě API Management v interním režimu nebude fungovat z důvodu omezení platformy. Další informace najdete v tématu [prostředky v jedné virtuální síti nemůže komunikovat s Azure internal balancer v partnerské virtuální síti](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> Řízení roviny IP adres

IP adresy jsou rozděleny podle **prostředí Azure**. Při povolení příchozích požadavků musí být adresa IP označená **globální** na seznamu povolených spolu s adresou IP specifickou **pro oblast.**

| **Prostředí Azure**|   **Oblasti**|  **IP adresa**|
|-----------------|-------------------------|---------------|
| Veřejný partnerský vztah Azure| Střední jižní USA (globální)| 104.214.19.224|
| Veřejný partnerský vztah Azure| Střední severní USA (globální)| 52.162.110.80|
| Veřejný partnerský vztah Azure| USA – středozápad| 52.253.135.58|
| Veřejný partnerský vztah Azure| Jižní Korea – střed| 40.82.157.167|
| Veřejný partnerský vztah Azure| Spojené království – západ| 51.137.136.0|
| Veřejný partnerský vztah Azure| Japonsko – západ| 40.81.185.8|
| Veřejný partnerský vztah Azure| USA – středosever| 40.81.47.216|
| Veřejný partnerský vztah Azure| Spojené království – jih| 51.145.56.125|
| Veřejný partnerský vztah Azure| Indie – západ| 40.81.89.24|
| Veřejný partnerský vztah Azure| USA – východ| 52.224.186.99|
| Veřejný partnerský vztah Azure| Západní Evropa| 51.145.179.78|
| Veřejný partnerský vztah Azure| Japonsko – východ| 52.140.238.179|
| Veřejný partnerský vztah Azure| Francie – střed| 40.66.60.111|
| Veřejný partnerský vztah Azure| Kanada – východ| 52.139.80.117|
| Veřejný partnerský vztah Azure| SAE Sever| 20.46.144.85|
| Veřejný partnerský vztah Azure| Brazílie – jih| 191.233.24.179|
| Veřejný partnerský vztah Azure| Jihovýchodní Asie| 40.90.185.46|
| Veřejný partnerský vztah Azure| Jižní Afrika – sever| 102.133.130.197|
| Veřejný partnerský vztah Azure| Střední Kanada| 52.139.20.34|
| Veřejný partnerský vztah Azure| Jižní Korea – jih| 40.80.232.185|
| Veřejný partnerský vztah Azure| Indie – střed| 13.71.49.1|
| Veřejný partnerský vztah Azure| USA – západ| 13.64.39.16|
| Veřejný partnerský vztah Azure| Austrálie – jihovýchod| 20.40.160.107|
| Veřejný partnerský vztah Azure| Austrálie – střed| 20.37.52.67|
| Veřejný partnerský vztah Azure| Indie – jih| 20.44.33.246|
| Veřejný partnerský vztah Azure| USA – střed| 13.86.102.66|
| Veřejný partnerský vztah Azure| Austrálie – východ| 20.40.125.155|
| Veřejný partnerský vztah Azure| USA – západ 2| 51.143.127.203|
| Veřejný partnerský vztah Azure| Východní USA 2 EUAP| 52.253.229.253|
| Veřejný partnerský vztah Azure| Centrální US EUAP| 52.253.159.160|
| Veřejný partnerský vztah Azure| USA – středojih| 20.188.77.119|
| Veřejný partnerský vztah Azure| USA – východ 2| 20.44.72.3|
| Veřejný partnerský vztah Azure| Severní Evropa| 52.142.95.35|
| Veřejný partnerský vztah Azure| Východní Asie| 52.139.152.27|
| Veřejný partnerský vztah Azure| Francie – jih| 20.39.80.2|
| Veřejný partnerský vztah Azure| Švýcarsko Západ| 51.107.96.8|
| Veřejný partnerský vztah Azure| Austrálie – střed 2| 20.39.99.81|
| Veřejný partnerský vztah Azure| SAE Centrální| 20.37.81.41|
| Veřejný partnerský vztah Azure| Švýcarsko Sever| 51.107.0.91|
| Veřejný partnerský vztah Azure| Jižní Afrika – západ| 102.133.0.79|
| Veřejný partnerský vztah Azure| Německo – západ – střed| 51.116.96.0|
| Veřejný partnerský vztah Azure| Německo Sever| 51.116.0.0|
| Veřejný partnerský vztah Azure| Norsko východ| 51.120.2.185|
| Veřejný partnerský vztah Azure| Norsko Západ| 51.120.130.134|
| Azure China 21Vianet| Čína Sever (Globální)| 139.217.51.16|
| Azure China 21Vianet| Čína východ (Globální)| 139.217.171.176|
| Azure China 21Vianet| Čína – sever| 40.125.137.220|
| Azure China 21Vianet| Čína – východ| 40.126.120.30|
| Azure China 21Vianet| Čína Sever 2| 40.73.41.178|
| Azure China 21Vianet| Čína východ 2| 40.73.104.4|
| Azure Government| USGov Virginia (Globální)| 52.127.42.160|
| Azure Government| USGov Texas (Globální)| 52.127.34.192|
| Azure Government| USGov Virginie| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USDoD centrální| 52.182.32.132|
| Azure Government| USDoD východ| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Související obsah
* [Připojení virtuální sítě k back-endu pomocí brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Připojení virtuální sítě z různých modelů nasazení](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Jak pomocí inspektoru rozhraní API sledovat volání ve správě rozhraní Azure API](api-management-howto-api-inspector.md)
* [Nejčastější dotazy týkající se virtuální sítě](../virtual-network/virtual-networks-faq.md)
* [Značky služeb](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
