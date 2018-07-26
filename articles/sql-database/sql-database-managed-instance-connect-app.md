---
title: Azure SQL Database Managed Instance připojit aplikace | Dokumentace Microsoftu
description: Tento článek popisuje, jak vaši aplikaci do Azure SQL Database Managed Instance připojit.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: c9d656908d265aeb6143e857b0ea4f635203bdd9
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258724"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Připojit vaši aplikaci ke spravované instanci Azure SQL Database

Už dnes máte více možností při rozhodování o tom, jak a kde hostujete vaší aplikace. 
 
Můžete se rozhodnout pro hostování aplikace v cloudu pomocí služby Azure App Service nebo některé z možností Azure pro virtuální síť (VNet) integrovaná jako je Azure App Service Environment, virtuální počítač, Virtual Machine Scale Sets. Může také využít hybridní cloudový přístup a ponechat přitom vašich aplikací v místním prostředí. 
 
Jakékoli volby provedené, můžete ho připojíte k Managed Instance (preview).  

![Vysoká dostupnost](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Připojení aplikace uvnitř stejné virtuální síti 

Tento scénář je nejjednodušší. Virtuální počítače v rámci virtuální sítě můžete připojit přímo k sobě navzájem i v případě, že jsou v různých podsítích. To znamená, že všechno, co potřebujete k připojení aplikace v prostředí Azure aplikace nebo virtuálního počítače je odpovídajícím způsobem nastavit připojovací řetězec.  
 
V případě, že nelze navázat připojení, zkontrolujte, pokud máte skupinu zabezpečení sítě, nastavte v podsíti aplikace. V takovém případě budete muset otevřít odchozí připojení na portu 1433 SQL také 11000 12000 rozsah portů pro přesměrování. 

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

## <a name="connect-an-azure-app-service-hosted-application"></a>Připojení aplikace hostované služby Azure App Service 

Spravovaná Instance je přístupný pouze prostřednictvím privátních IP adres tak, aby bylo možné získat přístup pomocí služby Azure App Service je nutné nejprve navázat připojení mezi aplikací a virtuální síť Managed Instance. Zobrazit [integrujte svou aplikaci s Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).  
 
Řešení potíží, najdete v části [řešení potíží s virtuálními sítěmi a aplikacemi](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Pokud nejde navázat připojení, zkuste [synchronizace konfigurace sítě](sql-database-managed-instance-sync-network-configuration.md). 
 
Zvláštním případem připojení služby Azure App Service k Managed Instance je při vám integraci že služby Azure App Service k síti s partnerskými uzly do spravované Instance virtuální sítě. Tento případ vyžaduje následující konfigurace až: 

- Virtuální síť spravované Instance není musí mít bránu  
- Spravované Instance virtuální síť musí mít nastavenou možnost použití vzdálené brány 
- Partnerské virtuální síti musí mít možnost přenosu brány povolit nastavení 
 
Tento scénář je znázorněn v následujícím diagramu:

![vytvoření partnerského vztahu integrované aplikace](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>Připojení aplikace ve službě box vývojáře 

Spravovaná Instance je přístupný pouze prostřednictvím privátních IP adres tak aby bylo možné přistupovat z vašeho seznamu pro vývojáře, musíte nejprve navázat připojení mezi vaší pole pro vývojáře a ve virtuální síti spravované Instance.  
 
Konfigurace připojení typu Point-to-Site k virtuální síti pomocí nativního certifikátu Azure ověřování článků ([webu Azure portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [rozhraní příkazového řádku Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) podrobně zobrazuje jak To se dělá.  

## <a name="next-steps"></a>Další postup

- Informace o Managed Instance najdete v tématu [co je Managed Instance](sql-database-managed-instance.md).
- Kurz ukazuje, jak vytvořit nový Managed Instance, najdete v tématu [vytvoříte Managed Instance](sql-database-managed-instance-create-tutorial-portal.md).
