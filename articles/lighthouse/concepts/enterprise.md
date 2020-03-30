---
title: Azure Lighthouse v podnikových scénářích
description: Možnosti Azure Lighthouse lze použít ke zjednodušení správy mezi tenanty v rámci podniku, který používá více klientů Azure AD.
ms.date: 09/25/2019
ms.topic: conceptual
ms.openlocfilehash: 91089a6fb1a965191489e87027ef508c7ebe2aa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749206"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse v podnikových scénářích

Nejběžnější scénář pro [Azure Lighthouse](../overview.md) je poskytovatel služeb, který spravuje prostředky v tenantech azure služby Azure Directory (Azure AD). Možnosti Azure Lighthouse však lze také použít ke zjednodušení správy mezi tenanty v rámci podniku, který používá více klientů Azure AD.

## <a name="single-vs-multiple-tenants"></a>Jeden vs více klientů

Pro většinu organizací je správa jednodušší s jedním klientem Azure AD. Mít všechny prostředky v rámci jednoho klienta umožňuje centralizaci úloh správy určenými uživateli, skupinami uživatelů nebo instančními objekty v rámci tohoto klienta. Doporučujeme používat jednoho klienta pro vaši organizaci, kdykoli je to možné.

Současně existují situace, které mohou vyžadovat, aby organizace udržovala více klientů Azure AD. V některých případech to může být dočasná situace, jako když došlo k akvizicím a dlouhodobá strategie konsolidace nájemců bude nějakou dobu trvat. Organizace může také potřebovat udržovat více klientů průběžně (z důvodu zcela nezávislých dceřiných společností, geografických nebo právních požadavků a tak dále). V případech, kdy je vyžadována architektura více klientů, lze správu delegovaných prostředků Azure použít k centralizaci a zjednodušení operací správy. Předplatná od více klientů může být na palubě pro [správu delegovaných prostředků Azure](azure-delegated-resource-management.md), což umožňuje určeným uživatelům ve správě tenanta provádět [funkce správy mezi tenanty](cross-tenant-management-experience.md) centralizovaným a škálovatelným způsobem.

## <a name="tenant-management-architecture"></a>Architektura správy tenantů

Při centralizaci operací správy mezi více klienty, budete muset určit, který tenant bude zahrnovat uživatele provádějící operace správy pro ostatní klienty. Jinými slovy budete muset určit, který tenant bude správu tenanta pro ostatní klienty.

Řekněme například, že vaše organizace má jednoho klienta, kterému budeme volat *tenanta A*. Vaše organizace pak získá dva další klienty, *tenanta B* a *tenanta C*, a máte obchodní důvody, které vyžadují jejich údržbu jako samostatné klienty.

Vaše organizace chce používat stejné definice zásad, postupy zálohování a procesy zabezpečení ve všech klientech. Vzhledem k tomu, že již máte uživatele (včetně skupin uživatelů a instančních objektů), kteří jsou zodpovědní za provádění těchto úkolů v rámci klienta A, můžete napalubě všechna předplatná v rámci tenanta B a tenanta C tak, aby tytéž uživatelé v tenanta A můžete provádět tyto Úkoly.

![Uživatelé v tenantovi Správa prostředků v tenantu B a Tenant C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Aspekty zabezpečení a přístupu

Ve většině podnikových scénářů budete chtít delegovat úplné předplatné pro správu delegovaných prostředků Azure, i když můžete také delegovat pouze určité skupiny prostředků v rámci předplatného.

Ať tak či onak, ujistěte se, že [dodržovat zásadu nejnižší oprávnění při definování, kteří uživatelé budou mít přístup k prostředkům](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). To pomáhá zajistit, že uživatelé mají pouze oprávnění potřebná k provádění požadovaných úkolů a snižuje pravděpodobnost neúmyslných chyb.

Azure Lighthouse a Azure delegované správy prostředků poskytují pouze logické vazby mezi správou klienta a spravovaných klientů, spíše než fyzicky přesunutí dat nebo prostředků. Kromě toho přístup vždy vede pouze jedním směrem, od správě klienta ke spravovaným klientům.  Uživatelé a skupiny ve správcovském tenantovi by měli při provádění operací správy s praženými prostředky klienta nadále používat vícefaktorové ověřování.

Podniky s interním nebo externím řízením a svodidla dodržování předpisů [můžou protokoly aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md) používat ke splnění svých požadavků na transparentnost. Když podnikoví klienti vytvořili vztahy klienta pro správu a správu, uživatelé v každém tenantovi mohou sledovat a získat přehled o akcích prováděných uživateli v druhém tenantovi zobrazením protokolované aktivity.

## <a name="onboarding-process-considerations"></a>Aspekty procesu registrace

Předplatná (nebo skupiny prostředků v rámci předplatného) se dají zavést do správy delegovaných prostředků Azure buď nasazením šablon Azure Resource Manageru, nebo prostřednictvím nabídek spravovaných služeb publikovaných na Azure Marketplace, a to buď soukromě, nebo soukromým nebo soukromým i soukromým i morovým vedením. Veřejně.

Vzhledem k tomu, že podnikoví uživatelé budou obvykle moct získat přímý přístup k klientům podniku a není potřeba nabízet nebo propagovat nabídku správy, je obecně rychlejší a jednodušší nasadit přímo pomocí šablon Azure Resource Manageru. I když v pokynech pro [zařazování](../how-to/onboard-customer.md)odkazujeme na poskytovatele služeb a zákazníky , podniky mohou používat stejné procesy.

Pokud chcete, klienti v rámci podniku mohou být na palubě [publikováním nabídky spravovaných služeb na Azure Marketplace](../how-to/publish-managed-services-offers.md). Chcete-li zajistit, aby nabídka byla k dispozici pouze pro příslušné klienty, ujistěte se, že vaše plány jsou označeny jako soukromé. Se soukromým plánem můžete zadat ID předplatného pro každého klienta, kterého chcete přijmout, a nikdo jiný nebude moct získat vaši nabídku.

## <a name="terminology-notes"></a>Terminologické poznámky

Pro správu mezi tenanty v rámci podniku lze odkazy na poskytovatele služeb v dokumentaci Azure Lighthouse pochopit, že se vztahují na spravovacího tenanta v rámci podniku – to znamená, že tenant, který zahrnuje uživatele, kteří budou spravovat prostředky v jiných tenantů prostřednictvím Azure delegované správy prostředků. Podobně odkazy na zákazníky lze chápat použít pro klienty, kteří delegují prostředky, které mají být spravovány prostřednictvím uživatelů v klientském klientovi správy.

Například v příkladu popsaném výše si klient A lze myslet jako tenanta poskytovatele služeb (správu tenanta) a klienta B a klienta C si lze zamyslet jako klienty zákazníka.

V tomto příkladu mohou uživatelé tenanta A s příslušnými oprávněními [zobrazit a spravovat delegované prostředky](../how-to/view-manage-customers.md) na stránce Moji **zákazníci** na portálu Azure. Podobně uživatelé tenanta B a tenanta C s příslušnými oprávněními můžou [zobrazit a spravovat prostředky, které byly delegovány](../how-to/view-manage-service-providers.md) na tenanta A na stránce **Zprostředkovatelé služeb** na portálu Azure.

## <a name="next-steps"></a>Další kroky

- Další informace o [prostředích správy mezi tenanty](cross-tenant-management-experience.md).
- Další informace o [správě delegovaných prostředků Azure](azure-delegated-resource-management.md)