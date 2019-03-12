---
title: Vysvětlení fakturace správní role pro Microsoft zákaznických smluv – Azure
description: Další informace o fakturace rolí pro fakturační účty v Azure pro Microsoft zákaznických smluv.
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 9b14174541117878d7fd98b79d1cd139babb1027
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543453"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Vysvětlení smlouvy zákazníků společnosti Microsoft pro správu role v Azure

Ke správě vašeho fakturačního účtu zákaznické smlouvy Microsoft, pomocí role popsané v následujících částech. Tyto role jsou kromě předdefinované role, které Azure nabízí pro řízení přístupu k prostředkům. Další informace najdete v tématu [předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md).

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-your-access-to-a-microsoft-customer-agreement).

## <a name="billing-role-definitions"></a>Fakturace definice rolí

Následující tabulka popisuje fakturace rolí umožňuje spravovat vaše fakturační účet fakturace profily a části faktury.

|Role|Popis|
|---|---|
|Vlastník fakturačního účtu|Spravovat všechno pro fakturační účet|
|Přispěvatel fakturačního účtu|Spravovat všechno kromě oprávnění na fakturační účet|
|Čtenář fakturačního účtu|Zobrazení jen pro čtení všeho na fakturační účet|
|Vlastník fakturačního profilu|Spravovat všechno fakturačních profilu|
|Přispěvatel fakturačního profilu|Spravovat všechno kromě oprávnění na fakturační profil|
|Čtenář fakturačního profilu|Zobrazení jen pro čtení všeho na fakturační profil|
|Správce faktur|Zobrazení a faktury za fakturační profil|
|Vlastník oddílu faktur|Správu všech položek v části faktury|
|Přispěvatel oddílu faktur|Spravovat všechno kromě oprávnění v části faktury|
|Čtenář oddílu faktur|Zobrazení jen pro čtení všeho v části faktury|
|Tvůrce předplatného Azure|Vytvářet předplatná Azure|

## <a name="billing-account-roles-and-tasks"></a>Fakturace účtu role a úlohy

Fakturační účet vám umožní spravovat fakturaci pro vaši organizaci. Fakturační účet můžete uspořádat nákladů, sledování nákladů a faktury a řízení přístupu k fakturaci pro vaši organizaci. Další informace najdete v tématu [pochopit fakturační účet](billing-mca-overview.md#understand-billing-account).

Jakou roli, budete potřebovat k provádění úloh v rámci fakturačního účtu v následujících tabulkách.

### <a name="manage-billing-account-permissions-and-properties"></a>Správa fakturačního účtu oprávnění a vlastnosti

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazit existující oprávnění pro fakturační účet|✔|✔|✔|
|Ostatním uživatelům oprávnění k zobrazení a správě fakturační účet|✔|✘|✘|
|Zobrazit vlastnosti fakturační účet, jako je název společnosti, adresu a další|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Správa fakturace profilů pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazit všechny profily fakturačního účtu|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Spravovat faktury pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazit všechny faktury v účtu|✔|✔|✔|
|Stažení faktury, využití a poplatky za soubory Azure, ceníky a daňové dokumenty v účtu|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Správa části faktury pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazit všechny části faktury v účtu|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Správa transakcí pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazit všechny transakce fakturace pro účet|✔|✔|✔|
|Zobrazit všechny produkty pro účet|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Správa předplatných pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazit všechna předplatná Azure ve fakturačním účtu|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Fakturační profil role a úlohy

Fakturační profil vám umožní spravovat faktury a platby. Měsíční faktuře se vygeneruje pro předplatná Azure a další produkty nákupu využít fakturační profil. Pomocí metody platby zaplatit fakturu. Další informace najdete v tématu [pochopit fakturaci profily](billing-mca-overview.md#understand-billing-profiles).

Následující tabulky popisují, jaké role je potřeba dokončit úlohy v rámci fakturační profil.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Spravovat oprávnění fakturační profil, vlastnosti a zásady

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktury|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazit existující oprávnění pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|
|Ostatním uživatelům oprávnění k zobrazení a správě fakturační profil|✔|✘|✘|✘|✘|✘|✘|
|Fakturační profil vlastnosti zobrazení, jako jsou PO číslo, předvoleb faktury e-mailu a dalších|✔|✔|✔|✔|✔|✔|✔|
|Aktualizovat vlastnosti fakturační profil |✔|✔|✘|✘|✘|✘|✘|
|Zobrazit zásady použity na fakturační profil jako povolit nákupy na Azure rezervace, povolit nákupy na Azure marketplace a další|✔|✔|✔|✔|✔|✔|✔|
|Použije zásady na fakturační profil |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Spravovat faktury pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktury|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazit všechny faktury pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|
|Stažení faktury, využití a poplatky za soubory Azure, ceníky a daňových dokumentů pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Správa části faktury pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktury|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazit všechny části faktury pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|
|Vytvořit nový oddíl faktury pro fakturační profil|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Správa transakcí pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktury|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazit všechny transakce fakturační pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Spravovat způsoby platby pro fakturaci profilu

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktury|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení způsoby platby pro fakturaci profil|✔|✔|✔|✔|✔|✔|✔|
|Sledování zůstatku kredity Azure pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Správa předplatných pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktury|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazit všechna předplatná Azure pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Role části faktury a úlohy

Oddíl faktury umožňuje uspořádat náklady na vaší faktuře. Můžete vytvořit části uspořádat svoje náklady podle oddělení, vývojové prostředí, nebo na základě potřeb vaší organizace. Dalším osobám udělit oprávnění vytvářet předplatná Azure pro oddíl. Poplatky za využívání a nákup předplatných a zobrazit v části faktury. Další informace najdete v tématu [části faktury porozumění](billing-mca-overview.md#understand-invoice-sections).

Následující tabulky popisují, jaké role, které potřebujete k provádění úloh v rámci části faktury.

### <a name="manage-invoice-section-permissions-and-properties"></a>Spravovat oprávnění části faktury a vlastnosti

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazit všechna oprávnění v části faktury|✔|✔|✔|✔|✔|✔|✔|
|Ostatním uživatelům oprávnění k zobrazení a správa části faktury|✔|✘|✘|✘|✘|✘|✘|
|Zobrazit vlastnosti části faktury|✔|✔|✔|✔|✔|✔|✔|
|Aktualizovat vlastnosti části faktury|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Správa produktů pro části faktury

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazit všechny produkty v části faktury|✔|✔|✔|✘|✔|✔|✔|
|Správa fakturace pro produkty pro části faktury jako zrušit, vypnout automatické prodloužení platnosti a další|✔|✔|✘|✘|✘|✘|✘|
|Změna části faktury pro produkty|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Správa předplatných pro části faktury

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazit všechna předplatná Azure pro části faktury|✔|✔|✔|✘|✔|✔|✔|
|Změna části faktury pro předplatná|✔|✔|✘|✘|✘|✘|✘|
|Požádat o vlastnictví fakturace předplatného uživatele v jiné fakturační účty|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Fakturace předplatného role a úlohy

Následující tabulka ukazuje, jakou roli, budete potřebovat k provádění úloh v rámci předplatného.

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|
|---|---|---|---|---|
|Vytvářet předplatná Azure|✔|✔|✘|✔|
|Nákladové středisko aktualizace pro předplatné|✔|✔|✘|✘|
|Změna části faktury pro předplatné|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Správa fakturace rolí na portálu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. Vyberte **řízení přístupu (IAM)** v oboru, jako je například fakturační účet, fakturační profil nebo části faktury, ve které chcete poskytnout přístup.

4. Stránku řízení přístupu (IAM) obsahuje seznam uživatelů a skupin, které jsou přiřazeny ke každé roli pro tento obor.

   ![Snímek obrazovky zobrazující seznam správce pro fakturační účet](./media/billing-understand-mca-roles/billing-list-admins.png)

5. Chcete-li udělit přístup pro uživatele, vyberte **přidat** z horní části stránky. V rozevíracím seznamu rolí vyberte roli. Zadejte e-mailovou adresu uživatele, kterým chcete udělit přístup. Vyberte **Uložit** přiřazení role.

   ![Snímek obrazovky zobrazující, přidáte k fakturačnímu účtu správce](./media/billing-understand-mca-roles/billing-add-admin.png)

6. K odebrání přístupu pro uživatele, vyberte uživatele, který chcete odebrat přiřazení role. Vyberte odebrat.

   ![Snímek obrazovky zobrazující odebrání fakturační účet správce](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory
Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup

Naleznete v následujících článcích se dozvíte o vašem fakturačním účtu:

- [Začněte s vaším fakturačním účtem pro smlouvy Microsoft zákazníka](billing-mca-overview.md)
- [Vytvořte předplatné Azure pro váš fakturační účet pro smlouvy Microsoft zákazníka](billing-mca-create-subscription.md)