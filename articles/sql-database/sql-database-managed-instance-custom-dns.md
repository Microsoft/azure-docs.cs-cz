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
ms.openlocfilehash: af9afcbf97df5f3d7fa82f6ea0163c714fa4f582
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051737"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurace vlastního DNS pro službu Azure SQL Database Managed Instance

Azure SQL Database Managed Instance (preview) musí být nasazen v rámci Azure [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md). Existuje několik scénářů (například propojené servery na jiné instance SQL ve vašem prostředí cloudu nebo hybridní), které vyžadují privátní hostitele vyřešen z Managed Instance. V takovém případě potřebujete nakonfigurovat vlastní DNS v Azure. Managed Instance používá stejnou službu DNS pro jeho vnitřní fungování, konfiguraci serveru DNS virtuální sítě musí být kompatibilní s Managed Instance. 

Chcete-li vlastní konfigurací DNS je kompatibilní s Managed Instance, budete muset: 
- Konfigurace vlastního serveru DNS tak, aby byl schopen přeložit názvy veřejné domény. 
- Vložit Azure rekurzivní Překladač DNS IP adresy 168.63.129.16 na konec seznamu DNS virtuální sítě 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>Nastavení vlastní konfigurace serverů DNS

1. Na webu Azure Portal vyhledejte vlastní možnost DNS pro vaši virtuální síť.

   ![možnost vlastní dns](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Přepnout na vlastní a zadat vlastní IP adresu serveru DNS, stejně jako Azure rekurzivní překladače IP adresy 168.63.129.16. 

   ![možnost vlastní dns](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Není nastavení rekurzivní překladače Azure ve službě DNS seznamu způsobí, že mi do chybného stavu. Obnovení ze stavu může vyžadovat k vytvoření nové instance ve virtuální síti s předpisy sítě, vytvořte dat na úrovni instance a obnovit své databáze. Zobrazit [konfigurace virtuální sítě](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance](sql-database-managed-instance.md)
- Kurz ukazuje, jak vytvořit nový Managed Instance, najdete v tématu [vytvoření Managed Instance](sql-database-managed-instance-create-tutorial-portal.md).
- Informace o konfiguraci virtuální sítě pro Managed Instance najdete v tématu [konfigurace virtuální sítě pro Managed instance](sql-database-managed-instance-vnet-configuration.md)
