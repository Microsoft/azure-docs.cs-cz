---
title: Azure Lighthouse v podnikových scénářích
description: Funkce Azure Lighthouse se dají použít ke zjednodušení správy mezi klienty v rámci podniku, který používá víc tenantů Azure AD.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 09/25/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: fbd87cc801824729025feb7aefa411ac38048949
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266724"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse v podnikových scénářích

Nejběžnější scénář pro Azure Lighthouse je poskytovatel služeb, který spravuje prostředky ve svých klientech Azure Active Directory (Azure AD). Funkce Azure Lighthouse se ale dají použít i ke zjednodušení správy mezi klienty v rámci podniku, který používá víc tenantů Azure AD.

## <a name="single-vs-multiple-tenants"></a>Jeden vs. více tenantů

Pro většinu organizací je Správa snazší díky jednomu tenantovi služby Azure AD. Všechny prostředky v rámci jednoho tenanta umožňují centralizaci úloh správy podle určených uživatelů, skupin uživatelů nebo instančních objektů v rámci tohoto tenanta. Pokud je to možné, doporučujeme používat jednoho tenanta pro vaši organizaci.

Ve stejnou chvíli existují situace, které mohou vyžadovat, aby organizace udržovala více tenantů Azure AD. V některých případech se může jednat o dočasnou situaci, jako když se povedlo pořízení, a dlouhodobá strategie konsolidace tenanta bude nějakou dobu trvat. Organizace může také potřebovat udržovat více tenantů průběžně (v důsledku zcela nezávislých poboček, zeměpisných nebo zákonných požadavků atd.). V případech, kdy je potřeba víceklientské architektury, se dá pomocí delegované správy prostředků Azure soustředit a zjednodušit operace správy. Pro [správu delegovaných prostředků Azure](azure-delegated-resource-management.md)se dá připojit k předplatným více tenantů, což umožňuje určeným uživatelům ve spravovaném tenantovi provádět [funkce pro správu mezi klienty](cross-tenant-management-experience.md) centralizovaným a škálovatelným způsobem.

## <a name="tenant-management-architecture"></a>Architektura správy tenanta

Při centralizaci operací správy napříč více klienty budete muset určit, který tenant bude obsahovat uživatele provádějící operace správy pro ostatní klienty. Jinými slovy, budete muset určit, který tenant bude spravovat tenanta pro ostatní klienty.

Řekněme například, že vaše organizace má jednoho tenanta, který budeme volat *jako tenanta a*. Vaše organizace pak získá dva další klienty, *tenanta B* a *tenanta C*a máte obchodní důvody, které vyžadují, abyste je zachovali jako samostatné klienty.

Vaše organizace chce použít stejné definice zásad, postupy pro zálohování a procesy zabezpečení ve všech klientech. Vzhledem k tomu, že už máte uživatele (včetně skupin uživatelů a instančních objektů), které jsou zodpovědné za provádění těchto úkolů v rámci tenanta a, můžete začlenit všechna předplatná v rámci tenanta B a tenanta C, aby je tito uživatelé v Tenantovi a mohli provádět. provádění.

![Uživatelé v Tenantovi A spravují prostředky v Tenantovi B a tenant C.](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Požadavky na zabezpečení a přístup

Ve většině podnikových scénářů budete chtít delegovat úplné předplatné pro správu delegovaných prostředků Azure, i když v rámci předplatného můžete taky delegovat jenom konkrétní skupiny prostředků.

V obou případech nezapomeňte [při definování, kteří uživatelé budou mít přístup k prostředkům, dodržovat princip nejnižší úrovně oprávnění](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Tím je zajištěno, že uživatelé mají pouze oprávnění potřebná k provedení požadovaných úloh a omezují riziko neúmyslných chyb.

Azure Lighthouse a Azure delegované správy prostředků poskytují pouze logické odkazy mezi správou tenanta a spravovaných tenantů místo fyzického přesouvání dat nebo prostředků. Přístup navíc vždy probíhá pouze v jednom směru, od správy tenanta po spravované klienty.  Uživatelé a skupiny v tenantovi pro správu by měli i nadále používat vícefaktorové ověřování při provádění operací správy u prostředků spravovaného tenanta.

Podniky s interním nebo externím řízením a dodržováním předpisů guardrails můžou používat [protokoly aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) ke splnění svých požadavků na transparentnost. Když podniková klienti navázali vztahy správy a spravovaného tenanta, můžou uživatelé v každém tenantovi monitorovat a získávat přehled o akcích provedených uživateli v jiném tenantovi zobrazením protokolované aktivity.

## <a name="onboarding-process-considerations"></a>Požadavky na registraci

Předplatná (nebo skupiny prostředků v rámci předplatného) se dají připojit ke správě delegovaných prostředků Azure, a to buď nasazením Azure Resource Manager šablon nebo prostřednictvím spravovaných služeb. nabídky se publikovaly do Azure Marketplace, ať už soukromě, nebo veřejně.

Vzhledem k tomu, že podnikoví uživatelé budou normálně moci získat přímý přístup k podnikovým klientům, a není potřeba uvádět na trh ani propagovat nabídku správy, je všeobecně rychlejší a jednodušší je nasadit přímo pomocí Azure Resource Manager šablon. I když v [pokynech k registraci](../how-to/onboard-customer.md)odkazujeme na poskytovatele služeb a zákazníky, můžou podniky používat stejné procesy.

Pokud dáváte přednost, můžou být klienti v rámci podniku připojeni pomocí [nabídky spravované služby na Azure Marketplace](../how-to/publish-managed-services-offers.md). Chcete-li zajistit, aby nabídka byla k dispozici pouze pro příslušné klienty, ujistěte se, že jsou vaše plány označeny jako soukromé. V případě privátního plánu můžete zadat ID předplatných pro každého tenanta, kterého se chystáte připojit, a nikdo jiný nebude moci získat vaši nabídku.

## <a name="terminology-notes"></a>Poznámky k terminologii

Pro správu mezi klienty v rámci podniku se odkazy na poskytovatele služeb v dokumentaci ke službě Azure Lighthouse můžou chápat, aby se mohly vztahovat na správu tenanta v podniku, tedy na tenanta, který obsahuje uživatele, kteří budou spravovat prostředky. v ostatních klientech prostřednictvím delegované správy prostředků Azure. Podobně se odkazy na zákazníky můžou chápat, aby se mohly vztahovat na klienty, kteří mají oprávnění ke správě prostředků prostřednictvím uživatelů v rámci správy tenanta.

Například v předchozím příkladu se dá tenant představit jako tenant poskytovatele služeb (Správa tenanta) a tenanta B a tenant C jako klienti zákazníka.

V tomto příkladu může klient uživatele s příslušnými oprávněními [Zobrazit a spravovat delegované prostředky](../how-to/view-manage-customers.md) na stránce **moji zákazníci** v Azure Portal. Stejně tak mohou uživatelé klienta B a tenanta C s příslušnými oprávněními [Zobrazit a spravovat prostředky, které byly delegovány](../how-to/view-manage-service-providers.md) na tenanta a na stránce Azure Portal **poskytovatelé služeb** .

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
- Další informace o [správě delegovaných prostředků Azure](azure-delegated-resource-management.md)