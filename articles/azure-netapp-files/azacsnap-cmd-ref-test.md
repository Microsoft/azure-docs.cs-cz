---
title: Test nástroje pro snímek konzistentních aplikací Azure pro Azure NetApp Files | Microsoft Docs
description: Vysvětluje, jak spustit test nástroje snímku konzistentního vzhledem k aplikacím Azure, který můžete použít s Azure NetApp Files.
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
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632681"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>Test nástroje pro snímek konzistentních aplikací Azure (Preview)

Tento článek vysvětluje, jak spustit test nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure, který můžete použít s Azure NetApp Files.

## <a name="introduction"></a>Úvod

Test konfigurace se provádí pomocí `azacsnap -c test` příkazu.

## <a name="command-options"></a>Možnosti příkazu

`-c test`Příkaz má následující možnosti:

- `--test hana`  Otestuje připojení k instanci SAP HANA.

- `--test storage` testuje komunikaci s podkladovým rozhraním úložiště vytvořením dočasného snímku úložiště na všech nakonfigurovaných `data` svazcích a jejich odebráním. 

- `--test all` provede `hana` `storage` testy a v pořadí.

- `[--configfile <config filename>]` je volitelný parametr, který umožňuje názvy vlastních konfiguračních souborů.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>Podívejte se na připojení pomocí SAP HANA `azacsnap -c test --test hana`

Tento příkaz zkontroluje připojení HANA pro všechny instance HANA v konfiguračním souboru. Pomocí HDBuserstore se připojí k SYSTEMDB a načte informace SID.

V případě protokolu SSL může tento příkaz převzít následující nepovinný argument:

- `--ssl=` vynutí šifrované připojení k databázi a definuje metodu šifrování používanou ke komunikaci s SAP HANA, a to buď `openssl` nebo `commoncrypto` . Pokud je tato příkaz definována, očekává se, že tento příkaz ve stejném adresáři najde dva soubory. Tyto soubory musí být pojmenovány za odpovídajícím identifikátorem SID. Informace o [komunikaci s SAP HANA najdete v tématu použití protokolu SSL](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana).

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>Výstup `azacsnap -c test --test hana` příkazu

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>Ověřit konektivitu s úložištěm `azacsnap -c test --test storage`

`azacsnap`Příkaz pozastaví snímek pro všechny nakonfigurované svazky dat, aby ověřil, že má správný přístup ke svazkům pro každou instanci SAP HANA. Vytvoří se dočasný snímek, který se pak odebere pro každý datový svazek, aby se ověřil přístup k snímku pro každý systém souborů.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>Výstup `azacsnap -c test --test storage` příkazu

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> V případě rozsáhlých instancí Azure `azacsnap -c test --test storage` příkaz odvodit generaci úložiště a SKU HLI.  Na základě těchto informací pak poskytuje pokyny ke konfiguraci spouštěcích snímků (viz řádek začínající `Action:` výstupem).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>Další kroky

- [Zálohování snímků pomocí nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure](azacsnap-cmd-ref-backup.md)
