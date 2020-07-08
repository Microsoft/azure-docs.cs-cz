---
title: AzCopy úlohy obnovit | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu pro obnovení úloh AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e143a5e82b817aaba37750a8cce08e3f74f0abc8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220030"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Obnoví existující úlohu s daným ID úlohy.

## <a name="synopsis"></a>Stručný obsah

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)
- [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Možnosti

|Možnost|Description|
|--|--|
|--cíl-řetězec SAS|Cílové SAS cíle pro daný JobId|
|--vyloučit řetězec|Filtr: při obnovování úlohy Vylučte tyto neúspěšné přenosy. Soubory by měly být oddělené znakem '; '.|
|-h,--help|Zobrazit obsah nápovědu pro příkaz Resume.|
|--zahrnout řetězec|Filtr: při obnovování úlohy zahrňte pouze tyto neúspěšné přenosy. Soubory by měly být oddělené znakem '; '.|
|--Source-řetězec SAS |zdrojové SAS zdroje pro daný JobId|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Description|
|---|---|
|--Cap – Mbps|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|
|--Trusted – řetězec Microsoft-přípony   |Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.|

## <a name="see-also"></a>Viz také

- [azcopy jobs](storage-ref-azcopy-jobs.md)
