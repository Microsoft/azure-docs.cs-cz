---
title: Nástroj pro modelování hrozeb uvolní – modelování nástroj Microsoft Threat – Azure | Dokumentace Microsoftu
description: Dokumentace v poznámkách k verzi nástroje Modelování ohrožení
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: c96b924294286be57de90dae7e6534b5ed9306ea
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57874568"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, vydání aktualizace 7.1.60126.1 – 29. 1. 2019

Verze 7.1.60126.1 nástroj pro modelování hrozeb Microsoftu 2019 29. ledna byla vydána a obsahuje následující změny:

- Minimální požadovaná verze rozhraní .NET byl zvýšen na [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- Minimální požadovaná verze Windows se zvýšil na [Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) kvůli závislosti rozhraní .NET.
- Přepínač funkce ověření modelu se přidala do nabídky Možnosti nástroje.
- Několik odkazů ve vlastnostech hrozby se aktualizovaly.
- Vedlejší uživatelského rozhraní se změní na domovské obrazovce nástroje.
- Nástroj pro modelování hrozeb nyní dědí nastavení TLS hostitelského operačního systému a je podporován v prostředí, které vyžadovaly TLS 1.2 nebo vyšší.

## <a name="feature-changes"></a>Funkce změny

### <a name="model-validation-option"></a>Možnost ověření modelu

Na základě názorů zákazníků, možnost se přidala do nástroje k povolení nebo zakázání ověření modelu. Dříve Pokud šablony používá jeden jednosměrnou datový tok mezi dvěma objekty, možná jste dostali chybovou zprávu oznamující zprávy rámce: ObjectsName musíte splnit aspoň jednu "Jakýkoli". Zakázání ověření modelu nebudou moct tato upozornění ze zobrazení v zobrazení.

Možnost zapínat a vypínat ověření modelu najdete v souboru -> Nastavení -> Možnosti nabídky. Výchozí hodnota pro toto nastavení je zakázána.

![Možnost ověření modelu](./media/azure-security-threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Systémové požadavky

- Podporované operační systémy
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Požadovaná verze rozhraní .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky
  - Připojení k Internetu je potřebný k přijetí aktualizací nástroje, jakož i šablon.

## <a name="known-issues"></a>Známé problémy

### <a name="unsupported-systems"></a>Nepodporovaná systémy

#### <a name="issue"></a>Problém

Uživatelé systémů Windows 10, které nejde nainstalovat .NET 4.7.1 nebo novější, jako je Windows 10 Enterprise LTSB (verze 1507), nelze otevřít nástroj po upgradu. Tyto starší verze Windows už nejsou podporované platformy pro nástroj pro modelování hrozeb a neměli instalovat nejnovější aktualizace.

#### <a name="workaround"></a>Alternativní řešení

Uživatelé Windows 10 Enterprise LTSB (verze 1507), které mají nainstalované nejnovější aktualizace můžete se vrátit k předchozí verzi nástroje modelování hrozeb pomocí dialogu odinstalovat v aplikace a funkce.

## <a name="documentation-and-feedback"></a>Dokumentace ke službě a zpětná vazba

- Dokumentace pro nástroj pro modelování hrozeb se nachází na [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)a obsahuje informace o [o použití nástroje](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Další postup

Stáhněte si nejnovější verzi [nástroj pro modelování hrozeb Microsoftu](https://aka.ms/threatmodelingtool).
