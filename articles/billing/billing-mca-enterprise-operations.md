---
title: Dokončení úkolů smlouvy Enterprise ve smlouvě zákazníků společnosti Microsoft – Azure | Dokumentace Microsoftu
description: Zjistěte, jak Enterprise Agreement úkoly v nové fakturačnímu účtu.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2018
ms.author: banders
ms.openlocfilehash: 5980b8b701695d002d58b6b5239e6fa04c02fb2f
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248819"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Provádění úloh smlouvy Enterprise ve vašem fakturačním účtu Microsoft zákaznické smlouvy

Pokud vaše organizace podepsala zákaznické smlouvy Microsoft obnovit smlouvu Enterprise, je musíte nastavit nové fakturační účet vytvořili smlouvy. Vaše nové fakturační účet poskytuje že rozšířené možnosti správy fakturace a nákladů prostřednictvím nové prostředí pro efektivní a jednotné správy. Další informace najdete v tématu [nastavení fakturačnímu účtu Microsoft zákaznické smlouvy](billing-mca-setup-account.md).

Po dokončení instalace, použijte tento článek pochopit, jak k provádění úloh, které jste provedli v smlouva Enterprise Agreement enrollment.

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-your-access-to-a-microsoft-customer-agreement).

## <a name="mapping-of-enterprise-agreements-billing-to-the-new-billing-account"></a>Mapování fakturaci Enterprise Agreement se nové fakturační účet

Fakturace do nového účtu je uspořádaný odlišně, než vaše smlouva Enterprise Agreement enrollment. Následující tabulka popisuje mapování fakturace z smlouvu Enterprise k fakturačnímu účtu. Další informace o fakturačním účtu nové, najdete v článku [Začínáme s fakturační účet pro smlouvy Microsoft zákazníka](billing-mca-overview.md).

| Smlouva Enterprise   | Smlouva se zákazníkem Microsoftu    |
|------------------------|--------------------------------------------------------|
| Registrace            | Fakturační profil vám spravují fakturaci pro vaši organizaci, podobné smlouva Enterprise Agreement enrollment. Tito správci budou vlastníky fakturační profil. Další informace o fakturaci profilů najdete v tématu [pochopit fakturaci profily](billing-mca-overview.md#understand-billing-profiles).
| Oddělení            | Oddíl faktury slouží k uspořádání náklady, podobně jako oddělením ve vaší registraci smlouvy Enterprise. Oddělení stane části faktury a správci oddělení budou vlastníky příslušných faktury oddílů. Další informace o části faktury, naleznete v tématu [části faktury porozumění](billing-mca-overview.md#understand-invoice-sections). |
| Účet               | Nové fakturační účet nepodporují účty, které byly vytvořeny ve vaší smlouvě Enterprise. Předplatná účtu patří do části příslušných faktury pro jejich oddělení. Vlastníci účtu můžete vytvářet a spravovat odběry pro příslušné oddíly faktury. |

## <a name="tasks-performed-by-an-enterprise-administrator"></a>Úlohy prováděné správce podnikové sítě

Pokud jste správce rozlehlé sítě v rámci smlouvy Enterprise, která je teď obnovit na základě smlouvy Microsoft, jsou přiřazeny následující role na nové fakturační účet vytvořili smlouvy:

**Vlastníka profilu fakturace** – máte přiřazenou roli vlastníka fakturační profil na fakturační profil, který byl vytvořen při podepsání smlouvy. Role umožňuje spravovat fakturaci pro vaši organizaci. Můžete zobrazit náklady a faktury, náklady na faktuře uspořádat, spravovat způsoby platby a řízení přístupu k fakturaci svojí organizace.

**Vlastník části faktury** -mají přiřazenou roli vlastník části faktury na všechny oddíly faktury, které jsou vytvořeny pro oddělením ve vaší registraci smlouvy Enterprise. Role vám umožňuje řídit, kdo může vytvářet předplatná Azure a koupit další produkty.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Zobrazení nákladů a kredity zůstatku pro vaši organizaci

Fakturační profil použijete ke sledování nákladů a zůstatkem kredity Azure pro vaši organizaci podobný smlouva Enterprise Agreement enrollment.

Zjistěte, jak zobrazit zůstatek kreditu pro fakturační profil, najdete v článku [zůstatek kreditu Azure sledování pro váš fakturační profil](billing-mca-check-azure-credits-balance.md).

Zjistěte, jak zobrazit poplatky za fakturační profil, najdete v článku [pochopit poplatky na faktuře smlouvy zákazníka Microsoftu](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Zobrazit náklady pro oddělení

Oddíl faktury se vytvoří pro každé oddělení, kterou jste používali ve vaší smlouvě Enterprise. Poplatky za oddíl faktury můžete zobrazit na webu Azure Portal. Další informace najdete v tématu [zobrazit transakce podle části faktury](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Zobrazení poplatky za účet

Nové fakturační účet nepodporují účty, které byly vytvořeny ve vaší registraci smlouvy Enterprise. Předplatná účtu patří do části příslušných faktury pro jejich oddělení. Vlastníci účtu můžete vytvářet a spravovat odběry pro příslušné oddíly faktury.

Chcete-li zobrazit souhrnné náklady pro předplatná, u kterých patřil k účtu, je nutné nastavit nákladové středisko pro každé předplatné. Využití a poplatky za Azure file sdíleného svazku clusteru můžete použít k filtrování předplatných ve nákladové středisko.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Stáhnout využití a poplatků sdíleného svazku clusteru, ceníku a daňové doklady

Měsíční faktuře se generuje pro každou fakturační profil ve vašem fakturačním účtu. Pro každé faktury můžete stáhnout Azure využití a poplatků souboru csv, ceníku a daňový doklad (pokud existuje). Můžete také stáhnout Azure využití a poplatků souboru .csv pro aktuální měsíc poplatky.

Zjistěte, jak stáhnout soubor Azure využití a poplatků sdíleného svazku clusteru, najdete v článku [stažení nebo zobrazení Azure billing invoice a denní data o využití](billing-download-azure-invoice-daily-usage-date.md). <!--Todo update the link -->

Další informace o stažení dokumentů list a daň ceny najdete v tématu [daňové doklady stáhnout fakturu](billing-download-azure-invoice-daily-usage-date.md). <!--Todo update the link -->

### <a name="add-an-additional-enterprise-administrator"></a>Přidat další podnikového správce

Použití **přístup Control(IAM)** stránky na webu Azure Portal poskytnout ostatním přístup k zobrazení a správě fakturační profil. Další informace o fakturaci rolemi profilů najdete v tématu [fakturace profilu role a úlohy](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Zjistěte, jak poskytnout, přístup k vaší fakturační profil, najdete v článku [Správa fakturace rolí na portálu Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Vytvořit nové oddělení

V nové fakturačnímu účtu pomocí oddíl faktury uspořádat svoje náklady podle svých potřeb, podobně jako oddělením ve vaší registraci smlouvy Enterprise. Na webu Azure Portal nastavit Azure pro nové oddělení můžete vytvořit nový oddíl faktury. Další informace najdete v tématu [vytvořit části na vaší faktuře uspořádat svoje náklady](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Vytvoření nového účtu

Nové fakturační účet nepodporuje kontext fakturačního účtu ve vaší smlouvě Enterprise. Můžete však poskytnout role Tvůrce předplatného Azure uživatelům ve vaší organizaci, aby se mohly vytvářet předplatná Azure. Další informace najdete v tématu [dalším osobám udělit oprávnění vytvářet předplatná Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="tasks-performed-by-a-department-administrator"></a>Úlohy prováděné správcem oddělení

Pokud jste správce oddělení v rámci smlouvy Enterprise, která obnovit na základě smlouvy Microsoft, jsou přiřazeny následující role na nové fakturační účet:

**Vlastník části faktury** -mají přiřazenou roli vlastník části faktury v části faktury, který je vytvořen pro oddělení, jste na smlouvu Enterprise. Role umožňuje zobrazení a sledování poplatky a ovládací prvek, kteří můžou vytvářet předplatná Azure a koupit další produkty pro části faktury.

Pokud jste správce oddělení více vodítko použijte oddělení ve smlouvě Enterprise, máte přiřazenou roli vlastník části faktury na všechny části faktury vytvořené pro jako vodítko použijte oddělení.

### <a name="view-charges-for-your-department"></a>Zobrazení nákladů pro vaše oddělení

Oddíl faktury se vytvoří pro každé oddělení, kterou jste používali ve vaší smlouvě Enterprise. V části faktury má stejný název jako vaše oddělení ve smlouvě Enterprise. Poplatky za část faktury můžete zobrazit na webu Azure Portal. <!-- Todo Add a link -->

### <a name="add-an-additional-department-administrator"></a>Přidat další oddělení správce

Oddíl faktury se vytvoří pro každé oddělení, kterou jste používali ve vaší smlouvě Enterprise. Můžete použít **přístup Control(IAM)** stránky na webu Azure Portal poskytnout ostatním přístup k zobrazení a správa části faktury. Další informace o rolích části faktury, najdete v článku [fakturovat profilu role a úlohy](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Zjistěte, jak poskytnout, přístup k vaší části faktury, najdete v článku [Správa fakturace rolí na portálu Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Vytvořit nový účet vašeho oddělení

Nové fakturační účet nepodporuje kontext fakturačního účtu ve vaší smlouvě Enterprise. Můžete však poskytnout role Tvůrce předplatného Azure uživatelům ve vaší organizaci, aby se mohly vytvářet předplatná Azure. Další informace najdete v tématu [dalším osobám udělit oprávnění vytvářet předplatná Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>Zobrazit náklady pro účty ve vaší oblasti

Nové fakturační účet nepodporuje kontext fakturačního účtu ve vaší smlouvě Enterprise. Předplatné na účet patří do části faktury vytvořené pro vaše oddělení. Vlastníci účtu můžete vytvářet a spravovat odběry pro části faktury.

Chcete-li zobrazit souhrnné náklady pro předplatná, u kterých patřil k účtu v oddělení, musíte nastavit nákladové středisko pro každé předplatné. Filtrovat předplatná podle nákladové středisko můžete použít Azure file využití a poplatků. <!-- Todo - can they go to cost analysis -->

## <a name="tasks-performed-by-an-account-owner"></a>Úlohy prováděné vlastníka účtu

Pokud jste vlastníkem účtu v rámci smlouvy Enterprise, která je teď obnovit na základě smlouvy Microsoft, jsou přiřazeny následující roli na nový fakturační účet vytvořený smlouvy:

**Předplatné Azure creator** -mají přiřazenou roli Tvůrce předplatného azure v části faktury, který je vytvořen pro vaše oddělení ve smlouvě Enterprise. Pokud váš účet nepatří do oddělení, předplatné bude patřit do části faktury s názvem výchozí části faktury a získat předplatné Azure creator v části. Role vám umožňuje vytvářet předplatná Azure pro části faktury.

### <a name="view-charges-for-your-account"></a>Zobrazit náklady pro váš účet

Nové fakturační účet nepodporuje kontext fakturačního účtu ve vaší smlouvě Enterprise. Předplatné na účet patří do části faktury vytvořené pro váš účet oddělení.

Chcete-li zobrazit poplatky za odběr služeb, které patřily do účtu, přejděte na **stránce předplatná** na webu Azure Portal. Na stránce předplatná se zobrazí poplatky pro vaše předplatné. <!-- Todo - can they go to cost analysis -->

### <a name="view-charges-for-a-subscription"></a>Zobrazit náklady pro odběr

Analýza nákladů na Azure můžete použít k zobrazení poplatky za předplatné. Další informace najdete v tématu [zkoumat a analyzovat náklady pomocí Cost analysis](../cost-management/quick-acm-cost-analysis.md).

### <a name="create-an-azure-subscription"></a>Vytvořit předplatné Azure

Vaše faktura část na webu Azure Portal můžete vytvářet předplatná Azure. Další informace najdete v tématu [vytvořte další předplatné Azure pro smlouvy Microsoft zákazníka](billing-mca-create-subscription.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup

- [Vysvětlení fakturační účet pro zákaznické smlouvy Microsoft](billing-mca-overview.md)
- [Vysvětlení faktury](billing-understand-your-bill.md)
- [Vysvětlení informací na faktuře](billing-understand-your-invoice.md)
- [Získat fakturační vlastnictví předplatného Azure od jiných uživatelů](billing-mca-request-billing-ownership.md)
