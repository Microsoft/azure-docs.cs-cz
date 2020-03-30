---
title: Microsoft Threat Modeling Tool vydání 10/16/2019 - Azure
description: Dokumentace poznámek k verzi pro nástroj modelování hrozeb
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 452b44653775a1bcb9456b62e1587b5ff2dff874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552045"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool, vydání aktualizace 7.1.61015.1 – 16. 10. 2019

Verze 7.1.61015.1 nástroje Microsoft Threat Modeling Tool (TMT) byla vydána 16.

- Vylepšení přístupnosti
- Opravy chyb
- Nové šablony pro aplikace Azure Logic Apps a Azure Data Explorer

## <a name="notable-bug-fixes"></a>Pozoruhodné opravy chyb

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Vylepšená zpětná kompatibilita se soubory vytvořenými v "Nástroji pro modelování hrozeb 2016"

Bylo opraveno několik chyb souvisejících s otevíráním nebo zobrazením souborů modelu hrozeb vytvořených v "Threat Modeling Tool 2016".

## <a name="feature-enhancements"></a>Vylepšení funkcí

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Nové šablony pro aplikace Azure Logic Apps a Azure Data Explorer

Nové vzorníky pro Azure Logic Apps a Azure Data Explorer byly přidány do azure vzorníku spolu s jejich přidružené hrozby a skutečnosti snižující závažnost rizika.

![Aplikace Logiky Azure a šablony Průzkumníka dat Azure](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Známé problémy

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Chyby související s hodnotami priority mimo očekávané rozsahy

Někteří zákazníci hlásili, že při otevírání souborů vytvořených v nástroji pro modelování hrozeb 2016 nebo vlastních šablonách obdrží následující chybovou zprávu:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Tento problém je předmětem šetření

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
