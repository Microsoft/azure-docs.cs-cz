---
title: Jak používat Azure API Management s virtuálními sítěmi
description: Zjistěte, jak nastavit připojení k virtuální síti ve webové služby Azure API Management a přístup k jejím prostřednictvím.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: apimpm
ms.openlocfilehash: db48db5ce9402267570ac9e41f9f4b5bec2781ad
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527944"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Jak používat Azure API Management s virtuálními sítěmi
Virtuální sítě Azure (Vnet) umožňuje umístit některé z vašich prostředků Azure, které řídí přístup k síti možnosti směrování Internetu jiných. Potom se dá propojit tyto sítí k místním sítím pomocí různých technologií VPN. Další informace o Azure Virtual Networks začínat tyto informace tady: [Přehled služby Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Azure API Management se dá nasadit ve virtuální síti (VNET), aby měl přístup k back-end služby v rámci sítě. Portál pro vývojáře a Brána rozhraní API, můžete nakonfigurovat byla přístupná z Internetu nebo pouze v rámci virtuální sítě.

> [!NOTE]
> Azure API Management podporuje classic a Azure Resource virtuálních sítí správce.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li provést postup popsaný v tomto článku, budete potřebovat:

+ Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Instanci služby APIM. Další informace najdete v tématu [vytvoření instance Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Povolení připojení k virtuální síti

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Povolit připojení k virtuální síti pomocí webu Azure portal

1. Přejděte k vaší instanci APIM v [webu Azure portal](https://portal.azure.com/).
2. Vyberte **virtuální sítě**.
3. Konfigurace instance API Management k nasazení ve virtuální síti.

    ![Virtuální síť nabídky služby API Management][api-management-using-vnet-menu]
4. Vyberte typ požadovaného přístupu:

   * **Externí**: brány a vývojářského portálu API Management, jsou přístupné z veřejného Internetu prostřednictvím externím vyrovnáváním zatížení. Brána lze přistupovat k prostředkům v rámci virtuální sítě.

     ![Veřejný partnerský vztah][api-management-vnet-public]

   * **Interní**: brány a vývojářského portálu API Management je přístupný jenom v rámci virtuální sítě prostřednictvím interního nástroje load balancer. Brána lze přistupovat k prostředkům v rámci virtuální sítě.

     ![Soukromý partnerský vztah][api-management-vnet-private]

     Nyní se zobrazí seznam všech oblastech, kde je zřízené služby API Management. Vyberte virtuální síť a podsíť pro každou oblast. V seznamu se vyplní classic i Resource Manager k dispozici ve vašem předplatném Azure, které jsou nastavené v oblasti, kterou konfigurujete virtuální sítě.

     > [!NOTE]
     > **Koncový bod služby** v diagramu výše zahrnuje brána nebo proxy serveru, na webu Azure portal, na portálu pro vývojáře, GIT a Direct koncový bod správy.
     > **Koncový bod správy** v diagramu je koncový bod hostované na službě pro správu konfigurace pomocí webu Azure portal a Powershell.
     > Mějte také na paměti, že i když diagram znázorňuje IP adresy pro své různé koncové body služby API Management **pouze** reaguje na jeho nakonfigurované názvy hostitelů.

     > [!IMPORTANT]
     > Při nasazení instance služby Azure API Management k virtuální síti správce prostředků, služby musí být ve vyhrazené podsíti, která neobsahuje žádné prostředky s výjimkou instance Azure API Management. Pokud je proveden pokus o nasazení instance služby Azure API Management k podsíti virtuální sítě Resource Manageru, která obsahuje další prostředky, nasazení se nezdaří.
     >

     ![Vyberte sítě VPN][api-management-setup-vpn-select]

5. Klikněte na tlačítko **Uložit** v horní části obrazovky.

> [!NOTE]
> Virtuální IP adresy instance API Management se změní pokaždé, když virtuální síť je povoleno nebo zakázáno.
> Virtuální IP adresy se také změní, když API Management je přesunout z **externí** k **interní** nebo naopak
>

> [!IMPORTANT]
> Pokud odeberete API Management z virtuální sítě nebo změnit, který je nasazený v, může zůstat předchozích VNET uzamčené po dobu až dvou hodin. Během této doby nebude možné odstranit virtuální síť nebo nasadit nový prostředek k němu.

## <a name="enable-vnet-powershell"> </a>Povolit připojení virtuální sítě pomocí rutin prostředí PowerShell
Můžete také povolit připojení k virtuální síti pomocí rutin prostředí PowerShell

* **Vytvoření služby API Management v síti VNET**: Použijte rutinu [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) k vytvoření služby Azure API Management v síti VNET.

* **Nasazení služby API Management existující v síti VNET**: Použijte rutinu [aktualizace AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) přesunout existující služby Azure API Management ve virtuální síti.

## <a name="connect-vnet"> </a>Připojit k webové službě hostované v rámci virtuální sítě
Poté, co vaše služba API Management je připojený k virtuální síti, přístup ke službám back-endu v rámci něj se nijak neliší od přístupu k veřejné služby. Stačí zadat místní IP adresa nebo název hostitele (Pokud je nakonfigurovaný DNS server pro virtuální síť) do vaší webové služby **adresu URL webové služby** pole při vytváření nového rozhraní API nebo úpravou existující.

![Přidání rozhraní API od sítě VPN.][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Běžné problémy s konfigurací sítě
Tady je seznam častých problémech, které mohou nastat při nasazení služby API Management do virtuální sítě.

* **Vlastní nastavení serveru DNS**: Služba API Management závisí na několik služeb Azure. API Management je hostovaná ve virtuální síti pomocí vlastního serveru DNS, musí překládat názvy hostitelů těchto služeb Azure. Postupujte prosím podle [to](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) doprovodné materiály k vlastním nastavením DNS. Viz následující tabulka portů a další požadavky na síť pro odkaz.

> [!IMPORTANT]
> Pokud máte v plánu používat vlastní servery DNS pro virtuální síť, měli byste nastavit **před** nasazení služby API Management do něj. Jinak budete muset aktualizovat službu API Management pokaždé, když změníte spuštěním servery DNS [použít operace konfigurace sítě](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Porty vyžadované pro službu API Management**: Příchozí a odchozí provoz do podsítě, ve kterém je nasazená API Management se dá řídit pomocí [skupinu zabezpečení sítě][Network Security Group]. Pokud některý z těchto portů jsou k dispozici, API Management nebude fungovat správně a může být nepřístupný. Jeden nebo více z těchto portů blokované je dalším běžný problémem chybnou konfiguraci při použití služby API Management pomocí virtuální sítě.

<a name="required-ports"> </a> Když jsou instance služby API Management je hostované ve virtuální síti, se používají porty v následující tabulce.

| Zdrojové a cílové porty | Směr          | Přenosový protokol |   [Značky služeb](../virtual-network/security-overview.md#service-tags) <br> Zdroj a cíl   | Účel (*)                                                 | Typ virtuální sítě |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Příchozí            | TCP                | INTERNET / VIRTUAL_NETWORK            | Komunikace klienta do API managementu                      | Externí             |
| * / 3443                     | Příchozí            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Koncový bod správy pro Azure portal a Powershellu         | Externí a interní  |
| * / 80, 443                  | Odchozí           | TCP                | VIRTUAL_NETWORK / Storage             | **Závislost na Azure Storage**                             | Externí a interní  |
| * / 80, 443                  | Odchozí           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | Azure Active Directory (v případě potřeby)                   | Externí a interní  |
| * / 1433                     | Odchozí           | TCP                | VIRTUAL_NETWORK / SQL                 | **Přístup ke koncovým bodům Azure SQL**                           | Externí a interní  |
| * / 5672                     | Odchozí           | TCP                | VIRTUAL_NETWORK / EventHub            | Závislost pro protokol do zásady centra událostí a agenta monitorování | Externí a interní  |
| * / 445                      | Odchozí           | TCP                | VIRTUAL_NETWORK / Storage             | Závislost na sdílenou složku Azure pro GIT                      | Externí a interní  |
| * / 1886                     | Odchozí           | TCP                | VIRTUAL_NETWORK / INTERNET            | Potřebné k publikování stav Resource Health          | Externí a interní  |
| * / 443                     | Odchozí           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publikování diagnostické protokoly a metriky                        | Externí a interní  |
| * / 25                       | Odchozí           | TCP                | VIRTUAL_NETWORK / INTERNET            | Připojení k serveru SMTP pro odeslání e-mailů                    | Externí a interní  |
| * / 587                      | Odchozí           | TCP                | VIRTUAL_NETWORK / INTERNET            | Připojení k serveru SMTP pro odeslání e-mailů                    | Externí a interní  |
| * / 25028                    | Odchozí           | TCP                | VIRTUAL_NETWORK / INTERNET            | Připojení k serveru SMTP pro odeslání e-mailů                    | Externí a interní  |
| * / 6381 - 6383              | Příchozí a odchozí | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Pro instance Redis mezi RoleInstances přístup k Azure Cache          | Externí a interní  |
| * / *                        | Příchozí            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Nástroj pro vyrovnávání zatížení infrastruktury Azure                          | Externí a interní  |

>[!IMPORTANT]
> Porty, pro kterou *účel* je **tučné** jsou požadovány pro službu API Management na úspěšné nasazení. Ostatní porty blokuje ale způsobí snížení v možnost použití a sledování spuštěnou službu.

+ **Funkce SSL**: Povolit sestavení řetězu certifikátů SSL a ověření API Management musí služba ocsp.msocsp.com, mscrl.microsoft.com a crl.microsoft.com odchozího síťového připojení. Tato závislost není vyžadováno, pokud jakýkoliv certifikát, který nahrajete do služby API Management obsahuje úplný řetěz do kořenové certifikační Autority.

+ **DNS Access**: Odchozí přístup na port 53, je nutné pro komunikaci se servery DNS. Pokud vlastní server DNS existuje na druhém konci bránu VPN, DNS server musí být dostupný z podsítě hostování API Management.

+ **Metriky a monitorování stavu**: Odchozího síťového připojení k Azure monitorování koncových bodů, které vyřešit podle následujících domén:

    | Prostředí Azure | Koncové body                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. warm.ingestion.msftcloudes.com kde `East US 2` je eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure (Čína)       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **Nastavením předávání SMTP**: Odchozího síťového připojení pro předávání SMTP, který řeší v rámci hostitele `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` a `ies.global.microsoft.com`

+ **Portál pro vývojáře testu CAPTCHA**: Odchozího síťového připojení pro test CAPTCHA. portál pro vývojáře, která řeší v rámci hostitele `client.hip.live.com`.

+ **Portál Azure Diagnostics**: Povolení toku diagnostické protokoly z webu Azure portal, při použití rozšíření pro správu rozhraní API z uvnitř virtuální sítě, odchozí přístup k `dc.services.visualstudio.com` na portu 443 je povinný. To pomáhá s řešením problémů, na které může setkat při použití rozšíření.

+ **Vynucené tunelování provozu do brány Firewall v místním prostředí pomocí Express Route nebo síťové virtuální zařízení**: Běžnou konfigurací zákazníků je definovat vlastní výchozí trasa (0.0.0.0/0), která vynutí pro veškeré přenosy ze služby API Management delegované podsítě do flow na stránkách místní bráně firewall nebo do síťového virtuálního zařízení. Tento tok provozu vždy přeruší připojení k službě Azure API Management, protože odchozí provoz je blokované v místním nebo NAT by nerozpoznatelný sadu adresy, které přestane fungovat v různých koncových bodů Azure. Řešení je potřeba udělat několik věcí:

  * Povolení koncových bodů služby v podsíti, ve které nasazení služby API Management. [Koncové body služby] [ ServiceEndpoints] musí být povolené pro Azure Sql, Azure Storage, Azure EventHub a služby Azure Service Bus. Povolují se koncové body přímo ze služby API Management umožňuje delegovanou podsítě na tyto služby je, aby používaly páteřní síti Microsoft Azure poskytují optimální směrování provozu služeb. Pokud použijete koncové body služby pomocí vynuceného tunelového propojení Api Management, tunelové propojení výše uvedených služeb Azure, který provoz se nebude nuceně. API Management, které provoz závislostí služby je nucen tunelovat a nesmí se ztratit nebo služba API Management nebude správně fungovat.
    
  * Všechny ovládací prvek roviny provoz z Internetu na koncový bod správy ze služby API Management je směrován přes konkrétní sadu příchozí IP adresy hostované službou API Management. Když provoz procházejí vynuceným tunelovým propojením odpovědi nebude symetricky mapovat zpět do těchto příchozí zdrojové IP adresy. K překonání omezení, potřebujeme přidat následující trasy definované uživatelem ([trasy definované uživatelem][UDRs]) řídit provoz zpět do Azure tak, že nastavíte cíl tyto hostitele trasy, aby "Internet". Sada příchozí IP adresy pro řízení provozu roviny je následujícím způsobem:
    
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32

  * Pro další závislosti služby API Management, které procházejí vynuceným tunelovým propojením měla by existovat způsob, jak vyřešit název hostitele a kontaktujte koncový bod. Patří mezi ně
      - Monitorování stavu a metriky
      - Portál Azure Diagnostics
      - Nastavením předávání SMTP
      - Portál pro vývojáře test CAPTCHA.

## <a name="troubleshooting"> </a>Řešení potíží
* **Počáteční nastavení**: Po počátečním nasazení služby API Management do podsítě není úspěšné, se doporučuje pro virtuální počítač nasadit nejdřív do stejné podsítě. Další vzdálené plochy k virtuálnímu počítači a ověřit, že je spojení s jednou z každého prostředku pod ve vašem předplatném azure
    * Azure Storage blob
    * Azure SQL Database
    * Azure Storage Table

  > [!IMPORTANT]
  > Po ověření připojení nezapomeňte odebrat všechny prostředky nasazené v podsíti, před nasazením API Management do podsítě.

* **Přírůstkové aktualizace**: Při provádění změn k síti, najdete [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus), chcete-li ověřit, že služba API Management ještě ztratili přístup k důležitým prostředkům, které závisí na. Stav připojení by měl být aktualizováno každých 15 minut.

* **Navigačních odkazů prostředku**: Při nasazování do podsítě virtuální sítě Resource Manageru styl, API Management rezervuje podsítě, tak, že vytvoříte prostředek navigačního odkazu. Pokud podsíť již obsahuje prostředek od jiného výrobce, nasazení bude **selhání**. Podobně když změníte umístění služby API Management k jiné podsíti nebo ho odstranit, odebíráme této navigační odkaz prostředku.

## <a name="subnet-size"> </a> Požadavek na velikost podsítě
Některé IP adresy v rámci každé podsítě vyhrazuje Azure a tyto adresy nelze použít. První a poslední IP adresy podsítí jsou vyhrazené pro udržování souladu s protokoly, spolu s tři další adresy používané pro služby Azure. Další informace najdete v tématu [existují nějaká omezení týkající se použití IP adresy v rámci těchto podsítí?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Kromě IP adresy používané službou infrastruktury virtuální sítě Azure každá instance služby Api Management v podsíti využívá dvě IP adresy na jednotku SKU úrovně Premium nebo jednu IP adresu pro SKU pro vývojáře. Každá instance rezerv další nástroje pro vyrovnávání zatížení externí IP adresu. Při nasazování do interní virtuální síti, vyžaduje další IP adresy pro interní služby load balancer.

Výpočet vyšší než minimální velikost podsítě, ve kterém se dá nasadit API Management je minimální velikostí/29, která poskytuje tři IP adresy.

## <a name="routing"> </a> Směrování
+ K poskytnutí přístupu pro všechny koncové body služby se vyhradí s vyrovnáváním zatížení veřejnou IP adresu (VIP).
+ IP adresu z rozsahu podsítě protokolu IP (DIP) se použije pro přístup k prostředkům v rámci virtuální sítě a veřejné IP adresy (VIP) se použije pro přístup k prostředkům mimo virtuální síť.
+ Veřejná IP adresa s vyrovnáváním zatížení adresu najdete v okně Přehled/Essentials na webu Azure Portal.

## <a name="limitations"> </a>Omezení
* Podsíť obsahující instance API Management nemůže obsahovat další typy prostředků Azure.
* Podsítě a služby API Management musí být ve stejném předplatném.
* Podsíť obsahující instance API Management se nedají přesouvat mezi předplatnými.
* Pro nasazení API managementu ve více oblastech nakonfigurované v režimu interní virtuální síti uživatelé odpovědní za správu napříč několika oblastmi a vyrovnávání zatížení jako vlastní směrování.
* Připojení z prostředku v globálním partnerském vztahu virtuálních sítí v jiné oblasti pro službu API Management v interní režimu nebudou fungovat kvůli omezením platformy. Další informace najdete v tématu [prostředků v jedné virtuální sítě nemůže komunikovat se službou Azure interního nástroje load balancer v partnerské virtuální síti](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)


## <a name="related-content"> </a>Související obsah
* [Propojení virtuální sítě do back-endu pomocí Vpn Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Propojení virtuální sítě z různých modelů nasazení](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Jak používat nástroje pro inspekci API pro trasovacího volání ve službě Azure API Management](api-management-howto-api-inspector.md)
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
