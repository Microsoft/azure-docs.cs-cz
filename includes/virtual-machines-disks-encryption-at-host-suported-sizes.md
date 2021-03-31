---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86230961"
---
Všechny nejnovější generace velikostí virtuálních počítačů podporují šifrování na hostiteli:

|Typ  |Nepodporuje se  |Podporováno  |
|---------|---------|---------|
|Obecné účely     | Dv3, Dav4, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dasv4        |
|Optimalizované pro výpočty.     |         | Fsv2        |
|Optimalizované pro paměť.     | Ev3, Eav4        | DSv2, Esv3, M, Mv2, Easv4        |
|Optimalizované pro úložiště.     |         | Ls, Lsv2 (disky NVMe nejsou šifrované)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (Preview)        |
|Vysokovýkonné výpočetní prostředí     | H        | NeHBv2, HC,        |
|Předchozí generace     | F, A, D, L, G        | DS, GS, FS, NVv2        |

Upgrade velikosti virtuálního počítače bude mít za následek ověření, jestli nová velikost virtuálního počítače podporuje funkci EncryptionAtHost.
