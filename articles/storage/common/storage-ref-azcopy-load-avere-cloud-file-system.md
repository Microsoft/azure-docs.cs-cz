---
title: AzCopy načíst CLFS | Microsoft Docs
titleSuffix: Azure Storage
description: Tento článek poskytuje referenční informace pro příkaz AzCopy Load CLFS.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b36ea25180c31fef199aaacb10e46b3caa20f807
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878371"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Převede místní data do kontejneru a uloží ho do formátu CLFS (avere Cloud FileSystem) Microsoftu.

## <a name="synopsis"></a>Stručný obsah

Příkaz Load zkopíruje data do kontejnerů úložiště objektů BLOB v Azure a pak tato data uloží ve formátu avere Cloud FileSystem (CLFS) Microsoftu. Vlastní formát CLFS se používá v mezipaměti HPC Azure a avere vFXT pro produkty Azure.

Pokud chcete tento příkaz využít, nainstalujte potřebné rozšíření pomocí příkazu: PIP3 Install clfsload ~ = 1.0.23. Ujistěte se, že CLFSLoad.py je ve vaší cestě. Další informace o tomto kroku najdete v [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

Tento příkaz je jednoduchou možností pro přesun stávajících dat do cloudového úložiště za účelem použití s konkrétními produkty pro mezipaměť s vysokým výkonem Microsoft pro vysoce výkonné výpočty. 

Vzhledem k tomu, že tyto produkty pro správu dat používají speciální formát cloudového systému souborů, nelze tato data načíst prostřednictvím příkazu nativního kopírování. 

Místo toho je nutné data načíst prostřednictvím samotného produktu v mezipaměti nebo prostřednictvím tohoto příkazu Load, který používá správný speciální formát.
Tento příkaz umožňuje přenášet data bez použití mezipaměti. Například k předběžnému naplnění úložiště nebo k přidání souborů do pracovní sady bez zvýšení zatížení mezipaměti.

Cíl je prázdný Azure Storage kontejner. Po dokončení přenosu se cílový kontejner dá použít s instancí mezipaměti Azure HPC nebo avere vFXT pro cluster Azure.

> [!NOTE] 
> Toto je verze Preview příkazu Load. Ohlaste prosím všechny problémy v úložišti GitHub AzCopy.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Příklady

Načte celý adresář do kontejneru s SAS ve formátu CLFS:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Možnosti

**--kompresní typ** řetězec Určuje typ komprese, který se má použít pro přenosy. Dostupné hodnoty jsou: `DISABLED` , `LZ4` . (výchozí `LZ4` )

**–**    nápovědu k `azcopy load clfs` příkazu.

**--řetězec na úrovni protokolu** definuje podrobnosti protokolu pro soubor protokolu, dostupné úrovně: `DEBUG` , `INFO` , `WARNING` , `ERROR` . (výchozí `INFO` )

**--Max – chyby** UInt32 určují maximální počet selhání přenosu, který se má tolerovat. Pokud dojde k dostatečnému výskytu chyb, zastavte úlohu okamžitě.

**--New-Session**   Zahajte novou úlohu místo pokračování existující služby, jejíž informace o sledování jsou uchovávány na adrese `--state-path` . (výchozí hodnota true)

**--Preserve-hardlinks**    Zachovejte vztahy pevných odkazů.

**--State-cesta** požadovaná cesta k místnímu adresáři pro sledování stavu úlohy. Aby bylo možné úlohu obnovit, musí cesta nasměrovat na existující adresář. Pro novou úlohu musí být prázdný.

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – MB/s float|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|
|--Trusted – řetězec Microsoft-přípony   | Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.|

## <a name="see-also"></a>Viz také

- [AzCopy](storage-ref-azcopy.md)
