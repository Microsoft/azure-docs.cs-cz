---
title: Nasazení agenta pro připojení dat cef k Azure Sentinel Preview| Dokumenty společnosti Microsoft
description: Zjistěte, jak nasadit agenta pro připojení dat CeF k Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588463"
---
# <a name="step-1-deploy-the-agent"></a>Krok 1: Nasazení agenta


V tomto kroku je třeba vybrat počítač s Linuxem, který bude fungovat jako proxy mezi Azure Sentinel a vaše řešení zabezpečení. Budete muset spustit skript na proxy počítači, který:
- Nainstaluje agenta Log Analytics a nakonfiguruje ho podle potřeby pro naslouchání zprávám Syslog.
- Konfiguruje daemon Syslog poslouchat zprávy Syslog pomocí portu TCP 514 a pak předá pouze zprávy CEF agenta Log Analytics pomocí portu TCP 25226.
- Nastaví agenta Syslog u shromažďovat data a bezpečně odeslat do Azure Sentinelu, kde je analyzována a obohacena.
 
## <a name="deploy-the-agent"></a>Nasazení agenta
 
1. Na portálu Azure Sentinel klikněte na **Datové konektory** a vyberte **Společný formát událostí (CEF)** a potom **otevřít stránku konektoru**. 

1. V části **Instalace a konfigurace agenta Syslogu**vyberte typ počítače, buď Azure, jiný cloud, nebo místní. 
   > [!NOTE]
   > Vzhledem k tomu, že skript v dalším kroku nainstaluje agenta Log Analytics a připojí počítač k pracovnímu prostoru Azure Sentinel, ujistěte se, že tento počítač není připojený k žádnému jinému pracovnímu prostoru.
1. Musíte mít zvýšená oprávnění (sudo) na vašem počítači. Ujistěte se, že máte Python na vašem počítači pomocí následujícího příkazu:`python –version`

1. Spusťte následující skript v počítači proxy.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Během spuštění skriptu zkontrolujte, zda se nezobrazí žádné chybové nebo varovné zprávy.

Pokračujte [krokem 2: Nakonfigurujte bezpečnostní řešení tak, aby přesměrovává zprávy CeF](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit zařízení CEF k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).

