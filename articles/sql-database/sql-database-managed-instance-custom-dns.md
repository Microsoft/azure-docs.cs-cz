---
title: Azure SQL Database Managed Instance vlastní DNS | Dokumentace Microsoftu
description: Toto téma popisuje možnosti konfigurace pro vlastní DNS pomocí Azure SQL Database Managed Instance.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: d5bb2f2f4b79c4b03e631fc844a712f76fc69109
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258163"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurace vlastního DNS pro službu Azure SQL Database Managed Instance

Azure SQL Database Managed Instance (preview) musí být nasazen v rámci Azure [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md). Existuje několik scénářů, propojené servery na jiné instance SQL ve vašem cloudu nebo hybridního prostředí, které vyžadují privátní hostitele vyřešen z Managed Instance. V takovém případě potřebujete nakonfigurovat vlastní DNS v Azure. Managed Instance používá stejnou službu DNS pro jeho vnitřní fungování, konfiguraci serveru DNS virtuální sítě musí být kompatibilní s Managed Instance. 

Chcete-li vlastní konfigurací DNS kompatibilní s Managed Instance, je třeba provést následující kroky: 
- Konfigurace vlastního DNS ke směrování žádostí na službu Azure DNS 
- Nastavení vlastního DNS jako primární a DNS Azure jako sekundární sítě vnet 
- Zaregistrujte se jako sekundární vlastního DNS jako primární a Azure DNS

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Konfigurace vlastního DNS ke směrování žádostí na službu Azure DNS 

Chcete-li nakonfigurujte předávání DNS ve Windows serveru 2016, použijte tyto kroky: 

1. V **správce serveru**, klikněte na tlačítko **nástroje**a potom klikněte na tlačítko **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Dvakrát klikněte na panel **předávání**.

   ![Servery pro předávání](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Klikněte na **Upravit**. 

   ![Seznam serverů pro předávání](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Zadejte Azure rekurzivní překladače IP adresu, jako jsou adresy 168.63.129.16.

   ![Ip adresu rekurzivního překladače](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Nastavení vlastního DNS jako primární a DNS Azure jako sekundární 
 
Konfigurace DNS na virtuální síť Azure vyžaduje, zadejte IP adresy, takže konfigurace virtuálního počítače Azure, který je hostitelem serveru DNS se statickou IP adresu pomocí následujících kroků Další: 

1. Na webu Azure Portal otevřete síťové rozhraní vlastní virtuální počítač DNS.

   ![síťové rozhraní](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. V části Konfigurace protokolu IP. Vyberte konfigurace IP adresy 

   ![Konfigurace protokolu IP](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Nastavit jako statickou privátní IP adresu. Zapište si IP adresu (adresu 10.0.1.5 na tomto snímku obrazovky) 

   ![statická](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Zaregistrujte se jako sekundární vlastního DNS jako primární a Azure DNS 

1. Na webu Azure Portal vyhledejte vlastní možnost DNS pro vaši virtuální síť.

   ![možnost vlastní dns](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Přepnout na vlastní a zadat vlastní IP adresu serveru DNS, stejně jako Azure rekurzivní překladače IP adresu, jako jsou adresy 168.63.129.16. 

   ![možnost vlastní dns](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Není nastavení rekurzivní překladače Azure ve službě DNS seznamu způsobí, že mi do chybného stavu. Obnovení ze stavu může vyžadovat k vytvoření nové instance ve virtuální síti s předpisy sítě, vytvořte dat na úrovni instance a obnovit své databáze. Zobrazit [konfigurace virtuální sítě](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance](sql-database-managed-instance.md)
- Kurz ukazuje, jak vytvořit nový Managed Instance, najdete v tématu [vytvoření Managed Instance](sql-database-managed-instance-create-tutorial-portal.md).
- Informace o konfiguraci virtuální sítě pro Managed Instance najdete v tématu [konfigurace virtuální sítě pro Managed instance](sql-database-managed-instance-vnet-configuration.md)
