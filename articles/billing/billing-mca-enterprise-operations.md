---
title: Provádění úloh smlouvy Enterprise ve smlouvě zákazníků společnosti Microsoft – Azure
description: Zjistěte, jak Enterprise Agreement úkoly v nové fakturačnímu účtu.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 9404908b7c486801480474c5a2c9ff7688e1de48
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490716"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Provádění úloh smlouvy Enterprise ve vašem fakturačním účtu Microsoft zákaznické smlouvy

Pokud vaše organizace podepsala zákaznické smlouvy Microsoft obnovit smlouvu Enterprise, se vytvoří nové fakturační účet smlouvy. Fakturace do nového účtu je uspořádaný odlišně od vaší smlouvy Enterprise. Tento článek popisuje, jak můžete nové fakturační účet k provádění úloh, které jste provedli ve vaší smlouvě Enterprise.

## <a name="billing-organization-in-the-new-account"></a>Fakturace v nový účet organizace

Následující diagram popisuje, jakým způsobem je organizována fakturace v nové fakturačnímu účtu.

![Obrázek ea mca-post přechodu – hierarchie](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Smlouva Enterprise   | Smlouva se zákazníkem Microsoftu    |
|------------------------|--------------------------------------------------------|
| Registrace            | Fakturační profil vám spravují fakturaci pro vaši organizaci, podobné smlouva Enterprise Agreement enrollment. Tito správci budou vlastníky fakturační profil. Další informace o fakturaci profilů najdete v tématu [pochopit fakturaci profily](billing-mca-overview.md#billing-profiles).
| Oddělení            | Oddíl faktury slouží k uspořádání náklady, podobně jako oddělením ve vaší registraci smlouvy Enterprise. Oddělení stane části faktury a správci oddělení budou vlastníky příslušných faktury oddílů. Další informace o části faktury, naleznete v tématu [části faktury porozumění](billing-mca-overview.md#invoice-sections). |
| Účet               | Nové fakturační účet nepodporují účty, které byly vytvořeny ve vaší smlouvě Enterprise. Předplatná účtu patří do části příslušných faktury pro jejich oddělení. Vlastníci účtu můžete vytvářet a spravovat odběry pro příslušné oddíly faktury. |

## <a name="changes-for-enterprise-administrators"></a>Změny pro správce rozlehlé sítě

Následující změny budou použity u enterprise administrators v rámci smlouvy Enterprise, která je teď na základě smlouvy Microsoft obnovit.

- Fakturační profil se vytvoří pro vaši registraci. Fakturační profil, který budete používat ke správě fakturace pro vaši organizaci, jako je smlouva Enterprise Agreement enrollment. Další informace o fakturaci profily [pochopit fakturaci profily](billing-mca-overview.md#billing-profiles).
- Oddíl faktury se vytvoří pro každé oddělení v smlouva Enterprise Agreement enrollment. Části faktury budete používat ke správě vašeho oddělení. Můžete vytvořit nové části faktury nastavit další oddělení. Další informace o části faktury, naleznete v tématu [vysvětlení části faktury](billing-mca-overview.md#invoice-sections).
- Role Tvůrce předplatnému Azure budete používat v části faktury ostatním uživatelům oprávnění k vytvoření předplatného Azure, jako jsou účty, které byly vytvořeny v smlouva Enterprise Agreement enrollment.
- Budete používat [webu Azure portal](https://portal.azure.com) pro správu fakturace pro vaši organizaci, ne na portálu Azure EA.

Následující role jsou uvedeny na nové fakturační účet:

**Vlastníka profilu fakturace** – máte přiřazenou roli vlastníka fakturační profil na fakturační profil, který byl vytvořen při podepsání smlouvy. Role umožňuje spravovat fakturaci pro vaši organizaci. Můžete zobrazit náklady a faktury, náklady na faktuře uspořádat, spravovat způsoby platby a řízení přístupu k fakturaci svojí organizace.

**Vlastník části faktury** -mají přiřazenou roli vlastník části faktury na všechny oddíly faktury, které jsou vytvořeny pro oddělením ve vaší registraci smlouvy Enterprise. Role vám umožňuje řídit, kdo může vytvářet předplatná Azure a koupit další produkty.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Zobrazení nákladů a kredity zůstatku pro vaši organizaci

Fakturační profil použijete ke sledování nákladů a zůstatkem kredity Azure pro vaši organizaci podobný smlouva Enterprise Agreement enrollment.

Zjistěte, jak zobrazit zůstatek kreditu pro fakturační profil, najdete v článku [zůstatek kreditu Azure pro váš fakturační profil sledovat](billing-mca-check-azure-credits-balance.md).

Zjistěte, jak zobrazit poplatky za fakturační profil, najdete v článku [pochopit poplatky na faktuře smlouvy zákazníka Microsoftu](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Zobrazit náklady pro oddělení

Oddíl faktury se vytvoří pro každé oddělení, kterou jste používali ve vaší smlouvě Enterprise. Poplatky za oddíl faktury můžete zobrazit na webu Azure Portal. Další informace najdete v tématu [zobrazit transakce podle části faktury](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Zobrazení poplatky za účet

Nové fakturační účet nepodporují účty, které byly vytvořeny ve vaší registraci smlouvy Enterprise. Předplatná účtu patří do části příslušných faktury pro jejich oddělení. Vlastníci účtu můžete vytvářet a spravovat odběry pro příslušné oddíly faktury.

Chcete-li zobrazit souhrnné náklady pro předplatná, u kterých patřil k účtu, je nutné nastavit nákladové středisko pro každé předplatné. Využití a poplatky za Azure file sdíleného svazku clusteru můžete použít k filtrování předplatných ve nákladové středisko.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Stáhnout využití a poplatků sdíleného svazku clusteru, ceníku a daňové doklady

Měsíční faktuře se generuje pro každou fakturační profil ve vašem fakturačním účtu. Pro každé faktury můžete stáhnout Azure využití a poplatků souboru csv, ceníku a daňový doklad (pokud existuje). Můžete také stáhnout Azure využití a poplatků souboru .csv pro aktuální měsíc poplatky.

Zjistěte, jak stáhnout soubor Azure využití a poplatků sdíleného svazku clusteru, najdete v článku [stáhnout využití pro smlouvy Microsoft zákazníka](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement).

Zjistěte, jak stáhnout ceníku, najdete v článku [stáhnout ceny pro smlouvy Microsoft zákazníka](billing-ea-pricing.md#microsoft-customer-agreement-pricing).

Zjistěte, jak stáhnout daňové doklady, najdete v článku [zobrazení daňové doklady pro smlouvy Microsoft zákazníka](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Přidat další podnikového správce

Udělit přístup uživatelům k fakturační profil, který umožní jejich zobrazení a spravují fakturaci pro vaši organizaci. Můžete použít **řízení přístupu (IAM)** stránky na webu Azure Portal přístup.  Další informace o fakturaci rolemi profilů najdete v tématu [fakturace profilu role a úlohy](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Zjistěte, jak poskytnout, přístup k vaší fakturační profil, najdete v článku [Správa fakturace rolí na portálu Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Vytvořit nové oddělení

Vytvořte oddíl faktury uspořádat svoje náklady podle svých potřeb, jako je oddělením ve vaší registraci smlouvy Enterprise. Na webu Azure Portal můžete vytvořit nový oddíl faktury. Další informace najdete v tématu [vytvořit části na vaší faktuře uspořádat svoje náklady](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Vytvoření nového účtu

Přiřadíte uživatele roli Tvůrce předplatného Azure v části faktury jim dát oprávnění k vytvoření předplatného Azure, jako jsou účty, které jsou vytvořeny v smlouva Enterprise Agreement enrollment. Další informace najdete v tématu [dalším osobám udělit oprávnění vytvářet předplatná Azure](billing-mca-create-subscription.md#give-others-permission).

## <a name="changes-for-department-administrators"></a>Změny pro oddělení správce

Správcům oddělení v rámci smlouvy Enterprise, která je teď obnovit na základě smlouvy Microsoft platí následující změny.

- Oddíl faktury se vytvoří pro každé oddělení v smlouva Enterprise Agreement enrollment. Nadbytečné oddíly faktury budete používat ke správě vašeho oddělení. Další informace o části faktury, naleznete v tématu [vysvětlení části faktury](billing-mca-overview.md#invoice-sections).
- Role Tvůrce předplatnému Azure budete používat v části faktury ostatním uživatelům oprávnění k vytvoření předplatného Azure, jako jsou účty, které jsou vytvořeny v smlouva Enterprise Agreement enrollment.
- Na webu Azure portal budete používat ke správě fakturace pro vaši organizaci, ne na portálu Azure EA.

Následující role jsou uvedeny na nové fakturační účet:

**Vlastník části faktury** -mají přiřazenou roli vlastník části faktury v části faktury, který je vytvořen pro oddělení, jste na smlouvu Enterprise. Role umožňuje zobrazení a sledování poplatky a ovládací prvek, kteří můžou vytvářet předplatná Azure a koupit další produkty pro části faktury.

### <a name="view-charges-for-your-department"></a>Zobrazení nákladů pro vaše oddělení

Uvidíte poplatky za části faktury, který je vytvořen pro vaše oddělení, webu Azure Portal [Správa nákladů a fakturace stránky](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).

### <a name="add-an-additional-department-administrator"></a>Přidat další oddělení správce

Oddíl faktury se vytvoří pro každé oddělení, kterou jste používali ve vaší smlouvě Enterprise. Můžete použít **přístup Control(IAM)** stránky na webu Azure Portal poskytnout ostatním přístup k zobrazení a správa části faktury. Další informace o rolích části faktury, najdete v článku [fakturovat část role a úlohy](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Zjistěte, jak poskytnout, přístup k vaší části faktury, najdete v článku [Správa fakturace rolí na portálu Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Vytvořit nový účet vašeho oddělení

Přiřadíte uživatele roli Tvůrce předplatného Azure na fakturu oddílu, který je vytvořen pro vaše oddělení. Další informace najdete v tématu [dalším osobám udělit oprávnění vytvářet předplatná Azure](billing-mca-create-subscription.md#give-others-permission).

### <a name="view-charges-for-accounts-in-your-departments"></a>Zobrazit náklady pro účty ve vaší oblasti

Nové fakturační účet nepodporují účty, které byly vytvořeny ve vaší registraci smlouvy Enterprise. Předplatná účtu patří do části příslušných faktury pro jejich oddělení. Vlastníci účtu můžete vytvářet a spravovat odběry pro příslušné oddíly faktury.

Chcete-li zobrazit souhrnné náklady pro předplatná, u kterých patřil k účtu v oddělení, musíte nastavit nákladové středisko pro každé předplatné. Filtrovat předplatná podle nákladové středisko můžete použít Azure file využití a poplatků.

## <a name="changes-for-account-owners"></a>Změny pro vlastníky účtů

Vlastníci účtu na smlouvu Enterprise získat oprávnění vytvářet předplatná Azure na nové fakturační účet. Vaše existující předplatná Azure, patří do části faktury, který je vytvořen pro vaše oddělení. Pokud váš účet nepatří do oddělení, vaše předplatná patří faktury oddílu s názvem výchozí části faktury.

Vytvářet další předplatná Azure, jsou uvedeny následující roli na nové fakturační účet.

**Předplatné Azure creator** -mají přiřazenou roli Tvůrce předplatného azure v části faktury, který je vytvořen pro vaše oddělení ve smlouvě Enterprise. Pokud váš účet nepatří do oddělení, získáte oddíl s názvem části faktury výchozí role Tvůrce předplatného Azure. Role vám umožňuje vytvářet předplatná Azure pro části faktury.

### <a name="create-an-azure-subscription"></a>Vytvořit předplatné Azure

Vaše faktura část na webu Azure Portal můžete vytvářet předplatná Azure. Další informace najdete v tématu [vytvořte další předplatné Azure pro smlouvy Microsoft zákazníka](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>Zobrazit náklady pro váš účet

Chcete-li zobrazit poplatky za odběr služeb, které patřily do účtu, přejděte na [stránce předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal. Na stránce předplatná se zobrazí poplatky pro vaše předplatné.

### <a name="view-charges-for-a-subscription"></a>Zobrazit náklady pro odběr

Poplatky za předplatné můžete zobrazit buď na [stránce předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nebo Azure analýza nákladů. Další informace o analýzy nákladů na Azure, najdete v části [zkoumat a analyzovat náklady pomocí Cost analysis](../cost-management/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup

- [Vysvětlení fakturační účet pro zákaznické smlouvy Microsoft](billing-mca-overview.md)
- [Vysvětlení faktury](billing-understand-your-bill.md)
- [Vysvětlení informací na faktuře](billing-understand-your-invoice.md)
- [Získat fakturační vlastnictví předplatného Azure od jiných uživatelů](billing-mca-request-billing-ownership.md)
