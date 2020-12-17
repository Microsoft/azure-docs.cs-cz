---
title: Odstranit pomocí nástroje pro vytváření snímků konzistentního pro aplikace Azure pro Azure NetApp Files | Microsoft Docs
description: Poskytuje návod pro spuštění příkazu Odstranit nástroje pro snímek konzistentní aplikace Azure, který můžete použít s Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0e2e4beebedb93524da43c5a3fad750b0295f5cd
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632724"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Odstranění pomocí nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure (Preview)

Tento článek poskytuje návod pro spuštění příkazu Odstranit v rámci nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure, který můžete použít s Azure NetApp Files.

## <a name="introduction"></a>Úvod

Pomocí příkazu je možné odstranit snímky svazků a položky katalogu databáze `azacsnap -c delete` .

> [!IMPORTANT]
> Snímky vytvořené méně než 10 minut před spuštěním tohoto příkazu by neměly být odstraněny z důvodu potenciálního rušení při replikaci snímků.

## <a name="command-options"></a>Možnosti příkazu

`-c delete`Příkaz má následující možnosti:

- `--delete hana` Při použití s možnostmi `--hanasid <SID>` se `--hanabackupid <HANA backup id>` odstraní položky z katalogu SAP HANA Backup, které odpovídají kritériím.

- `--delete storage` Při použití s možností `--snapshot <snapshot name>` odstraní snímek z back-endového systému úložiště.

- `--delete sync` Pokud se použije s možnostmi `--hanasid <SID>` a `--hanabackupid <HANA backup id>` Získá název snímku úložiště z katalogu záloh pro `<HANA backup id>` , a pak položku odstraní v katalogu záloh _a_ snímku ze všech svazků, které obsahují pojmenovaný snímek.

- `--delete sync` Při použití s nástrojem se `--snapshot <snapshot name>` zkontroluje, jestli všechny položky v katalogu záloh pro `<snapshot name>` , získá ID zálohy SAP Hana a odstraní jak položku v katalogu záloh, _tak_ i snímek z libovolného svazku obsahujícího pojmenovaný snímek.

- `[--force]` volitelné *Používejte s opatrností*.  Tato operace vynutí odstranění bez výzvy k potvrzení.

- `[--configfile <config filename>]` je volitelný parametr, který umožňuje názvy vlastních konfiguračních souborů.

### <a name="delete-a-snapshot-using-sync-option"></a>Odstranění snímku pomocí `sync` Možnosti

```bash
azacsnap -c delete --delete sync --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Kontroluje všechny položky v katalogu záloh pro SAP HANA záložního ID 157979797979, získá název snímku úložiště a odstraní jak položku v katalogu záloh, tak i snímek ze všech svazků, které obsahují pojmenovaný snímek.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Kontroluje všechny položky v katalogu záloh pro snímek s názvem hana_hourly .2020-01 -22 _2358, získá ID zálohy SAP HANA a odstraní položku v katalogu záloh i ve snímku ze všech svazků, které obsahují pojmenovaný snímek.

### <a name="delete-a-snapshot-using-hana-option"></a>Odstranění snímku pomocí `hana` Možnosti

```bash
azacsnap -c delete --delete hana --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Odstraní ID zálohy SAP HANA 157979797979 ze katalogu záloh pro identifikátor SID H80.

### <a name="delete-a-snapshot-using-storage-option"></a>Odstranění snímku pomocí `storage` Možnosti

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Odstraní snímek ze všech svazků, které obsahují snímek s názvem hana_hourly .2020-01 -22 _2358.

**Výstup pomocí `--delete storage` Možnosti**

Uživateli se zobrazí výzva k potvrzení odstranění.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

Je možné vyhnout se potvrzení uživateli pomocí volitelného `--force` parametru následujícím způsobem:

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>Další kroky

- [Získat podrobnosti o snímku](azacsnap-cmd-ref-details.md)
- [Vytvořit zálohu](azacsnap-cmd-ref-backup.md)
