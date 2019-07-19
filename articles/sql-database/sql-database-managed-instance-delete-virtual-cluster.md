---
title: Odstranění podsítě po odstranění spravované instance Azure SQL Database | Microsoft Docs
description: Naučte se odstranit virtuální síť Azure po odstranění Azure SQL Database spravované instance.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: ead7ea91e172f608c5364e4d5164d2a71dbf2f5f
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297625"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Odstranění podsítě po odstranění spravované instance Azure SQL Database

Tento článek poskytuje pokyny k ručnímu odstranění podsítě po odstranění poslední Azure SQL Database spravované instance, která je v ní umístěná.

SQL Database používá [virtuální cluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) k zahrnutí odstraněné spravované instance. Virtuální cluster trvá 12 hodin od odstranění instance a umožní vám rychle vytvořit spravované instance ve stejné podsíti. Za udržování prázdného virtuálního clusteru se neúčtují žádné poplatky. Během této doby není možné odstranit podsíť přidruženou k virtuálnímu clusteru.

Pokud nechcete čekat 12 hodin a chcete hned odstranit virtuální cluster a jeho podsíť, můžete to udělat ručně. Odstraňte virtuální cluster ručně pomocí Azure Portal nebo rozhraní API pro virtuální clustery.

> [!NOTE]
> Virtuální cluster by neměl obsahovat žádné spravované instance, aby bylo odstranění úspěšné.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Odstranit virtuální cluster z Azure Portal

Pokud chcete virtuální cluster odstranit pomocí Azure Portal, vyhledejte prostředky virtuálního clusteru.

![Snímek obrazovky Azure Portal se zvýrazněným polem hledání](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Až vyhledáte virtuální cluster, který chcete odstranit, vyberte tento prostředek a vyberte **Odstranit**. Budete vyzváni k potvrzení odstranění virtuálního clusteru.

![Snímek obrazovky řídicího panelu virtuálních clusterů Azure Portal s zvýrazněnou možností odstranit](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

V oblasti oznámení Azure Portal se zobrazí potvrzení, že se virtuální cluster odstranil. Úspěšné odstranění virtuálního clusteru okamžitě uvolní podsíť pro opakované použití.

> [!TIP]
> Pokud se ve virtuálním clusteru nezobrazí žádné spravované instance a virtuální cluster nemůžete odstranit, ujistěte se, že neprobíhá nasazení probíhající instance. To zahrnuje zahájená a zrušená nasazení, která stále probíhá. Kontrola nasazení skupiny prostředků, na kterou se instance nasadila, bude označovat, že nasazení probíhá. V takovém případě můžete očekávat, že nasazení bude dokončeno, odstranit spravovanou instanci a pak virtuální cluster.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Odstranění virtuálního clusteru pomocí rozhraní API

Pokud chcete virtuální cluster odstranit přes rozhraní API, použijte parametry identifikátoru URI zadané v [metodě odstranit virtuální clustery](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je spravovaná instance?](sql-database-managed-instance.md).
- Přečtěte si o [architektuře připojení ve spravované instanci](sql-database-managed-instance-connectivity-architecture.md).
- Naučte se, jak [Upravit existující virtuální síť pro spravovanou instanci](sql-database-managed-instance-configure-vnet-subnet.md).
- Kurz, ve kterém se dozvíte, jak vytvořit virtuální síť, vytvořit spravovanou instanci a obnovit databázi ze zálohy databáze, najdete v tématu [Vytvoření spravované instance Azure SQL Database](sql-database-managed-instance-get-started.md).
- Problémy se službou DNS najdete v tématu [Konfigurace vlastního serveru DNS](sql-database-managed-instance-custom-dns.md).
