---
title: zahrnout soubor
description: zahrnout soubor
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9ad1208283527f35e04dede2706fd0e424096dc5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54300657"
---
Následující omezení platí do služby Azure Backup.

| **Limit** | **Výchozí** |
| --- | --- |
| Serverů nebo počítačů, které mohou být registrovány v trezoru | Windows Server/Windows klient/System Center DPM: 50 <br/><br/> Virtuální počítače IaaS: 1000  |
| Velikost zdroje dat v trezoru úložiště |Maximálně 54400 GB. Omezení neplatí pro zálohování virtuálních počítačů IaaS |
| Trezory služby Backup v rámci předplatného Azure |500 trezorů na oblast |
| Naplánovat denní zálohování |Windows Server nebo klienta: 3 denně<br/> System Center DPM: 2 za den <br/> Virtuální počítače IaaS: Jednou za den  |
| Datové disky připojené k virtuálnímu počítači Azure Backup | 16 |
| Jednotlivé datový disk připojený k virtuálnímu počítači Azure Backup| 4095 GB|
