---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3d100ec08b7b6d70366e605daf9c67edc6ab3bf3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331064"
---
Účty úložiště úrovně Premium mají následující cíle škálovatelnosti:

| Celkový počet účtů kapacity | Celková šířka pásma pro účet místně redundantního úložiště |
| --- | --- | 
| Kapacita disku: 35 TB <br>Kapacita snímku: 10 TB | Až 50 gigabity za sekundu pro příchozí<sup>1</sup> + odchozí<sup>2</sup> |

<sup>1</sup> všechna data (požadavky) odesílané do účtu úložiště

<sup>2</sup> všechna data (požadavky), které byly přijaty z účtu úložiště

Pokud používáte účty služby premium storage pro nespravované disky a vaše aplikace překračuje cíle škálovatelnosti z jednoho účtu úložiště, může být vhodné k migraci na spravované disky. Pokud už nechcete migrovat do managed disks, sestavení aplikace pro použití více účtů úložiště. Potom data rozdělte mezi tyto účty úložiště. Například pokud chcete připojení disků 51 TB napříč několika virtuálními počítači, rozloženy je dva účty úložiště. 35 TB je limit pro účet úložiště jedné úrovně premium. Ujistěte se, že účet úložiště úrovně premium jeden nikdy zajišťované disky větší než 35 TB.