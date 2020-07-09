---
title: Správa delegovaných prostředků Azure
description: Správa delegovaných prostředků Azure je klíčovou součástí Azure Lighthouse, která umožňuje poskytovatelům služeb spravovat delegované prostředky ve velkém měřítku s flexibilitou a přesností.
ms.date: 05/28/2020
ms.topic: conceptual
ms.openlocfilehash: bbe3c28cdcd252755b8350eaa5d2e72044981174
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120887"
---
# <a name="azure-delegated-resource-management"></a>Správa delegovaných prostředků Azure

Správa delegovaných prostředků Azure je jednou z klíčových součástí [Azure Lighthouse](../overview.md). Pomocí delegované správy prostředků Azure můžou poskytovatelé služeb zjednodušit zapojení zákazníků a zprovoznění při správě delegovaných prostředků s flexibilitou a přesností.

## <a name="what-is-azure-delegated-resource-management"></a>Co je Správa prostředků delegovaná Azure?

Správa delegovaných prostředků v Azure umožňuje logickou projekci prostředků z jednoho tenanta do jiného tenanta. To umožňuje autorizovaným uživatelům v jednom Azure Active Directory (Azure AD) provádět operace správy v různých klientech Azure AD, které patří svým zákazníkům. Poskytovatelé služeb se můžou přihlásit ke svému vlastnímu tenantovi Azure AD a mít autorizaci pro práci v delegovaných zákaznických předplatných a skupinách prostředků. To jim umožní provádět operace správy jménem svých zákazníků, aniž by se museli přihlašovat ke každému klientovi v jednotlivých zákaznících.

> [!NOTE]
> Správu delegovaných prostředků Azure je možné použít i [v rámci podniku, který má více tenantů Azure AD vlastní](enterprise.md) pro zjednodušení správy mezi klienty.

Se správou delegovaných prostředků Azure můžou autorizovaní uživatelé pracovat přímo v kontextu zákaznického předplatného, aniž by museli mít účet v tenantovi daného zákazníka nebo je spoluvlastníkem tenanta zákazníka. Můžou si taky [Zobrazit a spravovat všechna delegovaná předplatná zákazníka na stránce noví **moji zákazníci** ](../how-to/view-manage-customers.md) v Azure Portal.

[Prostředí pro správu mezi klienty](cross-tenant-management-experience.md) pomáhá efektivněji pracovat s Azure Management Services, jako je Azure Policy, Azure Security Center a další. Aktivita všechny poskytovatele služeb se sleduje v protokolu aktivit, který je uložený v tenantovi zákazníka (a může je zobrazit uživatelé ve správě tenanta). To znamená, že zákazník i poskytovatel služeb můžou snadno identifikovat uživatele přidruženého k jakýmkoli změnám.

Když zařadíte zákazníka do správy delegovaných prostředků Azure, budou mít přístup ke stránce **poskytovatelé služeb** v Azure Portal, kde můžou [Potvrdit a spravovat své nabídky, poskytovatele služeb a delegované prostředky](../how-to/view-manage-service-providers.md). Pokud zákazník někdy chce odvolat přístup pro poskytovatele služeb, může to udělat kdykoli.

[Nový typ nabídky spravované služby můžete publikovat do Azure Marketplace](../how-to/publish-managed-services-offers.md) , abyste mohli snadno připojit zákazníky do správy delegovaných prostředků Azure. Alternativně můžete [dokončit proces připojování nasazením Azure Resource Manager šablon](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Jak funguje Správa delegovaných prostředků v Azure

V takovém případě funguje Správa delegovaných prostředků v Azure na nejvyšší úrovni:

1. Jako poskytovatel služeb identifikujete přístup (role), které vaše skupiny, instanční objekty nebo uživatelé budou potřebovat ke správě prostředků Azure daného zákazníka. Definice přístupu obsahuje ID tenanta poskytovatele služeb společně s požadovaným přístupem pro nabídku, která je definovaná pomocí **principalId** identit z vašeho tenanta namapovaného na [předdefinované **rutiny roledefinition** hodnoty](../../role-based-access-control/built-in-roles.md) (přispěvatel, přispěvatel virtuálních počítačů, čtenář atd.).
2. Tento přístup určíte a zařadíte zákazníka do správy delegovaných prostředků Azure jedním ze dvou způsobů:
   - [Publikování nabídky spravované služby Azure Marketplace](../how-to/publish-managed-services-offers.md) (soukromé nebo veřejné), kterou bude zákazník akceptovat
   - [Nasazení šablony Azure Resource Manager do tenanta zákazníka](../how-to/onboard-customer.md) pro jedno nebo více konkrétních předplatných nebo skupin prostředků
3. Po zprovoznění zákazníka se oprávnění uživatelé můžou přihlásit k tenantovi poskytovatele služeb a provádět úlohy správy v daném oboru zákazníka na základě přístupu, který jste definovali.

> [!NOTE]
> Můžete spravovat delegované prostředky, které se nacházejí v různých [oblastech](../../availability-zones/az-overview.md#regions). Nicméně delegování předplatných v rámci [národního cloudu](../../active-directory/develop/authentication-national-cloud.md) a veřejného cloudu Azure nebo ve dvou různých národních cloudech se nepodporuje.

## <a name="support-for-azure-delegated-resource-management"></a>Podpora správy delegovaných prostředků Azure

Pokud potřebujete pomoc související se správou delegovaných prostředků Azure, můžete na Azure Portal otevřít žádost o podporu. Jako **typ problému**vyberte **technický**. Vyberte předplatné a pak vyberte **Lighthouse** (pod položkou **monitorování & Správa**).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
- Seznamte [se s nabídkami spravovaných služeb v Azure Marketplace](managed-services-offers.md).