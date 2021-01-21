---
title: Řešení potíží s tím, že Windows nemůže dokončit konfiguraci systému
titlesuffix: Azure Virtual Machines
description: Tento článek popisuje kroky k vyřešení problémů, které proces Sysprep znemožňuje spustit virtuální počítač Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 6cb3467fec99bd12810ed058a61de1be7b39cdd0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629585"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Řešení potíží s tím, že Windows nemůže dokončit konfiguraci systému

Tento článek popisuje kroky k vyřešení problémů, které proces Sysprep znemožňuje spustit virtuální počítač Azure.

## <a name="symptom"></a>Příznak

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje chybu instalace systému Windows, když instalační program systému Windows spouští služby. Tato chyba zobrazí zprávu:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![Obrázek 1 zobrazí chybu instalace systému Windows se zprávou: "Windows nemůže dokončit konfiguraci systému. Chcete-li se pokusit o obnovení konfigurace, restartujte počítač. Instalační program spouští služby.](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Příčina

Tato chyba je způsobena tím, že operační systém (OS) nemůže dokončit [proces Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview). K této chybě dojde, když se pokusíte o počáteční spuštění zobecněného virtuálního počítače. Pokud k tomuto problému dojde, znovu vytvořte zobecněnou bitovou kopii, protože bitová kopie je ve stavu undeployed a nelze ji obnovit.

## <a name="solution"></a>Řešení

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

Pokud chcete tento problém vyřešit, postupujte podle pokynů [pro Azure na stránce Příprava/zachycení image](../windows/upload-generalized-managed.md) a Příprava nové generalizované image.