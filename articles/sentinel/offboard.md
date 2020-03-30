---
title: Odebrání Azure Sentinelu| Dokumenty společnosti Microsoft
description: Jak odstranit instanci Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581680"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Odebrání Azure Sentinelu z pracovního prostoru

Pokud už nechcete používat Azure Sentinel, tento článek vysvětluje, jak odebrat z pracovního prostoru.

## <a name="how-to-remove-azure-sentinel"></a>Jak odebrat Azure Sentinel

Podle tohoto postupu odeberte Azure Sentinel z pracovního prostoru:

1. Přejděte na **Azure Sentinel**, následovaný **nastavením**a vyberte kartu **Odebrat Azure Sentinel**.

1. Než Azure Sentinel odeberete, dejte nám prosím vědět, proč ho odstraňujete.

1. Vyberte **Odebrat Azure Sentinel z pracovního prostoru**.
    
    ![Odstranění řešení SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Co se děje v zákulisí?

Když odeberete řešení, Azure Sentinel trvá až 48 hodin k dokončení první fáze procesu odstranění.

Po identifikaci odpojení začne proces odřapzování.

**Konfigurace těchto konektorů je odebrána:**
-   Office 365

-   AWS

-   Výstrahy zabezpečení služeb Microsoftu (Azure ATP, Microsoft Cloud App Security včetně cloudových zjišťování stínových it reportingů, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Analýza hrozeb

-   Běžné protokoly zabezpečení (včetně protokolů založených na cef, Barracuda a Syslog) (Pokud máte Azure Security Center, tyto protokoly budou nadále shromažďovat.)

-   Události zabezpečení systému Windows (Pokud máte Azure Security Center, tyto protokoly budou nadále shromažďovat.)

Během prvních 48 hodin data a analytická pravidla (včetně konfigurace automatizace v reálném čase) již nebude přístupná nebo dotazovatelná v Azure Sentinelu.

**Po 30 dnech jsou tyto zdroje odebrány:**

-   Incidenty (včetně metadat vyšetřování)

-   Analytická pravidla

-   Záložky

Vaše playbooky, uložené sešity, uložené lovecké dotazy a poznámkové bloky se neodeberou. **Některé mohou dojít k přerušení kvůli odebraná data. Můžete je odstranit ručně.**

Po odebrání služby je období odkladu 30 dnů, během kterého můžete znovu povolit řešení a vaše data a analytická pravidla budou obnovena, ale nakonfigurované konektory, které byly odpojeny, musí být znovu připojeny.

> [!NOTE]
> Pokud odeberete řešení, vaše předplatné bude nadále registrované u poskytovatele prostředků Azure Sentinelu. **Můžete jej odstranit ručně.**




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak odebrat službu Azure Sentinel. Pokud si to rozmyslíte a budete ho chtít nainstalovat znovu:
- Začínáme [při nástupu do Služby Azure Sentinel](quickstart-onboard.md).
