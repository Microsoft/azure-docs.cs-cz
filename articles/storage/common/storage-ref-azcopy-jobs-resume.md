---
title: AzCopy úlohy obnovit | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu pro obnovení úloh AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 497aa3a77397a8a587badc0911176cd092eb1f23
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503333"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Obnoví existující úlohu s daným ID úlohy.

## <a name="synopsis"></a>Stručný obsah

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|--cíl-řetězec SAS|Cílové SAS cíle pro dané ID úlohy|
|--vyloučit řetězec|Filtr: při obnovování úlohy Vylučte tyto neúspěšné přenosy. Soubory by měly být oddělené znakem '; '.|
|-h,--help|Zobrazit obsah nápovědu pro příkaz Resume.|
|--zahrnout řetězec|Filtr: při obnovování úlohy zahrňte pouze tyto neúspěšné přenosy. Soubory by měly být oddělené znakem '; '.|
|--Source-řetězec SAS |zdrojové SAS zdroje pro dané ID úlohy|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – MB/s float|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|
|--Trusted – řetězec Microsoft-přípony   |Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.|

## <a name="see-also"></a>Viz také

- [azcopy jobs](storage-ref-azcopy-jobs.md)
