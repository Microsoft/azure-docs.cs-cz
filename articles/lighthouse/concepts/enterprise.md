---
title: Azure Lighthouse v podnikových scénářích
description: Funkce Azure Lighthouse se dají použít ke zjednodušení správy mezi klienty v rámci podniku, který používá víc tenantů Azure AD.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 3f452e6810fa6809b5ba1b83b664f8b38d82a895
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004241"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse v podnikových scénářích

Běžným scénářem pro [Azure Lighthouse](../overview.md) je poskytovatel služeb spravující prostředky v klientech Azure Active Directory (Azure AD) svých zákazníků. Funkce Azure Lighthouse se ale dají použít i ke zjednodušení správy mezi klienty v rámci podniku, který používá víc tenantů Azure AD.

## <a name="single-vs-multiple-tenants"></a>Jeden vs. více tenantů

Pro většinu organizací je Správa snazší díky jednomu tenantovi služby Azure AD. Všechny prostředky v rámci jednoho tenanta umožňují centralizaci úloh správy podle určených uživatelů, skupin uživatelů nebo instančních objektů v rámci tohoto tenanta. Pokud je to možné, doporučujeme používat jednoho tenanta pro vaši organizaci. Některé organizace ale můžou mít několik tenantů Azure AD. V některých případech se může jednat o dočasnou situaci, jako když se povedlo pořízení, ale dlouhodobá strategie konsolidace tenanta ještě není definovaná. Jindy může organizace potřebovat průběžně udržovat několik tenantů, protože jde o zcela nezávislé pobočky, geografické nebo zákonné požadavky nebo jiné okolnosti.

V případech, kdy je potřeba víceklientské architektury, může Azure Lighthouse pomáhat centralizovat a zjednodušit operace správy. Pomocí [delegované správy prostředků Azure](azure-delegated-resource-management.md)můžou uživatelé v jednom správě tenanta provádět [funkce pro správu mezi klienty](cross-tenant-management-experience.md) centralizovaným a škálovatelným způsobem.

## <a name="tenant-management-architecture"></a>Architektura správy tenanta

Pokud chcete používat Azure Lighthouse v podniku, musíte určit, který tenant bude obsahovat uživatele, kteří provádějí operace správy v ostatních klientech. Jinými slovy, budete muset určit, který tenant bude spravovat tenanta pro ostatní klienty.

Řekněme například, že vaše organizace má jednoho tenanta, který budeme volat *jako tenanta a*. Vaše organizace pak získá *tenanta B* a *tenanta C* a máte obchodní důvody, které vyžadují, abyste je zachovali jako samostatné klienty.

Vaše organizace chce použít stejné definice zásad, postupy pro zálohování a procesy zabezpečení ve všech klientech. Vzhledem k tomu, že tenant A již obsahuje uživatele zodpovědné za tyto úkoly, můžete připojit odběry v rámci tenanta B a tenanta C a povolit tak stejným uživatelům v Tenantovi a provádět tyto úlohy.

![Diagram znázorňující uživatele v Tenantovi A spravující prostředky v klientech B a Tenantovi C.](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Požadavky na zabezpečení a přístup

Ve většině podnikových scénářů budete chtít delegovat úplné předplatné služby Azure Lighthouse. Můžete také delegovat pouze konkrétní skupiny prostředků v rámci předplatného.

V obou případech nezapomeňte [při definování, kteří uživatelé budou mít přístup k delegovaným prostředkům, dodržovat princip nejnižší úrovně oprávnění](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Tím je zajištěno, že uživatelé mají pouze oprávnění potřebná k provedení požadovaných úloh a omezují riziko neúmyslných chyb.

Azure Lighthouse poskytuje jenom logické odkazy mezi správou tenanta a spravovaných tenantů místo fyzického přesouvání dat nebo prostředků. Přístup navíc vždy probíhá pouze v jednom směru, od správy tenanta po spravované klienty.  Uživatelé a skupiny v tenantovi pro správu by měli i nadále používat vícefaktorové ověřování při provádění operací správy u prostředků spravovaného tenanta.

Podniky s interním nebo externím řízením a dodržováním předpisů guardrails můžou používat [protokoly aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md) ke splnění svých požadavků na transparentnost. Když podniková klienti navázali vztahy správy a spravovaného tenanta, můžou uživatelé v každém tenantovi zobrazit protokolované aktivity a zobrazit tak akce provedené uživateli ve správě tenanta.

## <a name="onboarding-considerations"></a>Pokyny k připojování

Předplatná (nebo skupiny prostředků v rámci předplatného) se dají připojit do Azure Lighthouse nasazením šablon Azure Resource Manager nebo prostřednictvím nabídek spravovaných služeb publikovaných na Azure Marketplace.

Vzhledem k tomu, že podnikoví uživatelé budou mít obvykle přímý přístup k podnikovým klientům a není potřeba uvádět na trh ani propagovat nabídku správy, je obvykle rychlejší a jednodušší nasadit Azure Resource Manager šablony. I když [pokyny k registraci](../how-to/onboard-customer.md) odkazují na poskytovatele služeb a zákazníky, podniky můžou ke zprovoznění svých tenantů použít stejné procesy.

Pokud dáváte přednost, můžou být klienti v rámci podniku připojeni pomocí [nabídky spravované služby na Azure Marketplace](../how-to/publish-managed-services-offers.md). Chcete-li zajistit, aby nabídka byla k dispozici pouze pro příslušné klienty, ujistěte se, že jsou vaše plány označeny jako soukromé. V případě privátního plánu poskytnete ID předplatného pro každého tenanta, kterého se chystáte připojit, a nikdo jiný nebude moci získat vaši nabídku.

## <a name="terminology-notes"></a>Poznámky k terminologii

Pro správu napříč klienty v rámci podniku se odkazy na poskytovatele služeb v dokumentaci ke službě Azure Lighthouse můžou chápat, že se vztahují na správu tenanta v podniku – to znamená, že tenant zahrnující uživatele, kteří budou spravovat prostředky v jiných klientech prostřednictvím Azure Lighthouse. Podobně je možné porozumět všem odkazům na zákazníky, aby se mohly vztahovat na klienty, kteří mají oprávnění ke správě prostředků prostřednictvím uživatelů v rámci správy tenanta.

Například v předchozím příkladu se dá tenant představit jako tenant poskytovatele služeb (Správa tenanta) a tenanta B a tenant C jako klienti zákazníka.

V tomto příkladu může klient uživatele s příslušnými oprávněními [Zobrazit a spravovat delegované prostředky](../how-to/view-manage-customers.md) na stránce **moji zákazníci** v Azure Portal. Stejně tak mohou uživatelé klienta B a tenanta C s příslušnými oprávněními [Zobrazit a spravovat prostředky, které byly delegovány](../how-to/view-manage-service-providers.md) na tenanta a na stránce Azure Portal **poskytovatelé služeb** .

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
- Další informace o [správě delegovaných prostředků Azure](azure-delegated-resource-management.md)