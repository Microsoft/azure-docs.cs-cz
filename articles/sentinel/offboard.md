---
title: Odpojení Azure Sentinel | Microsoft Docs
description: Jak odstranit instanci Sentinel Azure
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: rkarlin
ms.openlocfilehash: 4c0c415235fd290bc47ac402a6b81a1afa7af903
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74777430"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Odebrání služby Azure Sentinel z pracovního prostoru

Pokud už nechcete používat službu Azure Sentinel, Tento článek vysvětluje, jak ho odebrat z pracovního prostoru.

## <a name="how-to-delete-azure-sentinel"></a>Jak odstranit Azure Sentinel

Když na pozadí nainstalujete službu Azure Sentinel, řešení **SecurityInsights** se nainstaluje na vybraný pracovní prostor. Takže první věc, kterou potřebujete udělat, je odebrat řešení **SecurityInsights** .

1.  Klikněte na **Azure Sentinel**, potom na **Konfigurace**, potom na **Nastavení pracovního prostoru**a pak na **řešení**.

2.  Vyberte `SecurityInsights` a klikněte na něj.

    ![Najít řešení SecurityInsights](media/offboard/find-solution.png)

3.  V horní části stránky vyberte **Odstranit**.

    > [!IMPORTANT]
    > Pokud pracovní prostor odeberete, může to mít vliv na jiná řešení a zdroje dat, které používají tento pracovní prostor, včetně Azure Monitor. Pokud chcete zjistit, která řešení používají tento pracovní prostor, přečtěte si článek [seznam nainstalovaných řešení monitorování](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions). Pokud chcete zjistit, která řešení se ingestují do pracovního prostoru, přečtěte si téma principy ingestované [datové svazky](../azure-monitor/platform/manage-cost-storage.md#understanding-ingested-data-volume).

    ![Odstranění řešení SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Co se stane na pozadí?

Při odstranění řešení vyžaduje Azure Sentinel až 48 hodin, než se dokončí první fáze procesu odstranění.

Po zjištění odpojení se spustí proces zrušení.

**Konfigurace těchto konektorů se odstraní:**
-   Office 365

-   AWS

-   Výstrahy zabezpečení služeb Microsoftu (ATP Azure, Microsoft Cloud App Security včetně Cloud Discovery stínových sestav IT, Azure AD Identity Protection, ATP v programu Microsoft Defender, Azure Security Center)

-   Analýza hrozeb

-   Běžné protokoly zabezpečení (včetně protokolů založených na CEF, Barracuda a syslog) (Pokud máte Azure Security Center, budou tyto protokoly i nadále shromažďovány.)

-   Události zabezpečení systému Windows (Pokud máte Azure Security Center budou tyto protokoly nadále shromažďovány.)

Během prvních 48 hodin už nebudou pravidla dat a upozornění (včetně konfigurace automatizace v reálném čase) dostupná ani Queryable ve službě Azure Sentinel.

**Po 30 dnech se tyto prostředky odstraní:**

-   Incidenty (včetně metadat šetření)

-   Pravidla upozornění

-   záložky

Vaše playbooky, uložené sešity, uložené lovecké dotazy a poznámkové bloky se neodstraňují. **Některé mohou být z důvodu odebraných dat přerušeny. Můžete je odebrat ručně.**

Po odebrání služby dojde k uplynutí období 30 dnů, během kterého můžete řešení znovu povolit, a data a pravidla výstrah budou obnovena, ale odpojené odpojené konektory se musí znovu připojit.

> [!NOTE]
> Pokud řešení odeberete, vaše předplatné bude i nadále zaregistrováno u poskytovatele prostředků Azure Sentinel. **Můžete jej odebrat ručně.**




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak odebrat službu Azure Sentinel. Pokud si to rozmyslíte a chcete ji znovu nainstalovat:
- Začínáme s připojováním [Azure Sentinel](quickstart-onboard.md).

