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
ms.openlocfilehash: b345283f87c446ff3b583b0c5dd8a4be222303ae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
Následující omezení platí pro zálohování Azure.

| Identifikátor limit | Výchozí omezení |
| --- | --- |
| Počet serverů/počítačů, které lze registrovat k trezoru |50 pro Windows Server nebo klienta nebo SCDPM <br/> 200 pro virtuální počítače IaaS |
| Velikost zdroje dat pro data uložená v úložišti Azure trezoru |54400 GB max<sup>1</sup> |
| Počet trezorů záloh, které lze vytvořit v rámci předplatného Azure |25 trezorů služeb zotavení podle oblastí |
| Počet pokusů, které lze naplánovat zálohování za den |3 za den pro Windows Server nebo klienta <br/> 2 na každý den pro SCDPM <br/> Jednou denně pro virtuální počítače IaaS |
| Datových disků připojených k virtuální počítač pro zálohování Azure |16 |
| Velikost jednotlivých datový disk připojen k virtuální počítač pro zálohování Azure| 4095 GB <sup>2</sup>|

* <sup>1</sup>54400 GB omezení se nevztahuje na zálohování virtuálních počítačů IaaS.
 

