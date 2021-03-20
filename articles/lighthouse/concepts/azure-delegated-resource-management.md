---
title: Správa delegovaných prostředků Azure
description: Správa delegovaných prostředků Azure je klíčovou součástí Azure Lighthouse, která umožňuje poskytovatelům služeb spravovat delegované prostředky ve velkém měřítku s flexibilitou a přesností.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: d484e61fc4ab3714eb362b26d64d449890065888
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92203853"
---
# <a name="azure-delegated-resource-management"></a>Správa delegovaných prostředků Azure

Správa delegovaných prostředků Azure je jednou z klíčových součástí [Azure Lighthouse](../overview.md). Pomocí delegované správy prostředků Azure můžou poskytovatelé služeb zjednodušit zapojení zákazníků a zprovoznění při správě delegovaných prostředků s flexibilitou a přesností. Zákazníci udržují kontrolu nad tím, kteří poskytovatelé služeb mají přístup ke svým klientům, udržují kontrolu nad tím, kdo bude mít přístup ke svému tenantovi, k jakým prostředkům mají přístup, a jaké akce je možné učinit.

## <a name="what-is-azure-delegated-resource-management"></a>Co je Správa prostředků delegovaná Azure?

Správa delegovaných prostředků v Azure umožňuje logickou projekci prostředků z jednoho tenanta do jiného tenanta. To umožňuje autorizovaným uživatelům v jednom Azure Active Directory (Azure AD) provádět operace správy v různých klientech Azure AD, které patří svým zákazníkům. Poskytovatelé služeb se můžou přihlásit ke svému vlastnímu tenantovi Azure AD a mít autorizaci pro práci v delegovaných zákaznických předplatných a skupinách prostředků. To jim umožní provádět operace správy jménem svých zákazníků, aniž by se museli přihlašovat ke každému klientovi v jednotlivých zákaznících.

> [!TIP]
> Správu delegovaných prostředků Azure je možné použít i [v rámci podniku, který má více tenantů Azure AD vlastní](enterprise.md) pro zjednodušení správy mezi klienty.

Se správou delegovaných prostředků Azure můžou autorizovaní uživatelé pracovat přímo v kontextu zákaznického předplatného, aniž by museli mít účet v tenantovi daného zákazníka nebo je spoluvlastníkem tenanta zákazníka.

[Prostředí pro správu mezi klienty](cross-tenant-management-experience.md) umožňuje efektivněji pracovat s Azure Management Services, jako je Azure Policy, Azure Security Center a další. Aktivita všechny poskytovatele služeb se sleduje v protokolu aktivit, který je uložený v tenantovi zákazníka (a může je zobrazit uživatelé ve správě tenanta). To znamená, že uživatelé jak spravovat, tak i spravovaného tenanta můžou snadno identifikovat uživatele přidruženého k jakýmkoli změnám.

[Nový typ nabídky spravované služby můžete publikovat do Azure Marketplace](../how-to/publish-managed-services-offers.md) , abyste mohli snadno připojit zákazníky do Azure Lighthouse. Alternativně můžete [dokončit proces připojování nasazením Azure Resource Manager šablon](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Jak funguje Správa delegovaných prostředků v Azure

V takovém případě funguje Správa delegovaných prostředků v Azure na nejvyšší úrovni:

1. Nejdřív Identifikujte přístup (role), které vaše skupiny, instanční objekty nebo uživatelé budou potřebovat ke správě prostředků Azure daného zákazníka. Definice přístupu obsahuje ID tenanta pro správu společně s **principalId** identitami ze svého tenanta namapovaného na [předdefinované **rutiny roledefinition** hodnoty](../../role-based-access-control/built-in-roles.md) (přispěvatel, přispěvatel virtuálních počítačů, čtenář atd.).
2. Tento přístup určíte a zařadíte zákazníka do Azure Lighthouse jedním ze dvou způsobů:
   - [Publikování nabídky spravované služby Azure Marketplace](../how-to/publish-managed-services-offers.md) (soukromé nebo veřejné), kterou bude zákazník akceptovat
   - [Nasazení šablony Azure Resource Manager do tenanta zákazníka](../how-to/onboard-customer.md) pro jedno nebo více konkrétních předplatných nebo skupin prostředků

3. Po zprovoznění zákazníka se oprávnění uživatelé můžou přihlásit ke svému tenantovi a provádět úkoly v daném oboru zákazníka na základě přístupu, který jste definovali. Zákazníci si můžou prohlédnout akce poskytovatele služeb a mít možnost v případě potřeby odebrat přístup.

> [!NOTE]
> Můžete spravovat delegované prostředky, které se nacházejí v různých [oblastech](../../availability-zones/az-overview.md#regions). Nicméně delegování předplatných v rámci [národního cloudu](../../active-directory/develop/authentication-national-cloud.md) a veřejného cloudu Azure nebo ve dvou různých národních cloudech se nepodporuje.

## <a name="support-for-azure-delegated-resource-management"></a>Podpora správy delegovaných prostředků Azure

Pokud potřebujete pomoc související se správou delegovaných prostředků Azure, můžete na Azure Portal otevřít žádost o podporu. Jako **typ problému** vyberte **technický**. Vyberte předplatné a pak vyberte **Lighthouse** (pod položkou **monitorování & Správa**).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
- Seznamte [se s nabídkami spravovaných služeb v Azure Marketplace](managed-services-offers.md).
