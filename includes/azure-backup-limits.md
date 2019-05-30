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
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238616"
---
Následující omezení platí do služby Azure Backup.

| **Limit** | **Výchozí** |
| --- | --- |
| Servery nebo počítače, které mohou být registrovány v trezoru. | Windows Server/Windows klient/System Center Data Protection Manager: 50. <br/><br/> Virtuální počítače IaaS: 1,000.  |
| Velikost zdroje dat v trezoru úložiště. |Maximálně 54,400 GB. Omezení neplatí pro zálohování virtuálních počítačů IaaS. |
| Trezory služby Backup v předplatném Azure. |500 trezorů na oblast. |
| Naplánujte denní zálohování. |Windows Server nebo klienta: Tři denně.<br/> System Center DPM: Dvě denně. <br/> Virtuální počítače IaaS: Jednou za den.  |
| Datové disky připojené k virtuálnímu počítači Azure Backup. | 16 |
| Jednotlivé datový disk připojený k virtuálnímu počítači Azure Backup.| 4 095 GB|
