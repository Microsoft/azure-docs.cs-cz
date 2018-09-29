---
title: Upgrade zařízení Kolektoru ve službě Azure Migrate | Dokumentace Microsoftu
description: Obsahuje informace o upgradech pro zařízení Azure Migrate Collector.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 6080096194ec5b11f1d5593fcbb6732d1551667f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431388"
---
# <a name="collector-update-release-history"></a>Historie verzí aktualizace kolekce

Tento článek shrnuje informace o upgradu pro zařízení Kolektoru v [Azure Migrate](migrate-overview.md).

Azure Migrate Collector je zjednodušené zařízení, která se používá ke zjišťování v místním prostředí, vCenter, pro účely posouzení před migrací do Azure. [Další informace](concepts-collector.md).


## <a name="one-time-discovery-upgrade-versions"></a>Jednorázově: Upgrade verze

### <a name="version-10914"></a>Verze 1.0.9.14

Hodnota hash hodnoty pro upgrade [balíček 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>Verze 1.0.9.13

Hodnota hash hodnoty pro upgrade [balíček 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

### <a name="version-10911"></a>Verze 1.0.9.11

Hodnota hash hodnoty pro upgrade [balíček 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

### <a name="version-1097"></a>Verze 1.0.9.7:

Hodnota hash hodnoty pro upgrade [balíček 1.0.9.7:](https://aka.ms/migrate/col/upgrade_9_7)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="continuous-discovery-upgrade-versions"></a>Průběžná zjišťování: Upgrade verze

Ještě je k dispozici žádný upgrade pro průběžné zjišťování zařízení.

## <a name="run-an-upgrade"></a>Spuštění upgradu

Kolekce můžete upgradovat na nejnovější verzi bez stáhnout soubor OVA znovu.

1. Stažení nejnovější balíček s upgradem v níže uvedeném seznamu.
2. Tak, aby byl zabezpečený stažené oprav hotfix, otevřete okno příkazového řádku pro správce a spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor ZIP. Vygenerovaná hodnota hash by měla odpovídat symbolem hash uvedených proti konkrétní verzi:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Příklad: **C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. Soubor zip zkopírujte do kolekcí zařízení virtuálního počítače.
4. Klikněte pravým tlačítkem na soubor zip > **Extrahovat vše**.
5. Klikněte pravým tlačítkem na **Setup.ps1** > **spustit s prostředím PowerShell**a postupujte podle pokynů k instalaci.


## <a name="next-steps"></a>Další postup

- [Další informace](concepts-collector.md) o zařízení Kolektoru.
- [Spustit posouzení](tutorial-assessment-vmware.md) pro virtuální počítače VMware.
