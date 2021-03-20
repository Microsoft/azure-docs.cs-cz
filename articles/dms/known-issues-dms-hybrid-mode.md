---
title: Známé problémy/omezení migrace s využitím hybridního režimu
description: Přečtěte si o známých problémech nebo omezeních migrace pomocí Azure Database Migration Service v hybridním režimu.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 819fe5ced6c91819c817065305a31fca456ea5c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91291839"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Známé problémy/omezení migrace s využitím hybridního režimu

Známé problémy a omezení související s používáním Azure Database Migration Service v hybridním režimu jsou popsány v následujících částech.

## <a name="installer-fails-to-authenticate"></a>Nepodařilo se ověřit instalační program

Po nahrání certifikátu do AdApp nastane zpoždění až několik minut, než se bude moct ověřit pomocí Azure. Instalační program se pokusí o opakování s určitou prodlevou, ale je možné, že zpoždění šíření bude delší než opakování, a zobrazí se zpráva **FailedToGetAccessTokenException** . Pokud byl certifikát odeslán do správného AdApp a byl zadán správný identifikátor AppId v dmsSettings.js, zkuste znovu spustit instalační příkaz.

## <a name="service-offline-after-successful-installation"></a>Služba "offline" po úspěšné instalaci

Pokud se služba po úspěšném dokončení procesu instalace zobrazí jako offline, zkuste použít následující postup.

1. V Azure Portal v instanci Azure Database Migration Service přejděte na kartu **hybridní** nastavení a pak ověřte, že je pracovní proces zaregistrován pomocí kontroly mřížky registrovaných pracovních procesů.

    Stav tohoto pracovního procesu by měl být **online**, ale pokud dojde k potížím, může se zobrazit jako **offline** .

2. V pracovním počítači ověřte stav služby spuštěním následujícího příkazu PowerShellu:

    ```
    Get-Service Scenario*
    ```

    Tento příkaz vám poskytne stav služby systému Windows, která spouští pracovní proces. Měl by existovat jenom jeden výsledek. Pokud je pracovní proces zastavený, můžete se ho pokusit znovu spustit pomocí následujícího příkazu PowerShellu:

    ```
    Start-Service Scenario*
    ```

    Službu můžete také vyhledat v uživatelském rozhraní služeb systému Windows.

3. Pokud se služba Windows cyklicky spouští a zastavila, pak pracovní proces narazil na problémy. Pokud chcete zjistit problém, zkontrolujte protokoly Azure Database Migration Service Hybrid Worker.

    - Protokoly procesu instalace se ukládají do složky Logs ve složce, ze které se spustil spustitelný soubor instalačního programu.
    - Protokoly Azure Database Migration Service Hybrid Worker se ukládají do složky **WorkerLogs** ve složce, ve které je nainstalovaný pracovní proces. Výchozí umístění souborů protokolu hybridního pracovního procesu je **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Používání vlastního podepsaného certifikátu

Certifikát generovaný akcí GenerateCert je certifikát podepsaný svým držitelem, který nemusí být přijatelný v závislosti na vašich interních zásadách zabezpečení. Místo použití tohoto certifikátu můžete zadat vlastní certifikát a poskytnout kryptografický otisk v dmsSettings.js. Tento certifikát bude nutné nahrát do AdApp a nainstalovat na počítač, na který instalujete Azure Database Migration Service Hybrid Worker. Pak tento certifikát nainstalujte pomocí privátního klíče do úložiště certifikátů místního počítače.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Spuštění služby pracovního procesu jako účtu s nízkou úrovní oprávnění

Ve výchozím nastavení se služba Azure Database Migration Service Hybrid Worker spouští jako účet místního systému. Účet, který se používá pro tuto službu, můžete změnit, pokud účet, který používáte, má síťová oprávnění. Chcete-li změnit účet Spustit jako služby, použijte následující postup.

1. Zastavte službu buď prostřednictvím služeb systému Windows, nebo pomocí příkazu Stop-Service v prostředí PowerShell.

2. Aktualizujte službu tak, aby používala jiný přihlašovací účet.

3. V části CertMgr pro certifikáty místního počítače udělte tomuto účtu oprávnění privátního klíče pro nový účet pro **klíč hybridní aplikace DMS** a certifikáty pro **dvojici klíčů v modulu scénáře DMS** .

    a. Otevřete certmgr pro zobrazení následujících klíčů:

    - Klíč hybridní aplikace DMS
    - Instalační klíč Hybrid Worker DMS
    - Pár klíčů pro jádro scénáře DMS

    b. Klikněte pravým tlačítkem myši na položku **klíčová aplikace DMS Hybrid App** , přejděte na **všechny úlohy** a pak vyberte **Spravovat privátní klíče**.

    c. Na kartě **zabezpečení** vyberte **Přidat** a potom zadejte název účtu.

    d. Použijte stejný postup pro udělení oprávnění privátního klíče pro nový účet do certifikátu **páru klíčů pro modul DMS scénáře** .

## <a name="unregistering-the-worker-manually"></a>Ruční zrušení registrace pracovního procesu

Pokud již nemáte přístup k pracovnímu počítači, můžete zrušit registraci pracovního procesu a znovu použít instanci Azure Database Migration Service provedením následujících kroků:

1. V Azure Portal přijděte ke své instanci Azure Database Migration Service a pak přejděte na stránku **hybridní** nastavení.

   Položka pracovního procesu se zobrazí v seznamu se stavem zobrazeným v **režimu offline**.

2. Do pravého seznamu položek pracovního procesu vyberte tři tečky a pak vyberte zrušit **registraci**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Řešení problémů pro konkrétní scénáře migrace

Níže uvedené části popisují problémy specifické pro scénáře týkající se použití Azure Database Migration Service hybridního režimu k provedení migrace online.

### <a name="online-migrations-to-azure-sql-managed-instance"></a>Online migrace do spravované instance Azure SQL

**Vysoké využití procesoru**

**Problém**: u online migrace do spravované instance SQL se na počítači, na kterém hybridní pracovní proces pracuje, setkáte s vysokým využitím procesoru, pokud existuje příliš mnoho záloh nebo pokud jsou zálohy moc velké.

**Zmírnění rizika**: Pokud chcete tento problém zmírnit, použijte komprimované zálohy, rozdělte migraci tak, aby používala víc sdílených složek, nebo nahorizontální navýšení kapacity počítače, na kterém běží hybridní pracovní proces.
