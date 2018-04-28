---
title: zahrnout soubor
description: zahrnout soubor
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 1f41567f7d5dcc9b6536b47ef639ba53abff8ec4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
Následující omezení platí pro zálohování Azure.

| Identifikátor limit | Výchozí omezení |
| --- | --- |
| Počet serverů/počítačů, které lze registrovat k trezoru |50 pro Windows Server nebo klienta nebo SCDPM <br/> 1 000 pro virtuální počítače IaaS |
| Velikost zdroje dat pro data uložená v úložišti Azure trezoru |Maximální počet 54400 GB<sup>1</sup> |
| Počet trezorů záloh, které lze vytvořit v rámci předplatného Azure |500 trezory služeb zotavení podle oblastí |
| Počet pokusů, které lze naplánovat zálohování za den |3 za den pro Windows Server nebo klienta <br/> 2 na každý den pro SCDPM <br/> Jednou denně pro virtuální počítače IaaS |
| Datových disků připojených k virtuální počítač pro zálohování Azure |16 |
| Velikost jednotlivých datový disk připojen k virtuální počítač pro zálohování Azure| 4095 GB <sup>2</sup>|

* <sup>1</sup>54400 GB omezení se nevztahuje na zálohování virtuálních počítačů IaaS.
 

