---
title: Odstranění projektu Azure Migrate
description: Popisuje, jak vytvořit projekt Migrace Azure a přidat nástroj pro hodnocení a migraci.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512727"
---
# <a name="delete-an-azure-migrate-project"></a>Odstranění projektu Azure Migrate

Tento článek popisuje, jak odstranit projekt [Migrace Azure.](migrate-overview.md)


## <a name="before-you-start"></a>Než začnete

Před odstraněním projektu:

- Při odstranění projektu, projekt a zjištěné počítače metadata jsou odstraněny.
- Pokud jste k nástroji pro vyhodnocení serveru pro analýzu závislostí připojili pracovní prostor Analýzy protokolů, rozhodněte se, zda chcete pracovní prostor odstranit. 
    - Pracovní prostor se automaticky neodstraní. Odstraňte jej ručně.
    - Před odstraněním ověřte, k čemu se pracovní prostor používá. Stejný pracovní prostor Log Analytics lze použít pro více scénářů.
    - Před odstraněním projektu najdete odkaz na pracovní prostor v **Azure Migrate – Servery** > **Azure Migrate - Servery Assessment serveru**v části Pracovní prostor **OMS**.
    - Chcete-li po odstranění projektu odstranit pracovní prostor, vyhledejte pracovní prostor v příslušné skupině zdrojů a postupujte [podle těchto pokynů](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Odstranění projektu


1. Na webu Azure Portal otevřete skupinu prostředků, ve které byl projekt vytvořen.
2. Na stránce skupiny prostředků vyberte **Zobrazit skryté typy**.
3. Vyberte projekt a přidružené zdroje, které chcete odstranit.
    - Typ prostředku pro projekty Migrace Azure je **Microsoft.Migrate/migrateprojects**.
    - V další části zkontrolujte prostředky vytvořené pro zjišťování, hodnocení a migraci v projektu Migrace Azure.
    - Pokud skupina prostředků obsahuje jenom projekt Migrace Azure, můžete odstranit celou skupinu prostředků.
    - Pokud chcete odstranit projekt z předchozí verze Azure Migrate, kroky jsou stejné. Typ zdroje pro tyto projekty je **Projekt migrace**.


## <a name="created-resources"></a>Vytvořené zdroje

Tyto tabulky shrnují prostředky vytvořené pro zjišťování, hodnocení a migraci v projektu Migrace Azure.

> [!NOTE]
> Trezor klíčů odstraňujte opatrně, protože může obsahovat bezpečnostní klíče.

### <a name="vmwarephysical-server"></a>VMware / fyzický server

**Zdrojů** | **Typ**
--- | ---
"Appliancename"kv | Trezor klíčů
"Appliancename"site | Weby Microsoft.OffAzure/VMware
"Název projektu" | Microsoft.Migrate/migrateprojects
Projekt "Název_projektu" | Microsoft.Migrate/assessmentProjects
"Název projektu"rsvault | Trezor služby Recovery Services
"Název_projektu"-MigrateVault-* | Trezor služby Recovery Services
migrateappligwsa* | Účet úložiště
migrateapplilsa* | Účet úložiště
migrateapplicsa* | Účet úložiště
migrateapplikv* | Trezor klíčů
migrateapplisbns16041 | Service Bus Namespace

### <a name="hyper-v-vm"></a>Virtuální počítač s technologií Hyper-V 

**Zdrojů** | **Typ**
--- | ---
"Název projektu" | Microsoft.Migrate/migrateprojects
Projekt "Název_projektu" | Microsoft.Migrate/assessmentProjects
HyperV*kv | Trezor klíčů
HyperV*Lokalita | Microsoft.OffAzure/HypervSites
"Název_projektu"-MigrateVault-* | Trezor služby Recovery Services


## <a name="next-steps"></a>Další kroky

Přečtěte si, jak přidat další nástroje pro [hodnocení](how-to-assess.md) a [migraci.](how-to-migrate.md) 
