---
title: Microsoft Threat Modeling Tool verze 07/29/2020 – Azure
description: Dokumentace k verzi nástroje pro modelování hrozeb verze 7.3.00729.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: fc343f292fa32ed2db67dd74aba7a66dbc00d6ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317833"
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

- Dokumentace k Threat Modeling Tool je umístěna na [docs.Microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)a obsahuje informace [o použití nástroje](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
