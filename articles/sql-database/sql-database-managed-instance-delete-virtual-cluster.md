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
ms.date: 06/26/2019
ms.openlocfilehash: 4679ecda210fa78aad4315bc6602b67dd1795ce9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427977"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Odstranění podsítě po odstranění služby Azure SQL Database managed instance

Tento článek obsahuje pokyny o tom, jak ručně po odstranění posledního Azure SQL Database managed instance, které se nacházejí v ní odstranit podsíť.

SQL Database používá [virtuální cluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) tak, aby obsahovala odstraněné spravované instance. Virtuální cluster se uchovávají po dobu 12 hodin po odstranění instance vám umožní rychle vytvářet spravované instance ve stejné podsíti. Neplatí žádné poplatky za pořízení prázdný virtuální cluster. Během této doby není možné odstranit podsíť přidruženou k virtuálnímu clusteru.

Pokud nechcete, aby počkejte po dobu 12 hodin, a upřednostnit okamžitě odstranit virtuálního clusteru a její podsítě, lze provést ručně. Virtuální cluster odstraňte ručně pomocí webu Azure portal nebo rozhraní API virtuálních clusterů.

> [!NOTE]
> Virtuální cluster by měl obsahovat žádné spravované instance pro odstranění k dosažení úspěchu.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Odstranit virtuální cluster na webu Azure Portal

Odstranění virtuálního clusteru pomocí webu Azure portal, vyhledejte prostředky virtuálních clusterů.

![Snímek obrazovky webu Azure portal, pomocí vyhledávacího pole zvýrazněnou](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Po nalezení virtuálních clusterů, které chcete odstranit, vyberte tento prostředek a vyberte **odstranit**. Budete vyzváni k potvrzení odstranění virtuálního clusteru.

![Snímek obrazovky webu Azure portal virtuálních clusterů řídicí panel, se zvýrazněnou možností odstranit](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Oblast oznámení portálu Azure portal zobrazí potvrzení, že byl odstraněn virtuální cluster. Úspěšné odstranění virtuální cluster okamžitě uvolní podsítě pro další použití.

> [!TIP]
> Pokud neexistují žádné spravované instance uvedené v clusteru virtuální a nelze odstranit virtuální cluster, ujistěte se, že jste při nasazení probíhající instance v průběhu. To zahrnuje spuštěn a zrušené nasazení jsou stále probíhá. Kontrola nasazení karty, skupiny prostředků, instance byl nasazen na označí všechna nasazení v průběhu. V takovém případě await pro nasazení do dokončení, odstranit spravovanou instanci a potom virtuální cluster.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Odstranění virtuálního clusteru pomocí rozhraní API

Pokud chcete odstranit virtuální cluster prostřednictvím rozhraní API, použijte parametry identifikátoru URI zadaný v [virtuálních clusterů delete – metoda](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance?](sql-database-managed-instance.md).
- Další informace o [architektura připojení ve spravované instanci](sql-database-managed-instance-connectivity-architecture.md).
- Zjistěte, jak [upravit existující virtuální sítě pro Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md).
- Kurz ukazuje, jak vytvořit virtuální síť, vytvoříte Managed Instance a obnovit databázi ze zálohy databáze, najdete v tématu [vytvořit Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Problémy s DNS, najdete v části [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md).
