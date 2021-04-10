---
title: Zálohování pomocí nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure pro Azure NetApp Files | Microsoft Docs
description: Poskytuje návod pro spuštění příkazu Backup nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure, který můžete použít s Azure NetApp Files.
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
ms.openlocfilehash: 17c29fdf88495f6ecc40963eda08858887173fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730934"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>Zálohování pomocí nástroje Snapshot konzistentního pro aplikace Azure (Preview)

Tento článek poskytuje návod pro spuštění příkazu Backup nástroje pro snímek konzistentní aplikace Azure, který můžete použít s Azure NetApp Files.

## <a name="introduction"></a>Úvod

Zálohování na základě snímků úložiště se spouští pomocí `azacsnap -c backup` příkazu.  Tento příkaz provede orchestraci snímku úložiště konzistentní databáze na datových svazcích a snímku úložiště (bez nastavení konzistence databáze) na ostatních svazcích.  

Pro datové svazky `azacsnap` připraví databázi pro snímek úložiště a pak pozastaví snímek úložiště pro všechny nakonfigurované svazky, a konečně poradí databázi, kterou snímek dokončil.  Bude také spravovat všechny položky v databázi, které zaznamenávají aktivitu zálohování snímků (např. SAP HANA katalogu zálohování).

## <a name="command-options"></a>Možnosti příkazu

`-c backup`Příkaz přijímá následující argumenty:

- `--volume=` typ svazku, který se má snímek, tento parametr může obsahovat `data` nebo `other`
  - `data` vysnímků svazků v rámci stanzay datavolume konfiguračního souboru.
  - `other` vysnímků svazků v otherVolume Stanza konfiguračního souboru.
  
  > [!NOTE]
  > Vytvořením samostatného konfiguračního souboru se spouštěcím svazkem jako otherVolume je možné, že se `boot` snímky natrvaloy v rámci zcela jiného plánu (například denně).

- `--prefix=` Předpona snímku zákazníka pro název snímku. Tento parametr má dva účely. Za prvé účelem je poskytnout jedinečný název pro seskupení snímků. Za druhé k určení `--retention` počtu snímků úložiště, které jsou pro zadanou hodnotu zachované `--prefix` .

    > [!IMPORTANT]
    > Jsou povoleny pouze alfanumerické znaky ("A-Z, A-z, 0-9"), podtržítko ("_") a pomlčky ("-").

- `--retention` počet snímků definovaného `--prefix` k uchování. Po provedení nového snímku se odeberou všechny další snímky `--prefix` .

- `--trim` Tato možnost je k dispozici pro SAP HANA v2 a novější, udržuje Katalog zálohování a zálohy protokolů na disku a v katalogu. Počet položek, které se mají zachovat v katalogu záloh, je určený `--retention` výše uvedenou možností a odstraní staré položky pro definovanou předponu ( `--prefix` ) z katalogu záloh a související fyzické zálohy protokolů. Odstraní také všechny položky záloh protokolu, které jsou starší než nejstarší neprotokolované položky zálohování. Tato operace pomáhá zabránit tomu, aby zálohy protokolu používaly veškeré dostupné místo na disku.

  > [!NOTE]
  > Následující příklad příkazu zachová 9 snímků úložiště a zajistí nepřetržité zkrácení katalogu zálohování tak, aby odpovídalo zachovávání snímků úložiště 9.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` volitelný parametr, který definuje metodu šifrování používanou ke komunikaci s SAP HANA, buď `openssl` nebo `commoncrypto` . Pokud je tato příkaz definována, očekává se, že `azacsnap -c backup` příkaz ve stejném adresáři najde dva soubory. Tyto soubory musí být pojmenovány za odpovídajícím identifikátorem SID. Informace o [komunikaci s SAP HANA najdete v tématu použití protokolu SSL](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana). V následujícím příkladu je `hana` použit snímek typu s předponou `hana_TEST` a zachová `9` se jim komunikace s SAP HANA pomocí protokolu SSL ( `openssl` ).

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` je volitelný parametr, který umožňuje názvy vlastních konfiguračních souborů.

## <a name="snapshot-backups-are-fast"></a>Zálohy snímků jsou rychlé

Doba trvání zálohování snímku je nezávislá na velikosti svazku, přičemž velikost 10 TB se přichycena v rámci stejné přibližné doby jako na 10 GB.  

Hlavní faktory, které mají vliv na celkovou dobu provádění, jsou počet svazků, které mají být snímky, a všechny změny v `--retention` parametru (kde snížení může zvýšit dobu provádění při odebrání nadbytečných snímků).

V příkladu konfigurace výše (pro **rozsáhlá instance Azure**) se dokončily snímky pro tyto dva svazky za méně než 5 sekund. Pro **Azure NetApp Files** budou snímky pro tyto dva svazky trvat přibližně 60 sekund.

> [!NOTE]
> Pokud `--retention` je v předchozím čase `azacsnap` spuštěný (například z `--retention 50` na `--retention 5` ), bude čas potřebný ke zvýšení, jak bude `azacsnap` potřeba odebrat nadbytečné snímky.

## <a name="example-with-data-parameter"></a>Příklad s `data` parametrem

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

Příkaz neprovádí výstup do konzoly, ale zapisuje do souboru protokolu, do souboru výsledků a do `/var/log/messages` .

*Soubor protokolu* se skládá z názvu příkazu + parametr-c + název konfiguračního souboru. Ve výchozím nastavení je název souboru protokolu pro `-c backup` spuštění s výchozím názvem souboru konfigurace `azacsnap-backup-azacsnap.log` .

*Výsledný* soubor má stejný základní název jako soubor protokolu s `.result` příponou jako jeho příponou, například `azacsnap-backup-azacsnap.result` obsahující následující výstup:

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

`/var/log/messages`Soubor obsahuje stejný výstup jako `.result` soubor. Podívejte se na následující příklad (Spustit jako root):

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>Příklad s `other` parametrem

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

Příkaz neprovádí výstup do konzoly, ale zapisuje pouze do souboru protokolu.  Nepíše _do_ souboru výsledků nebo `/var/log/messages` .

*Soubor protokolu* se skládá z názvu příkazu + parametr-c + název konfiguračního souboru. Ve výchozím nastavení je název souboru protokolu pro `-c backup` spuštění s výchozím názvem souboru konfigurace `azacsnap-backup-azacsnap.log` .

## <a name="example-with-other-parameter-to-backup-host-os"></a>Příklad s `other` parametrem (pro záložní hostitelský operační systém)

> [!NOTE]
> Použití jiného konfiguračního souboru ( `--configfile bootVol.json` ), který obsahuje pouze spouštěcí svazky.

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

Příkaz neprovádí výstup do konzoly, ale zapisuje pouze do souboru protokolu.  Nepíše _do_ souboru výsledků nebo `/var/log/messages` .

Název *souboru protokolu* v tomto příkladu je `azacsnap-backup-bootVol.log` .

> [!NOTE]
> Název souboru protokolu se skládá z příkazu (název příkazu – ( `-c` možnost) – (název souboru config).  Pokud například použijete `-c backup` možnost s názvem souboru protokolu `h80.json` , bude se volat soubor protokolu `azacsnap-backup-h80.log` .  Nebo pokud použijete `-c test` možnost se stejným konfiguračním souborem, bude se volat soubor protokolu `azacsnap-test-h80.log` .

- Typ velké instance HANA: Existují dvě platné hodnoty `TYPEI` nebo `TYPEII` závislé na jednotce velké instance Hana.
- V části [dostupné SKU pro velké instance Hana](../virtual-machines/workloads/sap/hana-available-skus.md) potvrďte dostupné SKU.

## <a name="next-steps"></a>Další kroky

- [Získat podrobnosti o snímku](azacsnap-cmd-ref-details.md)
- [Odstranit snímky](azacsnap-cmd-ref-delete.md)