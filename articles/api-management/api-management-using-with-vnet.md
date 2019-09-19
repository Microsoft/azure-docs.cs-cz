---
title: Jak používat API Management Azure s virtuálními sítěmi
description: Naučte se, jak nastavit připojení k virtuální síti ve službě Azure API Management a přistupovat k webovým službám prostřednictvím ní.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/09/2019
ms.author: apimpm
ms.openlocfilehash: cc4426ee1bb13eaf66e664c261c51f8893fdf10b
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129773"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Jak používat API Management Azure s virtuálními sítěmi
Virtuální sítě Azure (virtuální sítě) umožňují umístit jakékoli prostředky Azure do sítě, ve které není Internet směrovatelné, ke kterým budete mít přístup. Tyto sítě je pak možné připojit k místním sítím pomocí různých technologií VPN. Další informace o virtuálních sítích Azure najdete tady: [Přehled služby Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Službu Azure API Management lze nasadit v rámci virtuální sítě (VNET), aby mohla přistupovat k back-end službám v síti. Portál pro vývojáře a brána API je možné nakonfigurovat tak, aby byly přístupné buď z Internetu, nebo jenom v rámci virtuální sítě.

> [!NOTE]
> Adresa URL pro import dokumentu v rozhraní API musí být hostovaná na veřejně přístupné internetové adrese.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků popsaných v tomto článku musíte mít:

+ Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Instance APIM Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Povolit připojení k virtuální síti

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Povolení připojení VNET pomocí Azure Portal

1. V [Azure Portal](https://portal.azure.com/)přejděte na svou instanci APIM.
2. Vyberte **Virtual Network**.
3. Nakonfigurujte instanci API Management, která se má nasadit v rámci virtuální sítě.

    ![Nabídka virtuální síť API Management][api-management-using-vnet-menu]
4. Vyberte požadovaný typ přístupu:

   * **Off**: Toto je výchozí nastavení. API Management není nasazený ve virtuální síti.

   * **Externí**: API Management brána a portál pro vývojáře jsou přístupné z veřejného Internetu prostřednictvím externího nástroje pro vyrovnávání zatížení. Brána má přístup k prostředkům v rámci virtuální sítě.

     ![Veřejný partnerský vztah][api-management-vnet-public]

   * **Interní**: API Management brána a portál pro vývojáře jsou přístupné jenom z virtuální sítě prostřednictvím interního nástroje pro vyrovnávání zatížení. Brána má přístup k prostředkům v rámci virtuální sítě.

     ![Soukromý partnerský vztah][api-management-vnet-private]

     Zobrazí se seznam všech oblastí, ve kterých se zřídí vaše služba API Management. Vyberte virtuální síť a podsíť pro každou oblast. V seznamu se naplní klasické i Správce prostředků virtuální sítě, které jsou dostupné ve vašich předplatných Azure, které jsou nastavené v oblasti, kterou konfigurujete.

     > [!IMPORTANT]
     > Při nasazování instance Azure API Management do virtuální sítě Správce prostředků musí být služba ve vyhrazené podsíti, která neobsahuje žádné další prostředky s výjimkou instancí Azure API Management. Pokud se provede pokus o nasazení instance služby Azure API Management do podsítě Správce prostředků virtuální sítě, která obsahuje další prostředky, nasazení se nezdaří.
     >

     ![Vybrat síť VPN][api-management-setup-vpn-select]

5. V horním navigačním panelu klikněte na **Uložit** .
6. V horním navigačním panelu klikněte na **použít konfiguraci sítě** .

> [!NOTE]
> Adresa VIP instance API Management se změní pokaždé, když je virtuální síť povolená nebo zakázaná.
> Tato IP adresa se změní také při přesunu API Management z **externích** do **interního** nebo naopak.
>

> [!IMPORTANT]
> Pokud API Management odeberete z virtuální sítě nebo změníte nasazení v nástroji, dříve použitá síť VNET může zůstat uzamčena po dobu až šesti hodin. Během této doby nebude možné virtuální síť odstranit ani do ní nasadit nový prostředek. Toto chování platí pro klienty, kteří používají rozhraní API verze 2018-01-01 a starší. Klienti používající rozhraní API verze 2019-01-01 a novější, virtuální síť se uvolní hned po odstranění přidružené API Management služby.

## <a name="enable-vnet-powershell"> </a>Povolení připojení VNet pomocí rutin PowerShellu
Připojení k virtuální síti můžete taky povolit pomocí rutin PowerShellu.

* **Vytvoření služby API Management v rámci virtuální**sítě: Pomocí rutiny [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) vytvořte službu Azure API Management v rámci virtuální sítě.

* **Nasazení existující služby API Management v rámci virtuální**sítě: Pomocí rutiny [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) můžete přesunout existující službu Azure API Management do Virtual Network.

## <a name="connect-vnet"> </a>Připojení k webové službě hostované ve virtuální síti
Po připojení služby API Management k virtuální síti se přístup k back-endu službám v rámci této sítě neliší od přístupu k veřejným službám. Stačí zadat místní IP adresu nebo název hostitele (Pokud je server DNS nakonfigurovaný pro virtuální síť) webové služby na pole **Adresa URL webové služby** při vytváření nového rozhraní API nebo úpravou existujícího rozhraní API.

![Přidat rozhraní API z sítě VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Běžné problémy s konfigurací sítě
Následuje seznam běžných potíží s chybou konfigurace, ke kterým může dojít při nasazování služby API Management do Virtual Network.

* **Nastavení vlastního serveru DNS**: Služba API Management závisí na několika službách Azure. Pokud je API Management hostovaný ve virtuální síti s vlastním serverem DNS, musí přeložit názvy hostitelů těchto služeb Azure. Postupujte prosím [](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) podle pokynů pro vlastní nastavení DNS. Podívejte se na tabulku porty níže a další požadavky na síť pro referenci.

> [!IMPORTANT]
> Pokud máte v úmyslu používat pro virtuální síť vlastní servery DNS, měli byste ji nastavit **před** nasazením služby API Management do ní. V opačném případě je potřeba aktualizovat API Management službu pokaždé, když změníte servery DNS spuštěním [operace použít konfiguraci sítě](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates) .

* **Porty vyžadované pro API Management**: Příchozí a odchozí provoz do podsítě, ve které je nasazená API Management, se dá řídit pomocí [skupiny zabezpečení sítě][Network Security Group]. Pokud některý z těchto portů není dostupný, API Management nemusí správně fungovat a může být nepřístupný. Pokud se některý z těchto portů zablokuje, je při použití API Management s virtuální sítí další běžný problém s konfigurací.

<a name="required-ports"></a> Když je instance služby API Management hostovaná ve virtuální síti, použijí se porty v následující tabulce.

| Zdrojový nebo cílový port (y) | Direction          | Transportní protokol |   [Značky služeb](../virtual-network/security-overview.md#service-tags) <br> Zdroj/cíl   | Účel (*)                                                 | Typ Virtual Network |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Příchozí            | TCP                | INTERNET/VIRTUAL_NETWORK            | Komunikace klienta s API Management                      | Externí             |
| * / 3443                     | Příchozí            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Koncový bod správy pro Azure Portal a PowerShell         | Externí & interní  |
| * / 80, 443                  | Odchozí           | TCP                | VIRTUAL_NETWORK/úložiště             | **Závislost na Azure Storage**                             | Externí & interní  |
| * / 80, 443                  | Odchozí           | TCP                | VIRTUAL_NETWORK/Azureactivedirectory selhala | Azure Active Directory (Pokud je k dispozici)                   | Externí & interní  |
| * / 1433                     | Odchozí           | TCP                | VIRTUAL_NETWORK / SQL                 | **Přístup k koncovým bodům SQL Azure**                           | Externí & interní  |
| * / 5672                     | Odchozí           | TCP                | VIRTUAL_NETWORK / EventHub            | Závislost pro protokolování do zásad centra událostí a agenta monitorování | Externí & interní  |
| * / 445                      | Odchozí           | TCP                | VIRTUAL_NETWORK/úložiště             | Závislost na sdílené složce Azure pro GIT                      | Externí & interní  |
| * / 1886                     | Odchozí           | TCP                | VIRTUAL_NETWORK/INTERNET            | Je potřeba publikovat stav Resource Health          | Externí & interní  |
| */443                     | Odchozí           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publikování diagnostických protokolů a metrik                        | Externí & interní  |
| * / 25                       | Odchozí           | TCP                | VIRTUAL_NETWORK/INTERNET            | Připojení k předávání SMTP pro odesílání e-mailů                    | Externí & interní  |
| * / 587                      | Odchozí           | TCP                | VIRTUAL_NETWORK/INTERNET            | Připojení k předávání SMTP pro odesílání e-mailů                    | Externí & interní  |
| * / 25028                    | Odchozí           | TCP                | VIRTUAL_NETWORK/INTERNET            | Připojení k předávání SMTP pro odesílání e-mailů                    | Externí & interní  |
| * / 6381 - 6383              | Příchozí & odchozí | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Přístup k mezipaměti Azure pro instance Redis mezi RoleInstances          | Externí & interní  |
| * / *                        | Příchozí            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Load Balancer infrastruktury Azure                          | Externí & interní  |

>[!IMPORTANT]
> Pro úspěšné nasazení API Management služby se vyžadují porty, pro které je *účel* **tučný** . Blokování dalších portů ale způsobí, že bude možné používat a monitorovat běžící službu.

+ **Funkce SSL**: Pokud chcete povolit sestavení a ověření řetězu certifikátů SSL, služba API Management potřebuje odchozí síťové připojení k ocsp.msocsp.com, mscrl.microsoft.com a crl.microsoft.com. Tato závislost není povinná, pokud kterýkoli certifikát, který nahráváte do API Management, obsahuje úplný řetěz k kořenovému adresáři certifikační autority.

+ **Přístup DNS**: Pro komunikaci se servery DNS se vyžaduje odchozí přístup na portu 53. Pokud na druhém konci brány VPN existuje vlastní server DNS, musí být server DNS dosažitelný z podsítě hostující API Management.

+ **Metriky a sledování stavu**: Odchozí připojení k síti ke koncovým bodům monitorování Azure, které se řeší v následujících doménách:

    | Prostředí Azure | Koncové body                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Veřejné Azure      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. Warm.ingestion.msftcloudes.com kde `East US 2` je eastus2.Warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure (Čína)       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **Předávání SMTP**: Odchozí připojení k síti pro přenos SMTP, které se řeší v rámci `smtpi-co1.msn.com`hostitele `smtpi-ch1.msn.com` `smtpi-db3.msn.com`, `smtpi-sin.msn.com` , a`ies.global.microsoft.com`

+ **CAPTCHA portálu pro vývojáře**: Odchozí připojení k síti pro CAPTCHA portálu pro vývojáře, které se řeší v `client.hip.live.com` hostitelích `partner.hip.live.com`a.

+ **Diagnostika Azure Portal**: Pokud chcete povolit tok diagnostických protokolů z Azure Portal při použití rozšíření API Management v rámci Virtual Network, je vyžadován odchozí přístup k `dc.services.visualstudio.com` portu 443. To pomáhá při řešení problémů, se kterými se můžete setkat při používání rozšíření.

+ **Vynucení tunelového přenosu do brány firewall Prem pomocí Express Route nebo síťového virtuálního zařízení**: Běžnou zákaznickou konfigurací je definování vlastní výchozí trasy (0.0.0.0/0), která vynucuje veškerý provoz z API Management delegované podsítě k toku přes místní bránu firewall nebo síťové virtuální zařízení. Tento tok přenosů invariably přerušení připojení k Azure API Management, protože odchozí přenosy jsou buď blokované místně, nebo překlad adres (NAT) na nerozpoznatelnou sadu adres, které už nefungují s různými koncovými body Azure. Řešení vyžaduje, abyste provedete několik věcí:

  * Povolte koncové body služby v podsíti, ve které je nainstalovaná služba API Management. Pro Azure SQL, Azure Storage, Azure EventHub a Azure ServiceBus musí být povolené [koncové body služby][ServiceEndpoints] . Povolení koncových bodů přímo z API Management delegované podsítě těmto službám umožňuje používat páteřní síť Microsoft Azure, která poskytuje optimální směrování pro provoz služeb. Pokud používáte koncové body služby s vynuceným tunelovou správou rozhraní API, výše uvedený provoz služeb Azure není vynuceně tunelování. Druhý provoz závislosti služby API Management je vynucené tunelování a nelze ho ztratit nebo služba API Management nebude správně fungovat.
    
  * Všechny přenosy řídicích rovin z Internetu do koncového bodu správy služby API Management jsou směrovány přes konkrétní sadu příchozích IP adres hostovaných API Management. V případě vynuceného tunelování nebudou odpovědi symetricky mapovány zpět na tyto příchozí IP adresy příchozích dat. Abychom překonali omezení, musíme přidat následující uživatelsky definované trasy ([udr][UDRs]) k řízení provozu zpátky do Azure tím, že nastavíte cíl těchto hostitelských tras na Internet. Sada příchozích IP adres pro přenos rovin řízení je následující:
    
     | Prostředí Azure | IP adresy pro správu                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Veřejné Azure      | 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 52.159.16.255/32, 40.82.157.167/32, 51.137.136.0/32, 40.81.185.8/32, 40.81.47.216/32, 51.145.56.125/32, 40.81.89.24/32, 52.224.186.99/32, 51.145.179.78/32, 52.140.238.179/32, 40.66.60.111/32, 52.139.80.117/32, 20.46.144.85/32, 191.233.24.179/32, 40.90.185.46/32, 102.133.130.197/32, 52.139.20.34/32, 40.80.232.185/32, 13.71.49.1/32, 13.64.39.16/32, 20.40.160.107/32, 20.37.52.67/32, 20.44.33.246/32, 13.86.102.66/32, 20.40.125.155/32, 51.143.127.203/32, 52.253.225.124/32, 52.253.159.160/32, 20.188.77.119/32, 20.44.72.3/32, 52.142.95.35/32, 52.139.152.27/32, 20.39.80.2/32, 51.107.96.8/ 32, 20.39.99.81/32, 20.37.81.41/32, 51.107.0.91/32, 102.133.0.79/32, 51.116.96.0/32, 51.116.0.0/32 |
    | Azure Government  | 52.127.42.160/32, 52.127.34.192/32 |
    | Azure (Čína)       | 139.217.51.16/32, 139.217.171.176/32 |

  * Pro jiné závislosti API Management služby, které jsou vynuceným tunelovým propojením, by měl existovat způsob, jak tento název hostitele vyřešit a jak se obrátit na koncový bod. Mezi ně patří
      - Metriky a sledování stavu
      - Diagnostika Azure Portal
      - Předávání SMTP
      - Portál pro vývojáře CAPTCHA

## <a name="troubleshooting"> </a>Řešení potíží
* **Počáteční nastavení**: Když počáteční nasazení služby API Management do podsítě neproběhne úspěšně, doporučuje se nejdřív nasadit virtuální počítač do stejné podsítě. Další Vzdálená plocha na virtuální počítač a ověřte, že existuje připojení k jednomu z prostředků níže v předplatném Azure.
    * Azure Storage objekt BLOB
    * Azure SQL Database
    * Azure Storage tabulka

  > [!IMPORTANT]
  > Po ověření připojení nezapomeňte odebrat všechny prostředky nasazené v podsíti, než nasadíte API Management do podsítě.

* **Přírůstkové aktualizace**: Při provádění změn v síti se podívejte na [NETWORKSTATUS API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus), abyste ověřili, že služba API Management neztratila přístup k žádnému z kritických prostředků, na kterých závisí. Stav připojení by se měl aktualizovat každých 15 minut.

* **Navigační odkazy prostředků**: Když nasadíte do podsítě virtuální sítě ve stylu Správce prostředků, API Management si vyhradí podsíť tím, že se vytvoří odkaz pro navigaci v prostředku. Pokud podsíť již obsahuje prostředek od jiného poskytovatele, nasazení se **nezdaří**. Obdobně platí, že když přesunete API Management službu do jiné podsítě nebo ji odstraníte, odebereme tento odkaz na tento prostředek.

## <a name="subnet-size"></a> Požadavek na velikost podsítě
Azure rezervuje některé IP adresy v rámci každé podsítě a tyto adresy se nedají použít. První a poslední IP adresa podsítí jsou vyhrazené pro shodu protokolu a tři další adresy, které se používají pro služby Azure. Další informace najdete v tématu [jakákoli omezení používání IP adres v těchto](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) podsítích.

Kromě IP adres, které používá infrastruktura virtuální sítě Azure, každá instance služby API Management v podsíti používá dvě IP adresy na jednotku SKU úrovně Premium nebo jednu IP adresu pro SKU pro vývojáře. Každá instance si vyhrazuje další IP adresu pro externí nástroj pro vyrovnávání zatížení. Při nasazování do interní virtuální sítě musí pro interní nástroj pro vyrovnávání zatížení vyžadovat další IP adresu.

Vzhledem k výpočtu výše minimální velikosti podsítě, ve které API Management lze nasadit, je/29, což poskytuje tři IP adresy.

## <a name="routing"></a> Směrování
+ Veřejná IP adresa (VIP) s vyrovnáváním zatížení bude vyhrazena pro poskytování přístupu ke všem koncovým bodům služby.
+ IP adresa z rozsahu IP adres podsítě (DIP) se použije pro přístup k prostředkům v rámci virtuální sítě a veřejná IP adresa (VIP) se použije pro přístup k prostředkům mimo virtuální síť.
+ Veřejnou IP adresu vyrovnávání zatížení najdete v okně Přehled/základní informace v Azure Portal.

## <a name="limitations"> </a>Omezení
* Podsíť obsahující API Management Instances nemůže obsahovat žádné další typy prostředků Azure.
* Podsíť a služba API Management musí být ve stejném předplatném.
* Podsíť obsahující API Management instance se nedá přesunout mezi předplatnými.
* Pro API Management nasazení ve více oblastech, která jsou nakonfigurovaná v režimu interní virtuální sítě, se uživatelům zodpovídá za správu vyrovnávání zatížení napříč různými oblastmi, a to při vlastním směrování.
* Připojení z prostředku v globálně partnerské virtuální síti v jiné oblasti do API Management služby v interním režimu nebude fungovat v důsledku omezení platformy. Další informace najdete v tématu [prostředky v jedné virtuální síti nemůžou komunikovat s interním nástrojem pro vyrovnávání zatížení Azure v partnerské virtuální síti](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) .


## <a name="related-content"> </a>Související obsah
* [Připojení Virtual Network k back-endu pomocí služby VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Připojení Virtual Network z různých modelů nasazení](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Jak používat inspektora rozhraní API k trasování volání v Azure API Management](api-management-howto-api-inspector.md)
* [Virtual Network Faq](../virtual-network/virtual-networks-faq.md)
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
