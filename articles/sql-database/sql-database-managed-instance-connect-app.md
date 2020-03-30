---
title: Aplikace pro připojení spravované instance
description: Tento článek popisuje, jak připojit aplikaci ke spravované instanci Azure SQL Database.
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
ms.openlocfilehash: 9f592c345b7cfcf5f21d816fde1fae6b8e6b98c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823380"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Připojení aplikace ke spravované instanci Azure SQL Database

Dnes máte několik možností při rozhodování o tom, jak a kde budete hostovat aplikaci.

Můžete se rozhodnout hostovat aplikace v cloudu buď pomocí služby Azure App Service nebo některé z integrovaných možností virtuální sítě Azure (VNet), jako je Azure App Service Environment, Virtual Machine, Virtual Machine Scale Set. Můžete také použít přístup hybridního cloudu a udržovat vaše aplikace místně.

Bez ohledu na to, jakou volbu jste provedli, můžete ji připojit ke spravované instanci.  

![vysoká dostupnost](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Připojení aplikace uvnitř stejné virtuální sítě

Tento scénář je nejjednodušší. Virtuální počítače uvnitř virtuální sítě se můžou navzájem připojit přímo, i když jsou uvnitř různých podsítí. To znamená, že vše, co potřebujete k připojení aplikace uvnitř aplikačního prostředí Azure nebo virtuálního počítače, je správně nastavit připojovací řetězec.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Připojení aplikace v jiné virtuální síti

Tento scénář je trochu složitější, protože spravovaná instance má privátní IP adresu ve své vlastní virtuální síti. K připojení aplikace potřebuje přístup k virtuální síti, kde se nasazuje spravovaná instance. Takže nejprve musíte vytvořit připojení mezi aplikací a virtuální sítí spravované instance. Virtuální sítě nemusí být ve stejném předplatném, aby tento scénář fungoval.

Existují dvě možnosti pro připojení virtuálních sítí:

- [Partnerský vztah virtuální sítě Azure](../virtual-network/virtual-network-peering-overview.md)
- Brána VPN virtuální sítě k virtuální síti[(portál Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)Portal , [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI)](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)

Možnost partnerského vztahu je vhodnější, protože partnerský vztah používá páteřní síť Microsoftu, takže z hlediska připojení není žádný znatelný rozdíl v latenci mezi virtuálními počítači v partnerské virtuální síti a ve stejné virtuální síti. Partnerský vztah virtuální sítě je omezen na sítě ve stejné oblasti.  

> [!IMPORTANT]
> Scénář partnerského vztahu virtuální sítě pro spravovanou instanci je omezen na sítě ve stejné oblasti z důvodu [omezení partnerského vztahu globální virtuální sítě](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Další podrobnosti najdete v příslušné části článku [nejčastější dotazy virtuálních sítí Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 

## <a name="connect-an-on-premises-application"></a>Připojení místní aplikace

Ke spravované instanci lze přistupovat pouze prostřednictvím privátní IP adresy. Chcete-li k němu přistupovat z místního prostředí, musíte vytvořit připojení site-to-site mezi aplikací a virtuální sítí spravované instance.

Existují dvě možnosti, jak se připojit místně k virtuální síti Azure:

- Připojení VPN od lokality k webu ([portál Azure,](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [Připojení ExpressRoute](../expressroute/expressroute-introduction.md)  

Pokud jste úspěšně navázali místní připojení k Azure a nemůžete navázat připojení ke spravované instanci, zkontrolujte, jestli má brána firewall otevřené odchozí připojení na portu SQL 1433 a také 11000-11999 rozsah portů pro přesměrování.

## <a name="connect-an-application-on-the-developers-box"></a>Připojení aplikace v Developer Boxu

Ke spravované instanci lze přistupovat pouze prostřednictvím privátní IP adresy, takže abyste k ní měli přístup z vývojářského pole, musíte nejdřív navázat spojení mezi vývojářským polem a virtuální sítí spravované instance. Chcete-li tak učinit, nakonfigurujte připojení typu Point-to-Site k virtuální síti pomocí nativního ověřování certifikátu Azure. Další informace najdete [v tématu Konfigurace připojení point-to-site pro připojení k spravované instanci Azure SQL Database ze místního počítače](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Připojení z místního prostředí pomocí partnerského vztahu virtuální sítě

Dalším scénářem implementovaným zákazníky je místo, kde je brána VPN nainstalována v samostatné virtuální síti a předplatné z jedné hostované spravované instance. Dvě virtuální sítě jsou pak peered. Následující ukázkový diagram architektury ukazuje, jak to lze implementovat.

![Partnerské vztahy virtuálních sítí](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Jakmile máte nastavenou základní infrastrukturu, musíte upravit některá nastavení tak, aby brána VPN viděla IP adresy ve virtuální síti, která je hostitelem spravované instance. Chcete-li tak učinit, proveďte následující velmi specifické změny v **nastavení partnerského vztahu**.

1. Ve virtuální síti, která je hostitelem brány VPN, přejděte na **partnerské partnerské partnerské partnerské vztahy**, pak na připojení partnerské sítě spravované instance a klikněte na **Povolit přenos brány**.
2. Ve virtuální síti, která je hostitelem spravované instance, přejděte na **partnerské partnerské partnery**, pak na připojení partnerské sítě vpn gateway a klikněte na **Použít vzdálené brány**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Připojení hostované aplikace služby Azure App Service

Ke spravované instanci lze přistupovat pouze prostřednictvím privátní IP adresy, takže abyste k ní měli přístup ze služby Azure App Service, musíte nejdřív navázat připojení mezi aplikací a virtuální sítí spravované instance. Viz [Integrace aplikace s virtuální sítí Azure](../app-service/web-sites-integrate-with-vnet.md).  

Řešení potíží najdete [v tématu Poradce při potížích s virtuálními sítěmi a aplikacemi](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Pokud nelze navázat připojení, zkuste [synchronizovat konfiguraci sítě](sql-database-managed-instance-sync-network-configuration.md).

Zvláštní případ připojení služby Azure App Service ke spravované instanci je, když jste integrovali službu Azure App Service do sítě partnerské sítě spravanou instancí. Tento případ vyžaduje následující konfiguraci, která má být nastavena:

- Virtuální síť spravované instance nesmí mít bránu.  
- Virtuální síť spravované instance musí mít nastavenou možnost Použít vzdálené brány.
- Partnerská virtuální síť musí mít nastavenou možnost Povolit přenos brány.

Tento scénář je znázorněno v následujícím diagramu:

![partnerský vztah integrované aplikace](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>Funkce integrace virtuální sítě neintegruje aplikaci s virtuální sítí, která má bránu ExpressRoute. I v případě, že je brána ExpressRoute nakonfigurovaná v režimu koexistence, integrace virtuální sítě nefunguje. Pokud potřebujete přístup k prostředkům prostřednictvím připojení ExpressRoute, pak můžete použít prostředí služby App Service, které běží ve vaší virtuální síti.

## <a name="troubleshooting-connectivity-issues"></a>Řešení potíží s připojením

Řešení potíží s připojením naleznete v následujících tématech:

- Pokud se nemůžete připojit ke spravované instanci z virtuálního počítače Azure v rámci stejné virtuální sítě, ale v jiné podsíti, zkontrolujte, jestli máte v podsíti virtuálního počítače nastavenou skupinu zabezpečení sítě, která může blokovat přístup. Dále si všimněte, že je třeba otevřít odchozí připojení na portu SQL 1433, stejně jako porty v rozsahu 11000-11999, protože jsou potřebné pro připojení přes přesměrování uvnitř hranice Azure.
- Ujistěte se, že šíření Protokolu BGP je **nastaveno** na Povoleno pro směrovací tabulku přidruženou k virtuální síti.
- Pokud používáte P2S VPN, zkontrolujte konfiguraci na webu Azure Portal a zjistěte, jestli se zobrazí čísla **Příchozí přenos dat/odchozí přenos** dat. Nenulová čísla označují, že Azure směruje provoz do nebo z místního.

   ![příchozí/odchozí čísla](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Zkontrolujte, zda klientský počítač (se kterým je klient VPN) má položky trasy pro všechny virtuální sítě, ke kterým potřebujete přístup. Trasy jsou uloženy `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`v aplikaci .

   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Jak je znázorněno na tomto obrázku, existují dvě položky pro každou zapojenou virtuální síť a třetí položku pro koncový bod VPN, který je nakonfigurovaný na portálu.

   Dalším způsobem, jak zkontrolovat trasy, je pomocí následujícího příkazu. Výstup zobrazuje trasy do různých podsítí:

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

- Pokud používáte partnerský vztah virtuální sítě, ujistěte se, že jste postupovali podle pokynů pro nastavení [Povolit přenos brány a používat vzdálené brány](#connect-from-on-premises-with-vnet-peering).

## <a name="required-versions-of-drivers-and-tools"></a>Požadované verze ovladačů a nástrojů

Chcete-li se připojit ke spravované instanci, doporučujeme následující minimální verze nástrojů a ovladačů:

| Ovladač/nástroj | Version |
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

- Informace o spravované instanci najdete v [tématu Co je spravovaná instance](sql-database-managed-instance.md).
- Kurz, který vám ukáže, jak vytvořit novou spravovanou instanci, najdete [v tématu Vytvoření spravované instance](sql-database-managed-instance-get-started.md).
