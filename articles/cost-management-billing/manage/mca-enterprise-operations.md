---
title: Úlohy EA ve smlouvě se zákazníkem Microsoftu – Azure
description: Naučte se provádět úkoly ze smlouvy Enterprise v novém fakturačním účtu.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: e5b1ecfd7b434ceefcff65178b7532575a759d45
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440622"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Provádění úkolů ze smlouvy Enterprise ve fakturačním účtu pro smlouvu se zákazníkem Microsoftu

Pokud vaše organizace podepsala smlouvu se zákazníkem Microsoftu za účelem prodloužení registrace smlouvy Enterprise, vytvoří se pro tuto smlouvu nový fakturační účet. Fakturace v novém účtu je uspořádána jinak než ve smlouvě Enterprise. Tento článek popisuje, jak můžete pomocí nového fakturačního účtu provádět úkoly, které jste prováděli ve smlouvě Enterprise.

## <a name="billing-organization-in-the-new-account"></a>Uspořádání fakturace v novém účtu

Následující diagram popisuje uspořádání fakturace v novém fakturačním účtu.

![Obrázek změn v hierarchii po přechodu](./media/mca-enterprise-operations/mca-post-transition-hierarchy.png)

| Smlouva Enterprise   | Smlouva se zákazníkem Microsoftu    |
|------------------------|--------------------------------------------------------|
| Registrace            | Ke správě fakturace ve vaší organizaci používáte fakturační profil, podobně jako u registrace smlouvy Enterprise. Podnikoví správci se stanou vlastníky fakturačního profilu. Další informace o fakturačních profilech najdete ve [vysvětlení fakturačních profilů](../understand/mca-overview.md#billing-profiles).
| Oddělení            | K uspořádání nákladů používáte oddíl faktury, podobně jako oddělení v registraci smlouvy Enterprise. Z oddělení se stanou oddíly faktury a správci oddělení se stanou vlastníky odpovídajících oddílů faktury. Další informace o oddílech faktury najdete ve [vysvětlení oddílů faktury](../understand/mca-overview.md#invoice-sections). |
| Účet               | Účty, které byly vytvořené u smlouvy Enterprise, se v novém fakturačním účtu nepodporují. Předplatná účtu patří do odpovídajícího oddílu faktury vytvořeného pro jejich oddělení. Vlastníci účtu můžou vytvářet a spravovat předplatná pro svoje oddíly faktury. |

## <a name="changes-for-enterprise-administrators"></a>Změny pro podnikové správce

Následující změny platí pro podnikové správce u smlouvy Enterprise, která byla prodloužena jako smlouva se zákazníkem Microsoftu.

- Pro registraci se vytvoří fakturační profil. Fakturační profil budete používat ke správě fakturace ve vaší organizaci, podobně jako u registrace smlouvy Enterprise. Další informace o fakturačních profilech najdete ve [vysvětlení fakturačních profilů](../understand/mca-overview.md#billing-profiles).
- V registraci smlouvy Enterprise se vytvoří oddíl faktury pro každé oddělení. Oddíly faktury slouží ke správě oddělení. Pokud chcete nastavit další oddělení, můžete vytvořit nové oddíly faktury. Další informace o oddílech faktury najdete ve [vysvětlení oddílů faktury](../understand/mca-overview.md#invoice-sections).
- Roli tvůrce předplatného Azure v oddílech faktury použijete k tomu, abyste ostatním udělili oprávnění k vytvoření předplatného Azure, podobně jako u účtů vytvořených v registraci smlouvy Enterprise.
- Fakturace organizace se spravuje na webu [Azure Portal](https://portal.azure.com), nikoli na webu Azure EA Portal.

U nového fakturačního účtu máte následující role:

**Vlastník fakturačního profilu** – máte přiřazenu roli vlastníka fakturačního profilu u fakturačního profilu, který byl vytvořen při podpisu smlouvy. Role vám umožňuje spravovat fakturaci vaší organizace. Můžete zobrazit poplatky a faktury, uspořádat náklady na faktuře, spravovat způsoby platby a řídit přístup k fakturaci organizace.

**Vlastník oddílu faktur** – máte přiřazenu roli vlastníka oddílu faktury u všech oddílů faktur, které byly vytvořeny pro oddělení v registraci smlouvy Enterprise. Role vám umožňuje řídit, kdo může vytvořit předplatná Azure a nakupovat další produkty.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Zobrazení poplatků a zůstatku kreditů vaší organizace

Ke sledování poplatků a zůstatku kreditů Azure ve vaší organizaci používáte fakturační profil, podobně jako u registrace smlouvy Enterprise.

Další informace o zobrazení zůstatku kreditů pro fakturační profil najdete v tématu o [sledování zůstatku kreditů Azure pro váš fakturační profil](mca-check-azure-credits-balance.md).

Další informace o zobrazení poplatků pro fakturační profil najdete ve [vysvětlení poplatků na faktuře smlouvy se zákazníkem Microsoftu](../understand/review-customer-agreement-bill.md).

### <a name="view-charges-for-a-department"></a>Zobrazení poplatků pro oddělení

Pro každé oddělení, které máte ve smlouvě Enterprise, se vytvoří oddíl faktury. Poplatky pro oddíl faktury můžete zobrazit na webu Azure Portal. Další informace najdete v tématu [Zobrazení transakcí podle oddílů faktury](../understand/review-customer-agreement-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Zobrazení poplatků pro účet

Účty, které byly vytvořeny v registraci smlouvy Enterprise, se v novém fakturačním účtu nepodporují. Předplatná účtu patří do odpovídajícího oddílu faktury vytvořeného pro jejich oddělení. Vlastníci účtu můžou vytvářet a spravovat předplatná pro svoje oddíly faktury.

Pokud chcete zobrazit souhrnné náklady pro předplatná, která patří do účtu, musíte pro jednotlivá předplatná nastavit nákladové středisko. Potom můžete v souboru CSV s informacemi o využití a poplatcích vyfiltrovat předplatná podle nákladového střediska.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Stažení souboru CSV s informacemi o využití a poplatcích, ceníku a daňových dokladů

Pro každý fakturační profil se ve vašem fakturačním účtu vygeneruje měsíční faktura. Pro každou fakturu si můžete stáhnout soubor CSV s informacemi o využití a poplatcích za Azure, ceník a daňový doklad (pokud je k dispozici). Soubor CSV s informacemi o využití a poplatcích za Azure si můžete také stáhnout pro poplatky aktuálního měsíce.

Další informace o stažení souboru CSV s informacemi o využití a poplatcích za Azure najdete v tématu o [stažení informací o využití pro smlouvu se zákazníkem Microsoftu](../understand/download-azure-daily-usage.md).

Další informace o stažení ceníku najdete v tématu o [stažení informací o cenách pro smlouvu se zákazníkem Microsoftu](ea-pricing.md).

Další informace o stažení daňových dokladů najdete v tématu o [zobrazení daňových dokladů pro smlouvu se zákazníkem Microsoftu](../understand/mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Přidání dalšího podnikového správce

Dejte uživatelům přístup k fakturačnímu profilu a umožněte jim zobrazení a správu fakturace pro vaši organizaci. Přístup můžete udělit na stránce **Řízení přístupu (IAM)** na webu Azure Portal.  Další informace o rolích fakturačního profilu najdete v tématu [Role a úlohy fakturačního profilu](understand-mca-roles.md#billing-profile-roles-and-tasks).

Další informace o poskytování přístupu k fakturačnímu profilu najdete v tématu o [správě fakturačních rolí na webu Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Vytvoření nového oddělení

K uspořádání nákladů podle svých potřeb vytvoříte oddíl faktury, podobně jako u oddělení v registraci smlouvy Enterprise. Nový oddíl faktury můžete vytvořit na webu Azure Portal. Další informace najdete v tématu o [uspořádání nákladů na faktuře pomocí oddílů](mca-section-invoice.md).

### <a name="create-a-new-account"></a>Vytvoření nového účtu

Přiřaďte uživatelům roli Tvůrce předplatného Azure v oddílech faktury a dejte jim tak oprávnění k vytvoření předplatného Azure stejně jako u účtů vytvořených v registraci smlouvy Enterprise. Další informace o přiřazování rolí najdete v tématu [Správa fakturačních rolí na webu Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="changes-for-department-administrators"></a>Změny pro správce oddělení

Následující změny platí pro správce oddělení ve smlouvě Enterprise, která byla prodloužena jako smlouva se zákazníkem Microsoftu.

- V registraci smlouvy Enterprise se vytvoří oddíl faktury pro každé oddělení. Oddíly faktury slouží ke správě oddělení. Další informace o oddílech faktury najdete ve [vysvětlení oddílů faktury](../understand/mca-overview.md#invoice-sections).
- Roli tvůrce předplatného Azure v oddílu faktury použijete k tomu, abyste ostatním udělili oprávnění k vytvoření předplatného Azure, jako u účtů vytvořených v registraci smlouvy Enterprise.
- Fakturace organizace se spravuje na webu Azure Portal, nikoli na webu Azure EA Portal.

U nového fakturačního účtu máte následující role:

**Vlastník oddílu faktur** – máte přiřazenu roli vlastníka oddílu faktury u oddílu faktury vytvořeného pro oddělení, která máte ve smlouvě Enterprise. Role vám umožňuje zobrazit a sledovat poplatky a řídit to, kdo můžete vytvářet předplatná Azure a kupovat další produkty pro oddíl faktury.

### <a name="view-charges-for-your-department"></a>Zobrazení poplatků pro oddělení

Poplatky pro oddíl faktury vytvořený pro vaše oddělení můžete zobrazit na [stránce Cost Management a fakturace](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) na webu Azure Portal.

### <a name="add-an-additional-department-administrator"></a>Přidání dalšího správce oddělení

Pro každé oddělení, které máte ve smlouvě Enterprise, se vytvoří oddíl faktury. K udělení přístupu pro zobrazení a správu oddílu faktury můžete použít stránku **Řízení přístupu (IAM)** na webu Azure Portal. Další informace o rolích oddílu faktury najdete v tématu o [rolích a úlohách oddílu faktury](understand-mca-roles.md#invoice-section-roles-and-tasks).

Další informace o poskytování přístupu k oddílu faktury najdete v tématu o [správě fakturačních rolí na webu Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Vytvoření nového účtu v oddělení

Přiřaďte uživatelům roli tvůrce předplatného Azure u oddílu faktury vytvořeného pro vaše oddělení. Další informace o přiřazování rolí najdete v tématu [Správa fakturačních rolí na webu Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="view-charges-for-accounts-in-your-departments"></a>Zobrazení poplatků pro účty ve vašem oddělení

Účty, které byly vytvořeny v registraci smlouvy Enterprise, se v novém fakturačním účtu nepodporují. Předplatná účtu patří do odpovídajícího oddílu faktury vytvořeného pro jejich oddělení. Vlastníci účtu můžou vytvářet a spravovat předplatná pro svoje oddíly faktury.

Pokud chcete zobrazit souhrnné náklady pro předplatná, která patří do účtu ve vašem oddělení, musíte pro jednotlivá předplatná nastavit nákladové středisko. Potom můžete v souboru s informacemi o využití a poplatcích vyfiltrovat předplatná podle nákladového střediska.

## <a name="changes-for-account-owners"></a>Změny pro vlastníky účtů

Vlastníci účtů se smlouvu Enterprise získají oprávnění k vytváření předplatných Azure u nového fakturačního účtu. Vaše existující předplatná Azure patří od oddílu faktury, která je vytvořena pro vaše oddělení. Pokud váš účet nepatří do oddělení, vaše předplatná patří do oddílu faktury s názvem Výchozí oddíl faktury.

Abyste mohli vytvářet další předplatná Azure, máte u nového fakturačního účtu přidělenu následující roli.

**Tvůrce předplatného Azure** – máte přiřazenu roli tvůrce předplatného Azure u oddílu faktury, který je vytvořen pro vaše oddělení ve smlouvě Enterprise. Pokud váš účet nepatří do oddělení, získáte roli tvůrce předplatného Azure u oddílu s názvem Výchozí oddíl faktury. Role vám umožňuje vytvářet předplatná Azure pro oddíl faktury.

### <a name="create-an-azure-subscription"></a>Vytvoření předplatného Azure

Předplatná Azure pro váš oddíl faktury můžete vytvářet na webu Azure Portal. Další informace najdete v tématu o [vytvoření dalšího předplatného Azure pro smlouvu se zákazníkem Microsoftu](create-subscription.md).

### <a name="view-charges-for-your-account"></a>Zobrazení poplatků pro účet

Pokud chcete zobrazit poplatky pro předplatná, která patří do účtu, přejděte na webu Azure Portal na [stránku předplatných](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Na stránce předplatných se zobrazí poplatky pro všechna vaše předplatná.

### <a name="view-charges-for-a-subscription"></a>Zobrazení poplatků pro předplatné

Na poplatky pro předplatné se můžete podívat buď na [stránce předplatných](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), nebo v analýze nákladů Azure. Další informace o analýze nákladů Azure najdete v tématu o [prozkoumání a analýze nákladů pomocí služby Analýza nákladů](../costs/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

- [Vysvětlení fakturačních účtů pro smlouvu se zákazníkem Microsoftu](../understand/mca-overview.md)
- [Vysvětlení faktury](../understand/review-individual-bill.md)
- [Vysvětlení informací na faktuře](../understand/understand-invoice.md)
- [Získání vlastnictví fakturace předplatných Azure od ostatních uživatelů](mca-request-billing-ownership.md)
