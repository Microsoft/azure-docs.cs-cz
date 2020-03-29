---
title: Správa napříč tenanty v Azure Security Center | Dokumenty společnosti Microsoft
description: Zjistěte, jak nastavit správu mezi klienty pro správu stavu zabezpečení více klientů v Centru zabezpečení pomocí správy delegovaných prostředků Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 2aeb2ab4cfb4ed5e8652638aaced320cc7119d3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919477"
---
# <a name="cross-tenant-management-in-security-center"></a>Správa napříč tenanty v Centru zabezpečení

Správa mezi tenanty umožňuje zobrazit a spravovat stav zabezpečení více klientů v Centru zabezpečení využitím [správy delegovaných prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md) . Spravujte více klientů efektivně, z jednoho zobrazení, aniž byste se museli přihlašovat do adresáře každého klienta.

- Poskytovatelé služeb mohou spravovat stav zabezpečení prostředků pro více zákazníků z vlastního tenanta.

- Týmy zabezpečení organizací s více klienty mohou zobrazit a spravovat své stav zabezpečení z jednoho umístění.

## <a name="set-up-cross-tenant-management"></a>Nastavení správy mezi klienty

Nastavte správu mezi klienty delegováním přístupu k prostředkům spravovaných klientů na vlastního tenanta pomocí [správy delegovaných prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md).

> [!NOTE]
> Správa delegovaných prostředků Azure je jednou z klíčových součástí Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Jak funguje správa mezi tenanty v Centru zabezpečení

Můžete kontrolovat a spravovat předplatná napříč více tenanty stejným způsobem, jakým spravujete více předplatných v jednom tenantovi.

V horním řádku nabídek klikněte na ikonu filtru a vyberte předplatná z adresáře každého klienta, které chcete zobrazit.

  ![Filtrovat klienty](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Názory a akce jsou v podstatě stejné. Zde je několik příkladů:

- **Správa zásad zabezpečení**: Z jednoho zobrazení můžete spravovat stav zabezpečení mnoha prostředků pomocí [zásad](tutorial-security-policy.md), přijímat akce s doporučeními zabezpečení a shromažďovat a spravovat data související se zabezpečením.
- **Zlepšit zabezpečení skóre a dodržování předpisů držení těla**: Cross-tenant viditelnost umožňuje zobrazit celkový stav zabezpečení všech vašich klientů a kde a jak nejlépe zlepšit [bezpečné skóre](security-center-secure-score.md) a dodržování předpisů [držení těla](security-center-compliance-dashboard.md) pro každého z nich.
- **Náprava doporučení**: Monitorování a náprava [doporučení](security-center-recommendations.md) pro mnoho prostředků z různých klientů najednou. Poté můžete okamžitě řešit slabá místa, která představují nejvyšší riziko pro všechny klienty.
- **Spravovat výstrahy**: Zjišťovat [výstrahy](security-center-alerts-overview.md) v různých tenantů. Provést akci na prostředky, které jsou v rozporu s akce [naváděnými nápravnými kroky](security-center-managing-and-responding-alerts.md).

- **Správa pokročilých funkcí ochrany cloudu a další:** Správa různých služeb ochrany před hrozbami, jako je [například přístup k virtuálním montovnám just-in-time (JIT),](security-center-just-in-time.md) [adaptivní posílení zabezpečení sítě](security-center-adaptive-network-hardening.md), adaptivní řízení [aplikací](security-center-adaptive-application.md)a další.
 
## <a name="next-steps"></a>Další kroky
Tento článek vysvětluje, jak funguje správa mezi tenanty v Centru zabezpečení. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Posilte svůj stav zabezpečení pomocí Azure Security Center](security-center-monitoring.md) – zjistěte, jak monitorovat stav prostředků Azure.
* [Nejčastější dotazy centra zabezpečení Azure –](faq-general.md) nejčastější dotazy týkající se používání služby.
* [Další informace o Azure Lighthouse v podnikových scénářích](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) – Zjistěte, jak Azure Lighthouse může zjednodušit správu mezi klienty v rámci podniku, který používá více klientů Azure AD.