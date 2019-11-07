---
title: Azure SQL Database aplikace spravované instance Connect
description: Tento článek popisuje, jak připojit aplikaci k Azure SQL Database spravované instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 5dc287ab58636b4921c14ae71f9a62ab36d9c596
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688237"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Připojení aplikace k Azure SQL Database Managed instance

Dnes máte několik možností, jak se rozhodnout, jak a kde budete hostovat svoji aplikaci.

Můžete se rozhodnout hostovat aplikace v cloudu buď pomocí Azure App Service nebo některých integrovaných možností virtuální sítě Azure (VNet), jako je Azure App Service Environment, virtuální počítač a sada škálování virtuálního počítače. Můžete také využít hybridní cloudový přístup a zachovat místní aplikace.

Bez ohledu na to, kterou možnost provedete, můžete ji připojit ke spravované instanci.  

![Vysoká dostupnost](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Připojení aplikace v rámci stejné virtuální sítě

Tento scénář je nejjednodušší. Virtuální počítače uvnitř virtuální sítě se můžou přímo připojit, i když jsou v různých podsítích. To znamená, že ke správnému nastavení připojovacího řetězce je potřeba, abyste k připojení aplikace v prostředí aplikace Azure nebo ve virtuálním počítači nastavili odpovídající připojovací řetězec.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Připojení aplikace v jiné virtuální síti

Tento scénář je trochu složitější, protože spravovaná instance má privátní IP adresu ve své vlastní virtuální síti. Aplikace potřebuje přístup k virtuální síti, kde je nasazená spravovaná instance. Nejdříve je třeba vytvořit propojení mezi aplikací a virtuální sítí spravované instance. Aby mohl tento scénář fungovat, nemusí být virtuální sítě ve stejném předplatném.

K dispozici jsou dvě možnosti připojení virtuální sítě:

- [Partnerský vztah Azure Virtual Network](../virtual-network/virtual-network-peering-overview.md)
- Brána VPN typu VNet-to-VNet ([Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [POWERSHELL](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Možnost partnerského vztahu je vhodnější, protože partnerský vztah používá páteřní síť Microsoftu, takže z perspektivy připojení nedochází k znatelnému rozdílu v latenci mezi virtuálními počítači v partnerské virtuální síti a ve stejné virtuální síti. Síť VNet peering je omezená na sítě ve stejné oblasti.  

> [!IMPORTANT]
> Scénář partnerského vztahu virtuálních sítí pro spravovanou instanci je omezený na sítě ve stejné oblasti, protože se jedná o [omezení globálního partnerského vztahu Virtual Network](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Další podrobnosti najdete v článku o nejčastějších dotazech v příslušné části [Azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . 

## <a name="connect-an-on-premises-application"></a>Připojení místní aplikace

K spravované instanci lze přistupovat pouze přes privátní IP adresu. Aby k nim bylo možné přistupovat z místního prostředí, je nutné vytvořit připojení typu Site-to-site mezi aplikací a virtuální sítí spravované instance.

Existují dvě možnosti, jak se připojit k virtuální síti Azure v místním prostředí:

- Připojení VPN typu Site-to-Site ([Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [POWERSHELL](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- Připojení [ExpressRoute](../expressroute/expressroute-introduction.md)  

Pokud jste připojení k Azure úspěšně navázali a nemůžete navázat připojení ke spravované instanci, ověřte, jestli má brána firewall otevřené odchozí připojení na portu SQL 1433 a také 11000-11999 rozsah portů pro přesměrování.

## <a name="connect-an-application-on-the-developers-box"></a>Připojení aplikace v poli vývojářů

K spravované instanci se dá přistupovat jenom přes soukromou IP adresu. aby k ní měli přístup z pole pro vývojáře, musíte nejdřív vytvořit připojení mezi vaším vývojářským polem a virtuální sítí spravované instance. Provedete to tak, že nakonfigurujete připojení typu Point-to-site k virtuální síti s použitím nativního ověřování certifikátů Azure. Další informace najdete v tématu [Konfigurace připojení typu Point-to-site pro připojení k Azure SQL Database spravované instanci z místního počítače](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Připojení z místního prostředí pomocí partnerského vztahu virtuálních sítí

Dalším scénářem implementovaným zákazníky je, kde je služba VPN Gateway nainstalovaná v samostatné virtuální síti a v rámci předplatného od jedné hostující spravované instance. Tyto dvě virtuální sítě jsou pak partnerského vztahu. Následující příklad diagramu architektury ukazuje, jak to lze provést.

![Partnerské vztahy virtuálních sítí](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Jakmile máte nastavenou základní infrastrukturu, je nutné upravit některá nastavení tak, aby VPN Gateway mohla zobrazit IP adresy ve virtuální síti, která je hostitelem spravované instance. Provedete to tak, že v **Nastavení partnerského vztahu**provedete následující velmi specifické změny.

1. Ve virtuální síti, která hostuje bránu VPN, přejděte do **partnerských vztahů**, pak na připojení spravované instance s partnerským vztahem a potom klikněte na možnost **povolení přenosu brány**.
2. Ve virtuální síti, která je hostitelem spravované instance, přejděte do **partnerských vztahů**, pak na připojení VPN Gateway partnerské virtuální sítě a potom klikněte na **použít vzdálené brány**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Připojení hostované aplikace Azure App Service

K spravované instanci lze přistupovat pouze prostřednictvím privátní IP adresy. aby k ní bylo možné přistupovat z Azure App Service je třeba nejprve vytvořit připojení mezi aplikací a virtuální sítí spravované instance. Viz [integrace aplikace s Virtual Network Azure](../app-service/web-sites-integrate-with-vnet.md).  

Řešení potíží najdete v tématu [řešení potíží s virtuální sítě a aplikacemi](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Pokud nelze navázat připojení, zkuste [synchronizovat konfiguraci sítě](sql-database-managed-instance-sync-network-configuration.md).

Zvláštním případem připojení Azure App Service ke spravované instanci je při integraci Azure App Service do sítě partnerského vztahu k virtuální síti spravované instance. Tento případ vyžaduje, aby se nastavila následující konfigurace:

- Virtuální síť spravované instance nesmí mít bránu.  
- Virtuální síť spravované instance musí mít nastavenou možnost používat vzdálené brány.
- Virtuální síť s partnerským vztahem musí mít nastavenou možnost povolený přenos brány.

Tento scénář je znázorněný v následujícím diagramu:

![partnerský vztah integrovaných aplikací](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>Funkce integrace virtuální sítě Neintegruje aplikaci s virtuální sítí, která má bránu ExpressRoute. I v případě, že je brána ExpressRoute nakonfigurovaná v režimu koexistence, integrace virtuální sítě nefunguje. Pokud potřebujete přístup k prostředkům prostřednictvím připojení ExpressRoute, můžete použít App Service Environment, která běží ve vaší virtuální síti.

## <a name="troubleshooting-connectivity-issues"></a>Řešení potíží s připojením

Pokud chcete řešit potíže s připojením, přečtěte si následující témata:

- Pokud se nemůžete připojit ke spravované instanci z virtuálního počítače Azure v rámci stejné virtuální sítě, ale v jiné podsíti, ověřte, jestli máte v podsíti virtuálních počítačů nastavenou skupinu zabezpečení sítě, která by mohla blokovat přístup. Kromě toho si všimněte, že musíte otevřít odchozí připojení na portu SQL 1433 a také porty v rozsahu 11000-11999, protože jsou potřeba pro připojení přes hranice Azure v rámci přesměrování.
- Zajistěte, aby bylo pro směrovací tabulku přidružené k virtuální síti **povoleno** šíření protokolu BGP.
- Pokud používáte P2S VPN, zkontrolujte konfiguraci v Azure Portal a podívejte se, jestli se zobrazují čísla pro příchozí **nebo odchozí přenosy** . Nenulová čísla označují, že Azure směruje provoz do místního prostředí.

   ![vstupní/výstupní čísla](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Ověřte, zda má klientský počítač (se spuštěným klientem sítě VPN) položky směrování pro všechny virtuální sítě, ke kterým potřebujete získat přístup. Trasy jsou uloženy v `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![Route. txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Jak je znázorněno na tomto obrázku, jsou k dispozici dvě položky pro každou virtuální síť a třetí záznam pro koncový bod VPN, který je nakonfigurován na portálu.

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

- Pokud používáte partnerský vztah virtuálních sítí, ujistěte se, že jste postupovali podle pokynů k nastavení [povolení přenosu brány a používání vzdálených bran](#connect-from-on-premises-with-vnet-peering).

## <a name="required-versions-of-drivers-and-tools"></a>Požadované verze ovladačů a nástrojů

Pokud se chcete připojit ke spravované instanci, doporučuje se používat následující minimální verze nástrojů a ovladačů:

| Ovladač/nástroj | Verze |
| --- | --- |
|.NET Framework | 4.6.1 (nebo .NET Core) |
|Ovladač ODBC| v17 |
|Ovladač PHP| 5.2.0 |
|Ovladač JDBC| 6.4.0 |
|Ovladač Node.js| bodů |
|Ovladač OLEDB| 18.0.2.0 |
|SSMS| 18,0 nebo [vyšší](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) nebo vyšší |

## <a name="next-steps"></a>Další kroky

- Informace o spravované instanci najdete v tématu [Co je spravovaná instance](sql-database-managed-instance.md).
- Kurz, ve kterém se dozvíte, jak vytvořit novou spravovanou instanci, najdete v tématu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md).
