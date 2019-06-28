---
title: Odstranění podsítě po odstranění služby Azure SQL Database managed instance | Dokumentace Microsoftu
description: Zjistěte, jak odstranit virtuální síť Azure po odstranění služby Azure SQL Database managed instance.
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
ms.openlocfilehash: ec5d99e160e739f59e2bf2ea369fe83e9900a1f1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295302"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Odstranění podsítě po odstranění služby Azure SQL Database managed instance

Tento článek obsahuje pokyny o tom, jak ručně po odstranění posledního Azure SQL Database managed instance, které se nacházejí v ní odstranit podsíť.

SQL Database používá [virtuální cluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) tak, aby obsahovala odstraněné spravované instance. Virtuální cluster se uchovávají po dobu 12 hodin po odstranění instance vám umožní rychle vytvářet spravované instance ve stejné podsíti. Neplatí žádné poplatky za pořízení prázdný virtuální cluster. Během tohoto období se nedá odstranit podsíť přidružená virtuální cluster.

Pokud nechcete, aby počkejte po dobu 12 hodin, a upřednostnit okamžitě odstranit virtuálního clusteru a její podsítě, lze provést ručně. Virtuální cluster odstraňte ručně pomocí webu Azure portal nebo rozhraní API virtuálních clusterů.

> [!NOTE]
> Virtuální cluster by měl obsahovat žádné spravované instance pro odstranění k dosažení úspěchu.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Odstranit virtuální cluster na webu Azure Portal

Odstranění virtuálního clusteru pomocí webu Azure portal, vyhledejte prostředky virtuálních clusterů.

![Snímek obrazovky webu Azure portal, pomocí vyhledávacího pole zvýrazněnou](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Po nalezení virtuálních clusterů, které chcete odstranit, vyberte tento prostředek a vyberte **odstranit**. Budete vyzváni k potvrzení odstranění virtuálního clusteru.

![Snímek obrazovky webu Azure portal virtuálních clusterů řídicí panel, se zvýrazněnou možností odstranit](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Oblast oznámení portálu Azure portal zobrazí potvrzení, že byl odstraněn virtuální cluster. Úspěšné odstranění virtuální cluster okamžitě uvolní podsítě pro další použití.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Odstranění virtuálního clusteru pomocí rozhraní API

Pokud chcete odstranit virtuální cluster prostřednictvím rozhraní API, použijte parametry identifikátoru URI zadaný v [virtuálních clusterů delete – metoda](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance?](sql-database-managed-instance.md).
- Další informace o [architektura připojení ve spravované instanci](sql-database-managed-instance-connectivity-architecture.md).
- Zjistěte, jak [upravit existující virtuální sítě pro Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md).
- Kurz ukazuje, jak vytvořit virtuální síť, vytvoříte Managed Instance a obnovit databázi ze zálohy databáze, najdete v tématu [vytvořit Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Problémy s DNS, najdete v části [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md).
