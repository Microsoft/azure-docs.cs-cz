---
title: Privátní propojení – databáze Azure pro PostgreSQL – jeden server
description: Zjistěte, jak privátní odkaz funguje pro Azure Database for PostgreSQL – jeden server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4216abdf8cc8aae00e3ba0c57961c4b8b7403672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371677"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Privátní propojení pro databázi Azure pro server PostgreSQL-Single

Privátní odkaz umožňuje vytvářet privátní koncové body pro Azure Database for PostgreSQL – jeden server a tak přináší služby Azure uvnitř vaší privátní virtuální sítě (VNet). Soukromý koncový bod zveřejňuje privátní IP adresu, kterou můžete použít k připojení k databázovému serveru stejně jako jakýkoli jiný prostředek ve virtuální síti.

Seznam služeb PaaS, které podporují funkce Private Link, naleznete v [dokumentaci](https://docs.microsoft.com/azure/private-link/index)k soukromému odkazu . Privátní koncový bod je privátní IP adresa v rámci konkrétní [virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) a podsítě.

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for PostgreSQL Single server podporuje cenové úrovně optimalizované pro obecné účely a paměť.

## <a name="data-exfiltration-prevention"></a>Prevence exfiltrace dat

Data ex filtrace v Azure Database pro PostgreSQL single server je v případě, že oprávněný uživatel, jako je správce databáze, je schopen extrahovat data z jednoho systému a přesunout je do jiného umístění nebo systému mimo organizaci. Uživatel například přesune data do účtu úložiště vlastněného třetí stranou.

Zvažte scénář s uživatelem, který používá PGAdmin uvnitř virtuálního počítače Azure (VM), který se připojuje k databázi Azure pro server PostgreSQL Single zřízeného v západní USA. Následující příklad ukazuje, jak omezit přístup s veřejnými koncovými body na Azure Database for PostgreSQL Single server pomocí ovládacích prvků přístupu k síti.

* Zakažte veškerý provoz služeb Azure na Azure Database for PostgreSQL Single server přes veřejný koncový bod nastavením *Povolit služby Azure* na OFF. Zajistěte, aby k serveru neumožňoval přístup k serveru prostřednictvím [pravidel brány firewall](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) nebo [koncových bodů služby virtuální sítě](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet).

* Povolit pouze provoz do databáze Azure pro postgreSQL jeden server pomocí privátní IP adresu virtuálního počítače. Další informace najdete v článcích o pravidlech [služby Koncový bod](concepts-data-access-and-security-vnet.md) a [pravidla brány firewall virtuální sítě.](howto-manage-vnet-using-portal.md)

* Na virtuálním počítači Azure zúžit rozsah odchozí připojení pomocí skupin zabezpečení sítě (NSGs) a značky služeb takto

    * Zadejte pravidlo nsg povolit provoz pro *výrobní číslo = SQL. WestUS* – jenom povolení připojení k Azure Database for PostgreSQL Single server in West US
    * Zadejte pravidlo nsg (s vyšší prioritou) pro odepření provozu pro *výrobní značku = SQL* - odepření připojení k databázi PostgreSQL ve všech oblastech</br></br>

Na konci tohoto nastavení virtuálnípočítač Azure můžete připojit jenom k Azure Database pro PostgreSQL single server v oblasti západní USA. Připojení však není omezeno na jednu databázi Azure pro server PostgreSQL Single. Virtuální počítač se pořád může připojit k libovolné databázi Azure pro server PostgreSQL Single v oblasti Západní USA, včetně databází, které nejsou součástí předplatného. I když jsme snížili rozsah exfiltrace dat ve výše uvedeném scénáři na konkrétní oblast, neodstranili jsme ji úplně.</br>

Pomocí služby Private Link můžete nyní nastavit ovládací prvky přístupu k síti, jako jsou skupiny zabezpečení sítě, a omezit tak přístup k privátnímu koncovému bodu. Jednotlivé prostředky Azure PaaS se pak namapují na konkrétní soukromé koncové body. Škodlivý insider má přístup jenom k mapovanému prostředku PaaS (například k databázi Azure pro server PostgreSQL Single) a k žádnému jinému prostředku.

## <a name="on-premises-connectivity-over-private-peering"></a>Místní připojení přes soukromý partnerský vztah

Když se připojíte k veřejnému koncovému bodu z místních počítačů, musí být vaše IP adresa přidána do brány firewall založené na protokolu IP pomocí pravidla brány firewall na úrovni serveru. Zatímco tento model funguje dobře pro povolení přístupu k jednotlivým počítačům pro dev nebo testovací úlohy, je obtížné spravovat v produkčním prostředí.

Pomocí služby Private Link můžete povolit přístup k privátnímu koncovému bodu pomocí [služby Express Route](https://azure.microsoft.com/services/expressroute/) (ER), soukromého partnerského vztahu nebo [tunelového propojení VPN](https://docs.microsoft.com/azure/vpn-gateway/). Následně mohou zakázat veškerý přístup přes veřejný koncový bod a nepoužívat bránu firewall založenou na protokolu IP.

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Konfigurace privátního spojení pro databázi Azure pro server PostgreSQL Single

### <a name="creation-process"></a>Proces vytváření

Soukromé koncové body jsou nutné k povolení private link. To lze provést pomocí následujících návodů.

* [Portál Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal)
* [Cli](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Schvalovací proces
Jakmile správce sítě vytvoří privátní koncový bod (PE), správce PostgreSQL můžete spravovat privátní koncový bod připojení (PEC) do databáze Azure pro PostgreSQL. Toto oddělení povinností mezi správcem sítě a DBA je užitečné pro správu Azure Database pro připojení PostgreSQL. 

* Přejděte na prostředek azure databáze pro postgresql server na webu Azure Portal. 
    * Výběr soukromých připojení koncového bodu v levém podokně
    * Zobrazí seznam všech privátních připojení koncových bodů (PEC)
    * Odpovídající privátní koncový bod (PE) vytvořený

![výběr privátního portálu koncového bodu](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Vyberte jednotlivé PEC ze seznamu výběrem.

![vyberte soukromý koncový bod čekající na schválení](media/concepts-data-access-and-security-private-link/select-private-link.png)

* Správce serveru PostgreSQL se může rozhodnout pec schválit nebo odmítnout a volitelně přidat krátkou textovou odpověď.

![výběr zprávy soukromého koncového bodu](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Po schválení nebo odmítnutí bude seznam odrážet příslušný stav spolu s textem odpovědi

![vyberte konečný stav soukromého koncového bodu](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Případy použití privátního propojení pro azure databázi pro PostgreSQL

Klienti se můžou připojit k privátnímu koncovému bodu ze stejné virtuální sítě, partnerské virtuální sítě ve stejné oblasti nebo prostřednictvím připojení virtuální sítě k virtuální síti napříč oblastmi. Kromě toho se klienti mohou připojit z místního prostředí pomocí ExpressRoute, soukromého partnerského vztahu nebo tunelového propojení VPN. Níže je zjednodušený diagram znázorňující běžné případy použití.

![výběr přehledu soukromého koncového bodu](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Připojení z virtuálního počítače Azure v partnerské virtuální síti (Virtuální síť)
Nakonfigurujte [partnerský vztah virtuální sítě](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) tak, aby bylo možné navázat připojení k databázi Azure pro PostgreSQL – jeden server z virtuálního počítače Azure v partnerské virtuální síti.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Připojení z virtuálního počítače Azure v prostředí Virtuální sítě k virtuální síti
Konfigurace připojení brány VPN virtuální [sítě k virtuální síti](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) k navázání připojení k databázi Azure pro PostgreSQL – jeden server z virtuálního počítače Azure v jiné oblasti nebo předplatném.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Připojení z místního prostředí přes VPN
Chcete-li vytvořit připojení z místního prostředí k databázi Azure pro PostgreSQL – jeden server, zvolte a implementujte jednu z možností:

* [Připojení z bodu na pracoviště](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Připojení site-to-site VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Okruh ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Soukromé propojení v kombinaci s pravidly brány firewall

Následující situace a výsledky jsou možné při použití private link v kombinaci s pravidly brány firewall:

* Pokud nenakonfigurujete žádná pravidla brány firewall, pak ve výchozím nastavení žádný provoz nebude mít přístup k Azure Database for PostgreSQL Single server.

* Pokud nakonfigurujete veřejný provoz nebo koncový bod služby a vytvoříte soukromé koncové body, pak jsou různé typy příchozích přenosů autorizovány odpovídajícím typem pravidla brány firewall.

* Pokud nenakonfigurujete žádný veřejný provoz nebo koncový bod služby a vytvoříte privátní koncové body, pak je databáze Azure pro server PostgreSQL Single přístupná jenom prostřednictvím privátních koncových bodů. Pokud nenakonfigurujete veřejný provoz nebo koncový bod služby, po všechny schválené soukromé koncové body jsou odmítnuty nebo odstraněny, žádný provoz bude mít přístup k databázi Azure pro postgreSQL jeden server.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Odepření veřejného přístupu pro databázi Azure pro server PostgreSQL Single

Pokud se chcete spoléhat jenom na privátní koncové body pro přístup k jejich azure databáze pro postgreSQL jeden server, můžete zakázat nastavení všech veřejných koncových bodů([pravidla brány firewall](concepts-firewall-rules.md) a koncové body [služby virtuální sítě](concepts-data-access-and-security-vnet.md)) nastavením konfigurace odepřít přístup k veřejné **síti** na databázovém serveru. 

Pokud je toto nastavení nastaveno na *ANO,* jsou do databáze Azure pro PostgreSQL povolena pouze připojení přes privátní koncové body. Když je toto nastavení nastavené na *žádné* klienti se můžou připojit k databázi Azure pro PostgreSQL na základě nastavení koncového bodu služby Brána firewall nebo Virtuální sítě. Navíc po nastavení hodnoty přístupu k privátní síti pro zákazníky nelze přidat nebo aktualizovat existující "pravidla brány firewall" a koncového bodu služby virtuální sítě pravidlo

> [!Note]
> Tato funkce je dostupná ve všech oblastech Azure, kde Azure Database for PostgreSQL – jeden server podporuje cenové úrovně optimalizované pro obecné účely a paměť.
>
> Toto nastavení nemá žádný vliv na konfigurace SSL a TLS pro váš server Azure Database for PostgreSQL Single.

Informace o tom, jak nastavit **odepřít přístup k veřejné síti** pro váš server Azure Database for PostgreSQL Single z webu Azure Portal, najdete v článku Jak [nakonfigurovat přístup k veřejné síti](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení Azure Database for PostgreSQL Single server najdete v následujících článcích:

* Pokud chcete nakonfigurovat bránu firewall pro Azure Database pro server PostgreSQL Single, přečtěte si informace [o podpoře brány firewall](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules).

* Informace o konfiguraci koncového bodu služby virtuální sítě pro server Azure Database for PostgreSQL Single najdete [v tématu Konfigurace přístupu z virtuálních sítí](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet).

* Přehled připojení k databázi Azure pro postgreSQL single server najdete v článku [Azure Database for PostgreSQL Connectivity Architecture](https://docs.microsoft.com/azure/postgresql/concepts-connectivity-architecture)