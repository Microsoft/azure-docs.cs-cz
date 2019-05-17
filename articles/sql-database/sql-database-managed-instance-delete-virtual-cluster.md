---
title: Odstranit virtuální síť po odstranění Azure SQL Database managed instance | Dokumentace Microsoftu
description: Zjistěte, jak odstranit virtuální síť po odstranění Azure SQL Database managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 61f6c25031c4906e65c2f75a7679600741e8311a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791376"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>Odstranit podsíť po odstranění Azure SQL Database managed instance

Tento článek obsahuje pokyny o tom, jak ručně odstranit podsíť po odstranění posledního Azure SQL Database managed instance, které se nacházejí v ní.

[Virtuální cluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) , která je obsažená na odstraněný spravovanou instanci se uchovají po dobu 12 hodin před odstraněním instance. Virtuální cluster je zachováno záměrné umožňuje rychlejší vytvoření spravované instance ve stejné podsíti. Udržování prázdný virtuální cluster je zdarma. Během tohoto období se nedá odstranit podsíť přidružená virtuální cluster.

Okamžité uvolnění používán clusterem prázdný virtuální podsítě je možné provádět prostřednictvím ručního odstranění virtuálního clusteru. Odstranění virtuální cluster lze provádět pomocí webu Azure portal nebo virtuálních clusterů rozhraní API.

> [!NOTE]
> Virtuální cluster by měl obsahovat žádné spravované instance pro odstranění k dosažení úspěchu.

## <a name="delete-virtual-cluster-from-azure-portal"></a>Odstranění virtuálního clusteru z webu Azure portal

Odstranění virtuálního clusteru pomocí webu Azure portal, vyhledejte prostředky virtuálních clusterů pomocí předdefinovaných vyhledávacích.

![Vyhledejte virtuální cluster.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Po nalezení virtuální cluster, který chcete odstranit, vyberte tento prostředek a vyberte možnost odstranit. Zobrazí se výzva k potvrzení odstranění virtuálního clusteru.

![Virtuální cluster odstraňte.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Potvrzení, že byl odstraněn virtuální cluster najdete v oznámení portálu Azure portal. Úspěšné odstranění virtuální cluster okamžitě uvolní podsítě pro další použití.

## <a name="delete-virtual-cluster-using-api"></a>Odstranění virtuálního clusteru pomocí rozhraní API

Chcete-li odstranit virtuálního clusteru pomocí rozhraní API parametry identifikátoru URI zadaný v [virtuálních clusterů delete – metoda](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance?](sql-database-managed-instance.md).
- Další informace o [architektura připojení ve spravované instanci](sql-database-managed-instance-connectivity-architecture.md).
- Zjistěte, jak [upravit existující virtuální sítě pro Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md).
- Kurz ukazuje, jak vytvořit virtuální síť, vytvoříte Managed Instance a obnovit databázi ze zálohy databáze, najdete v tématu [vytvořit Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Problémy s DNS, najdete v části [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md).
