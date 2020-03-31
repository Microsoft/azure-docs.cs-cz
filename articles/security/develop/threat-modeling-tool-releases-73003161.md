---
title: Microsoft Threat Modeling Tool verze 03/22/2020 - Azure
description: Dokumentace poznámek k verzi pro nástroj modelování hrozeb
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146859"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Aktualizace nástroje pro modelování hrozeb 7.3.00316.1 - 03/22/2020

Verze 7.3.00316.1 nástroje Microsoft Threat Modeling Tool (TMT) byla vydána 22.

- Vylepšení přístupnosti
- Opravy chyb
- Nová funkce aplikace DiagramReader

## <a name="notable-bug-fixes"></a>Pozoruhodné opravy chyb

### <a name="exporting-the-threat-list-to-csv"></a>Export seznamu hrozeb do csv

Funkce exportu do systému CSV nekonzistentně vydávala, která pole ze seznamu hrozeb budou exportována. Nyní budou všechna pole ze seznamu hrozeb exportována do souboru CSV. 

### <a name="ux-bugs"></a>Chyby uživatelského x

- Nabídky nápovědy v primárním pracovním postupu (vytvoření/otevření/analýza) a prostředí editoru šablon mají nyní konzistentní možnosti nabídky.
- Vyhledávací panel v podokně vzorníků má nyní standardní kurzor a byly přidány příslušné popisky.

## <a name="new-features"></a>Nové funkce

### <a name="diagramreader-feature-has-been-added"></a>Byla přidána funkce diagramreaderu.

V hlavní nabídce byla přidána nová funkce aplikace DiagramReader, která je otevřena. Tato funkce převede grafické znázornění modelu na textovou interpretaci. 

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy:
  - [Aktualizace Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Je vyžadována verze rozhraní .NET:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky:
  - Připojení k internetu pro příjem aktualizací nástroje, stejně jako šablony

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k nástroji pro modelování hrozeb je umístěna na [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)a obsahuje informace [o jeho používání](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [nástroje Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
