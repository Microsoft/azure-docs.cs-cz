---
title: Odstranění podsítě po odstranění spravované instance spravované instance SQL
description: Zjistěte, jak odstranit virtuální síť Azure po odstranění spravované instance spravované instance Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 0b8ceee1260e4209420778d7f327d771b858a899
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617719"
---
# <a name="delete-a-subnet-after-deleting-a-managed-instance-of-sql-managed-instance"></a>Odstranění podsítě po odstranění spravované instance spravované instance SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek poskytuje pokyny, jak ručně odstranit podsíť po odstranění poslední spravované instance spravované instance Azure SQL, která je v ní umístěná.

Spravované instance se nasazují do [virtuálních clusterů](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Každý virtuální cluster je přidružen k podsíti. Po uplynutí 12 hodin od posledního odstranění instance bude virtuální cluster dál používat k tomu, aby bylo možné rychleji vytvořit spravované instance ve stejné podsíti. Za udržování prázdného virtuálního clusteru se neúčtují žádné poplatky. Během této doby není možné odstranit podsíť přidruženou k virtuálnímu clusteru.

Pokud nechcete čekat 12 hodin a chcete před tím, než budete virtuální cluster a jeho podsíť odstranit dřív, můžete to udělat ručně. Odstraňte virtuální cluster ručně pomocí Azure Portal nebo rozhraní API pro virtuální clustery.

> [!IMPORTANT]
> - Virtuální cluster by neměl obsahovat žádné spravované instance, aby bylo odstranění úspěšné. 
> - Odstranění virtuálního clusteru je dlouhodobě běžící operace trvající přibližně 1,5 hodin (viz [operace správy spravované instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) pro aktuální čas odstranění virtuálního clusteru). Virtuální cluster bude na portálu stále viditelný, dokud tento proces nebude dokončen.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Odstranění virtuálního clusteru z Azure Portal

Pokud chcete virtuální cluster odstranit pomocí Azure Portal, vyhledejte prostředky virtuálního clusteru.

![Snímek obrazovky Azure Portal se zvýrazněným polem hledání](./media/virtual-cluster-delete/virtual-clusters-search.png)

Až vyhledáte virtuální cluster, který chcete odstranit, vyberte tento prostředek a vyberte **Odstranit**. Budete vyzváni k potvrzení odstranění virtuálního clusteru.

![Snímek obrazovky řídicího panelu virtuálních clusterů Azure Portal s zvýrazněnou možností odstranit](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Oznámení o Azure Portal vám ukáže potvrzení, že žádost o odstranění virtuálního clusteru se úspěšně odeslala. Tato operace odstranění bude trvat přibližně 1,5 hodin, během které bude virtuální cluster stále viditelný na portálu. Až se proces dokončí, virtuální cluster se už nebude zobrazovat a k opakovanému použití se uvolní přidružená podsíť.

> [!TIP]
> Pokud se ve virtuálním clusteru nezobrazí žádné spravované instance a virtuální cluster nemůžete odstranit, ujistěte se, že neprobíhá nasazení probíhající instance. To zahrnuje zahájená a zrušená nasazení, která stále probíhá. Důvodem je, že tyto operace budou virtuální cluster stále používat a zamkne se před odstraněním. Kontrola karty **nasazení** skupiny prostředků, na kterou se instance nasadila, bude označovat, že probíhají nasazení. V takovém případě počkejte, než se nasazení dokončí, odstraňte spravovanou instanci a pak odstraňte virtuální cluster.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Odstranění virtuálního clusteru pomocí rozhraní API

Pokud chcete virtuální cluster odstranit přes rozhraní API, použijte parametry identifikátoru URI zadané v [metodě odstranit virtuální clustery](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md).
- Seznamte [se s architekturou připojení ve spravované instanci SQL](connectivity-architecture-overview.md).
- Naučte se, jak [Upravit existující virtuální síť pro spravovanou instanci SQL](vnet-existing-add-subnet.md).
- Kurz, ve kterém se dozvíte, jak vytvořit virtuální síť, vytvořit spravovanou instanci a obnovit databázi ze zálohy databáze, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Problémy se službou DNS najdete v tématu [Konfigurace vlastního serveru DNS](custom-dns-configure.md).
