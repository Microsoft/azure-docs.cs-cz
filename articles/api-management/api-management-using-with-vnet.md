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
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 067404193507f9787c994e82267679737ebe4832
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145413"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Jak používat Azure API Management s virtuálními sítěmi
Virtuální sítě Azure (Vnet) umožňuje umístit některé z vašich prostředků Azure, které řídí přístup k síti možnosti směrování Internetu jiných. Potom se dá propojit tyto sítí k místním sítím pomocí různých technologií VPN. Další informace o Azure Virtual Networks začínat tyto informace tady: [Přehled služby Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Azure API Management se dá nasadit ve virtuální síti (VNET), aby měl přístup k back-end služby v rámci sítě. Portál pro vývojáře a Brána rozhraní API, můžete nakonfigurovat byla přístupná z Internetu nebo pouze v rámci virtuální sítě.

> [!NOTE]
> Azure API Management podporuje classic a Azure Resource virtuálních sítí správce.
>

## <a name="prerequisites"></a>Požadavky

Chcete-li provést postup popsaný v tomto článku, budete potřebovat:

+ Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Instanci služby APIM. Další informace najdete v tématu [vytvoření instance Azure API Management](get-started-create-service-instance.md).
+ Připojení virtuální sítě je k dispozici jenom úrovně Premium a pro vývojáře. Přepnout na jednu z těchto úrovní pomocí následujícího postupu v [upgradu a škálování](upgrade-and-scale.md#upgrade-and-scale) tématu.

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

    ![Soukromý partnerský vztah][api-management-vnet-private]`

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

* **Vytvoření služby API Management v síti VNET**: použijte rutinu [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) k vytvoření služby Azure API Management v síti VNET.

* **Nasazení služby API Management existující v síti VNET**: použijte rutinu [aktualizace AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) přesunout existující služby Azure API Management ve virtuální síti.

## <a name="connect-vnet"> </a>Připojit k webové službě hostované v rámci virtuální sítě
Poté, co vaše služba API Management je připojený k virtuální síti, přístup ke službám back-endu v rámci něj se nijak neliší od přístupu k veřejné služby. Stačí zadat místní IP adresa nebo název hostitele (Pokud je nakonfigurovaný DNS server pro virtuální síť) do vaší webové služby **adresu URL webové služby** pole při vytváření nového rozhraní API nebo úpravou existující.

![Přidání rozhraní API od sítě VPN.][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Běžné problémy s konfigurací sítě
Tady je seznam častých problémech, které mohou nastat při nasazení služby API Management do virtuální sítě.

* **Vlastní nastavení serveru DNS**: Správa rozhraní API služby závisí na několik služeb Azure. API Management je hostovaná ve virtuální síti pomocí vlastního serveru DNS, musí překládat názvy hostitelů těchto služeb Azure. Postupujte prosím podle [to](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) doprovodné materiály k vlastním nastavením DNS. Viz následující tabulka portů a další požadavky na síť pro odkaz.

> [!IMPORTANT]
> Pokud máte v plánu používat vlastní servery DNS pro virtuální síť, měli byste nastavit **před** nasazení služby API Management do něj. Jinak budete muset aktualizovat službu API Management pokaždé, když změníte spuštěním servery DNS [použít operace konfigurace sítě](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Porty vyžadované pro službu API Management**: příchozí a odchozí provoz do podsítě, ve kterém je nasazená API Management se dá řídit pomocí [skupinu zabezpečení sítě][Network Security Group]. Pokud některý z těchto portů jsou k dispozici, API Management nebude fungovat správně a může být nepřístupný. Jeden nebo více z těchto portů blokované je dalším běžný problémem chybnou konfiguraci při použití služby API Management pomocí virtuální sítě.

Když jsou instance služby API Management je hostované ve virtuální síti, se používají porty v následující tabulce.

| Zdrojové a cílové porty | Směr | Přenosový protokol | Zdroj a cíl | Účel (*) | Typ virtuální sítě |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |Příchozí |TCP |INTERNET / VIRTUAL_NETWORK|Komunikace klienta do API managementu|Externí |
| * / 3443 |Příchozí |TCP |INTERNET / VIRTUAL_NETWORK|Koncový bod správy pro Azure portal a Powershellu |Interní |
| * / 80, 443 |Odchozí |TCP |VIRTUAL_NETWORK / INTERNET|**Závislost na Azure Storage**, Azure Service Bus a Azure Active Directory (v případě potřeby).|Externí a interní |
| * / 1433 |Odchozí |TCP |VIRTUAL_NETWORK / SQL|**Přístup ke koncovým bodům Azure SQL** |Externí a interní |
| * / 5672 |Odchozí |TCP |VIRTUAL_NETWORK / INTERNET|Závislost pro protokol do zásady centra událostí a agenta monitorování |Externí a interní |
| * / 445 |Odchozí |TCP |VIRTUAL_NETWORK / INTERNET|Závislost na sdílenou složku Azure pro GIT |Externí a interní |
| * / 1886 |Odchozí |TCP |VIRTUAL_NETWORK / INTERNET|Potřebné k publikování stav Resource Health |Externí a interní |
| * / 25028 |Odchozí |TCP |VIRTUAL_NETWORK / INTERNET|Připojení k serveru SMTP pro odeslání e-mailů |Externí a interní |
| * / 6381 - 6383 |Příchozí a odchozí |TCP |VIRTUAL_NETWORK / VIRTUAL_NETWORK|Instance služby Redis Cache přístupu mezi RoleInstances |Externí a interní |
| * / * | Příchozí |TCP |AZURE_LOAD_BALANCER / VIRTUAL_NETWORK| Nástroj pro vyrovnávání zatížení infrastruktury Azure |Externí a interní |

>[!IMPORTANT]
> Porty, pro kterou *účel* je **tučné** jsou požadovány pro službu API Management na úspěšné nasazení. Ostatní porty blokuje ale způsobí snížení v možnost použití a sledování spuštěnou službu.

* **Funkce SSL**: vytvoření řetězce certifikátu protokolu SSL a ověření API Management služba potřebuje odchozího síťového připojení a ocsp.msocsp.com, mscrl.microsoft.com crl.microsoft.com. Tato závislost není vyžadováno, pokud jakýkoliv certifikát, který nahrajete do služby API Management obsahuje úplný řetěz do kořenové certifikační Autority.

* **Přístup DNS**: odchozí přístup na port 53, je nutné pro komunikaci se servery DNS. Pokud vlastní server DNS existuje na druhém konci bránu VPN, DNS server musí být dostupný z podsítě hostování API Management.

* **Metriky a monitorování stavu**: odchozího síťového připojení k Azure monitorování koncových bodů, které vyřešit podle následujících domén: 

    | Prostředí Azure | Koncové body |
    | --- | --- |
    | Veřejné Azure | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.Metrics.nsatc.NET</li><li>prod3.Metrics.nsatc.NET</li><li>prod3 black.prod3.metrics.nsatc.net</li><li>prod3 red.prod3.metrics.nsatc.net</li></ul> |
    | Azure Government | <ul><li>fairfax.warmpath.usgovcloudapi.NET</li><li>shoebox2.Metrics.nsatc.NET</li><li>prod3.Metrics.nsatc.NET</li></ul> |
    | Azure (Čína) | <ul><li>mooncake.warmpath.chinacloudapi.CN</li><li>shoebox2.Metrics.nsatc.NET</li><li>prod3.Metrics.nsatc.NET</li></ul> |

* **Expresní instalace trasy**: běžnou konfigurací zákazníků je definovat vlastní výchozí trasa (0.0.0.0/0), která vynutí odchozí internetový provoz místo toho tok místní. Tento tok provozu vždy přeruší připojení k službě Azure API Management, protože odchozí provoz je blokované v místním nebo NAT by nerozpoznatelný sadu adresy, které přestane fungovat v různých koncových bodů Azure. Toto řešení je definování (nejméně) trasy definované uživatelem ([trasy definované uživatelem][UDRs]) na podsíť, která obsahuje Azure API Management. Trasu UDR definuje konkrétní podsítě tras, které bude použito místo výchozí trasu.
  Pokud je to možné doporučuje se použijte následující konfiguraci:
 * Konfigurace ExpressRoute inzeruje 0.0.0.0/0 a ve výchozím nastavení vynucené tunelů všechny odchozí provoz do místní.
 * Uživatelem definovaná TRASA použitá na podsíť obsahující Azure API Management definuje 0.0.0.0/0 s dalším segmentem směrování typu Internet.
 Celkové požadavky z těchto kroků je, že úrovni podsítě uživatelem definovaná TRASA má přednost před ExpressRoute vynucené tunelování, čímž zajišťuje odchozí internetový přístup ze služby Azure API Management.

* **Směrování prostřednictvím síťových virtuálních zařízení**: konfigurace, které používají trasu UDR s výchozí trasa (0.0.0.0/0) směrovat přenosy z Internetu směřující z podsítě pro správu rozhraní API přes síťové virtuální zařízení běží v Azure budou blokovat. Správa provoz přicházející z Internetu do instance služby API Management, který je nasazený v podsíti virtuální sítě. Tato konfigurace není podporovaná.

>[!WARNING]
>Azure API Management není podporované v konfiguracích ExpressRoute, který **nesprávně inzerování tras z cesty veřejného partnerského vztahu do cestou soukromého partnerského vztahu mezi**. Konfigurace ExpressRoute, které mají veřejné partnerské vztahy nakonfigurované, bude přijímají inzerci tras od Microsoftu pro velkou sadu rozsahů adres IP adres Microsoft Azure. Pokud tyto rozsahy adres nesprávně křížová inzerce na cestou soukromého partnerského vztahu, konečný výsledek je, že všechny odchozí síťové pakety z podsítě instance Azure API Management jsou správně vynucuje tunelové propojení pro zákazníka v místní síti infrastruktura. Tento tok sítí dělí Azure API Management. Řešení tohoto problému je ukončit křížovou inzerci tras z cesty veřejného partnerského vztahu pro cestou soukromého partnerského vztahu.


## <a name="troubleshooting"> </a>Řešení potíží
* **Počáteční nastavení**: při počátečním nasazení služby API Management do podsítě selže, se doporučuje pro virtuální počítač nasadit nejdřív do stejné podsítě. Další vzdálené plochy k virtuálnímu počítači a ověřit, že je spojení s jednou z každého prostředku pod ve vašem předplatném azure
    * Azure Storage blob
    * Azure SQL Database

 > [!IMPORTANT]
 > Po ověření připojení nezapomeňte odebrat všechny prostředky nasazené v podsíti, před nasazením API Management do podsítě.

* **Přírůstkové aktualizace**: při provádění změn k síti, najdete [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus), chcete-li ověřit, že služba API Management ještě ztratili přístup k důležitým prostředkům, které závisí na. Stav připojení by měl být aktualizováno každých 15 minut.

* **Navigačních odkazů prostředku**: Při nasazování do podsítě virtuální sítě Resource Manageru styl, API Management rezervuje podsítě, tak, že vytvoříte prostředek navigačního odkazu. Pokud podsíť již obsahuje prostředek od jiného výrobce, nasazení bude **selhání**. Podobně když změníte umístění služby API Management k jiné podsíti nebo ho odstranit, odebíráme této navigační odkaz prostředku.

* **Testování rozhraní API z portálu Azure portal**: při testování rozhraní API z portálu Azure a vaší instance služby API Management je integrovaný s interní virtuální síti, serverům DNS nakonfigurovaným na virtuální síť se použije pro překlad názvů. Pokud se zobrazí kód 404 při testování z portálu Azure portal, ujistěte se, že servery DNS pro virtuální síť můžete správně přeložit název hostitele vaší instance služby API Management. 

## <a name="subnet-size"> </a> Požadavek na velikost podsítě
Některé IP adresy v rámci každé podsítě vyhrazuje Azure a tyto adresy nelze použít. První a poslední IP adresy podsítí jsou vyhrazené pro udržování souladu s protokoly, spolu s tři další adresy používané pro služby Azure. Další informace najdete v tématu [existují nějaká omezení týkající se použití IP adresy v rámci těchto podsítí?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Kromě IP adresy používané službou infrastruktury virtuální sítě Azure každá instance služby Api Management v podsíti využívá dvě IP adresy na jednotku SKU úrovně Premium nebo jednu IP adresu pro SKU pro vývojáře. Každá instance rezerv další nástroje pro vyrovnávání zatížení externí IP adresu. Při nasazování do interní virtuální síti, vyžaduje další IP adresy pro interní služby load balancer.

Výpočet vyšší než minimální velikost podsítě, ve kterém se dá nasadit API Management je minimální velikostí/29, která poskytuje 3 IP adresy.

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
* [Nejčastější dotazy k virtuální síti](../virtual-network/virtual-networks-faq.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
