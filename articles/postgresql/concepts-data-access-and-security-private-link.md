---
title: Privátní odkaz pro Azure Database for PostgreSQL – jeden server (Preview)
description: Přečtěte si, jak privátní odkaz funguje pro Azure Database for PostgreSQL jeden server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: e3667a60a326838b490f9082fd55bdc92d038cf9
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898362"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server-preview"></a>Privátní odkaz pro Azure Database for PostgreSQL – jeden server (Preview)

Privátní odkaz vám umožní připojit se k různým službám PaaS v Azure prostřednictvím privátního koncového bodu. Privátní propojení Azure v podstatě přináší služby Azure do privátního Virtual Network (VNet). K prostředkům PaaS se dá dostat pomocí privátní IP adresy stejně jako u jakéhokoli jiného prostředku ve virtuální síti.

Seznam pro PaaS služby, které podporují funkce privátního propojení, najdete v [dokumentaci](https://docs.microsoft.com/azure/private-link/index)k privátním odkazům. Privátní koncový bod je privátní IP adresa v konkrétní [virtuální](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) síti a podsíti.

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for PostgreSQL jeden server podporuje Pro obecné účely a paměťově optimalizované cenové úrovně.

## <a name="data-exfiltration-prevention"></a>Prevence exfiltrace dat

Data ex filtrátu na Azure Database for PostgreSQL jednom serveru jsou v případě, že ověřený uživatel, jako je správce databáze, může extrahovat data z jednoho systému a přesunout je do jiného umístění nebo systému mimo organizaci. Uživatel například přesune data do účtu úložiště, jehož vlastníkem je třetí strana.

Vezměte v úvahu scénář s uživatelem, který běží na virtuálním počítači Azure s PGAdmin, který se připojuje k jednomu serveru Azure Database for PostgreSQL zřízenému v Západní USA. Následující příklad ukazuje, jak omezit přístup k veřejným koncovým bodům na Azure Database for PostgreSQL jednom serveru pomocí řízení přístupu k síti.

* Nastavením *Povolit službám Azure* na off zakažte veškerý provoz služeb azure na Azure Database for PostgreSQL jediný server prostřednictvím veřejného koncového bodu. Zajistěte, aby žádné IP adresy nebo rozsahy neumožňovaly přístup k serveru prostřednictvím [pravidel brány firewall](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) nebo [koncových bodů služby virtuální sítě](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet).

* Povolte provoz jenom na Azure Database for PostgreSQL jednom serveru pomocí privátní IP adresy virtuálního počítače. Další informace najdete v článcích o [pravidlech brány firewall pro virtuální](howto-manage-vnet-using-portal.md) počítače a [služby](concepts-data-access-and-security-vnet.md) .

* Na virtuálním počítači Azure upřesněte rozsah odchozího připojení pomocí skupin zabezpečení sítě (skupin zabezpečení sítě) a značek služeb následujícím způsobem.

    * Zadejte pravidlo NSG, které povolí provoz pro *tag Service = SQL. WestUS* – povoluje připojení pouze k jednomu serveru Azure Database for PostgreSQL v západní USA
    * Zadejte pravidlo NSG (s vyšší prioritou) pro odepření provozu pro *značku služby = SQL* – odepření připojení k databázi PostgreSQL ve všech oblastech.</br></br>

Na konci této instalace se virtuální počítač Azure může připojit pouze k Azure Database for PostgreSQL jednomu serveru v oblasti Západní USA. Připojení ale není omezené na jeden Azure Database for PostgreSQL jeden server. Virtuální počítač se může stále připojit k jakémukoli Azure Database for PostgreSQL jednomu serveru v oblasti Západní USA, včetně databází, které nejsou součástí předplatného. I když jsme snížili rozsah exfiltrace dat ve výše uvedeném scénáři na konkrétní oblast, neodstraníme ji úplně.</br>

Pomocí privátního odkazu teď můžete nastavit řízení přístupu k síti, jako je skupin zabezpečení sítě, a omezit tak přístup k privátnímu koncovému bodu. Jednotlivé prostředky Azure PaaS se pak namapují na konkrétní soukromé koncové body. Škodlivý program Insider může přistupovat jenom k mapovanému prostředku PaaS (například k jednomu serveru Azure Database for PostgreSQL) a žádnému jinému prostředku.

## <a name="on-premises-connectivity-over-private-peering"></a>Místní připojení přes soukromý partnerský vztah

Když se připojíte k veřejnému koncovému bodu z místních počítačů, vaše IP adresa musí být přidána do brány firewall založené na protokolu IP pomocí pravidla brány firewall na úrovni serveru. I když tento model funguje dobře a umožňuje přístup k jednotlivým počítačům pro vývoj nebo testování, je obtížné ho spravovat v produkčním prostředí.

Pomocí privátního odkazu můžete povolit přístup mezi různými místy k privátnímu koncovému bodu pomocí připojení typu [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), privátního partnerského vztahu nebo [tunelového připojení VPN](https://docs.microsoft.com/azure/vpn-gateway/). Můžou následně zakázat veškerý přístup prostřednictvím veřejného koncového bodu a nepoužívat bránu firewall na základě IP adresy.

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Konfigurace privátního odkazu pro Azure Database for PostgreSQL jeden server

### <a name="creation-process"></a>Proces vytváření

Aby bylo možné povolit privátní propojení, jsou vyžadovány privátní koncové body. To lze provést pomocí následujících průvodců.

* [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal)
* [Rozhraní příkazového řádku](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Proces schválení
Po vytvoření privátního koncového bodu (PE) správcem sítě může správce PostgreSQL spravovat připojení privátního koncového bodu (PEC) k Azure Database for PostgreSQL.

> [!NOTE]
> V současné době Azure Database for PostgreSQL jeden server podporuje pouze automatické schválení privátního koncového bodu.

* V Azure Portal přejděte na prostředek serveru Azure Database for PostgreSQL. 
    * Vyberte připojení privátního koncového bodu v levém podokně.
    * Zobrazuje seznam všech připojení privátního koncového bodu (PECs).
    * Byl vytvořen odpovídající privátní koncový bod (PE).

![vybrat portál privátního koncového bodu](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Vyberte jednotlivé řadiče PEC ze seznamu tím, že je vyberete.

![vyberte privátní koncový bod, který čeká na schválení.](media/concepts-data-access-and-security-private-link/select-private-link.png)

* Správce serveru PostgreSQL se může rozhodnout pro schválení nebo zamítnutí PEC a volitelně také přidat krátkou odpověď na text.

![Vyberte zprávu privátního koncového bodu.](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Po schválení nebo odmítnutí bude seznam odpovídat příslušnému stavu spolu s textem odpovědi.

![Vyberte koncový stav privátního koncového bodu.](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Případy použití privátního odkazu pro Azure Database for PostgreSQL

Klienti se můžou připojit ke soukromým koncovým bodem ze stejné virtuální sítě, partnerské virtuální sítě ve stejné oblasti nebo prostřednictvím připojení VNet-to-VNet napříč oblastmi. Klienti se navíc mohou připojit z místního prostředí pomocí ExpressRoute, privátního partnerského vztahu nebo tunelového propojení VPN. Níže je zjednodušený diagram znázorňující běžné případy použití.

![Výběr privátního koncového bodu – přehled](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Připojení z virtuálního počítače Azure v partnerském Virtual Network (VNet)
Nakonfigurujte [partnerský vztah](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) virtuálních sítí pro navázání připojení k serveru Azure Database for PostgreSQL-Single z virtuálního počítače Azure v partnerské virtuální síti.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Připojení z virtuálního počítače Azure v prostředí VNet-to-VNet
Nakonfigurujte [připojení k bráně VPN typu VNet-to-VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) , aby se navázalo připojení k Azure Database for PostgreSQLmu jednomu serveru z virtuálního počítače Azure v jiné oblasti nebo předplatném.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Připojení z místního prostředí přes síť VPN
Pokud chcete navázat připojení z místního prostředí na server Azure Database for PostgreSQL-Single, vyberte a implementujte jednu z možností:

* [Připojení Point-to-site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Připojení VPN typu Site-to-site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Okruh ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privátní propojení v kombinaci s pravidly brány firewall

Při použití privátního odkazu v kombinaci s pravidly brány firewall jsou možné následující situace a výsledky:

* Pokud žádná pravidla brány firewall nenakonfigurujete, nebude mít žádný provoz přístup k Azure Database for PostgreSQL jednomu serveru.

* Pokud nakonfigurujete veřejný provoz nebo koncový bod služby a vytvoříte privátní koncové body, budou se podle odpovídajícího typu pravidla brány firewall autorizovat různé typy příchozích přenosů.

* Pokud neprovedete konfiguraci žádného veřejného provozu nebo koncového bodu služby a vytvoříte privátní koncové body, je Azure Database for PostgreSQL jediný server přístupný jenom prostřednictvím privátních koncových bodů. Pokud nenastavíte veřejný provoz nebo koncový bod služby, po odmítnutí nebo odstranění všech schválených privátních koncových bodů nebude mít žádný provoz přístup k Azure Database for PostgreSQLmu jednomu serveru.

## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení na jednom serveru Azure Database for PostgreSQL najdete v následujících článcích:

* Pokud chcete nakonfigurovat bránu firewall pro Azure Database for PostgreSQL samostatný server, přečtěte si téma [Podpora brány firewall](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules).

* Informace o tom, jak nakonfigurovat koncový bod služby virtuální sítě pro váš Azure Database for PostgreSQL pro jeden server, najdete v tématu [Konfigurace přístupu z virtuálních sítí](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet).

* Přehled Azure Database for PostgreSQL připojení k jednomu serveru najdete v tématu [Architektura připojení Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-connectivity-architecture)