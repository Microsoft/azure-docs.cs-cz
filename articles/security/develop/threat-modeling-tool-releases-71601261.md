---
title: Verze Threat Modeling Tool – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Dokumentující poznámky k verzi pro nástroj pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: 3f857fd50fcaf926486b6ea480f87bcc4c690f45
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727968"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, vydání aktualizace 7.1.60126.1 – 29. 1. 2019

7\.1.60126.1 verze Microsoft Threat Modeling Tool byla vydaná v lednu 29 2019 a obsahuje následující změny:

- Minimální požadovaná verze rozhraní .NET byla zvýšena na [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- Minimální požadovaná verze Windows se zvýšila na [aktualizaci Windows 10 pro výročí](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) z důvodu závislosti rozhraní .NET.
- Do nabídky možností nástroje se přidala funkce přepínání modelu ověřování.
- Několik odkazů ve vlastnostech hrozeb bylo aktualizováno.
- Vedlejší uživatelské prostředí se mění na domovské obrazovce nástroje.
- Threat Modeling Tool teď dědí nastavení TLS hostitelského operačního systému a podporuje se v prostředích, která vyžadují TLS 1,2 nebo vyšší.

## <a name="feature-changes"></a>Změny funkcí

### <a name="model-validation-option"></a>Možnost ověření modelu

Na základě zpětné vazby od zákazníků byla k nástroji přidána možnost pro povolení nebo zakázání ověřování modelu. Pokud jste dřív použili jeden jednosměrný datový tok mezi dvěma objekty, možná jste v snímku zprávy obdrželi chybovou zprávu s oznámením: Atribut ' Objects ' vyžaduje aspoň jedno ' Any '. Zakázáním ověřování modelu zabráníte zobrazování těchto upozornění v zobrazení.

Možnost přepínání ověřování modelu zapnutá a vypnutá se dá najít v nabídce Možnosti souboru > Nastavení – >. Výchozí hodnota tohoto nastavení je zakázána.

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

- Dokumentace k Threat Modeling Tool je umístěna na [docs.Microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)a obsahuje informace [o použití nástroje](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
