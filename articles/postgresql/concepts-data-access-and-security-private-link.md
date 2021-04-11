---
title: Privátní Link-Azure Database for PostgreSQL – jeden server
description: Přečtěte si, jak privátní odkaz funguje pro Azure Database for PostgreSQL jeden server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: e4b6a6090bf0e5e332a960cba8ec565df9dd55c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872264"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Privátní odkaz pro server Azure Database for PostgreSQL-Single

Privátní odkaz vám umožní vytvořit soukromé koncové body pro Azure Database for PostgreSQL jeden server a přenést ho do Virtual Network (VNet). Soukromý koncový bod zpřístupňuje privátní IP adresu v rámci podsítě, kterou můžete použít pro připojení k databázovému serveru stejně jako jakýkoli jiný prostředek ve virtuální síti.

Seznam pro PaaS služby, které podporují funkce privátního propojení, najdete v [dokumentaci](../private-link/index.yml)k privátním odkazům. Privátní koncový bod je privátní IP adresa v konkrétní [virtuální](../virtual-network/virtual-networks-overview.md) síti a podsíti.

> [!NOTE]
> Funkce privátního odkazu je dostupná jenom pro Azure Database for PostgreSQL servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je databázový server v jedné z těchto cenových úrovní.

## <a name="data-exfiltration-prevention"></a>Prevence exfiltrace dat

Data ex filtrátu na Azure Database for PostgreSQL jednom serveru jsou v případě, že ověřený uživatel, jako je správce databáze, může extrahovat data z jednoho systému a přesunout je do jiného umístění nebo systému mimo organizaci. Uživatel například přesune data do účtu úložiště, jehož vlastníkem je třetí strana.

Vezměte v úvahu scénář s uživatelem, který běží na virtuálním počítači Azure s PGAdmin, který se připojuje k jednomu serveru Azure Database for PostgreSQL zřízenému v Západní USA. Následující příklad ukazuje, jak omezit přístup k veřejným koncovým bodům na Azure Database for PostgreSQL jednom serveru pomocí řízení přístupu k síti.

* Nastavením *Povolit službám Azure* na off zakažte veškerý provoz služeb azure na Azure Database for PostgreSQL jediný server prostřednictvím veřejného koncového bodu. Zajistěte, aby žádné IP adresy nebo rozsahy neumožňovaly přístup k serveru prostřednictvím [pravidel brány firewall](./concepts-firewall-rules.md) nebo [koncových bodů služby virtuální sítě](./concepts-data-access-and-security-vnet.md).

* Povolte provoz jenom na Azure Database for PostgreSQL jednom serveru pomocí privátní IP adresy virtuálního počítače. Další informace najdete v článcích o [pravidlech brány firewall pro virtuální](howto-manage-vnet-using-portal.md) počítače a [služby](concepts-data-access-and-security-vnet.md) .

* Na virtuálním počítači Azure upřesněte rozsah odchozího připojení pomocí skupin zabezpečení sítě (skupin zabezpečení sítě) a značek služeb následujícím způsobem.

    * Zadejte pravidlo NSG, které povolí provoz pro *tag Service = SQL. WestUS* – povoluje připojení pouze k jednomu serveru Azure Database for PostgreSQL v západní USA
    * Zadejte pravidlo NSG (s vyšší prioritou) pro odepření provozu pro *značku služby = SQL* – odepření připojení k databázi PostgreSQL ve všech oblastech.</br></br>

Na konci této instalace se virtuální počítač Azure může připojit pouze k Azure Database for PostgreSQL jednomu serveru v oblasti Západní USA. Připojení ale není omezené na jeden Azure Database for PostgreSQL jeden server. Virtuální počítač se může stále připojit k jakémukoli Azure Database for PostgreSQL jednomu serveru v oblasti Západní USA, včetně databází, které nejsou součástí předplatného. I když jsme snížili rozsah exfiltrace dat ve výše uvedeném scénáři na konkrétní oblast, neodstraníme ji úplně.</br>

Pomocí privátního odkazu teď můžete nastavit řízení přístupu k síti, jako je skupin zabezpečení sítě, a omezit tak přístup k privátnímu koncovému bodu. Jednotlivé prostředky Azure PaaS se pak namapují na konkrétní soukromé koncové body. Škodlivý program Insider může přistupovat jenom k mapovanému prostředku PaaS (například k jednomu serveru Azure Database for PostgreSQL) a žádnému jinému prostředku.

## <a name="on-premises-connectivity-over-private-peering"></a>Místní připojení přes privátní partnerský vztah

Když se připojíte k veřejnému koncovému bodu z místních počítačů, vaše IP adresa musí být přidána do brány firewall založené na protokolu IP pomocí pravidla brány firewall na úrovni serveru. I když tento model funguje dobře a umožňuje přístup k jednotlivým počítačům pro vývoj nebo testování, je obtížné ho spravovat v produkčním prostředí.

Pomocí privátního odkazu můžete povolit přístup mezi různými místy k privátnímu koncovému bodu pomocí připojení typu [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), privátního partnerského vztahu nebo [tunelového připojení VPN](../vpn-gateway/index.yml). Můžou následně zakázat veškerý přístup prostřednictvím veřejného koncového bodu a nepoužívat bránu firewall na základě IP adresy.

> [!NOTE]
> V některých případech jsou Azure Database for PostgreSQL a VNet-Subnet v různých předplatných. V těchto případech je nutné zajistit následující konfigurace:
> - Ujistěte se, že oba odběry mají zaregistrovaný poskytovatel prostředků **Microsoft. DBforPostgreSQL** . Další informace najdete v tématu [Resource-Manager – registrace][resource-manager-portal] .

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Konfigurace privátního odkazu pro Azure Database for PostgreSQL jeden server

### <a name="creation-process"></a>Proces vytváření

Aby bylo možné povolit privátní propojení, jsou vyžadovány privátní koncové body. To lze provést pomocí následujících průvodců.

* [Azure Portal](./howto-configure-privatelink-portal.md)
* [Rozhraní příkazového řádku](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Proces schválení
Po vytvoření privátního koncového bodu (PE) správcem sítě může správce PostgreSQL spravovat připojení privátního koncového bodu (PEC) k Azure Database for PostgreSQL. Tato oddělení povinností mezi správcem sítě a DBA je užitečné pro správu Azure Database for PostgreSQLho připojení. 

* V Azure Portal přejděte na prostředek serveru Azure Database for PostgreSQL. 
    * Vyberte připojení privátního koncového bodu v levém podokně.
    * Zobrazuje seznam všech připojení privátního koncového bodu (PECs).
    * Byl vytvořen odpovídající privátní koncový bod (PE).

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="vybrat portál privátního koncového bodu":::

* Vyberte jednotlivé řadiče PEC ze seznamu tím, že je vyberete.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="vyberte privátní koncový bod, který čeká na schválení.":::

* Správce serveru PostgreSQL se může rozhodnout pro schválení nebo zamítnutí PEC a volitelně také přidat krátkou odpověď na text.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="Vyberte zprávu privátního koncového bodu.":::

* Po schválení nebo odmítnutí bude seznam odpovídat příslušnému stavu spolu s textem odpovědi.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="Vyberte koncový stav privátního koncového bodu.":::

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Případy použití privátního odkazu pro Azure Database for PostgreSQL


Klienti se můžou připojit k privátnímu koncovému bodu ze stejné virtuální sítě, [partnerské virtuální](../virtual-network/virtual-network-peering-overview.md) sítě ve stejné oblasti nebo v různých oblastech nebo prostřednictvím [připojení VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) napříč oblastmi. Klienti se navíc mohou připojit z místního prostředí pomocí ExpressRoute, privátního partnerského vztahu nebo tunelového propojení VPN. Níže je zjednodušený diagram znázorňující běžné případy použití.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="Výběr privátního koncového bodu – přehled":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Připojení z virtuálního počítače Azure v partnerském Virtual Network (VNet)
Nakonfigurujte [partnerský vztah](../virtual-network/tutorial-connect-virtual-networks-powershell.md) virtuálních sítí pro navázání připojení k serveru Azure Database for PostgreSQL-Single z virtuálního počítače Azure v partnerské virtuální síti.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Připojení z virtuálního počítače Azure v prostředí VNet-to-VNet
Nakonfigurujte [připojení k bráně VPN typu VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) , aby se navázalo připojení k Azure Database for PostgreSQLmu jednomu serveru z virtuálního počítače Azure v jiné oblasti nebo předplatném.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Připojení z místního prostředí přes síť VPN
Pokud chcete navázat připojení z místního prostředí na server Azure Database for PostgreSQL-Single, vyberte a implementujte jednu z možností:

* [Připojení Point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Připojení site-to-site VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Okruh ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Privátní propojení v kombinaci s pravidly brány firewall

Při použití privátního odkazu v kombinaci s pravidly brány firewall jsou možné následující situace a výsledky:

* Pokud žádná pravidla brány firewall nenakonfigurujete, nebude mít žádný provoz přístup k Azure Database for PostgreSQL jednomu serveru.

* Pokud nakonfigurujete veřejný provoz nebo koncový bod služby a vytvoříte privátní koncové body, budou se podle odpovídajícího typu pravidla brány firewall autorizovat různé typy příchozích přenosů.

* Pokud neprovedete konfiguraci žádného veřejného provozu nebo koncového bodu služby a vytvoříte privátní koncové body, je Azure Database for PostgreSQL jediný server přístupný jenom prostřednictvím privátních koncových bodů. Pokud nenastavíte veřejný provoz nebo koncový bod služby, po odmítnutí nebo odstranění všech schválených privátních koncových bodů nebude mít žádný provoz přístup k Azure Database for PostgreSQLmu jednomu serveru.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Odepřít veřejný přístup pro Azure Database for PostgreSQL jeden server

Pokud chcete pro přístup k jejich Azure Database for PostgreSQL jednomu serveru spoléhat jenom na soukromé koncové body, můžete zakázat nastavení všech veřejných koncových bodů ([pravidla brány firewall](concepts-firewall-rules.md) a [koncových bodů služby virtuální](concepts-data-access-and-security-vnet.md)sítě) nastavením **Odepřít konfiguraci přístupu k veřejné síti** na databázovém serveru. 

Pokud je toto nastavení nastavené na *Ano* , můžou vaše Azure Database for PostgreSQL jenom připojení prostřednictvím privátních koncových bodů. Pokud je toto nastavení nastaveno na *žádné* klienty, se nemohou připojit k vašemu Azure Database for PostgreSQL v závislosti na nastavení koncového bodu brány firewall nebo služby virtuální sítě. Po nastavení hodnoty přístup k privátní síti můžou zákazníci taky přidávat a aktualizovat existující pravidla brány firewall a pravidla koncových bodů služby virtuální sítě.

> [!Note]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for PostgreSQL – jeden server podporuje Pro obecné účely a cenová úroveň optimalizované pro paměť.
>
> Toto nastavení nemá žádný vliv na konfigurace protokolu SSL a TLS pro váš Azure Database for PostgreSQL jeden server.

Informace o tom, jak nastavit **přístup k veřejné síti odepřít** pro váš Azure Database for PostgreSQL samostatný server z Azure Portal, najdete v tématu [jak nakonfigurovat přístup k veřejné síti odepřít](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení na jednom serveru Azure Database for PostgreSQL najdete v následujících článcích:

* Pokud chcete nakonfigurovat bránu firewall pro Azure Database for PostgreSQL samostatný server, přečtěte si téma [Podpora brány firewall](./concepts-firewall-rules.md).

* Informace o tom, jak nakonfigurovat koncový bod služby virtuální sítě pro váš Azure Database for PostgreSQL pro jeden server, najdete v tématu [Konfigurace přístupu z virtuálních sítí](./concepts-data-access-and-security-vnet.md).

* Přehled Azure Database for PostgreSQL připojení k jednomu serveru najdete v tématu [Architektura připojení Azure Database for PostgreSQL](./concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
