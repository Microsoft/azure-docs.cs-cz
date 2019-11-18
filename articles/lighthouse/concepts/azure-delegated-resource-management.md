---
title: Správa delegovaných prostředků Azure
description: Spravované služby umožňují poskytovatelům služeb prodávat nabídky správy prostředků zákazníkům v Azure Marketplace.
ms.date: 07/11/2019
ms.topic: overview
ms.openlocfilehash: e79252d38e5e3363ef1d6a82ec060b896fa247a8
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133387"
---
# <a name="azure-delegated-resource-management"></a>Správa delegovaných prostředků Azure

Správa delegovaných prostředků Azure je jednou z klíčových součástí Azure Lighthouse. Pomocí delegované správy prostředků Azure můžou poskytovatelé služeb zjednodušit zapojení zákazníků a zprovoznění při správě delegovaných prostředků s flexibilitou a přesností.

## <a name="what-is-azure-delegated-resource-management"></a>Co je Správa prostředků delegovaná Azure?

Správa delegovaných prostředků v Azure umožňuje logickou projekci prostředků z jednoho tenanta do jiného tenanta. To umožňuje autorizovaným uživatelům v jednom Azure Active Directory (Azure AD) provádět operace správy v různých klientech Azure AD, které patří svým zákazníkům. Poskytovatelé služeb se můžou přihlásit ke svému vlastnímu tenantovi Azure AD a mít autorizaci pro práci v delegovaných zákaznických předplatných a skupinách prostředků. To jim umožní provádět operace správy jménem svých zákazníků, aniž by se museli přihlašovat ke každému klientovi v jednotlivých zákaznících.

> [!NOTE]
> Správu delegovaných prostředků Azure je možné použít i [v rámci podniku, který má více tenantů Azure AD vlastní](enterprise.md) pro zjednodušení správy mezi klienty.

Se správou delegovaných prostředků Azure můžou autorizovaní uživatelé pracovat přímo v kontextu zákaznického předplatného, aniž by museli mít účet v tenantovi daného zákazníka nebo je spoluvlastníkem tenanta zákazníka. Můžou si taky [Zobrazit a spravovat všechna delegovaná předplatná zákazníka na stránce noví **moji zákazníci** ](../how-to/view-manage-customers.md) v Azure Portal.

[Prostředí pro správu mezi klienty](cross-tenant-management-experience.md) pomáhá efektivněji pracovat s Azure Management Services, jako je Azure Policy, Azure Security Center a další. Aktivita všechny poskytovatele služeb je sledována v protokolu aktivit, který je uložen v klientech služeb i v klientech zákazníka. To znamená, že zákazník i poskytovatel služeb můžou snadno identifikovat uživatele přidruženého k jakýmkoli změnám.

Když zařadíte zákazníka do správy delegovaných prostředků Azure, budou mít přístup k nové stránce **poskytovatelé služeb** v Azure Portal, kde můžou [Potvrdit a spravovat své nabídky, poskytovatele služeb a delegované prostředky](../how-to/view-manage-service-providers.md). Pokud zákazník někdy chce odvolat přístup pro poskytovatele služeb, může to udělat kdykoli.

[Nový typ nabídky spravovaných služeb můžete publikovat do Azure Marketplace](../how-to/publish-managed-services-offers.md) , abyste mohli snadno připojit zákazníky do správy delegovaných prostředků Azure. Alternativně můžete [dokončit proces připojování nasazením Azure Resource Manager šablon](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Jak funguje Správa delegovaných prostředků v Azure

V takovém případě funguje Správa delegovaných prostředků v Azure na nejvyšší úrovni:

1. Jako poskytovatel služeb identifikujete přístup (role), které vaše skupiny, instanční objekty nebo uživatelé budou potřebovat ke správě prostředků Azure daného zákazníka. Definice přístupu obsahuje ID tenanta poskytovatele služeb společně s požadovaným přístupem pro nabídku, která je definovaná pomocí **principalId** identit z vašeho tenanta namapovaného na [předdefinované **rutiny roledefinition** hodnoty](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (přispěvatel, virtuální počítač Přispěvatel, čtenář atd.).
2. Tento přístup určíte a zařadíte zákazníka do správy delegovaných prostředků Azure jedním ze dvou způsobů:
   - [Publikování nabídky spravované služby Azure Marketplace](../how-to/publish-managed-services-offers.md) (soukromé nebo veřejné), kterou bude zákazník akceptovat
   - [Nasazení šablony Azure Resource Manager do tenanta zákazníka](../how-to/onboard-customer.md) pro jedno nebo více konkrétních předplatných nebo skupin prostředků
3. Po zprovoznění zákazníka se oprávnění uživatelé můžou přihlásit k tenantovi poskytovatele služeb a provádět úlohy správy v daném oboru zákazníka na základě přístupu, který jste definovali.

## <a name="support-for-azure-delegated-resource-management"></a>Podpora správy delegovaných prostředků Azure

Pokud potřebujete pomoc související se správou delegovaných prostředků Azure, můžete na Azure Portal otevřít žádost o podporu. Jako **typ problému**vyberte **technický**. Vyberte předplatné a pak vyberte **delegovaná Správa prostředků** (pod položkou **monitorování & Správa**).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
- Seznamte [se s nabídkami spravovaných služeb v Azure Marketplace](managed-services-offers.md).