---
title: Aktualizovat antivirové ochrany Windows Defender v Azure stacku
description: Podrobnosti o tom, jak antivirový je udržovat ve službě Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 7899f235853991160e4d8b2c2c832d9a77171e83
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242191"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Aktualizovat antivirové ochrany Windows Defender v Azure stacku

[Antivirová ochrana v programu Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) je antimalwarové řešení, která poskytuje zabezpečení a antivirové ochrany. Všechny komponenty infrastruktury Azure stacku (hostitelů Hyper-V a virtuálních počítačů) je chráněný pomocí antivirové ochrany v programu Windows Defender. Pro aktuální ochranu vyžadovat pravidelné aktualizace definice antivirové ochrany v programu Windows Defender, jádro a platformy. Jak se aktualizace závisí na vaší konfiguraci.

## <a name="connected-scenario"></a>Připojené scénář

Antimalwarových definic a stroje a aktualizací, službě Azure Stack [poskytovatele prostředků aktualizace](azure-stack-updates.md#the-update-resource-provider) stáhne antimalwarových definic a aktualizací stroje více než jednou za den. Jednotlivé komponenty infrastruktury Azure stacku získá aktualizaci ze zprostředkovatele prostředků aktualizace a aktualizace použije automaticky.

Antimalwarová platforma aktualizací, použije [měsíční aktualizace služby Azure Stack](azure-stack-apply-updates.md). Měsíční aktualizace služby Azure Stack zahrnuje aktualizace platformy antivirové ochrany v programu Windows Defender pro daný měsíc.

## <a name="disconnected-scenario"></a>Odpojené scénář

 Použít [měsíční aktualizace služby Azure Stack](azure-stack-apply-updates.md). Měsíční aktualizace služby Azure Stack zahrnuje definice antivirové ochrany v programu Windows Defender, jádro a aktualizace platformy pro daný měsíc.

## <a name="next-steps"></a>Další postup

[Další informace o zabezpečení Azure stacku](azure-stack-security-foundations.md)
