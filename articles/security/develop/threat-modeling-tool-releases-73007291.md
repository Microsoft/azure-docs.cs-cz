---
title: Microsoft Threat Modeling Tool verze 07/29/2020 – Azure
description: Dokumentace k verzi nástroje pro modelování hrozeb verze 7.3.00729.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 3e6fcd52ad9cb6c127c14bac2f33223fb921519e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516369"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool vydání aktualizace 7.3.00729.1-07/29/2020

7.3.00729.1 verze Microsoft Threat Modeling Tool (TMT) byla vydána v červenci 29 2020 a obsahuje následující změny:

- Opravy chyb
 
## <a name="known-issues"></a>Známé problémy

### <a name="errors-related-to-tmt7application-file-deserialization"></a>Chyby související s TMT7. deserializace souboru aplikace

#### <a name="issue"></a>Problém

Někteří zákazníci nahlásili příjem následující chybové zprávy při stahování Threat Modeling Tool:

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

K této chybě dochází, protože některé prohlížeče nativně nepodporují instalaci technologie ClickOnce. V těchto případech je soubor aplikace ClickOnce stažen na pevný disk uživatele.

#### <a name="workaround"></a>Alternativní řešení

Tato chyba se zobrazí i v případě, že se Threat Modeling Tool spustí dvojitým kliknutím na soubor TMT7. Application. Nicméně po obnechání chyby bude nástroj fungovat normálně. Místo spuštění Threat Modeling Tool dvojitým kliknutím na soubor TMT7. Application by uživatelé měli použít zástupce vytvořené v nabídce Windows během instalace ke spuštění Threat Modeling Tool.

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy
  - [Microsoft Windows 10 – aktualizace pro výročí](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Vyžaduje se verze .NET.
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky
  - K získání aktualizací nástroje a také šablon je vyžadováno připojení k Internetu.

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k Threat Modeling Tool je umístěna na [docs.Microsoft.com](./threat-modeling-tool.md)a obsahuje informace [o použití nástroje](./threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).