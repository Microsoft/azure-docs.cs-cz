---
title: Postup zotavení po havárii
description: Přečtěte si Rady a osvědčené postupy pro použití Azure SQL Database k provádění postupů zotavení po havárii.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: f53a08a12c5afda8dbc3f25d9102f52b870ceea4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91321658"
---
# <a name="performing-disaster-recovery-drills"></a>Probíhají podrobné analýzy zotavení po havárii.
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Doporučuje se pravidelně provádět ověřování připravenosti aplikací pro pracovní postup obnovení. Ověření chování aplikace a důsledky ztráty dat a/nebo přerušení, které zahrnuje převzetí služeb při selhání, je dobrým technickým postupem. V rámci certifikace provozní kontinuity je to také požadavky na většinu průmyslových standardů.

Postup zotavení po havárii se skládá z těchto kroků:

* Simulace výpadku datové vrstvy
* Obnovení
* Ověřit platnost po obnovení integrity aplikace

V závislosti na tom, jak jste [aplikaci navrhli pro zajištění kontinuity podnikových prostředí](business-continuity-high-availability-disaster-recover-hadr-overview.md), se může pracovní postup, který se má spustit, lišit. Tento článek popisuje osvědčené postupy pro provedení postupu zotavení po havárii v souvislosti s Azure SQL Database.

## <a name="geo-restore"></a>Geografické obnovení

Chcete-li zabránit potenciálním ztrátám dat při provádění postupu zotavení po havárii, proveďte přechod pomocí testovacího prostředí tím, že vytvoříte kopii produkčního prostředí a použijete ji k ověření pracovního postupu převzetí služeb při selhání aplikace.

### <a name="outage-simulation"></a>Simulace výpadku

Chcete-li simulovat výpadek, můžete zdrojovou databázi přejmenovat. Tato změna názvu způsobí selhání připojení aplikace.

### <a name="recovery"></a>Obnovovací

* Proveďte geografickou obnovu databáze na jiný server, jak je popsáno [zde](disaster-recovery-guidance.md).
* Změňte konfiguraci aplikace tak, aby se připojila k obnovené databázi, a po dokončení obnovení použijte průvodce [konfigurací databáze po obnovení](disaster-recovery-guidance.md) .

### <a name="validation"></a>Ověřování

Dokončete přechod kontrolou integrity aplikace po obnovení (včetně připojovacích řetězců, přihlášení, základního testování funkčnosti nebo jiných ověření části standardních signoffs postupů aplikace).

## <a name="failover-groups"></a>Skupiny převzetí služeb při selhání

Pro databázi, která je chráněná pomocí skupin převzetí služeb při selhání, zahrnuje postupné cvičení plánovaného převzetí služeb při selhání na sekundární server. Plánované převzetí služeb při selhání zajistí, že primární a sekundární databáze ve skupině převzetí služeb při selhání zůstanou při přepínání rolí v synchronizaci. Na rozdíl od neplánovaného převzetí služeb při selhání Tato operace nevede ke ztrátě dat, takže je možné postupovat v produkčním prostředí.

### <a name="outage-simulation"></a>Simulace výpadku

Pro simulaci výpadku můžete zakázat webovou aplikaci nebo virtuální počítač, který je připojený k databázi. Tato simulace výpadků vede k selhání připojení webových klientů.

### <a name="recovery"></a>Obnovovací

* Zajistěte, aby konfigurace aplikace v oblasti DR odkazovala na bývalé sekundární, což se stalo plně přístupným novým primárním objektem.
* Spusťte [plánované převzetí služeb](scripts/setup-geodr-and-failover-database-powershell.md) při selhání ve skupině převzetí služeb při selhání ze sekundárního serveru.
* Dokončete obnovení podle průvodce [konfigurací databáze po obnovení](disaster-recovery-guidance.md) .

### <a name="validation"></a>Ověřování

Dokončete přechod kontrolou integrity aplikace po obnovení (včetně možnosti připojení, základního testování funkčnosti nebo dalších ověření potřebných pro signoffsí podrobností).

## <a name="next-steps"></a>Další kroky

* Další informace o scénářích kontinuity podnikových postupů najdete v tématu [scénáře kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Další informace o Azure SQL Database automatizovaných zálohách najdete v tématu [SQL Database automatizované zálohy](automated-backups-overview.md) .
* Další informace o použití automatizovaných záloh pro obnovení najdete v tématu [obnovení databáze ze zálohy iniciované službou](recovery-using-backups.md).
* Další informace o možnostech rychlejšího obnovení najdete v tématu [Aktivní geografická replikace](active-geo-replication-overview.md) a [skupiny automatického převzetí služeb při selhání](auto-failover-group-overview.md).
