---
title: Připojení aplikace k spravované instanci SQL
titleSuffix: Azure SQL Managed Instance
description: Tento článek popisuje, jak připojit vaši aplikaci ke spravované instanci Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 9d58a8c1dc79c10ed42fd1675115eb14f2ad4d3e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283709"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Připojení aplikace ke službě Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dnes máte několik možností, jak se rozhodnout, jak a kde budete hostovat svoji aplikaci.

Můžete se rozhodnout hostovat aplikace v cloudu pomocí Azure App Service nebo některé z integrovaných možností virtuální sítě Azure, jako jsou Azure App Service Environment, Azure Virtual Machines a Virtual Machine Scale Sets. Můžete také využít hybridní cloudový přístup a zachovat místní aplikace.

Bez ohledu na to, kterou možnost provedete, můžete připojit k spravované instanci Azure SQL. 

![Vysoká dostupnost](./media/connect-application-instance/application-deployment-topologies.png)

Tento článek popisuje, jak připojit aplikaci ke službě Azure SQL Managed Instance v několika různých scénářích aplikací. 

## <a name="connect-inside-the-same-vnet"></a>Připojit se v rámci stejné virtuální sítě

Nejjednodušší scénář je připojení aplikace ve stejné virtuální síti jako spravovaná instance SQL. Virtuální počítače uvnitř virtuální sítě se můžou vzájemně připojit přímo, i když jsou v různých podsítích. To znamená, že vše, co potřebujete k připojení aplikace v App Service Environment nebo virtuálním počítači, je nastavení připojovacího řetězce odpovídajícím způsobem.  

## <a name="connect-inside-a-different-vnet"></a>Připojení v jiné virtuální síti

Připojení aplikace v případě, že se nachází v jiné virtuální síti z spravované instance SQL je trochu složitější, protože spravovaná instance SQL má privátní IP adresy ve vlastní virtuální síti. Pro připojení aplikace potřebuje přístup k virtuální síti, ve které je nasazena spravovaná instance SQL. Proto potřebujete vytvořit propojení mezi aplikací a virtuální sítí spravované instance SQL. Aby mohl tento scénář fungovat, nemusí být virtuální sítě ve stejném předplatném.

Pro připojení virtuálních sítí existují dvě možnosti:

- [Partnerský vztah virtuální sítě Azure](../../virtual-network/virtual-network-peering-overview.md)
- Brána VPN typu VNet-to-VNet ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [POWERSHELL](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Partnerský vztah je vhodnější, protože používá páteřní síť Microsoftu, takže z perspektivy připojení nedochází k znatelnému rozdílu mezi virtuálními počítači v partnerské virtuální síti a ve stejné virtuální síti. Partnerský vztah virtuálních sítí je omezený na sítě ve stejné oblasti.  

> [!IMPORTANT]
> Scénář partnerského vztahu virtuální sítě pro spravovanou instanci SQL je omezený na sítě ve stejné oblasti z důvodu [omezení globálního partnerského vztahu virtuálních sítí](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Další podrobnosti najdete v článku o nejčastějších dotazech v příslušné části [Azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . 

## <a name="connect-from-on-premises"></a>Připojení z místního prostředí 

Místní aplikaci můžete také připojit k spravované instanci SQL. K spravované instanci SQL je možné přistupovat pouze přes privátní IP adresu. Aby k nim bylo možné přistupovat z místního prostředí, je nutné vytvořit připojení typu Site-to-site mezi aplikací a virtuální sítí spravované instance SQL.

Existují dvě možnosti, jak se připojit k virtuální síti Azure v místním prostředí:

- Připojení VPN typu Site-to-Site ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [POWERSHELL](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- Připojení [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)  

Pokud jste úspěšně navázali připojení místního prostředí k Azure a nemůžete navázat připojení k spravované instanci SQL, ověřte, jestli má brána firewall otevřené odchozí připojení na portu SQL 1433 a také 11000-11999 rozsah portů pro přesměrování.

## <a name="connect-the-developer-box"></a>Připojení pole pro vývojáře

Také je možné připojit pole pro vývojáře k spravované instanci SQL. K spravované instanci SQL se dá získat přístup jenom přes soukromou IP adresu, takže pokud k ní chcete přistupovat z pole pro vývojáře, musíte nejdřív vytvořit připojení mezi vaším polem pro vývojáře a virtuální sítí spravované instance SQL. Provedete to tak, že nakonfigurujete připojení typu Point-to-site k virtuální síti s použitím nativního ověřování certifikátů Azure. Další informace najdete v tématu  [Konfigurace připojení typu Point-to-site pro připojení ke spravované instanci Azure SQL z místního počítače](point-to-site-p2s-configure.md).

## <a name="connect-with-vnet-peering"></a>Připojení s partnerským vztahem virtuální sítě

Dalším scénářem implementovaným zákazníky je, že je Brána VPN nainstalovaná v samostatné virtuální síti a v rámci předplatného, které je hostitelem spravované instance SQL. Tyto dvě virtuální sítě jsou pak partnerského vztahu. Následující příklad diagramu architektury ukazuje, jak to lze provést.

![Partnerský vztah virtuální sítě](./media/connect-application-instance/vnet-peering.png)

Jakmile máte nastavenou základní infrastrukturu, budete muset změnit některá nastavení tak, aby brána VPN mohla zobrazit IP adresy ve virtuální síti, která je hostitelem spravované instance SQL. Provedete to tak, že v **Nastavení partnerského vztahu**provedete následující velmi specifické změny.

1. Ve virtuální síti, která hostuje bránu VPN, přejděte do **partnerských vztahů**, přejděte k partnerským připojením virtuální sítě pro SPRAVOVANOU instanci SQL a pak klikněte na možnost **povolení přenosu brány**.
2. Ve virtuální síti, která je hostitelem spravované instance SQL, přejděte na **partnerské vztahy**, přejděte na připojení s partnerskými virtuálními sítěmi pro bránu VPN a pak klikněte na **použít vzdálené brány**.

## <a name="connect-azure-app-service"></a>Připojit Azure App Service 

Můžete taky připojit aplikaci, která je hostovaná pomocí Azure App Service. K spravované instanci SQL se dá přistupovat jenom přes privátní IP adresu, takže pokud k ní chcete přistupovat z Azure App Service, musíte nejdřív vytvořit propojení mezi aplikací a virtuální sítí spravované instance SQL. Podívejte [se na téma integrace aplikace do služby Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md).  

Řešení potíží najdete v tématu [řešení potíží s virtuálními sítěmi a aplikacemi](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Pokud nelze navázat připojení, zkuste [synchronizovat konfiguraci sítě](azure-app-sync-network-configuration.md).

Zvláštním případem připojení Azure App Service k spravované instanci SQL je při integraci Azure App Service do sítě partnerského vztahu k virtuální síti spravované instance SQL. Tento případ vyžaduje, aby se nastavila následující konfigurace:

- Virtuální síť spravované instance SQL nesmí mít bránu.  
- Virtuální síť spravované instance SQL musí mít `Use remote gateways` nastavenou možnost.
- Virtuální síť s partnerským vztahem musí mít `Allow gateway transit` nastavenou možnost.

Tento scénář je znázorněný v následujícím diagramu:

![partnerský vztah integrovaných aplikací](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>Funkce integrace virtuální sítě Neintegruje aplikaci s virtuální sítí, která má bránu ExpressRoute. I v případě, že je brána ExpressRoute nakonfigurovaná v režimu koexistence, integrace virtuální sítě nefunguje. Pokud potřebujete přístup k prostředkům prostřednictvím připojení ExpressRoute, můžete použít App Service Environment, která běží ve vaší virtuální síti.

## <a name="troubleshooting-connectivity-issues"></a>Řešení potíží s připojením

Pokud chcete řešit potíže s připojením, přečtěte si následující témata:

- Pokud se nemůžete připojit ke spravované instanci SQL z virtuálního počítače Azure ve stejné virtuální síti, ale v jiné podsíti, ověřte, jestli máte v podsíti virtuálních počítačů nastavenou skupinu zabezpečení sítě, která by mohla blokovat přístup. Kromě toho otevřete odchozí připojení na portu SQL 1433 a také porty v rozsahu 11000-11999, protože ty jsou potřeba pro připojení přes hranice Azure v rámci přesměrování.
- Zajistěte, aby bylo pro směrovací tabulku přidružené k virtuální síti **povoleno** šíření protokolu BGP.
- Pokud používáte P2S VPN, zkontrolujte konfiguraci v Azure Portal a podívejte se, jestli se zobrazují čísla pro příchozí **nebo odchozí přenosy** . Nenulová čísla označují, že Azure směruje provoz do místního prostředí.

   ![vstupní/výstupní čísla](./media/connect-application-instance/ingress-egress-numbers.png)

- Ověřte, zda má klientský počítač (se spuštěným klientem VPN) položky směrování pro všechny virtuální sítě, ke kterým potřebujete získat přístup. Trasy jsou uloženy v `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` .

   ![route.txt](./media/connect-application-instance/route-txt.png)

   Jak je znázorněno na tomto obrázku, existují dvě položky pro každou danou virtuální síť a třetí záznam pro koncový bod VPN, který je nakonfigurován na portálu.

   Další možností, jak kontrolovat trasy, je pomocí následujícího příkazu. Výstup ukazuje trasy k různým podsítím:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Pokud používáte partnerský vztah virtuálních sítí, ujistěte se, že jste postupovali podle pokynů k nastavení [Povolení přenosů brány a používání vzdálených bran](#connect-from-on-premises).

- Pokud používáte partnerský vztah virtuálních sítí k připojení hostované aplikace Azure App Service a virtuální síť spravované instance SQL má rozsah veřejných IP adres, ujistěte se, že nastavení hostované aplikace umožní směrování odchozího provozu do veřejných IP sítí. Postupujte podle pokynů v tématu [integrace místní virtuální sítě](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration).

## <a name="required-versions-of-drivers-and-tools"></a>Požadované verze ovladačů a nástrojů

Pokud se chcete připojit ke spravované instanci SQL, doporučuje se používat následující minimální verze nástrojů a ovladačů:

| Ovladač/nástroj | Verze |
| --- | --- |
|.NET Framework | 4.6.1 (nebo .NET Core) |
|Ovladač ODBC| v17 |
|Ovladač PHP| 5.2.0 |
|Ovladač JDBC| 6.4.0 |
|Ovladač Node.js| 2.1.1 |
|Ovladač OLEDB| 18.0.2.0 |
|SSMS| 18,0 nebo [vyšší](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) nebo vyšší |

## <a name="next-steps"></a>Další kroky

- Informace o spravované instanci SQL najdete v tématu [co je Managed instance SQL?](sql-managed-instance-paas-overview.md).
- Kurz, ve kterém se dozvíte, jak vytvořit novou spravovanou instanci, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
