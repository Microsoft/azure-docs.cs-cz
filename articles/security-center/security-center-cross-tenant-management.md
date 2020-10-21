---
title: Správa více tenantů v Azure Security Center | Microsoft Docs
description: Naučte se, jak nastavit správu mezi klienty pro správu stav zabezpečení pro více tenantů v Security Center pomocí delegované správy prostředků Azure.
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
ms.openlocfilehash: 68338341d47b8fa15d0bd88ea23e951210105fc4
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340780"
---
# <a name="cross-tenant-management-in-security-center"></a>Správa mezi klienty v Security Center

Správa mezi klienty umožňuje zobrazit a spravovat stav zabezpečení pro více tenantů v Security Center využitím [delegované správy prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md) . Spravujte více tenantů efektivně z jednoho zobrazení, aniž byste se museli přihlašovat ke všem adresářům tenanta.

- Poskytovatelé služeb mohou spravovat stav zabezpečení prostředků pro více zákazníků z jejich vlastního tenanta.

- Bezpečnostní týmy organizací s více klienty můžou zobrazit a spravovat stav zabezpečení z jednoho místa.

## <a name="set-up-cross-tenant-management"></a>Nastavení správy různých tenantů

Pomocí [delegované správy prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md)můžete nastavit správu mezi klienty tím, že delegujete přístup k prostředkům spravovaných klientů do vašeho vlastního tenanta.

> [!NOTE]
> Správa delegovaných prostředků Azure je jednou z klíčových součástí Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Jak funguje Správa mezi klienty v Security Center

Můžete kontrolovat a spravovat odběry napříč více klienty stejným způsobem, jakým spravujete více předplatných v jednom tenantovi.

V horním řádku nabídek klikněte na ikonu filtru a vyberte odběry z adresáře každého tenanta, který chcete zobrazit.

  ![Filtrovat klienty](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Zobrazení a akce jsou v podstatě stejné. Tady je několik příkladů:

- **Správa zásad zabezpečení**: z jednoho zobrazení spravujte stav zabezpečení mnoha prostředků pomocí [zásad](tutorial-security-policy.md), provedete akce s doporučeními zabezpečení a shromáždíte a spravujete data související se zabezpečením.
- **Vylepšení zabezpečeného skóre a stav dodržování předpisů**: Díky viditelnosti mezi klienty můžete zobrazit celkové stav zabezpečení všech vašich klientů a kde a jak nejlépe vylepšit zabezpečení [skóre](secure-score-security-controls.md) a [dodržování předpisů stav](security-center-compliance-dashboard.md) pro každé z nich.
- **Opravit doporučení**: Sledujte a opravte [doporučení](security-center-recommendations.md) pro mnoho prostředků od různých tenantů najednou. Pak můžete okamžitě řešit ohrožení zabezpečení, která představují nejvyšší riziko ve všech klientech.
- **Spravovat výstrahy**: zjišťovat [výstrahy](security-center-alerts-overview.md) v různých klientech. Proveďte akci s prostředky, které nedodržují předpisy pro [nápravné kroky](security-center-managing-and-responding-alerts.md).

- **Spravujte pokročilé funkce ochrany cloudu a další informace**: Spravujte různé služby ochrany před internetovými útoky, jako je například [přístup k virtuálnímu počítači JIT (just-in-time)](security-center-just-in-time.md), [adaptivní posílení sítě](security-center-adaptive-network-hardening.md), [Adaptivní řízení aplikací](security-center-adaptive-application.md)a další.
 
## <a name="next-steps"></a>Další kroky
Tento článek vysvětluje, jak funguje Správa mezi klienty v Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Posílit stav zabezpečení pomocí Azure Security Center](security-center-monitoring.md) – Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Nejčastější](faq-general.md) dotazy k Azure Security Center – Přečtěte si nejčastější dotazy týkající se používání služby.
* [Přečtěte si o Azure Lighthouse v podnikových scénářích](../lighthouse/concepts/enterprise.md) – Zjistěte, jak může Azure Lighthouse zjednodušit správu mezi klienty v rámci podniku, který používá víc TENANTŮ Azure AD.