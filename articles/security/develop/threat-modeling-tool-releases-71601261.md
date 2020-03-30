---
title: Vydání nástroje Microsoft Threat Modeling Tool 1/29/2019
titleSuffix: Azure
description: Dokumentace poznámek k verzi pro nástroj modelování hrozeb
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 7d0be8d7243331264c10a407e3d78370ea798928
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269779"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, vydání aktualizace 7.1.60126.1 – 29. 1. 2019

Verze 7.1.60126.1 nástroje Microsoft Threat Modeling Tool byla vydána v lednu 29 2019 a obsahuje následující změny:

- Minimální požadovaná verze rozhraní .NET byla zvýšena na [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- Minimální požadovaná verze systému Windows byla zvýšena na [aktualizaci Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) z důvodu závislosti .NET.
- Do nabídky Možnosti nástroje byla přidána funkce přepínání ověření modelu.
- Bylo aktualizováno několik odkazů ve vlastnostech hrozeb.
- Drobné změny uživatelského uživatelského tlačítka na domovské obrazovce nástroje.
- Nástroj pro modelování hrozeb nyní dědí nastavení TLS hostitelského operačního systému a je podporován v prostředích, která vyžadují TLS 1.2 nebo vyšší.

## <a name="feature-changes"></a>Změny funkcí

### <a name="model-validation-option"></a>Možnost ověření modelu

Na základě zpětné vazby od zákazníků byla do nástroje přidána možnost povolení nebo zakázání ověření modelu. Dříve, pokud vaše šablona používá jeden jednosměrný tok dat mezi dvěma objekty, pravděpodobně jste obdrželi chybovou zprávu v rámci zprávy s uvedením: ObjectsName vyžaduje alespoň jeden "Any". Zakázání maješška modelu ověření zabrání zobrazení těchto upozornění v zobrazení.

Možnost zapínat a vypínat ověřování modelu naleznete v nabídce Možnosti nastavení >souboru >. Výchozí hodnota pro toto nastavení je Zakázáno.

![Možnost ověření modelu](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy
  - [Aktualizace Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Je vyžadována verze rozhraní .NET.
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky
  - K přijímání aktualizací nástroje i šablon je nutné připojení k Internetu.

## <a name="known-issues"></a>Známé problémy

### <a name="unsupported-systems"></a>Nepodporované systémy

#### <a name="issue"></a>Problém

Uživatelé systémů Windows 10, kteří nemohou nainstalovat rozhraní .NET 4.7.1 nebo novější, například Windows 10 Enterprise LTSB (verze 1507), nebudou moci po upgradu nástroj otevřít. Tyto starší verze systému Windows již nejsou podporovány platformy pro nástroj modelování hrozeb a neměly by instalovat nejnovější aktualizace.

#### <a name="workaround"></a>Alternativní řešení

Uživatelé systému Windows 10 Enterprise LTSB (verze 1507), kteří nainstalovali nejnovější aktualizaci, se mohou vrátit k předchozí verzi nástroje modelování hrozeb prostřednictvím dialogového okna odinstalace v aplikacích & funkcích.

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k nástroji pro modelování hrozeb je umístěna na [docs.microsoft.com](threat-modeling-tool.md)a obsahuje informace [o jeho používání](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [nástroje Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
