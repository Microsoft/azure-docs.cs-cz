---
title: Privátní propojení
description: Přehled funkce Soukromého koncového bodu
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: ab9c5c5c1134d2e09a790a788a3b7e55f807dd9b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78945376"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse"></a>Privátní propojení pro Azure SQL Database a datový sklad

Private Link umožňuje připojení k různým službám PaaS v Azure prostřednictvím **privátního koncového bodu**. Seznam služeb PaaS, které podporují funkce private link, naleznete na stránce [Dokumentace k soukromému odkazu.](../private-link/index.yml) Privátní koncový bod je privátní IP adresa v rámci konkrétní [virtuální sítě](../virtual-network/virtual-networks-overview.md) a podsítě. 

> [!IMPORTANT]
> Tento článek se vztahuje na Server Azure SQL a databáze SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse. Tento článek se *nevztahuje* na nasazení **spravované instance** v Azure SQL Database.

## <a name="data-exfiltration-prevention"></a>Prevence exfiltrace dat

Exfiltrace dat v Azure SQL Database je, když oprávněný uživatel, jako je například správce databáze, je schopen extrahovat data z jednoho systému a přesunout ho do jiného umístění nebo systému mimo organizaci. Uživatel například přesune data do účtu úložiště vlastněného třetí stranou.

Zvažte scénář s uživatelem, který používá SQL Server Management Studio (SSMS) uvnitř virtuálního počítače Azure připojení k databázi SQL. Tato databáze SQL je v datovém centru v USA v usa. Následující příklad ukazuje, jak omezit přístup s veřejnými koncovými body v databázi SQL pomocí ovládacích prvků přístupu k síti.

1. Zakažte veškerý provoz služeb Azure do databáze SQL prostřednictvím veřejného koncového bodu nastavením Povolit služby Azure **off**. Ujistěte se, že v pravidlech brány firewall na úrovni serveru a databáze nejsou povoleny žádné adresy IP. Další informace najdete v tématu [Azure SQL Database a ovládací prvky přístupu k síti datového skladu](sql-database-networkaccess-overview.md).
1. Povolit pouze provoz do databáze SQL pomocí privátní IP adresu virtuálního počítačů. Další informace naleznete v článcích o [pravidlech](sql-database-firewall-configure.md)brány firewall [služby Endpoint](sql-database-vnet-service-endpoint-rule-overview.md) a brány firewall virtuální sítě .
1. Na virtuálním počítači Azure zúžit rozsah odchozí připojení pomocí [skupin zabezpečení sítě (NSGs)](../virtual-network/manage-network-security-group.md) a značky služeb takto
    - Zadejte pravidlo nsg povolit provoz pro výrobní číslo = SQL. WestUs - povolení připojení k databázi SQL v západní USA
    - Zadejte pravidlo sítě zabezpečení sítě (s **vyšší prioritou),** které odepře provoz pro výrobní číslo = SQL - odepření připojení k databázi SQL ve všech oblastech

Na konci tohoto nastavení virtuálnípočítač Azure můžete připojit pouze k databázím SQL v oblasti západní USA. Připojení však není omezeno na jednu databázi SQL. Virtuální modul se stále může připojit k libovolné databáze SQL v oblasti západní USA, včetně databází, které nejsou součástí předplatného. I když jsme snížili rozsah exfiltrace dat ve výše uvedeném scénáři na konkrétní oblast, neodstranili jsme ji úplně.

Pomocí služby Private Link mohou zákazníci nyní nastavit ovládací prvky přístupu k síti, jako jsou skupiny zabezpečení sítě, a omezit tak přístup k privátnímu koncovému bodu. Jednotlivé prostředky Azure PaaS se pak namapují na konkrétní soukromé koncové body. Škodlivý insider může přistupovat pouze k mapovanému prostředku PaaS (například k databázi SQL) a k žádnému jinému prostředku. 

## <a name="on-premises-connectivity-over-private-peering"></a>Místní připojení přes soukromý partnerský vztah

Když se zákazníci připojují k veřejnému koncovému bodu z místních počítačů, je třeba jejich IP adresu přidat do brány firewall založené na protokolu IP pomocí [pravidla brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md). Zatímco tento model funguje dobře pro povolení přístupu k jednotlivým počítačům pro dev nebo testovací úlohy, je obtížné spravovat v produkčním prostředí.

Pomocí služby Private Link mohou zákazníci povolit přístup k privátnímu koncovému bodu pomocí [služby ExpressRoute](../expressroute/expressroute-introduction.md), soukromého partnerského vztahu nebo tunelového propojení VPN. Zákazníci pak mohou zakázat veškerý přístup prostřednictvím veřejného koncového bodu a nepoužívat bránu firewall založenou na protokolu IP k povolení jakýchkoli adres IP.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Jak nastavit privátní propojení pro Azure SQL Database 

### <a name="creation-process"></a>Proces vytváření
Privátní koncové body lze vytvořit pomocí portálu, Prostředí PowerShell nebo Azure CLI:
- [Portál](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [Cli](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Schvalovací proces
Jakmile správce sítě vytvoří privátní koncový bod (PE), správce SQL může spravovat privátní koncového bodu připojení (PEC) k databázi SQL.

1. Přejděte na prostředek serveru SQL na webu Azure Portal podle kroků uvedených na následujícím snímku obrazovky.

    - (1) V levém podokně vyberte soukromá připojení koncového bodu
    - (2) Zobrazuje seznam všech privátních koncových bodů připojení (PEC)
    - (3) Odpovídající soukromý koncový bod ![(PE) vytvořil snímek obrazovky všech PEC][3]

1. Vyberte jednotlivé PEC ze seznamu výběrem.
![Snímek obrazovky vybraný PEC][6]

1. Správce SQL může schválit nebo odmítnout PEC a volitelně přidat krátkou textovou odpověď.
![Snímek obrazovky se schválením PEC][4]

1. Po schválení nebo odmítnutí bude seznam odrážet příslušný stav spolu s textem odpovědi.
![Snímek obrazovky se všemi pes po schválení][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Případy použití privátního propojení pro Azure SQL Database 

Klienti se můžou připojit k privátnímu koncovému bodu ze stejné virtuální sítě, partnerské virtuální sítě ve stejné oblasti nebo prostřednictvím připojení virtuální sítě k virtuální síti napříč oblastmi. Kromě toho se klienti mohou připojit z místního prostředí pomocí ExpressRoute, soukromého partnerského vztahu nebo tunelového propojení VPN. Níže je zjednodušený diagram znázorňující běžné případy použití.

 ![Diagram možností připojení][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Testování připojení k databázi SQL z virtuálního počítače Azure ve stejné virtuální síti (VNet)

V tomto scénáři předpokládejme, že jste vytvořili virtuální počítač Azure (VM) se systémem Windows Server 2016. 

1. [Spusťte relaci vzdálené plochy (RDP) a připojte se k virtuálnímu počítači](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Potom můžete provést některé základní kontroly připojení k zajištění, že virtuální modul se připojuje k databázi SQL prostřednictvím privátního koncového bodu pomocí následujících nástrojů:
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Kontrola připojení pomocí programu Telnet

[Klient Telnet](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) je funkce systému Windows, kterou lze použít k testování připojení. V závislosti na verzi operačního systému Windows může být nutné tuto funkci explicitně povolit. 

Po instalaci programu Telnet otevřete okno příkazového řádku. Spusťte příkaz Telnet a zadejte adresu IP a soukromý koncový bod databáze SQL.

```
>telnet 10.1.1.5 1433
```

Když se Telnet úspěšně připojí, zobrazí se v příkazovém okně prázdná obrazovka, jako je následující obrázek:

 ![Diagram telnetu][2]

### <a name="check-connectivity-using-psping"></a>Kontrola připojení pomocí služby Psping

[Psping](/sysinternals/downloads/psping) lze použít takto ke kontrole, že soukromé koncového bodu připojení (PEC) naslouchá připojení na portu 1433.

Spusťte psping následujícím způsobem tím, že poskytuje hlavní název ndn pro váš sql databázový server a port 1433:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

Výstup ukazují, že psping může ping privátní IP adresu přidruženou k PEC.

### <a name="check-connectivity-using-nmap"></a>Kontrola připojení pomocí Nmap

Nmap (Network Mapper) je bezplatný a open-source nástroj používaný pro zjišťování sítě a auditování zabezpečení. Pro více informací a odkaz https://nmap.orgke stažení, navštivte . Tento nástroj můžete použít k zajištění, že soukromý koncový bod naslouchá připojení na portu 1433.

Spusťte Nmap takto poskytnutím rozsahu adres podsítě, která hostuje soukromý koncový bod.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Výsledek ukazuje, že jedna ADRESA IP je nahoru; která odpovídá IP adrese privátního koncového bodu.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Kontrola připojení pomocí aplikace SQL Server Management Studio (SSMS)
> [!NOTE]
> Použijte **plně kvalifikovaný název domény (FQDN)** serveru v připojovacích řetězcích pro své klienty. Všechny pokusy o přihlášení provedené přímo na IP adresu se nezdaří. Toto chování je záměrné, protože soukromý koncový bod směruje provoz do brány SQL v oblasti a musí být zadán okonečném prostřednictvím, aby bylo možné úspěšně přihlásit.

Postupujte podle pokynů zde použít [SSMS pro připojení k databázi SQL](sql-database-connect-query-ssms.md). Po připojení k databázi SQL pomocí SSMS ověřte, že se připojujete z privátní IP adresy virtuálního počítače Azure spuštěním následujícího dotazu:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>Omezení 
Připojení k privátnímu koncovému bodu podporují pouze **proxy** server jako [zásady připojení](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Připojení z virtuálního počítače Azure v partnerské virtuální síti (Virtuální síť) 

Nakonfigurujte [partnerský vztah virtuální sítě](../virtual-network/tutorial-connect-virtual-networks-powershell.md) tak, aby bylo možné vytvořit připojení k databázi SQL z virtuálního počítače Azure v partnerské virtuální síti.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Připojení z virtuálního počítače Azure v prostředí Virtuální sítě k virtuální síti

Nakonfigurujte připojení brány VPN virtuální [sítě k virtuální síti](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) a navazujte připojení k databázi SQL z virtuálního počítače Azure v jiné oblasti nebo předplatném.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Připojení z místního prostředí přes VPN

Chcete-li vytvořit připojení z místního prostředí k databázi SQL, zvolte a implementujte jednu z možností:
- [Připojení z bodu na pracoviště](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Připojení site-to-site VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Okruh ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>Připojení z datového skladu Azure SQL k úložišti Azure pomocí Polybase

PolyBase se běžně používá k načtení dat do Azure SQL Data Warehouse z účtů úložiště Azure. Pokud účet Azure Storage, který načítáte data z omezení přístupu jenom k sadě podsítí virtuální sítě prostřednictvím privátních koncových bodů, koncových bodů služby nebo bran firewall založených na PROTOKOLU IP, připojení z PolyBase k účtu se přeruší. Chcete-li povolit scénáře importu i exportu PolyBase pomocí Azure SQL Data Warehouse, který se připojuje k Azure Storage, který je zabezpečený k virtuální síti, postupujte podle pokynů [uvedených zde](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). 



## <a name="next-steps"></a>Další kroky

- Přehled zabezpečení Azure SQL Database najdete v tématu [Zabezpečení databáze](sql-database-security-overview.md)
- Přehled připojení k Azure SQL Database najdete v článku [Architektura připojení Azure SQL](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
