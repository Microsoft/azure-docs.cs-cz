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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755579"
---
Následující omezení platí do služby Azure Backup.

| Identifikátor omezení | Výchozí omezení |
| --- | --- |
| Počet serverů/počítačů, které můžete se zaregistrovat k trezoru |50 na Windows serveru/klienta/SCDPM <br/> 1 000 virtuálních počítačů IaaS |
| Velikost zdroje dat pro data uložená v trezoru Azure storage |Maximální počet 54400 GB<sup>1</sup> |
| Počet trezorů záloh vytvořené v rámci předplatného Azure |500 trezorů služby recovery Services na oblast |
| Počet pokusů, které je možné naplánovat zálohování za den |3 za den pro Windows Server nebo klienta <br/> 2 za den pro SCDPM <br/> Jednou za den pro virtuální počítače IaaS |
| Datové disky připojené k virtuálnímu počítači Azure Backup |16 |
| Velikost jednotlivých datový disk připojený k virtuálnímu počítači Azure Backup| 4095 GB <sup>2</sup>|

* <sup>1</sup>54400 GB limitu se nevztahují na zálohování virtuálních počítačů IaaS.
 

