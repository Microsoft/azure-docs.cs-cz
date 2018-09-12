---
title: zahrnout soubor
description: zahrnout soubor
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 9/10/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 64101ea5a3bbaac4a6b2e349a04d06ea84a87081
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381072"
---
Následující omezení platí do služby Azure Backup.

| Identifikátor omezení | Výchozí omezení |
| --- | --- |
| Počet serverů/počítačů, které můžete se zaregistrovat k trezoru |50 na Windows serveru/klienta/SCDPM <br/> 1 000 virtuálních počítačů IaaS |
| Velikost zdroje dat pro data uložená v trezoru Azure storage |Maximální počet 54400 GB<sup>1</sup> |
| Počet trezorů záloh vytvořené v rámci předplatného Azure |500 trezorů služby recovery Services na oblast |
| Počet pokusů, které je možné naplánovat zálohování za den |3 za den pro Windows Server nebo klienta <br/> 2 za den pro SCDPM <br/> Jednou za den pro virtuální počítače IaaS |
| Datové disky připojené k virtuálnímu počítači Azure Backup |16 |
| Velikost jednotlivých datový disk připojený k virtuálnímu počítači Azure Backup| 4095 GB|

* <sup>1</sup>54400 GB limitu se nevztahují na zálohování virtuálních počítačů IaaS.
 

