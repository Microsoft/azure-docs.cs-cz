---
title: zahrnout soubor
description: zahrnout soubor
services: backup
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: ca6c57b139b195b94dc7171336cfcc34d18b1872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828842"
---
Následující omezení platí do služby Azure Backup.

| **Omezení** | **Výchozí** |
| --- | --- |
| Servery nebo počítače, které je možné zaregistrovat v trezoru. | Windows Server/klient Windows/System Center Data Protection Manager: 50. <br/><br/> Virtuální počítače IaaS: 1 000.  |
| Velikost zdroje dat v úložišti trezoru. |54 400 – maximum GB Limit se nedá použít pro zálohování virtuálního počítače IaaS. |
| Trezory služby Backup v předplatném Azure. |500 trezorů na oblast |
| Naplánujte denní zálohy. |Windows Server/klient: tři dny.<br/> System Center DPM: dva dny. <br/> Virtuální počítače IaaS: jednou denně.  |
| Datové disky připojené k virtuálnímu počítači Azure pro zálohování. | 16 |
| Jednotlivý datový disk připojený k virtuálnímu počítači Azure, který se má zálohovat| 32 TB|
