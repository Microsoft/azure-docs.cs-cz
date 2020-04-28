---
title: Odstranění podsítě po odstranění spravované instance
description: Naučte se odstranit virtuální síť Azure po odstranění Azure SQL Database spravované instance.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "73820468"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Odstranění podsítě po odstranění spravované instance Azure SQL Database

Tento článek poskytuje pokyny k ručnímu odstranění podsítě po odstranění poslední Azure SQL Database spravované instance, která je v ní umístěná.

Spravované instance se nasazují do [virtuálních clusterů](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture). Každý virtuální cluster je přidružen k podsíti. Po uplynutí 12 hodin od posledního odstranění instance bude virtuální cluster dál používat k tomu, aby bylo možné rychleji vytvořit spravované instance ve stejné podsíti. Za udržování prázdného virtuálního clusteru se neúčtují žádné poplatky. Během této doby není možné odstranit podsíť přidruženou k virtuálnímu clusteru.

Pokud nechcete čekat 12 hodin a chcete před tím, než budete virtuální cluster a jeho podsíť odstranit dřív, můžete to udělat ručně. Odstraňte virtuální cluster ručně pomocí Azure Portal nebo rozhraní API pro virtuální clustery.

> [!IMPORTANT]
> - Virtuální cluster by neměl obsahovat žádné spravované instance, aby bylo odstranění úspěšné. 
> - Odstranění virtuálního clusteru je dlouhodobě běžící operace po dobu přibližně 1,5 hodin (viz [operace správy spravované instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) a aktuální čas odstranění virtuálního clusteru), během kterých se virtuální cluster na portálu stále zobrazuje, dokud nebude tento proces dokončen.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Odstranit virtuální cluster z Azure Portal

Pokud chcete virtuální cluster odstranit pomocí Azure Portal, vyhledejte prostředky virtuálního clusteru.

![Snímek obrazovky Azure Portal se zvýrazněným polem hledání](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Až vyhledáte virtuální cluster, který chcete odstranit, vyberte tento prostředek a vyberte **Odstranit**. Budete vyzváni k potvrzení odstranění virtuálního clusteru.

![Snímek obrazovky řídicího panelu virtuálních clusterů Azure Portal s zvýrazněnou možností odstranit](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Oznámení o Azure Portal vám ukáže potvrzení, že žádost o odstranění virtuálního clusteru se úspěšně odeslala. Operace odstranění bude trvat přibližně 1,5 hodin, během kterých bude virtuální cluster stále viditelný na portálu. Až se proces dokončí, virtuální cluster se už nebude zobrazovat a k opakovanému použití se uvolní přidružená podsíť.

> [!TIP]
> Pokud se ve virtuálním clusteru nezobrazí žádné spravované instance a virtuální cluster nemůžete odstranit, ujistěte se, že neprobíhá nasazení probíhající instance. To zahrnuje zahájená a zrušená nasazení, která stále probíhá. Důvodem je, že tyto operace budou stále používat virtuální cluster, protože ho zamkne před odstraněním. Kontrola nasazení skupiny prostředků, na kterou se instance nasadila, bude označovat, že nasazení probíhá. V takovém případě počkejte, než se nasazení dokončí, odstraňte spravovanou instanci a pak virtuální cluster.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Odstranění virtuálního clusteru pomocí rozhraní API

Pokud chcete virtuální cluster odstranit přes rozhraní API, použijte parametry identifikátoru URI zadané v [metodě odstranit virtuální clustery](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je spravovaná instance?](sql-database-managed-instance.md).
- Přečtěte si o [architektuře připojení ve spravované instanci](sql-database-managed-instance-connectivity-architecture.md).
- Naučte se, jak [Upravit existující virtuální síť pro spravovanou instanci](sql-database-managed-instance-configure-vnet-subnet.md).
- Kurz, ve kterém se dozvíte, jak vytvořit virtuální síť, vytvořit spravovanou instanci a obnovit databázi ze zálohy databáze, najdete v tématu [Vytvoření spravované instance Azure SQL Database](sql-database-managed-instance-get-started.md).
- Problémy se službou DNS najdete v tématu [Konfigurace vlastního serveru DNS](sql-database-managed-instance-custom-dns.md).
