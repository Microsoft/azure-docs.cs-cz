---
title: Microsoft Threat Modeling Tool verze 02/11/2020 - Azure
description: Dokumentace poznámek k verzi pro nástroj modelování hrozeb
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624836"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Aktualizace nástroje pro modelování hrozeb 7.3.00206.1 - 02/11/2020

Verze 7.3.00206.1 nástroje Microsoft Threat Modeling Tool (TMT) byla vydána 11.

- Opravy chyb

## <a name="notable-bug-fixes"></a>Pozoruhodné opravy chyb

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Chyby související s hodnotami priority mimo očekávané rozsahy

Někteří zákazníci hlásili, že při otevírání souborů vytvořených v nástroji pro modelování hrozeb 2016 nebo vlastních šablonách obdrželi následující chybovou zprávu:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Tento problém byl v této verzi vyřešen.

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy
  - [Aktualizace Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Je vyžadována verze rozhraní .NET.
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky
  - K přijímání aktualizací nástroje i šablon je nutné připojení k Internetu.

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k nástroji pro modelování hrozeb je umístěna na [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)a obsahuje informace [o jeho používání](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [nástroje Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
