---
title: Azure Delegovaná správa prostředků – maják Azure
description: Spravované služby, které nabízí umožňují poskytovatelům služeb resource management nabízí prodávat zákazníkům na webu Azure Marketplace.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: cec6453cdf339e82420a1b12af6c8e60526fdc03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809964"
---
# <a name="azure-delegated-resource-management"></a>Správa delegovaného prostředků Azure

Správa prostředků Azure delegované je jedním z klíčové komponenty Azure maják. Se správou prostředků Azure delegovaní poskytovatelé služeb může zjednodušit zapojení a připojování zkušeností zákazníků, při správě delegované prostředky v potřebném měřítku s agilitou a přesnost.

## <a name="what-is-azure-delegated-resource-management"></a>Co je Azure Delegovaná správa prostředků?

Správa Azure delegovanému prostředku umožňuje logické projekce prostředků z jednoho tenanta na jiného tenanta. To umožní oprávněným uživatelům ve jednoho tenanta Azure Active Directory (Azure AD), provádění operací správy v jiné službě Azure AD tenantů patřící do svých zákazníků. Poskytovatelé služeb můžou přihlásit k tenanta Azure AD a mají autorizaci pro práci v delegované zákaznických předplatných a skupin prostředků. Díky tomu mohou provádět operace správy zastoupení svých zákazníků, aniž byste museli přihlásit ke každému tenantovi zákazníka.

> [!NOTE]
> Správa prostředků Azure delegované lze také v rámci organizace, která má více tenantů Azure AD své vlastní pro zjednodušení správy napříč tenanty.

Se správou prostředků Azure delegované Autorizovaní uživatelé můžou pracovat přímo v rámci zákaznického předplatného bez nutnosti účet v tenantovi daného zákazníka nebo se spoluvlastník tenanta zákazníka. Mohou také [umožňuje zobrazit a spravovat všechny delegované zákaznických předplatných na novém **zákazníci** stránky](../how-to/view-manage-customers.md) na webu Azure Portal.

[Prostředí pro správu napříč tenanty](cross-tenant-management-experience.md) pomáhá efektivněji při práci s služeb Azure pro správu, jako je Azure Policy, Azure Security Center a další. Všechny aktivity poskytovatele služby jsou sledovány v protokolu aktivit, která je uložena v tenantech zákazníka i poskytovatele služeb. To znamená, že zákazník i služby poskytovatele mohli snadno identifikovat uživatele přidruženého k jakékoli změny.

Když jste připojení zákazníkům Azure nedelegovali správy prostředků, budou mít přístup k nové **poskytovatelé služeb** stránky na webu Azure Portal, kde můžete [potvrďte a spravovat jejich nabídky, poskytovatelů služeb a Delegovaná prostředky](../how-to/view-manage-service-providers.md). Pokud zákazník chce někdy odvolání přístupu pro poskytovatele služeb, dělají to tady kdykoli.

Je možné [publikovat nový typ nabídky služby spravované Azure Marketplace](../how-to/publish-managed-services-offers.md) snadno onboardingu zákazníků Azure delegované správy prostředků. Alternativně můžete [dokončit proces zařazení nasazením šablon Azure Resource Manageru](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Jak Azure Delegovaná správa prostředků funguje

Na vysoké úrovni zde je, jak Azure delegované funkce správy prostředků:

1. Jako poskytovatel služeb určit přístup (role), skupin, objektů služby nebo uživatelů, budete potřebovat ke správě prostředků Azure zákazníka. Definice přístup obsahuje ID tenanta poskytovatele služeb společně s požadovaný přístup pro nabídky, které jsou definovány pomocí **principalId** identit z vašeho tenanta namapované na [integrované  **rutiny roleDefinition** hodnoty](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (Přispěvatel, Přispěvatel virtuálních počítačů, čtenář, atd.).
2. Zadejte tento přístup a zapojení zákazníků Azure delegovat správu prostředků v jedné ze dvou způsobů:
   - [Publikování nabídky Azure Marketplace spravovaných služeb](../how-to/publish-managed-services-offers.md) (privátní nebo veřejné), který bude přijímat zákazníka
   - [Nasazení šablony Azure Resource Manageru do tenanta zákazníka](../how-to/onboard-customer.md) pro konkrétní předplatná nebo skupiny prostředků
3. Jakmile zákazník má byla zařazena do systému, Autorizovaní uživatelé přihlásit k vašemu tenantovi poskytovatele služby a provádět úlohy správy v oboru daného zákazníka, založená na přístupu, který jste definovali.

## <a name="support-for-azure-delegated-resource-management"></a>Podpora pro Azure Delegovaná správa prostředků

Pokud potřebujete pomoc s delegovat správu prostředků Azure, můžete otevřít žádost o podporu na webu Azure Portal. Pro **typ problému**, zvolte **technické**. Vyberte předplatné a pak vyberte **delegovat správu prostředků** (v části **monitorování a správa**).

## <a name="next-steps"></a>Další postup

- Další informace o [napříč tenanty činnosti správy](cross-tenant-management-experience.md).
- Další informace o [spravované nabídky služeb v Azure Marketplace](managed-services-offers.md).