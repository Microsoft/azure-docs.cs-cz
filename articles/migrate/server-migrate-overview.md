---
title: Vyberte možnost migrace VMware s Azure Migrate serveru migrace | Dokumentace Microsoftu
description: Poskytuje přehled možností pro migraci virtuálních počítačů VMware do Azure pomocí služby Azure Migrate migrace serveru
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811567"
---
# <a name="select-a-vmware-migration-option"></a>Vyberte možnost migrace VMware

Můžete migrovat virtuální počítače VMware do Azure pomocí nástroje Azure Migrate serveru migrace. Tento nástroj nabízí několik možností pro migraci virtuálních počítačů VMware:

- Migrace pomocí bez agentů replikace. Migrace virtuálních počítačů, aniž by bylo potřeba nic instalovat na ně.
- Migrace s vloženým agentem pro replikaci. Instalace agenta na virtuální počítač pro replikaci.


I když je snazší z hlediska nasazení bez agentů replikace, aktuálně má několik omezení.

## <a name="agentless-migration-limitations"></a>Omezení migrace bez agenta

Omezení jsou následující:

- **Souběžné replikace**: Maximálně 50 virtuálních počítačů je možné současně replikovat ze systému vCenter Server.<br/> Pokud máte více než 50 virtuálních počítačů pro migraci, vytvoření několika dávek virtuálních počítačů.<br/> Replikace více v jednu chvíli bude mít vliv na výkon.
- **Disky virtuálních počítačů**: Virtuální počítač, který chcete migrovat, musí mít 60 nebo méně disků.
- **Virtuální počítač operační systémy**: Obecně platí Azure Migrate můžete migrovat jakýkoliv Server Windows nebo Linuxem operačního systému, ale potřebovat změny na virtuálních počítačích, aby mohla spustit v Azure. Azure Migrate provede změny automaticky pro tyto operační systémy:
    - Red Hat Enterprise Linux verze 6.5 + 7.0 +
    - CentOS 6.5+, 7.0+
    - SUSE Linux Enterprise Server 12 SP1 +
    - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
    - Debian 7,8
    - Pro další operační systémy budete muset provést úpravy ručně před migrací. [Migrovat kurzu](tutorial-migrate-vmware.md) vysvětluje, jak to provést.
- **Linux spouštěcí**: Je-li Boot na vyhrazené oddílů, by měl být uložený na disk s operačním systémem a nesmí být rozděleny mezi několik disků.<br/> Pokud Boot v rámci oddílu kořenový adresář (/), pak oddílu '/' by měl být na disku s operačním systémem a ostatní disky nejsou span.
- **Spouštění UEFI**: Virtuální počítače se spouštěním UEFI se pro migraci podporováno.
- **Šifrované disky nebo svazky (BitLocker, cryptfs)** : Virtuální počítače s disky nebo svazky šifrované nejsou pro migraci podporováno.
- **RDM/průchozí disky**: Pokud virtuální počítače mají RDM nebo průchozí disky, nebude možné tyto disky replikovat do Azure
- **NFS**: Připojit jako svazky na virtuálních počítačích svazky systému souborů NFS, se replikovat nebudou.
- **Cílové úložiště**: Pouze můžete migrovat virtuální počítače VMware na virtuální počítače Azure se spravovanými disky (Standard HDD, Premium SSD).



## <a name="deployment-steps-comparison"></a>Porovnání kroky nasazení

Po zkontrolování omezení, pochopení kroků potřebných k nasazení jednotlivých řešení může pomoct při rozhodování, kterou možnost vybrat.

**Úloha** | **Podrobnosti** |**Bez agentů** | **Založené na agentovi**
--- | --- | --- | ---
**Příprava VMware serverů a virtuálních počítačů pro migraci** | Konfigurovat řadu nastavení na virtuální počítače a servery VMware. | Požadováno | Požadováno
**Přidat nástroj pro migraci serveru** | Přidáte nástroj pro migraci serveru migraci Azure v projektu Azure Migrate. | Požadováno | Požadováno
**Nasazení zařízení Azure Migrate** | Nastavte si jednoduchý zařízení na virtuálním počítači VMware pro zjišťování virtuálních počítačů a posouzení. | Požadováno | Není nutné.
**Instalace služby Mobility na virtuálních počítačích** | Instalace služby Mobility na každém virtuálním počítači, který chcete replikovat | Nepožaduje se | Požadováno
**Nasazení zařízení Azure Migrate serveru migrace replikace** | Nastavení zařízení na virtuálním počítači VMware ke zjištění virtuálních počítačů a propojit služby Mobility spuštěné na virtuálních počítačích a migrace serveru migrace Azure | Nepožaduje se | Požadováno
**Replikace virtuálních počítačů**. Povolení replikace virtuálních počítačů. | Konfigurace nastavení replikace a vyberte virtuální počítač k replikaci | Požadováno | Požadováno
**Spuštění testu migrace** | Spuštění testu migrace, abyste měli jistotu, že všechno funguje podle očekávání. | Požadováno | Požadováno
**Spustit plné migraci** | Migrace virtuálních počítačů. | Požadováno | Požadováno




## <a name="next-steps"></a>Další postup

[Migrace virtuálních počítačů VMware](tutorial-migrate-vmware.md) pomocí migrace bez agenta.



