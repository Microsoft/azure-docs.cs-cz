---
title: Databáze Azure SQL spravované Instance vlastní DNS | Microsoft Docs
description: Toto téma popisuje možnosti konfigurace pro vlastní DNS s spravované Instance databáze Azure SQL.
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 05a7b600ae8672447126b79cda10ca94c6d0fb48
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649325"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurace vlastní DNS pro Azure SQL Database spravované Instance

Azure SQL Database spravované Instance (preview) musí být nasazený v rámci Azure [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md). Je několik scénářů, odkazované servery na jiné instance systému SQL v cloudu nebo hybridní prostředí, které vyžadují názvů privátní hostitele z spravované Instance. V takovém případě musíte nakonfigurovat vlastní DNS v Azure. Vzhledem k tomu, že spravované Instance používá stejné DNS pro jeho vnitřního chodu, musí být kompatibilní s spravované Instance konfiguraci serveru DNS virtuální sítě. 

Chcete-li vlastní konfiguraci DNS kompatibilní s spravované Instance, je třeba provést následující kroky: 
- Konfigurace vlastní DNS pro předávání požadavků do Azure DNS 
- Nastavit vlastní DNS jako primární a Azure DNS jako sekundární sítě vnet 
- Zaregistrujte se jako sekundární vlastní DNS jako primární a Azure DNS

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Konfigurace vlastní DNS pro předávání požadavků do Azure DNS 

Ke konfiguraci předávání DNS v systému Windows Server 2016, použijte tyto kroky: 

1. V **správce serveru**, klikněte na tlačítko **nástroje**a potom klikněte na **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Klikněte dvakrát na **předávání**.

   ![Servery pro předávání](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Klikněte na **Upravit**. 

   ![Seznamu serverů pro předávání](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Zadejte Azure rekurzivní překladače IP adresu, jako je například 168.63.129.16.

   ![Rekurzivní překladače ip adresu](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Nastavit vlastní DNS jako primární a Azure DNS jako sekundární 
 
Konfigurace DNS na Azure VNet vyžaduje zadejte IP adresy, takže konfigurace virtuálního počítače Azure, který je hostitelem serveru DNS se statickou IP adresu pomocí následující další kroky: 

1. Na portálu Azure otevřete vlastní síťové rozhraní virtuálního počítače DNS.

   ![síťové rozhraní](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. V části Konfigurace protokolu IP. Vyberte konfiguraci protokolu IP 

   ![Konfigurace protokolu IP](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Nastavit jako statickou privátní IP adresu. Zapište si IP adresu (10.0.1.5 na tomto snímku obrazovky) 

   ![statická](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Zaregistrujte se jako sekundární vlastní DNS jako primární a Azure DNS 

1. Na portálu Azure najdete vlastní možnost DNS pro vaši virtuální síť.

   ![možnost vlastní dns](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Přepněte do vlastní a zadejte vlastní IP adresu serveru DNS, jakož i Azure rekurzivní překladače IP adresu, jako je například 168.63.129.16. 

   ![možnost vlastní dns](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Není nastavení Azure rekurzivní Překladač DNS seznamu způsobí, že spravované Instance k zadání vadný stav. Obnovení ze stavu může vyžadovat vám umožní vytvořit novou instanci ve virtuální síti s předpisy sítě, vytvořte dat na úrovni instance a obnovit své databáze. V tématu [konfiguraci virtuální sítě](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je spravované Instance](sql-database-managed-instance.md)
- Kurz ukazuje, jak vytvořit novou instanci spravované, najdete v části [vytvoření Instance spravované](sql-database-managed-instance-create-tutorial-portal.md).
- Informace o konfiguraci virtuální sítě pro instanci spravované najdete v tématu [konfiguraci virtuální sítě pro spravované instance](sql-database-managed-instance-vnet-configuration.md)
