---
title: Známé problémy/omezení migrace při použití hybridního režimu
description: Přečtěte si o známých problémech/omezenímigrace pomocí služby Migrace databáze Azure v hybridním režimu.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: aedc7ea3d778d52f6f348837430987568af188ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649598"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Známé problémy/omezení migrace s použitím hybridního režimu

Známé problémy a omezení spojené s používáním služby Migrace databáze Azure v hybridním režimu jsou popsány v následujících částech.

## <a name="installer-fails-to-authenticate"></a>Instalačnímu programu se nepodařilo ověřit

Po nahrání certifikátu do AdAppu dochází ke zpoždění až několik minut, než se bude moci ověřit pomocí Azure. Instalační program se pokusí opakovat s určitým zpožděním, ale je možné, že zpoždění šíření bude delší než opakování a zobrazí se zpráva **FailedToGetAccessTokenException.** Pokud byl certifikát nahrán na správný AdApp a správný AppId byl poskytnut v souboru dmsSettings.json, zkuste spustit příkaz install znovu.

## <a name="service-offline-after-successful-installation"></a>Služba "offline" po úspěšné instalaci

Pokud se služba po úspěšném dokončení procesu instalace zobrazí jako offline, zkuste použít následující kroky.

1. Na webu Azure Portal ve vaší instanci služby Migrace databáze Azure přejděte na kartu **Hybridní** nastavení a ověřte, zda je pracovník registrovaný, a to kontrolou mřížky registrovaných pracovníků.

    Stav tohoto pracovníka by měl být **online**, ale může se zobrazit jako **offline,** pokud dojde k potížím.

2. V pracovním počítači zkontrolujte stav služby spuštěním následujícího příkazu Prostředí PowerShell:

    ```
    Get-Service Scenario*
    ```

    Tento příkaz poskytuje stav služby systému Windows spuštěného pracovníka. Měl by existovat pouze jeden výsledek. Pokud je pracovník zastaven, můžete se ho pokusit restartovat pomocí následujícího příkazu Prostředí PowerShell:

    ```
    Start-Service Scenario*
    ```

    Můžete také zkontrolovat službu v uzdu služby systému Windows.

3. Pokud služby systému Windows cykly mezi spuštěna a zastavena, pak pracovník došlo k problémům spuštění. Chcete-li zjistit problém, zkontrolujte protokoly hybridních pracovních pracovníků služby Azure Database Migration Service.

    - Protokoly procesu instalace jsou uloženy ve složce "protokoly" ve složce, ze které byl spuštěn spustitelný soubor instalačního programu.
    - Protokoly hybridních pracovních pracovníků služby Azure Database Migration Service jsou uloženy ve složce **WorkerLogs** ve složce, ve které je nainstalován pracovník. Výchozí umístění souborů protokolu hybridního pracovního procesu je **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Použití vlastního podepsaného certifikátu

Certifikát generovaný akcí GenerateCert je certifikát podepsaný svým držitelem, který nemusí být přijatelný na základě zásad vnitřního zabezpečení. Namísto použití tohoto certifikátu můžete poskytnout vlastní certifikát a poskytnout kryptografický otisk v souboru dmsSettings.json. Tento certifikát bude potřeba nahrát do adAppu a nainstalovat do počítače, do kterého instalujete hybridního pracovníka služby Migrace databáze Azure. Potom nainstalujte tento certifikát s privátním klíčem do úložiště certifikátů místního počítače.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Spuštění pracovní služby jako účtu s nízkými oprávněními

Ve výchozím nastavení se služba hybridního pracovního procesu služby Migrace databáze Azure spustí jako účet místního systému. Účet používaný pro tuto službu můžete změnit, pokud má používaný účet síťová oprávnění. Chcete-li změnit účet služby "spustit jako", použijte následující proces.

1. Zastavte službu prostřednictvím služeb systému Windows nebo pomocí příkazu Stop-Service v prostředí PowerShell.

2. Aktualizujte službu tak, aby používala jiný přihlašovací účet.

3. V certifikátech certmgr pro místní počítač udělte oprávnění soukromého klíče k novému účtu pro certifikáty **DMS Hybrid App Key** a **DMS Scenario Engine Key Pair.**

    a. Chcete-li zobrazit následující klávesy, otevřete certmgr:

    - Klíč hybridní aplikace DMS
    - Klíč nastavení hybridního pracovního procesu DMS
    - DMS scénář dvojice klíčů motoru

    b. Klepněte pravým tlačítkem myši na položku **DMS Hybrid App Key** , přejděte na **všechny úkoly**a vyberte příkaz **Spravovat soukromé klíče**.

    c. Na kartě **Zabezpečení** vyberte **Přidat**a zadejte název účtu.

    d. Stejným postupem udělte oprávnění soukromého klíče pro nový účet certifikátu **spárem klíčů modulu DMS.**

## <a name="unregistering-the-worker-manually"></a>Ruční zrušení registrace pracovníka

Pokud už nemáte přístup k pracovnímu počítači, můžete zrušit registraci pracovníka a znovu použít instanci služby Migrace databáze Azure provedením následujících kroků:

1. Na webu Azure Portal se dostal do instance služby Migrace databáze Azure a pak přejděte na stránku **Hybridní** nastavení.

   Položka pracovníka se zobrazí v seznamu se stavem **offline**.

2. Zcela vpravo od výpisu položky pracovníka vyberte tři tečky a pak vyberte **Zrušit registraci**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Řešení problémů pro konkrétní scénáře migrace

Následující části popisují problémy specifické pro scénář související s použitím hybridního režimu služby Migrace databáze Azure k provedení migrace online.

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>Online migrace do spravované instance Azure SQL Database

**Vysoké využití procesoru**

**Problém**: Pro online migrace do instance spravované službou SQL Database počítač s hybridním pracovníkem narazí na vysoké využití procesoru, pokud existuje příliš mnoho záloh nebo pokud jsou zálohy příliš velké.

**Zmírnění :** Chcete-li tento problém zmírnit, použijte komprimované zálohy, rozdělte migraci tak, aby používala více sdílených složek, nebo vertikálně navýšit kapacitu počítače s hybridním pracovníkem.
