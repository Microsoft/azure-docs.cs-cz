---
title: Azure Private Link
description: Přehled funkce privátního koncového bodu.
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.custom: sqldbrb=1, fasttrack-edit
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 0ce8e45f301ac0fd84f7d5d27d81b63103768f89
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567708"
---
# <a name="azure-private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Privátní odkaz Azure pro Azure SQL Database a Azure synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Privátní odkaz vám umožní připojit se k různým službám PaaS v Azure prostřednictvím **privátního koncového bodu**. Seznam služeb PaaS, které podporují funkce privátního propojení, najdete na stránce [dokumentace k privátním odkazům](../../private-link/index.yml) . Privátní koncový bod je privátní IP adresa v konkrétní [virtuální](../../virtual-network/virtual-networks-overview.md) síti a podsíti.

> [!IMPORTANT]
> Tento článek se týká Azure SQL Database a Azure synapse Analytics. Pro zjednodušení pojem "Database" odkazuje jak na databáze Azure SQL Database, tak na Azure synapse Analytics. Podobně všechny odkazy na server se odkazují na [logický SQL Server](logical-servers.md) , který je hostitelem Azure SQL Database a Azure synapse Analytics. Tento článek se *nevztahuje na* **spravovanou instanci SQL Azure**.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Jak nastavit privátní odkaz pro Azure SQL Database 

### <a name="creation-process"></a>Proces vytváření
Soukromé koncové body lze vytvořit pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure:
- [Portál](../../private-link/create-private-endpoint-portal.md)
- [PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Rozhraní příkazového řádku](../../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Proces schválení
Po vytvoření privátního koncového bodu (PE) správcem sítě může správce SQL spravovat připojení privátního koncového bodu (PEC) k SQL Database.

1. V Azure Portal přejděte na prostředek serveru, jak je znázorněno na snímku obrazovky níže.

    - (1) v levém podokně vyberte připojení privátního koncového bodu.
    - (2) zobrazuje seznam všech připojení privátního koncového bodu (PECs).
    - (3) snímek obrazovky s odpovídajícím privátním koncovým bodem (PE) ![ všech Pecs][3]

1. Vyberte jednotlivé řadiče PEC ze seznamu tím, že je vyberete.
![Vybraná obrazovka PEC][6]

1. Správce SQL se může rozhodnout pro schválení nebo zamítnutí serveru PEC a volitelně také přidat krátkou odpověď na text.
![Snímek obrazovky s schválením typu PEC][4]

1. Po schválení nebo odmítnutí bude seznam odpovídat příslušnému stavu spolu s textem odpovědi.
![Snímek obrazovky všech PECs po schválení][5]

## <a name="on-premises-connectivity-over-private-peering"></a>Místní připojení přes privátní partnerský vztah

Když se zákazníci připojí k veřejnému koncovému bodu z místních počítačů, musí se jejich IP adresa přidat do brány firewall založené na protokolu IP pomocí [pravidla brány firewall na úrovni serveru](firewall-create-server-level-portal-quickstart.md). I když tento model funguje dobře a umožňuje přístup k jednotlivým počítačům pro vývoj nebo testování, je obtížné ho spravovat v produkčním prostředí.

Pomocí privátního propojení můžou zákazníci povolit přístup mezi různými místy k privátnímu koncovému bodu pomocí tunelového propojení [ExpressRoute](../../expressroute/expressroute-introduction.md), privátního partnerského vztahu nebo VPN. Zákazníci pak můžou zakázat veškerý přístup přes Veřejný koncový bod a nepoužívat bránu firewall založenou na protokolu IP k povolení jakýchkoli IP adres.

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Případy použití privátního odkazu pro Azure SQL Database 

Klienti se můžou připojit k privátnímu koncovému bodu ze stejné virtuální sítě, partnerské virtuální sítě ve stejné oblasti nebo přes virtuální síť přes připojení k virtuální síti napříč různými oblastmi. Klienti se navíc mohou připojit z místního prostředí pomocí ExpressRoute, privátního partnerského vztahu nebo tunelového propojení VPN. Níže je zjednodušený diagram znázorňující běžné případy použití.

 ![Diagram možností připojení][1]

Kromě toho můžou služby, které neběží přímo ve virtuální síti, ale s ní integrovat (například App Service webové aplikace nebo funkce), taky může dosáhnout privátního připojení k databázi. Další informace o tomto konkrétním případu použití najdete v tématu [Webová aplikace s privátním připojením ke scénáři architektury Azure SQL Database](/azure/architecture/example-scenario/private-web-app/private-web-app) .

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network"></a>Otestujte připojení k SQL Database z virtuálního počítače Azure ve stejné virtuální síti.

V tomto scénáři Předpokládejme, že jste vytvořili virtuální počítač Azure s Windows serverem 2016. 

1. [Spusťte relaci vzdálené plochy (RDP) a připojte se k virtuálnímu počítači](../../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Pak můžete provést některé základní kontroly připojení, abyste zajistili, že se virtuální počítač připojuje k SQL Database prostřednictvím privátního koncového bodu pomocí následujících nástrojů:
    1. Telnet
    1. PsPing
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Zjištění připojení pomocí Telnet

[Klient služby Telnet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) je funkce systému Windows, kterou lze použít k otestování připojení. V závislosti na verzi operačního systému Windows možná budete muset tuto funkci povolit explicitně. 

Po instalaci Telnet otevřete okno příkazového řádku. Spusťte příkaz Telnet a zadejte IP adresu a soukromý koncový bod databáze v SQL Database.

```
>telnet 10.1.1.5 1433
```

Po úspěšném připojení Telnet se v příkazovém okně zobrazí prázdná obrazovka, jak ukazuje následující obrázek:

 ![Diagram Telnet][2]

### <a name="check-connectivity-using-psping"></a>Ověření připojení pomocí Psping

[Psping](/sysinternals/downloads/psping) se dá použít k ověření, že připojení privátního koncového bodu (PEC) naslouchá připojením na portu 1433.

Spusťte psping následujícím způsobem zadáním plně kvalifikovaného názvu domény pro logický SQL Server a port 1433:

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

Výstup ukazuje, že Psping může testovat privátní IP adresu přidruženou k řadiči PEC.

### <a name="check-connectivity-using-nmap"></a>Ověření připojení pomocí nmap

Nmap (mapovač sítě) je bezplatný a open source nástroj, který se používá pro zjišťování sítě a auditování zabezpečení. Další informace a odkaz ke stažení najdete v části https://nmap.org . Tento nástroj můžete použít k zajištění toho, aby privátní koncový bod naslouchal připojení na portu 1433.

Spusťte Nmap následujícím způsobem zadáním rozsahu adres podsítě, která je hostitelem privátního koncového bodu.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Výsledkem je, že jedna IP adresa je nahoru. který odpovídá IP adrese privátního koncového bodu.

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Ověření připojení pomocí SQL Server Management Studio (SSMS)
> [!NOTE]
> Pro klienty () použijte **plně kvalifikovaný název domény (FQDN)** serveru v připojovacích řetězcích `<server>.database.windows.net` . Jakékoli pokusy o přihlášení provedené přímo na IP adrese nebo pomocí plně kvalifikovaného názvu domény () privátního propojení `<server>.privatelink.database.windows.net` se nezdaří. Toto chování je záměrné, protože privátní koncový bod směruje provoz do brány SQL v oblasti a pro úspěšné přihlášení musí být zadán správný plně kvalifikovaný název domény.

Pokud se [chcete k SQL Database připojit pomocí SSMS](connect-query-ssms.md), postupujte podle těchto kroků. Po připojení k SQL Database pomocí SSMS ověřte, že se připojujete z privátní IP adresy virtuálního počítače Azure spuštěním následujícího dotazu:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="data-exfiltration-prevention"></a>Prevence exfiltrace dat

Exfiltrace dat v Azure SQL Database je v případě, že ověřený uživatel, jako je správce databáze, může extrahovat data z jednoho systému a přesunout ho do jiného umístění nebo systému mimo organizaci. Uživatel například přesune data do účtu úložiště, jehož vlastníkem je třetí strana.

Vezměte v úvahu scénář s uživatelem běžícím SQL Server Management Studio (SSMS) ve virtuálním počítači Azure, který se připojuje k databázi v SQL Database. Tato databáze je v datovém centru Západní USA. Následující příklad ukazuje, jak omezit přístup k veřejným koncovým bodům na SQL Database pomocí řízení přístupu k síti.

1. Nastavením povolit službám Azure na **off** zakažte veškerý provoz služeb azure pro SQL Database prostřednictvím veřejného koncového bodu. Ujistěte se, že nejsou v pravidlech brány firewall na úrovni serveru a databáze povolená žádná IP adresa. Další informace najdete v tématu [Azure SQL Database a Azure synapse Analytics Network Access Controls](network-access-controls-overview.md).
1. Povolí provoz do databáze v SQL Database jenom pomocí privátní IP adresy virtuálního počítače. Další informace najdete v článcích o [pravidlech brány firewall](firewall-configure.md)pro [službu Endpoint Service](vnet-service-endpoint-rule-overview.md) a virtuální sítě.
1. Na virtuálním počítači Azure upřesněte rozsah odchozího připojení pomocí [skupin zabezpečení sítě (skupin zabezpečení sítě)](../../virtual-network/manage-network-security-group.md) a značek služeb následujícím způsobem.
    - Zadejte pravidlo NSG, které povolí provoz pro tag Service = SQL. WestUs – umožňuje připojení k SQL Database jenom v Západní USA
    - Zadejte pravidlo NSG (s **vyšší prioritou**) pro odepření provozu pro značku služby = SQL – odepření připojení k SQL Database ve všech oblastech.

Na konci této instalace se virtuální počítač Azure může připojit pouze k databázi v SQL Database v oblasti Západní USA. Připojení ale není omezené na izolovanou databázi v SQL Database. Virtuální počítač se stále může připojit k libovolné databázi v Západní USA oblasti, včetně databází, které nejsou součástí předplatného. I když jsme snížili rozsah exfiltrace dat ve výše uvedeném scénáři na konkrétní oblast, neodstraníme ji úplně.

Pomocí privátního propojení teď můžou zákazníci nastavit řízení přístupu k síti, jako je skupin zabezpečení sítě, aby se omezil přístup k privátnímu koncovému bodu. Jednotlivé prostředky Azure PaaS se pak namapují na konkrétní soukromé koncové body. Škodlivý program Insider má přístup jenom k mapovanému prostředku PaaS (například k databázi v SQL Database) a žádnému jinému prostředku. 

## <a name="limitations"></a>Omezení 
Připojení k privátnímu koncovému bodu podporují **proxy server** jenom jako [zásady připojení](connectivity-architecture.md#connection-policy) .


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network"></a>Připojení z virtuálního počítače Azure ve Virtual Network s partnerským vztahem 

Nakonfigurujte [partnerský vztah virtuálních sítí](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) tak, aby navázal připojení k SQL Database z virtuálního počítače Azure v partnerské virtuální síti.

## <a name="connecting-from-an-azure-vm-in-virtual-network-to-virtual-network-environment"></a>Připojení z virtuálního počítače Azure ve virtuální síti do prostředí virtuální sítě

Nakonfigurujte [připojení virtuální sítě k virtuální síti VPN Gateway](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) , aby se navázalo připojení k databázi v SQL Database z virtuálního počítače Azure v jiné oblasti nebo předplatném.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Připojení z místního prostředí přes síť VPN

Pokud chcete navázat připojení z místního prostředí k databázi v SQL Database, vyberte a implementujte jednu z možností:
- [Připojení Point-to-site](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Připojení site-to-site VPN](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Okruh ExpressRoute](../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase-and-the-copy-statement"></a>Připojení ze služby Azure synapse Analytics k Azure Storage pomocí základu a příkazu COPY

Základ a příkaz COPY se běžně používá k načtení dat do služby Azure synapse Analytics z účtů Azure Storage. Pokud účet Azure Storage, ze kterého načítáte data z omezení, přistupuje jenom k sadě podsítí virtuální sítě přes soukromé koncové body, koncové body služby nebo brány firewall založené na protokolu IP, dojde k přerušení připojení z základu a příkazu Kopírovat k účtu. Pokud chcete povolit scénáře importu i exportu pomocí služby Azure synapse Analytics, která se připojuje k Azure Storage zabezpečené k virtuální síti, postupujte podle kroků uvedených [tady](vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). 

## <a name="next-steps"></a>Další kroky

- Přehled zabezpečení Azure SQL Database najdete v tématu [zabezpečení databáze](security-overview.md) .
- Přehled připojení Azure SQL Database najdete v tématu [Architektura připojení k Azure SQL](connectivity-architecture.md) .
- Může vás také zajímat [Webová aplikace s privátním připojením ke scénáři architektury Azure SQL Database](/azure/architecture/example-scenario/private-web-app/private-web-app) , která spojuje webovou aplikaci mimo virtuální síť s privátním koncovým bodem databáze.

<!--Image references-->
[1]: media/quickstart-create-single-database/pe-connect-overview.png
[2]: media/quickstart-create-single-database/telnet-result.png
[3]: media/quickstart-create-single-database/pec-list-before.png
[4]: media/quickstart-create-single-database/pec-approve.png
[5]: media/quickstart-create-single-database/pec-list-after.png
[6]: media/quickstart-create-single-database/pec-select.png