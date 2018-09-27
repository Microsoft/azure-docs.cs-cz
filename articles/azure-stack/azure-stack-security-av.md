---
title: Aktualizovat antivirové ochrany Windows Defender v Azure stacku
description: Podrobnosti o tom, jak antivirový je udržovat ve službě Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 09/26/2018
ms.author: patricka
ms.reviewer: fiseraci
ms.openlocfilehash: 59c59705e840d3cdd0d3c5310d84d711cd00c96a
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394005"
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
