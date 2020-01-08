---
title: Nasazení agenta pro připojení dat CEF k Azure Sentinel Preview | Microsoft Docs
description: Naučte se, jak nasadit agenta pro připojení dat CEF k Sentinel Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: rkarlin
ms.openlocfilehash: 5451382763195172b48e93ef2ea859552b05d154
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615369"
---
# <a name="step-1-deploy-the-agent"></a>Krok 1: nasazení agenta


V tomto kroku musíte vybrat počítač se systémem Linux, který bude fungovat jako proxy server mezi službou Azure Sentinel a řešením zabezpečení. Na proxy počítači budete muset spustit skript, který:
- Nainstaluje agenta Log Analytics a nakonfiguruje ho podle potřeby, aby naslouchal zprávy syslog.
- Nakonfiguruje démon procesu Syslog k naslouchání zpráv syslog pomocí portu TCP 514 a přepošle pouze zprávy CEF agentovi Log Analytics pomocí portu TCP 25226.
- Nastaví agenta syslog ke shromáždění dat a jeho bezpečnému odeslání do služby Azure Sentinel, kde je analyzovaný a obohacený.
 
## <a name="deploy-the-agent"></a>Nasazení agenta
 
1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **Common Event Format (CEF)** a pak **otevřete stránku konektor**. 

1. V části **instalace a konfigurace agenta SYSLOG**vyberte typ počítače, Azure, jiný Cloud nebo místní prostředí. 
   > [!NOTE]
   > Vzhledem k tomu, že skript v dalším kroku nainstaluje agenta Log Analytics a připojí počítač k pracovnímu prostoru Sentinel Azure, ujistěte se, že tento počítač není připojený k žádnému jinému pracovnímu prostoru.
1. Na počítači musíte mít zvýšená oprávnění (sudo). Ujistěte se, že máte v počítači Python, a to pomocí následujícího příkazu: `python –version`

1. Spusťte na proxy počítači následující skript.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Když je skript spuštěný, zkontrolujte, že nezískáváte žádné chybové zprávy ani upozornění.

Pokračujte [krokem 2: konfigurace řešení zabezpečení pro přeposílání zpráv CEF](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit zařízení CEF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).

