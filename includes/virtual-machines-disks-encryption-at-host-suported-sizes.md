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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86230961"
---
Všechny nejnovější generace velikostí virtuálních počítačů podporují šifrování na hostiteli:

|Typ  |Nepodporuje se  |Podporováno  |
|---------|---------|---------|
|Obecné účely     | Dv3, Dav4, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dasv4        |
|Optimalizované z hlediska výpočetních služeb     |         | Fsv2        |
|Optimalizované z hlediska paměti     | Ev3, Eav4        | DSv2, Esv3, M, Mv2, Easv4        |
|Optimalizované z hlediska úložiště     |         | Ls, Lsv2 (disky NVMe nejsou šifrované)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (Preview)        |
|Vysokovýkonné výpočetní prostředí     | H        | NeHBv2, HC,        |
|Předchozí generace     | F, A, D, L, G        | DS, GS, FS, NVv2        |

Upgrade velikosti virtuálního počítače bude mít za následek ověření, jestli nová velikost virtuálního počítače podporuje funkci EncryptionAtHost.
