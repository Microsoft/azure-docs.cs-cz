---
title: Microsoft Threat Modeling Tool verze 03/22/2020 – Azure
description: Dokumentace k verzi nástroje pro modelování hrozeb verze 7.3.00316.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: e182d40d20529b5743fa9105c68108a8ae55d943
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516896"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool vydání aktualizace 7.3.00316.1-03/22/2020

7.3.00316.1 verze Microsoft Threat Modeling Tool (TMT) byla vydána v březnu 22 2020 a obsahuje následující změny:

- Vylepšení přístupnosti
- Opravy chyb
- Nová funkce DiagramReader

## <a name="notable-bug-fixes"></a>Významné opravy chyb

### <a name="exporting-the-threat-list-to-csv"></a>Export seznamu hrozeb do sdíleného svazku clusteru

Funkce exportu do sdíleného svazku clusteru nekonzistentně vybrala pole ze seznamu hrozeb, která by byla exportována. Všechna pole ze seznamu hrozeb se teď exportují do souboru CSV. 

### <a name="ux-bugs"></a>Chyby uživatelského rozhraní

- Nabídky Help v primárním pracovním postupu (vytvořit/otevřít/analyzovat) a prostředí editoru šablon teď mají konzistentní možnosti nabídky.
- Panel hledání v podokně vzorníků teď má standardní kurzor a přidaly se odpovídající popisky.

## <a name="new-features"></a>Nové funkce

### <a name="diagramreader-feature-has-been-added"></a>Byla přidána funkce DiagramReader

Do hlavní nabídky se přidala nová funkce DiagramReader, zatímco je model otevřený. Tato funkce převede grafickou reprezentaci modelu na textový mluvený komentář. 

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy:
  - [Microsoft Windows 10 – aktualizace pro výročí](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Vyžaduje se verze .NET:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky:
  - Připojení k Internetu pro příjem aktualizací nástroje i šablon

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k Threat Modeling Tool je umístěna na [docs.Microsoft.com](./threat-modeling-tool.md)a obsahuje informace [o použití nástroje](./threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).