---
title: Cvičení zotavení po havárii
description: Naučte se pokyny a osvědčené postupy pro použití Azure SQL Database k provádění cvičení pro zotavení po havárii.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825858"
---
# <a name="performing-disaster-recovery-drill"></a>Provedení cvičení zotavení po havárii

Doporučuje se, aby se pravidelně provádělo ověřování připravenosti aplikace pro pracovní postup obnovení. Ověření chování aplikace a důsledky ztráty dat nebo narušení, které zahrnuje převzetí služeb při selhání, je osvědčeným technickým postupem. To je také požadavek většiny průmyslových standardů jako součást certifikace kontinuity provozu.

Provedení cvičení zotavení po havárii se skládá z:

* Simulace výpadku datové vrstvy
* Obnovení
* Ověřit integritu aplikace po obnovení

V závislosti na tom, jak jste [aplikaci navrhli pro kontinuitu provozu](sql-database-business-continuity.md), se pracovní postup pro spuštění přechodu na pracovní postup může lišit. Tento článek popisuje osvědčené postupy pro provádění procházení zotavení po havárii v kontextu Azure SQL Database.

## <a name="geo-restore"></a>Geografické obnovení

Chcete-li zabránit potenciální ztrátě dat při provádění cvičení pro zotavení po havárii, proveďte cvičení pomocí testovacího prostředí vytvořením kopie produkčního prostředí a jeho použitím k ověření pracovního postupu převzetí služeb při selhání aplikace.

### <a name="outage-simulation"></a>Simulace výpadku

Chcete-li simulovat výpadek, můžete přejmenovat zdrojovou databázi. Tato změna názvu způsobí selhání připojení aplikace.

### <a name="recovery"></a>Obnovení

* Provádějte geografické obnovení databáze na jiný server, jak je popsáno [zde](sql-database-disaster-recovery.md).
* Změňte konfiguraci aplikace pro připojení k obnovené databázi a postupujte podle pokynů [pro konfiguraci databáze po obnovení](sql-database-disaster-recovery.md) a dokončete obnovení.

### <a name="validation"></a>Ověřování

Dokončete postup ověřením obnovení integrity aplikace (včetně připojovacích řetězců, přihlášení, testování základních funkcí nebo jiných ověřovacích částí standardních postupů odhlašování aplikací).

## <a name="failover-groups"></a>Skupiny převzetí služeb při selhání

Pro databázi, která je chráněna pomocí skupin y převzetí služeb při selhání, cvičení procházení zahrnuje plánované převzetí služeb při selhání na sekundární server. Plánované převzetí služeb při selhání zajišťuje, že primární a sekundární databáze ve skupině převzetí služeb při selhání zůstanou synchronizovány při přepnutí rolí. Na rozdíl od neplánovanépřevzetí služeb při selhání tato operace nevede ke ztrátě dat, takže vrták lze provést v provozním prostředí.

### <a name="outage-simulation"></a>Simulace výpadku

Chcete-li simulovat výpadek, můžete zakázat webovou aplikaci nebo virtuální počítač připojený k databázi. Tato simulace výpadku má za následek selhání připojení pro webové klienty.

### <a name="recovery"></a>Obnovení

* Ujistěte se, že konfigurace aplikace v oblasti zotavení po Havárii odkazuje na předchozí sekundární, která se stane plně přístupné nové primární.
* Inicializovat [plánované převzetí služeb při selhání](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) skupiny převzetí služeb při selhání ze sekundárního serveru.
* Chcete-li dokončit obnovení, postupujte podle pokynů [pro konfiguraci databáze po obnovení.](sql-database-disaster-recovery.md)

### <a name="validation"></a>Ověřování

Dokončete postup ověřením obnovení integrity aplikace (včetně připojení, testování základních funkcí nebo jiných ověření požadovaných pro odhlášení vrtáků).

## <a name="next-steps"></a>Další kroky

* Další informace o scénářích kontinuity provozu naleznete v [tématu Scénáře kontinuity](sql-database-business-continuity.md).
* Další informace o automatickém zálohování azure sql database najdete v tématu [automatické zálohování databáze SQL Database.](sql-database-automated-backups.md)
* Další informace o použití automatických záloh pro obnovení naleznete [v tématu obnovení databáze ze záloh iniciovaných službou](sql-database-recovery-using-backups.md).
* Informace o rychlejších možnostech obnovení naleznete [v tématu Aktivní geografická replikace](sql-database-active-geo-replication.md) a [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md).
