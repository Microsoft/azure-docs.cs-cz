---
title: Upgrade zařízení Kolektoru ve službě Azure Migrate | Dokumentace Microsoftu
description: Obsahuje informace o upgradech pro zařízení Azure Migrate Collector.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: f9f2ef25a2054d92cdcc3ab33ea9234883e352d6
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484504"
---
# <a name="collector-appliance-updates"></a>Aktualizace zařízení kolektoru

## <a name="how-to-upgrade-the-appliance"></a>Postup upgradu na zařízení

Kolekce můžete upgradovat na nejnovější verzi bez stáhnout soubor OVA znovu.

1. Zavřete všechna okna prohlížeče a všechny otevření souborů/složek v zařízení.
2. Stáhněte si nejnovější balíček s upgradem z v seznamu novinek níže uvedených v tomto článku.
3. K zajištění, že staženého balíčku je zabezpečené, otevřete okno příkazového řádku správce a spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor ZIP. Vygenerovaná hodnota hash by měla odpovídat symbolem hash uvedených proti konkrétní verzi:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Příklad: **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
4. Soubor zip zkopírujte do kolekcí zařízení virtuálního počítače.
5. Klikněte pravým tlačítkem na soubor zip > **Extrahovat vše**.
6. Klikněte pravým tlačítkem na **Setup.ps1** > **spustit s prostředím PowerShell**a postupujte podle pokynů k instalaci.

## <a name="collector-update-release-history"></a>Historie verzí aktualizace kolekce

Tento článek shrnuje informace o upgradu pro zařízení Kolektoru v [Azure Migrate](migrate-overview.md).

Azure Migrate Collector je zjednodušené zařízení, která se používá ke zjišťování v místním prostředí, vCenter, pro účely posouzení před migrací do Azure. [Další informace](concepts-collector.md).

### <a name="continuous-discovery-upgrade-versions"></a>Průběžná zjišťování: Upgrade verze

#### <a name="version-101012-released-on-03132019"></a>Verze 1.0.10.12 (vydaného 03/13/2019)

Obsahuje opravy problémů při výběru Azure cloud v zařízení.

Hodnota hash hodnoty pro upgrade [balíček 1.0.10.12](https://aka.ms/migrate/col/upgrade_10_12)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | 27704154082344c058238000dff9ae44
SHA1 | 41e9e2fb71a8dac14d64f91f0fd780e0d606785e
SHA256 | c6e7504fcda46908b636bfe25b8c73f067e3465b748f77e50027e66f2727c2a9

### <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>Jednorázové zjišťování (teď zastaralé): Předchozí upgrade verze

> [!NOTE]
> Jednorázové zjišťování zařízení je nyní zastaralý a tato metoda spoléhal na vCenter serveru nastavení statistiky dostupnosti bodu dat výkonu shromážděných čítačů průměrný výkon, které umožňují snížení velikosti virtuálních počítačů pro migraci do Azure.

#### <a name="version-10916-released-on-10292018"></a>Verze 1.0.9.16 (vydaná 10/29 nebo 2018)

Obsahuje opravy nástroje PowerCLI problémy, kterým čelí při nastavování zařízení.

Hodnota hash hodnoty pro upgrade [balíček 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

#### <a name="version-10914"></a>Verze 1.0.9.14

Hodnota hash hodnoty pro upgrade [balíček 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="version-10913"></a>Verze 1.0.9.13

Hodnota hash hodnoty pro upgrade [balíček 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="next-steps"></a>Další postup

- [Další informace](concepts-collector.md) o zařízení Kolektoru.
- [Spustit posouzení](tutorial-assessment-vmware.md) pro virtuální počítače VMware.
