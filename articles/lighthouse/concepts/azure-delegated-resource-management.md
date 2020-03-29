---
title: Správa delegovaných prostředků Azure
description: Nabídky spravovaných služeb umožňují poskytovatelům služeb prodávat nabídky správy prostředků zákazníkům na Azure Marketplace.
ms.date: 01/30/2020
ms.topic: conceptual
ms.openlocfilehash: 15814b1ca3b1b78de521033836e3614d18fd0c71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904315"
---
# <a name="azure-delegated-resource-management"></a>Správa delegovaných prostředků Azure

Správa delegovaných prostředků Azure je jednou z klíčových součástí Azure Lighthouse. Díky správě delegovaných prostředků Azure můžou poskytovatelé služeb zjednodušit zapojení zákazníků a možnosti registrace a zároveň spravovat delegované prostředky ve velkém měřítku s flexibilitou a přesností.

## <a name="what-is-azure-delegated-resource-management"></a>Co je správa delegovaných prostředků Azure?

Správa delegovaných prostředků Azure umožňuje logickou projekci prostředků z jednoho klienta do jiného klienta. To umožňuje oprávněným uživatelům v jednom tenantovi Azure Active Directory (Azure AD) provádět operace správy napříč různými tenanty Azure AD, kteří patří k jejich zákazníkům. Poskytovatelé služeb se můžou přihlásit ke svému vlastnímu tenantovi Azure AD a mít oprávnění k práci v delegovaných zákaznických předplatných a skupinách prostředků. To jim umožňuje provádět operace správy jménem svých zákazníků, aniž by se museli přihlašovat ke každému jednotlivému klientovi zákazníka.

> [!NOTE]
> Azure delegované správy prostředků lze také použít [v rámci podniku, který má více klientů Azure AD vlastní](enterprise.md) zjednodušit správu mezi klienty.

Díky správě delegovaných prostředků Azure můžou oprávnění uživatelé pracovat přímo v kontextu předplatného zákazníka, aniž by měli účet v tenantovi zákazníka nebo byli spoluvlastníkem klienta zákazníka. Můžou taky [zobrazit a spravovat všechna delegovaná zákaznická předplatná na nové stránce Moji **zákazníci** ](../how-to/view-manage-customers.md) na webu Azure Portal.

[Prostředí pro správu napříč tenanty](cross-tenant-management-experience.md) vám pomůže efektivněji pracovat se službami pro správu Azure, jako jsou Azure Policy, Azure Security Center a další. Veškerá aktivita poskytovatele služeb je sledována v protokolu aktivit, který je uložen jak v tenantech poskytovatele služeb, tak v tenantech zákazníka. To znamená, že zákazník i poskytovatel služeb mohou snadno identifikovat uživatele přidruženého k jakýmkoli změnám.

Když založíte zákazníka do Azure delegované správy prostředků, bude mít přístup k nové stránce **poskytovatelů služeb** na webu Azure Portal, kde může [potvrdit a spravovat své nabídky, poskytovatele služeb a delegované prostředky](../how-to/view-manage-service-providers.md). Pokud zákazník někdy chce odvolat přístup pro poskytovatele služeb, může tak učinit kdykoli.

Nový [typ nabídky spravovaných služeb](../how-to/publish-managed-services-offers.md) můžete publikovat na Azure Marketplace, abyste snadno připojili zákazníky ke správě delegovaných prostředků Azure. Případně můžete [dokončit proces registrace nasazením šablon Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Jak funguje správa delegovaných prostředků Azure

Na vysoké úrovni funguje správa delegovaných prostředků Azure:

1. Jako poskytovatel služeb identifikujete přístup (role), který budou vaše skupiny, instanční objekty nebo uživatelé potřebovat ke správě prostředků Azure zákazníka. Definice přístupu obsahuje ID klienta poskytovatele služeb spolu s požadovaným přístupem k nabídce, definované pomocí identit **principalId** z vašeho tenanta mapované na [předdefinované hodnoty **roledefinition** ](../../role-based-access-control/built-in-roles.md) (přispěvatel, přispěvatel virtuálního počítače, čtenář atd.).
2. Tento přístup a připojení zákazníka k azure delegované správy prostředků jedním ze dvou způsobů:
   - [Publikování nabídky spravovaných služeb Azure Marketplace](../how-to/publish-managed-services-offers.md) (soukromé nebo veřejné), kterou zákazník přijme
   - [Nasazení šablony Azure Resource Manageru do klienta zákazníka](../how-to/onboard-customer.md) pro jedno nebo více konkrétních předplatných nebo skupin prostředků
3. Jakmile je zákazník na palubě, oprávnění uživatelé se mohou přihlásit k tenantovi poskytovatele služeb a provádět úlohy správy v daném oboru zákazníka na základě přístupu, který jste definovali.

> [!NOTE]
> Delegování předplatného mezi dvěma klienty v rámci samostatných cloudů není podporováno.

## <a name="support-for-azure-delegated-resource-management"></a>Podpora správy delegovaných prostředků Azure

Pokud potřebujete pomoc související se správou delegovaných prostředků Azure, můžete otevřít žádost o podporu na webu Azure Portal. Pro **typ problému**zvolte **Technické**. Vyberte předplatné a pak vyberte **Maják** (v části **Sledování & managementu).**

## <a name="next-steps"></a>Další kroky

- Další informace o [prostředích správy mezi tenanty](cross-tenant-management-experience.md).
- Přečtěte si o [nabídkách spravovaných služeb na Azure Marketplace](managed-services-offers.md).