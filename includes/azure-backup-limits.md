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
ms.openlocfilehash: 63922eb623576379058c9a8a367d6e52249115f2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109089"
---
Následující omezení platí do služby Azure Backup.

| **Limit** | **Výchozí** |
| --- | --- |
| Serverů nebo počítačů, které mohou být registrovány v trezoru | Windows Server/Windows klient/System Center DPM: 50 <br/><br/> Virtuální počítače IaaS: 1000  |
| Velikost zdroje dat v trezoru úložiště |Maximálně 54400 GB. Omezení neplatí pro zálohování virtuálních počítačů IaaS |
| Trezory služby Backup v rámci předplatného Azure |500 trezorů na oblast |
| Naplánovat denní zálohování |Windows Server nebo klienta: 3 denně<br/> System Center DPM: 2 denně <br/> Virtuální počítače IaaS: Jednou denně  |
| Datové disky připojené k virtuálnímu počítači Azure Backup | 32 |
| Jednotlivé datový disk připojený k virtuálnímu počítači Azure Backup| 4095 GB|



