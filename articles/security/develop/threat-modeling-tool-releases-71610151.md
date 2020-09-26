---
title: Microsoft Threat Modeling Tool verze 10/16/2019 – Azure
description: Dokumentace k verzi nástroje pro modelování hrozeb verze 7.1.61015.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: e45d1347993611130d1bb5036ad4638210c15517
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317935"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool, vydání aktualizace 7.1.61015.1 – 16. 10. 2019

7.1.61015.1 verze Microsoft Threat Modeling Tool (TMT) byla vydána v říjnu 16 2019 a obsahuje následující změny:

- Vylepšení přístupnosti
- Opravy chyb
- Nové vzorníky pro Azure Logic Apps a Azure Průzkumník dat

## <a name="notable-bug-fixes"></a>Významné opravy chyb

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Zlepšení zpětné kompatibility se soubory vytvořenými v Threat Modeling Tool 2016

Bylo opraveno několik chyb souvisejících s otevřením nebo zobrazením souborů modelů hrozeb vytvořených v Threat Modeling Tool 2016.

## <a name="feature-enhancements"></a>Vylepšení funkcí

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Nové vzorníky pro Azure Logic Apps a Azure Průzkumník dat

Nové vzorníky pro Azure Logic Apps a Azure Průzkumník dat byly přidány do vzorníku Azure spolu s jejich přidruženými hrozbami a riziky.

![Azure Logic Apps a vzorníky Azure Průzkumník dat](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Známé problémy

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Chyby související s prioritními hodnotami mimo očekávané rozsahy

Někteří zákazníci oznámili, že při otevírání souborů vytvořených v Threat Modeling Tool 2016 nebo ve vlastních šablonách obdrželi následující chybovou zprávu:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Tento problém je v šetření.

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy
  - [Microsoft Windows 10 – aktualizace pro výročí](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Vyžaduje se verze .NET.
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky
  - K získání aktualizací nástroje a také šablon je vyžadováno připojení k Internetu.

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k Threat Modeling Tool je umístěna na [docs.Microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)a obsahuje informace [o použití nástroje](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
