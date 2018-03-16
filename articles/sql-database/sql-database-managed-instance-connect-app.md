---
title: "Azure SQL Database spravované Instance připojit aplikace | Microsoft Docs"
description: "Tento článek popisuje postup připojení aplikace k provádění příkazu spravované Instance databáze SQL Azure."
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: f02311026e3f28d4cf41dfe9b155f928885ae938
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Připojení aplikace k provádění příkazu spravované Instance databáze SQL Azure

Dnes mají více možností při rozhodování o tom, jak a kde hostování vaší aplikace. 
 
Můžete rozhodnout, který bude hostovat aplikaci v cloudu, buď pomocí služby Azure App Service nebo jen některé z Azure a virtuální síť (VNet) integrované možnosti jako sadu škálování virtuálního počítače v Azure App Service Environment, virtuální počítač. Také můžete postup hybridní cloud a zachovat aplikace místní. 
 
Ať volbou, která jste udělali, můžete se připojit k instanci spravované (preview).  

![Vysoká dostupnost](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Připojení aplikace uvnitř stejnou virtuální síť 

Tento scénář je nejjednodušší. Virtuální počítače uvnitř sítě VNet můžete připojit přímo k sobě navzájem i v případě, že jsou v různých podsítích. To znamená, všechny potřebné k připojení aplikace v prostředí aplikace Azure nebo virtuálního počítače je správně nastavit připojovací řetězec.  
 
V případě, že nelze navázat připojení, zkontrolujte, pokud máte skupinu zabezpečení sítě, nastavte v podsíti aplikace. V takovém případě budete muset otevřít odchozí připojení na portu SQL 1433, jakož i 11000 12000 rozsah portů pro přesměrování. 

## <a name="connect-an-application-inside-a-different-vnet"></a>Připojení aplikace uvnitř jiné virtuální sítě 

Tento scénář je trochu složitější, protože spravované Instance má privátní IP adresu ve své vlastní virtuální síti. Pro připojení, aplikace potřebuje přístup k virtuální síti, kde je nasazená spravované Instance. Ano je třeba nejprve připojení mezi aplikací a virtuální síť spravované Instance. Virtuální sítě nemusí být ve stejném předplatném v pořadí pro tento scénář fungovat. 
 
Existují dvě možnosti připojení virtuální sítě: 
- [Partnerský vztah Azure virtuální sítě](../virtual-network/virtual-network-peering-overview.md) 
- Brána sítě VPN VNet-to-VNet ([portál Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [prostředí PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [rozhraní příkazového řádku Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
Možnost partnerského vztahu je vhodnější než ten, protože partnerský vztah používá tedy páteřní síti Microsoft z hlediska připojení, neexistuje žádné významné rozdíly v latenci mezi virtuálními počítači ve virtuální síti peered a ve stejné virtuální síti. VNet peering je omezený na sítí ve stejné oblasti, i když mezi oblastmi partnerského vztahu je povolené v některé oblasti v náhledu.  
 
> [!IMPORTANT]
> Virtuální síť partnerských vztahů mezi oblastmi vytvořili nemusí mít stejnou úroveň dostupnost a spolehlivost jako partnerské vztahy v obecné dostupnosti verzi. Virtuální síť partnerských vztahů může mít omezené možnosti a nemusí být k dispozici ve všech oblastech Azure. Nejaktuálnější upozornění na stav této funkce a dostupnost, zkontrolujte [Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network) aktualizací stránky. 

## <a name="connect-an-on-premises-application"></a>Připojení místní aplikace 

Spravované Instance je přístupné pouze prostřednictvím privátní IP adresy. Chcete-li získat přístup pomocí místní, budete muset vytvořit připojení Site-to-Site mezi aplikací a virtuální síť spravované Instance. 
 
Jak připojit místní virtuální síť Azure existují dvě možnosti: 
- Připojení VPN typu Site-to-Site ([portál Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [prostředí PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [rozhraní příkazového řádku Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [ExpressRoute](../expressroute/expressroute-introduction.md) připojení  
 
Pokud jste vytvořili místním nasazením a Azure připojení úspěšně a nemůže navázat připojení k spravované Instance, zkontrolujte, jestli brána firewall má otevřené odchozí připojení na portu SQL 1433, jakož i 11000 12000 rozsah portů pro přesměrování. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Připojení aplikace hostované službě Azure App Service 

Spravované Instance jsou přístupné pouze prostřednictvím privátní IP adresy, aby bylo možné získat přístup pomocí služby Azure App Service je nutné nejprve navázat připojení mezi aplikací a virtuální síť spravované Instance. V tématu [integraci aplikace s virtuální sítě Azure](../app-service/web-sites-integrate-with-vnet.md).  
 
Řešení potíží, najdete v části [řešení potíží s virtuálními sítěmi a aplikace](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Pokud nelze navázat připojení, zkuste [synchronizace konfigurace sítí](sql-database-managed-instance-sync-network-configuration.md). 
 
Zvláštním případem propojíte spravované Instance služby Azure App Service je když je integrované že peered Azure App Service k síti do spravovaných Instance virtuální sítě. Tento případ vyžaduje nastavit následující konfiguraci: 

- Spravované Instance virtuální síť musí mít není brány  
- Spravované Instance virtuální síť musí mít sadu možností použití vzdáleného brány 
- Peered virtuální síť musí mít možnost přenosu brány povolit nastavení 
 
Tento scénář je znázorněn v následujícím diagramu:

![partnerský vztah integrované aplikace](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>Připojení se v poli vývojáři aplikace 

Spravované Instance se dá dostat pouze privátní IP adresy, aby měli přístup z vaší vývojáře pole, je nutné nejprve zkontrolujte připojení mezi vaší vývojáře pole a virtuální síť spravované Instance.  
 
Konfigurace připojení typu Point-to-Site k virtuální síti pomocí nativní Azure certifikát ověřování články ([portál Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [prostředí PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [rozhraní příkazového řádku Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) podrobně zobrazuje jak může být provedeno.  

## <a name="next-steps"></a>Další postup

- Informace o instanci spravované najdete v tématu [co je Instance spravované](sql-database-managed-instance.md).
- Podívejte se kurz [vytvořit instanci spravované](sql-database-managed-instance-tutorial-portal.md).
