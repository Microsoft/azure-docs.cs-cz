---
title: Microsoft Threat Modeling Tool verze 1/29/2019
titleSuffix: Azure
description: Přečtěte si poznámky k verzi pro Microsoft Threat Modeling Tool vydané v 1/29/2019. Poznámky obsahují změny funkcí a známé problémy.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 17147d412bd888cdd3cd270829ad6d6103867b34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87539046"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, vydání aktualizace 7.1.60126.1 – 29. 1. 2019

7.1.60126.1 verze Microsoft Threat Modeling Tool byla vydaná v lednu 29 2019 a obsahuje následující změny:

- Minimální požadovaná verze rozhraní .NET byla zvýšena na [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- Minimální požadovaná verze Windows se zvýšila na [aktualizaci Windows 10 pro výročí](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) z důvodu závislosti rozhraní .NET.
- Do nabídky možností nástroje se přidala funkce přepínání modelu ověřování.
- Několik odkazů ve vlastnostech hrozeb bylo aktualizováno.
- Vedlejší uživatelské prostředí se mění na domovské obrazovce nástroje.
- Threat Modeling Tool teď dědí nastavení TLS hostitelského operačního systému a podporuje se v prostředích, která vyžadují TLS 1,2 nebo vyšší.

## <a name="feature-changes"></a>Změny funkcí

### <a name="model-validation-option"></a>Možnost ověření modelu

Na základě zpětné vazby od zákazníků byla k nástroji přidána možnost pro povolení nebo zakázání ověřování modelu. Pokud se dřív použila jedna jednosměrná datová rychlost mezi dvěma objekty, možná jste v snímku zprávy s oznámením, že hodnota Objects vyžaduje aspoň jedno klíčové slovo any, se zobrazila chybová zpráva. Zakázáním ověřování modelu zabráníte zobrazování těchto upozornění v zobrazení.

Možnost přepínání ověřování modelu zapnutá a vypnutá se dá najít v nabídce Možnosti souboru >nastavení – >. Výchozí hodnota tohoto nastavení je zakázána.

![Možnost ověření modelu](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy
  - [Microsoft Windows 10 – aktualizace pro výročí](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Vyžaduje se verze .NET.
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky
  - K získání aktualizací nástroje a také šablon je vyžadováno připojení k Internetu.

## <a name="known-issues"></a>Známé problémy

### <a name="unsupported-systems"></a>Nepodporované systémy

#### <a name="issue"></a>Problém

Uživatelé systémů Windows 10, které nemůžou nainstalovat .NET 4.7.1 nebo novější, jako je Windows 10 Enterprise LTSB (verze 1507), nebudou moct po upgradu otevřít nástroj. Tyto starší verze Windows už nejsou podporované platformy pro Threat Modeling Tool a neměli byste instalovat nejnovější aktualizaci.

#### <a name="workaround"></a>Alternativní řešení

Uživatelé Windows 10 Enterprise LTSB (verze 1507), které mají nainstalovanou nejnovější aktualizaci, se můžou vrátit k předchozí verzi Threat Modeling Tool prostřednictvím dialogového okna odinstalovat v aplikacích & funkce.

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k Threat Modeling Tool je umístěna na [docs.Microsoft.com](threat-modeling-tool.md)a obsahuje informace [o použití nástroje](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
