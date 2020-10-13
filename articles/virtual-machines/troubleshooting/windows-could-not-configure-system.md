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
ms.openlocfilehash: bde091b4a4559c3574ee122d74574d1f9477f3fd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977044"
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

Pokud chcete tento problém vyřešit, postupujte podle pokynů [pro Azure na stránce Příprava/zachycení image](../windows/upload-generalized-managed.md) a Příprava nové generalizované image.