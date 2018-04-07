---
title: Cvičení obnovení po havárii databáze SQL | Microsoft Docs
description: Další pokyny a osvědčené postupy pro používání Azure SQL Database k provedení projde obnovení po havárii.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/01/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 948b8c2c7eac7306336e5dacafffb62a337b8191
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="performing-disaster-recovery-drill"></a>Provedením postupu zotavení po havárii
Doporučujeme, aby se pravidelně provádí ověřování připravenosti aplikace pro pracovní postup obnovení. Ověření chování aplikace a dopad ztráty dat nebo přerušení zahrnuje tento převzetí služeb při selhání je dobrým zvykem engineering. Je také požadavek ve většině oborové standardy jako součást Certifikační kontinuity firmy.

Provádění postupu zotavení po havárii zahrnuje:

* Simulace výpadku datové vrstvy
* Obnovení
* Ověření obnovení post integritu aplikací

V závislosti na tom, jak můžete [určená aplikace pro kontinuitu podnikových procesů](sql-database-business-continuity.md), pracovní postup spuštění podrobné se může lišit. Tento článek popisuje osvědčené postupy pro provádění postupu zotavení po havárii v kontextu databáze SQL Azure.

## <a name="geo-restore"></a>Geografické obnovení
Aby se zabránilo potenciální ztrátě dat při provádění postupu zotavení po havárii, proveďte procházení testovacím prostředí pomocí vytvořit kopii produkčního prostředí a používat jej ověření pracovní postup převzetí služeb při selhání aplikace.

#### <a name="outage-simulation"></a>Simulace výpadku
Pro simulaci se výpadek, můžete přejmenovat zdrojové databáze. To způsobí selhání připojení aplikace.

#### <a name="recovery"></a>Obnovení
* Provést geografické obnovení databáze do jiného serveru, jak se popisuje [zde](sql-database-disaster-recovery.md).
* Změna konfigurace aplikace pro připojení k obnovené databáze a postupujte podle [nakonfigurovat databázi po obnovení](sql-database-disaster-recovery.md) průvodce dokončí obnovení.

#### <a name="validation"></a>Ověření
* Dokončete podrobné sestavy pomocí ověření obnovení post integrity aplikace (včetně připojovací řetězce, přihlášení, základní funkce testování nebo jinou ověření část postupy signoffs standardní aplikace).

## <a name="failover-groups"></a>Skupiny převzetí služeb při selhání
Pro databáze, který je chráněný pomocí skupin převzetí služeb při selhání zahrnuje cvičení procházení plánované převzetí služeb při selhání na sekundární server. Plánované převzetí služeb při selhání zaručuje, že primární a sekundární databází ve skupině pro převzetí služeb při selhání zachovány synchronizované při přepínání role. Na rozdíl od neplánované převzetí služeb při selhání tato operace není způsobit ztrátu dat, takže podrobné sestavy lze provést v provozním prostředí.

#### <a name="outage-simulation"></a>Simulace výpadku
Pro simulaci se výpadek, můžete zakázat webové aplikace nebo virtuální počítač připojen k databázi. Pro klienty webového výsledkem chyby připojení.

#### <a name="recovery"></a>Obnovení
* Zkontrolujte konfiguraci aplikace v bodech oblast zotavení po Havárii na dřívějším sekundární, který se stane plně dostupný nový primární.
* Zahájit [plánované převzetí služeb při selhání](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) převzetí služeb při selhání skupiny ze sekundárního serveru.
* Postupujte podle [nakonfigurovat databázi po obnovení](sql-database-disaster-recovery.md) průvodce dokončí obnovení.

#### <a name="validation"></a>Ověření
Dokončete podrobné sestavy pomocí ověření obnovení post integrity aplikace (včetně připojení, testování základní funkce nebo další ověření požadované pro procházení signoffs).

## <a name="next-steps"></a>Další postup
* Další informace o obchodních scénářů kontinuity najdete v tématu [kontinuity scénáře](sql-database-business-continuity.md).
* Další informace o Azure SQL Database automatizované zálohování najdete v tématu [automatizované zálohování SQL Database](sql-database-automated-backups.md)
* Další informace o použití automatizované zálohování pro obnovení, najdete v části [obnovit databázi ze zálohy spouštěná služba](sql-database-recovery-using-backups.md).
* Další informace o možnosti rychlejší obnovení najdete v tématu [aktivní geografickou replikaci a převzetí služeb při selhání skupiny](sql-database-geo-replication-overview.md).  
