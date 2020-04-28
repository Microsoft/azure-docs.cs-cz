---
title: Přechody k zotavení po havárii
description: Přečtěte si Rady a osvědčené postupy pro použití Azure SQL Database k provádění postupů zotavení po havárii.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 3ca00a03976ae38b7956616b8287220a7bc5998c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "73825858"
---
# <a name="performing-disaster-recovery-drill"></a>Přechod na další postup zotavení po havárii

Doporučuje se pravidelně provádět ověřování připravenosti aplikací pro pracovní postup obnovení. Ověření chování aplikace a důsledky ztráty dat a/nebo přerušení, které zahrnuje převzetí služeb při selhání, je dobrým technickým postupem. V rámci certifikace provozní kontinuity je to také požadavky na většinu průmyslových standardů.

Postup zotavení po havárii se skládá z těchto kroků:

* Simulace výpadku datové vrstvy
* Obnovení
* Ověřit platnost po obnovení integrity aplikace

V závislosti na tom, jak jste [aplikaci navrhli pro zajištění kontinuity podnikových prostředí](sql-database-business-continuity.md), se může pracovní postup, který se má spustit, lišit. Tento článek popisuje osvědčené postupy pro provedení postupu zotavení po havárii v souvislosti s Azure SQL Database.

## <a name="geo-restore"></a>Geografické obnovení

Chcete-li zabránit potenciálním ztrátám dat při provádění postupu zotavení po havárii, proveďte přechod pomocí testovacího prostředí tím, že vytvoříte kopii produkčního prostředí a použijete ji k ověření pracovního postupu převzetí služeb při selhání aplikace.

### <a name="outage-simulation"></a>Simulace výpadku

Chcete-li simulovat výpadek, můžete zdrojovou databázi přejmenovat. Tato změna názvu způsobí selhání připojení aplikace.

### <a name="recovery"></a>Obnovení

* Proveďte geografickou obnovu databáze na jiný server, jak je popsáno [zde](sql-database-disaster-recovery.md).
* Změňte konfiguraci aplikace tak, aby se připojila k obnovené databázi, a po dokončení obnovení použijte průvodce [konfigurací databáze po obnovení](sql-database-disaster-recovery.md) .

### <a name="validation"></a>Ověřování

Dokončete přechod kontrolou integrity aplikace po obnovení (včetně připojovacích řetězců, přihlášení, základního testování funkčnosti nebo jiných ověření části standardních signoffs postupů aplikace).

## <a name="failover-groups"></a>Skupiny převzetí služeb při selhání

Pro databázi, která je chráněná pomocí skupin převzetí služeb při selhání, zahrnuje postupné cvičení plánovaného převzetí služeb při selhání na sekundární server. Plánované převzetí služeb při selhání zajistí, že primární a sekundární databáze ve skupině převzetí služeb při selhání zůstanou při přepínání rolí v synchronizaci. Na rozdíl od neplánovaného převzetí služeb při selhání Tato operace nevede ke ztrátě dat, takže je možné postupovat v produkčním prostředí.

### <a name="outage-simulation"></a>Simulace výpadku

Pro simulaci výpadku můžete zakázat webovou aplikaci nebo virtuální počítač, který je připojený k databázi. Tato simulace výpadků vede k selhání připojení webových klientů.

### <a name="recovery"></a>Obnovení

* Zajistěte, aby konfigurace aplikace v oblasti DR odkazovala na bývalé sekundární, což se stalo plně přístupným novým primárním objektem.
* Spusťte [plánované převzetí služeb](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) při selhání ve skupině převzetí služeb při selhání ze sekundárního serveru.
* Dokončete obnovení podle průvodce [konfigurací databáze po obnovení](sql-database-disaster-recovery.md) .

### <a name="validation"></a>Ověřování

Dokončete přechod kontrolou integrity aplikace po obnovení (včetně možnosti připojení, základního testování funkčnosti nebo dalších ověření potřebných pro signoffsí podrobností).

## <a name="next-steps"></a>Další kroky

* Další informace o scénářích kontinuity podnikových postupů najdete v tématu [scénáře kontinuity](sql-database-business-continuity.md).
* Další informace o Azure SQL Database automatizovaných zálohách najdete v tématu [SQL Database automatizované zálohy](sql-database-automated-backups.md) .
* Další informace o použití automatizovaných záloh pro obnovení najdete v tématu [obnovení databáze ze zálohy iniciované službou](sql-database-recovery-using-backups.md).
* Další informace o možnostech rychlejšího obnovení najdete v tématu [Aktivní geografická replikace](sql-database-active-geo-replication.md) a [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md).
