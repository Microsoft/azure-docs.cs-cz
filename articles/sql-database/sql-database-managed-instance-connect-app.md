---
title: Azure SQL Database Managed Instance připojit aplikace | Dokumentace Microsoftu
description: Tento článek popisuje, jak vaši aplikaci do Azure SQL Database Managed Instance připojit.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: fe73d5a2aa63cf127f5df835484cfcc75ef702aa
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514957"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Připojit vaši aplikaci ke spravované instanci Azure SQL Database

Už dnes máte více možností při rozhodování o tom, jak a kde hostujete vaší aplikace. 
 
Můžete se rozhodnout pro hostování aplikace v cloudu pomocí služby Azure App Service nebo některé z možností Azure pro virtuální síť (VNet) integrovaná jako je Azure App Service Environment, virtuální počítač, Virtual Machine Scale Sets. Může také využít hybridní cloudový přístup a ponechat přitom vašich aplikací v místním prostředí. 
 
Jakékoli volby provedené, můžete ho připojit do spravované Instance.  

![Vysoká dostupnost](./media/sql-database-managed-instance/application-deployment-topologies.png)  
## <a name="connect-an-application-inside-the-same-vnet"></a>Připojení aplikace uvnitř stejné virtuální síti 

Tento scénář je nejjednodušší. Virtuální počítače v rámci virtuální sítě můžete připojit přímo k sobě navzájem i v případě, že jsou v různých podsítích. To znamená, že všechno, co potřebujete k připojení aplikace v prostředí Azure aplikace nebo virtuálního počítače je odpovídajícím způsobem nastavit připojovací řetězec.  
 
## <a name="connect-an-application-inside-a-different-vnet"></a>Připojení aplikace uvnitř jiné virtuální síti 

Tento scénář je o něco složitější, protože Managed Instance je privátní IP adresou ve své vlastní virtuální sítě. Pro připojení, aplikace potřebuje přístup k virtuální síti, ve kterém je nasazená Managed Instance. Ano je třeba nejprve navázat připojení mezi aplikací a virtuální síť Managed Instance. Virtuální sítě nemusí být ve stejném předplatném, aby se tento scénář fungovat. 
 
Existují dvě možnosti pro propojení virtuálních sítí: 
- [Partnerský vztah Azure Virtual Network](../virtual-network/virtual-network-peering-overview.md) 
- Připojení typu VNet-to-VNet VPN gateway ([webu Azure portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [rozhraní příkazového řádku Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
Možnost vytvoření partnerského vztahu je vhodnější než ten, protože partnerský vztah pomocí páteřní síti Microsoft tedy z hlediska připojení, není žádný rozdíl znatelný latence mezi virtuálními počítači v partnerské virtuální síti a ve stejné virtuální síti. Služba VNet peering je omezená na sítě ve stejné oblasti.  
 
> [!IMPORTANT]
> Scénář sítě VNet peering pro Managed Instance je omezená na sítě ve stejné oblasti kvůli [omezení partnerských vztahů virtuálních sítí globální](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). 

## <a name="connect-an-on-premises-application"></a>Připojení místní aplikaci 

Spravovaná Instance je přístupný pouze prostřednictvím privátní IP adresu. Aby bylo možné získat přístup z místních, budete muset vytvoření připojení Site-to-Site mezi aplikace a virtuální sítí Managed Instance. 
 
Jak se připojit k virtuální síti Azure v místním existují dvě možnosti: 
- Připojení VPN typu Site-to-Site ([webu Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [rozhraní příkazového řádku Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [ExpressRoute](../expressroute/expressroute-introduction.md) připojení  
 
Pokud jste úspěšně navázáno připojení Azure k místní a nemůže navázat připojení k Managed Instance, zkontrolujte, jestli brána firewall má otevřené odchozí připojení na portu 1433 SQL také 11000 12000 rozsah portů pro přesměrování. 

## <a name="connect-an-application-on-the-developers-box"></a>Připojení aplikace ve službě box vývojáře

Spravovaná Instance je přístupný pouze prostřednictvím privátních IP adres tak aby bylo možné přistupovat z vašeho seznamu pro vývojáře, musíte nejprve navázat připojení mezi vaší pole pro vývojáře a ve virtuální síti spravované Instance. Uděláte to tak, konfigurace připojení typu Point-to-Site k virtuální síti s použitím nativního ověřování certifikátů Azure. Další informace najdete v tématu [konfigurace připojení typu point-to-site pro připojení k Azure SQL Database Managed Instance z místní počítač](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Připojení z místního s využitím partnerského vztahu virtuální sítě
Jiný scénář implementovat zákazníky je instalaci brány sítě VPN v samostatné virtuální sítě a předplatné z jedné hostování Managed Instance. Dvě virtuální sítě pak vytvoření partnerského vztahu. Následující ukázka diagram architektury ukazuje, jak to lze provést.

![Partnerské vztahy virtuálních sítí](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Jakmile máte nastavenou základní infrastrukturu, budete muset upravit některé nastavení tak, aby brána sítě VPN můžete zobrazit IP adresy ve virtuální síti, který je hostitelem Managed Instance. Uděláte to tak, proveďte následující změny velmi specifický pod **nastavení partnerského vztahu**.
1.  Ve virtuální síti, který je hostitelem brány sítě VPN, přejděte na **partnerské vztahy**k Managed Instance partnerský vztah virtuální sítě připojení a pak klikněte na tlačítko **povolit průchod bránou**.
2.  Ve virtuální síti, který je hostitelem Managed Instance, přejděte na **partnerské vztahy**ke službě VPN Gateway partnerský vztah virtuální sítě připojení a pak klikněte na tlačítko **používat vzdálené brány**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Připojení aplikace hostované služby Azure App Service 

Spravovaná Instance je přístupný pouze prostřednictvím privátních IP adres tak, aby bylo možné získat přístup pomocí služby Azure App Service je nutné nejprve navázat připojení mezi aplikací a virtuální síť Managed Instance. Zobrazit [integrujte svou aplikaci s Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).  
 
Řešení potíží, najdete v části [řešení potíží s virtuálními sítěmi a aplikacemi](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Pokud nejde navázat připojení, zkuste [synchronizace konfigurace sítě](sql-database-managed-instance-sync-network-configuration.md). 
 
Zvláštním případem připojení služby Azure App Service k Managed Instance je při vám integraci že služby Azure App Service k síti s partnerskými uzly do spravované Instance virtuální sítě. Tento případ vyžaduje následující konfigurace až: 

- Virtuální síť spravované Instance není musí mít bránu  
- Spravované Instance virtuální síť musí mít nastavenou možnost použití vzdálené brány 
- Partnerské virtuální síti musí mít možnost přenosu brány povolit nastavení 
 
Tento scénář je znázorněn v následujícím diagramu:

![vytvoření partnerského vztahu integrované aplikace](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>Funkci integrace virtuální sítě nejde integrovat do aplikace virtuální síť, která má bránu ExpressRoute. I když bránu ExpressRoute je nakonfigurované v režimu koexistence nefunguje integrace virtuální sítě. Pokud potřebujete přístup k prostředkům pomocí připojení ExpressRoute, můžete pomocí služby App Service Environment, která běží ve vaší virtuální síti.
>
 
## <a name="troubleshooting-connectivity-issues"></a>Řešení potíží s připojením

Pro řešení potíží s připojením, zkontrolujte následující položky:
- Pokud se nemůžete připojit k Managed Instance z virtuálního počítače Azure v rámci stejné virtuální síti, ale jiné podsíti, zkontrolujte, pokud máte skupinu zabezpečení sítě v podsíti virtuálních počítačů, která může blokovat přístup k nastavení. Všimněte si také, že budete muset otevřít odchozí připojení na portu 1433 SQL, jakož i porty v rozsahu 11000 12000, protože ty jsou potřeba pro připojení prostřednictvím přesměrování v rámci Azure. 
- Zkontrolujte, že šíření protokolu BGP je nastavená na **povoleno** pro směrovací tabulku přidruženou k virtuální síti.
- Pokud používáte P2S VPN, zkontrolujte konfiguraci na portálu Azure, pokud chcete zobrazit, pokud se zobrazí **příchozí a odchozí přenos** čísla. Nenulový čísla označují, že Azure je směrování provozu do a z místní.

   ![příchozí a odchozí přenos čísla](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Zkontrolujte, že klientský počítač, (, na kterém běží klient VPN) položky trasy pro všechny virtuální sítě, které potřebujete pro přístup k. Trasy se ukládají v `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.


   ![Route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Jak je znázorněno na tomto obrázku jsou dvě položky pro každou virtuální síť a třetí položka pro koncový bod VPN, který je nakonfigurovaný na portálu.

   Další způsob kontroly trasy je pomocí následujícího příkazu. Výstup ukazuje trasy pro různé podsítě: 

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

- Pokud používáte partnerský vztah virtuální sítě, ujistěte se, že jste postupovali podle pokynů pro nastavení [povolit průchod bránou a používat vzdálené brány](#connect-from-on-premises-with-vnet-peering). 

## <a name="required-versions-of-drivers-and-tools"></a>Požadovaná verze ovladače a nástrojů

Následující minimální verze nástrojů a ovladačů se doporučuje, pokud se chcete připojit k Managed Instance:

| Ovladač nebo nástroj | Verze |
| --- | --- |
|.NET Framework | 4.6.1 (nebo .NET Core) | 
|Ovladač ODBC    | v17 |
|Ovladač PHP | 5.2.0 |
|Ovladač JDBC    | 6.4.0 |
|Ovladač Node.js | 2.1.1 |
|Ovladač OLEDB   | 18.0.2.0 |
|SSMS   | 17.8.1 nebo [vyšší](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) |

## <a name="next-steps"></a>Další postup

- Informace o spravované instanci najdete v tématu [Co je spravovaná instance](sql-database-managed-instance.md).
- Kurz ukazuje, jak vytvořit nový Managed Instance, najdete v tématu [vytvoříte Managed Instance](sql-database-managed-instance-get-started.md).
