---
title: Azure SQL Database Managed Instance vlastní DNS | Dokumentace Microsoftu
description: Toto téma popisuje možnosti konfigurace pro vlastní DNS pomocí Azure SQL Database Managed Instance.
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
ms.date: 09/23/2018
ms.openlocfilehash: 7ad3b81b792b37d2c3667dd554d41319a5727045
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336405"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurace vlastního DNS pro službu Azure SQL Database Managed Instance

Azure SQL Database Managed Instance musí být nasazen v rámci Azure [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md). Existuje několik scénářů (například db e-mailu, propojené servery na jiné instance SQL ve vašem prostředí cloudu nebo hybridní), které vyžadují privátní hostitele vyřešen z Managed Instance. V takovém případě potřebujete nakonfigurovat vlastní DNS v Azure. Managed Instance používá stejnou službu DNS pro jeho vnitřní fungování, konfiguraci serveru DNS virtuální sítě musí být kompatibilní s Managed Instance. 

   > [!IMPORTANT]
   > Vždy používejte plně kvalifikované názvy domén (FQDN) pro poštovní servery, servery SQL a dalším službám, i v případě, že jsou v rámci vaší privátní zóny DNS. Například použijte `smtp.contoso.com` pro poštovní server protože jednoduché `smtp` nebudou správně přeložit.

Chcete-li vlastní konfigurací DNS je kompatibilní s Managed Instance, budete muset: 
- Konfigurace vlastního serveru DNS tak, aby byl schopen přeložit názvy veřejné domény. 
- Vložit Azure rekurzivní Překladač DNS IP adresy 168.63.129.16 na konec seznamu DNS virtuální sítě 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>Nastavení vlastní konfigurace serverů DNS

1. Na webu Azure Portal vyhledejte vlastní možnost DNS pro vaši virtuální síť.

   ![možnost vlastní dns](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Přepnout na vlastní a zadat vlastní IP adresu serveru DNS, stejně jako Azure rekurzivní překladače IP adresy 168.63.129.16. 

   ![možnost vlastní dns](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > V seznamu DNS nastavit rekurzivní překladače Azure může způsobit Managed Instance zadání chybného stavu, pokud z nějakého důvodu nejsou k dispozici vlastní servery DNS. Obnovení ze stavu může vyžadovat k vytvoření nové instance ve virtuální síti s předpisy sítě, vytvořte dat na úrovni instance a obnovit své databáze. Rekurzivní překladače Azure pro nastavení, protože zajišťuje, že poslední položka v seznamu DNS, i v případě, že všechny vlastní servery DNS selže, veřejné názvy stále lze přeložit.

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance](sql-database-managed-instance.md)
- Kurz ukazuje, jak vytvořit nový Managed Instance, najdete v tématu [vytvoření Managed Instance](sql-database-managed-instance-get-started.md).
- Informace o konfiguraci virtuální sítě pro Managed Instance najdete v tématu [konfigurace virtuální sítě pro Managed instance](sql-database-managed-instance-connectivity-architecture.md)
