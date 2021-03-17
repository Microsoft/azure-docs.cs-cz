---
title: zahrnout soubor
description: " – soubor"
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283895"
---
Pokud jste vybrali některý z virtuálních počítačů, které jsou předem nakonfigurované s operačním systémem (a volitelné další služby), už jste si vybrali standardní velikost virtuálního počítače Azure. Doporučený postup je spuštění řešení s předem nakonfigurovaným operačním systémem. Pokud ale instalujete operační systém ručně, musíte v imagi virtuálního počítače nastavit velikost primárního virtuálního pevného disku. Zajistěte, aby velikost disku operačního systému byla v mezích pro Linux nebo Windows.

| Operační systém | Velikost virtuálního pevného disku |
| --- | --- |
| Linux | 30 až 1023 GB |
| Windows | 30 až 250 GB |
|

Základní image Windows nebo SQL Server poskytované jako schválená základní hodnota již splňují tyto požadavky, proto neměňte formát nebo velikost virtuálního pevného disku.

Datové disky můžou být velké až 1 TB. Při rozhodování o velikosti si pamatujte, že zákazníci nemůžou měnit velikost VHD v rámci bitové kopie v době nasazení. Virtuální pevné disky s daty se vytvářejí jako virtuální pevné disky s pevným formátem. Měly by být také rozšiřitelné (zhuštěné/dynamické). Datové disky můžou být zpočátku prázdné nebo obsahovat data.