---
title: Odebrat Sentinel Azure Microsoft Docs
description: Jak odstranit instanci Sentinel Azure
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: f9c400b55b0da47495db4f1ff4ceb86aa39fe2cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885840"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Odebrání služby Azure Sentinel z pracovního prostoru

Pokud už nechcete používat Azure Sentinel, Tento článek vysvětluje, jak ho odebrat z pracovního prostoru.

## <a name="how-to-remove-azure-sentinel"></a>Jak odebrat Sentinel Azure

Pomocí tohoto postupu odeberte Azure Sentinel z pracovního prostoru:

1. Přejít na **Azure Sentinel**, potom na **Nastavení**a vyberte kartu **Odebrat Sentinel Azure**.

1. Před odebráním ověřovacího rozhraní Azure použijte zaškrtávací políčka a sdělte nám prosím, proč jste ji odebrali.

1. **V pracovním prostoru vyberte Odebrat Azure Sentinel**.
    
    ![Odstranění řešení SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Co se stane na pozadí?

Když odeberete řešení, Azure Sentinel bude trvat až 48 hodin, než se dokončí první fáze procesu odstranění.

Po zjištění odpojení se spustí proces zrušení.

**Konfigurace těchto konektorů se odebere:**
-   Office 365

-   AWS

-   Výstrahy zabezpečení služeb Microsoftu: Microsoft Defender pro identitu (*dřív Azure ATP*), Microsoft Cloud App Security včetně Cloud Discovery stínových sestav IT, Azure AD Identity Protection, Microsoft Defenderu pro koncový bod (*dříve Microsoft Defender ATP*), výstrahy v Azure Defenderu od Azure Security Center

-   Analýza hrozeb

-   Běžné protokoly zabezpečení (včetně protokolů založených na CEF, Barracuda a syslog) (Pokud obdržíte výstrahy Azure Defenderu od Azure Security Center, budou se tyto protokoly dál shromažďovat.)

-   Události zabezpečení Windows (Pokud obdržíte výstrahy Azure Defenderu od Azure Security Center, budou se tyto protokoly dál shromažďovat.)

Během prvních 48 hodin už nebudou pravidla pro data a analýzy (včetně konfigurace automatizace v reálném čase) dostupná ani Queryable ve službě Azure Sentinel.

**Po 30 dnech se tyto prostředky odeberou:**

-   Incidenty (včetně metadat šetření)

-   Analytická pravidla

-   Záložky

Vaše playbooky, uložené sešity, uložené lovecké dotazy a poznámkové bloky se neodstraňují. **Některé mohou být z důvodu odebraných dat přerušeny. Můžete je odebrat ručně.**

Po odebrání služby dojde k uplynutí období 30 dnů, během kterého můžete řešení znovu povolit, a data a analytická pravidla budou obnovena, ale odpojené konektory se musí znovu připojit.

> [!NOTE]
> Pokud řešení odeberete, vaše předplatné bude i nadále zaregistrováno u poskytovatele prostředků Azure Sentinel. **Můžete jej odebrat ručně.**




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak odebrat službu Azure Sentinel. Pokud si to rozmyslíte a chcete ji znovu nainstalovat:
- Začínáme s připojováním [Azure Sentinel](quickstart-onboard.md).
