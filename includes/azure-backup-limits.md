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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "67175407"
---
Následující omezení platí do služby Azure Backup.

| **Limit** | **Výchozí** |
| --- | --- |
| Servery nebo počítače, které je možné zaregistrovat v trezoru. | Windows Server/klient Windows/System Center Data Protection Manager: 50. <br/><br/> Virtuální počítače IaaS: 1 000.  |
| Velikost zdroje dat v úložišti trezoru. |54 400 – maximum GB Limit se nedá použít pro zálohování virtuálního počítače IaaS. |
| Trezory služby Backup v předplatném Azure. |500 trezorů na oblast |
| Naplánujte denní zálohy. |Windows Server/klient: Tři dny.<br/> System Center DPM: Dva dny. <br/> Virtuální počítače IaaS: Jednou denně.  |
| Datové disky připojené k virtuálnímu počítači Azure pro zálohování. | 16 |
| Jednotlivý datový disk připojený k virtuálnímu počítači Azure, který se má zálohovat| 4 095 GB|
