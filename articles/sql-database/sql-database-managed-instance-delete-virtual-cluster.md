---
title: Odstranění podsítě po odstranění spravované instance
description: Zjistěte, jak odstranit virtuální síť Azure po odstranění spravované instance Azure SQL Database.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820468"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Odstranění podsítě po odstranění spravované instance Azure SQL Database

Tento článek obsahuje pokyny, jak ručně odstranit podsíť po odstranění poslední instance spravované azure SQL Database, která se v ní nachází.

Spravované instance se nasazují do [virtuálních clusterů](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture). Každý virtuální cluster je přidružen k podsíti. Virtuální cluster potrvá podle návrhu po dobu 12 hodin po odstranění poslední instance, abyste mohli rychleji vytvářet spravované instance ve stejné podsíti. Za udržování prázdného virtuálního clusteru se neplatí žádný poplatek. Během této doby není možné odstranit podsíť přidruženou k virtuálnímu clusteru.

Pokud nechcete čekat 12 hodin a raději odstranit virtuální cluster a jeho podsíť dříve, můžete tak učinit ručně. Virtuální cluster odstraňte ručně pomocí portálu Azure nebo rozhraní API virtuálních clusterů.

> [!IMPORTANT]
> - Virtuální cluster by neměl obsahovat žádné spravované instance, aby bylo odstranění úspěšné. 
> - Odstranění virtuálního clusteru je dlouhotrvající operace trvající přibližně 1,5 hodiny (viz [Operace správy spravované instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) pro aktuální dobu odstranění virtuálního clusteru), během které bude virtuální cluster stále viditelný na portálu, dokud nebude tento proces dokončen.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Odstranění virtuálního clusteru z webu Azure Portal

Pokud chcete virtuální cluster odstranit pomocí portálu Azure, vyhledejte prostředky virtuálního clusteru.

![Snímek obrazovky s portálem Azure se zvýrazněným vyhledávacím polem](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Po vyhledání virtuálního clusteru, který chcete odstranit, vyberte tento prostředek a vyberte **odstranit**. Zobrazí se výzva k potvrzení odstranění virtuálního clusteru.

![Snímek obrazovky s řídicím panelem Virtuálníclustery portálu Azure se zvýrazněnou možností Odstranit](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Oznámení na webu Azure vám zobrazí potvrzení, že žádost o odstranění virtuálního clusteru byla úspěšně odeslána. Samotná operace odstranění bude trvat přibližně 1,5 hodiny, během kterého virtuální cluster bude stále viditelné na portálu. Po dokončení procesu virtuální cluster již nebude viditelný a podsíť s ním spojená bude uvolněna k opětovnému použití.

> [!TIP]
> Pokud ve virtuálním clusteru nejsou zobrazeny žádné spravované instance a virtuální cluster nelze odstranit, ujistěte se, že neprobíhá průběžné nasazení instance. To zahrnuje spuštěná a zrušená nasazení, která stále probíhají. Důvodem je, že tyto operace budou i nadále používat virtuální cluster uzamykání z odstranění. Kontrola na kartě Nasazení skupiny prostředků, do které byla instance nasazena, bude znamenat všechna probíhající nasazení. V takovém případě počkejte na dokončení nasazení, odstraňte spravovanou instanci a potom virtuální cluster.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Odstranění virtuálního clusteru pomocí rozhraní API

Chcete-li odstranit virtuální cluster prostřednictvím rozhraní API, použijte parametry URI zadané ve [virtuálních clusterech delete .](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Co je spravovaná instance?](sql-database-managed-instance.md).
- Informace o [architektuře připojení ve spravované instanci](sql-database-managed-instance-connectivity-architecture.md).
- Přečtěte si, jak [upravit existující virtuální síť pro spravovanou instanci](sql-database-managed-instance-configure-vnet-subnet.md).
- Kurz, který ukazuje, jak vytvořit virtuální síť, vytvořit spravovanou instanci a obnovit databázi ze zálohy databáze, najdete [v tématu Vytvoření spravované instance Azure SQL Database .](sql-database-managed-instance-get-started.md)
- Problémy se službou DNS naleznete [v tématu Konfigurace vlastního dns](sql-database-managed-instance-custom-dns.md).
