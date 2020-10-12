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
ms.openlocfilehash: 9c170607d6300c4921285e85ac78db5a8a18ad9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90078802"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Řešení potíží s tím, že Windows nemůže dokončit konfiguraci systému

Tento článek popisuje kroky k vyřešení problémů, které proces Sysprep znemožňuje spustit virtuální počítač Azure.

## <a name="symptom"></a>Příznak

Když pomocí [diagnostiky spouštění](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje chybu instalace systému Windows, když instalační program systému Windows spouští služby. Tato chyba zobrazí zprávu:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![Obrázek 1 zobrazí chybu instalace systému Windows se zprávou: "Windows nemůže dokončit konfiguraci systému. Chcete-li se pokusit o obnovení konfigurace, restartujte počítač. Instalační program spouští služby.](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Příčina

Tato chyba je způsobena tím, že operační systém (OS) nemůže dokončit [proces Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-process-overview). K této chybě dojde, když se pokusíte o počáteční spuštění zobecněného virtuálního počítače. Pokud k tomuto problému dojde, znovu vytvořte zobecněnou bitovou kopii, protože bitová kopie je ve stavu undeployed a nelze ji obnovit.

## <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, postupujte podle pokynů [pro Azure na stránce Příprava/zachycení image](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed) a Příprava nové generalizované image.
