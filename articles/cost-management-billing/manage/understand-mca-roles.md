---
title: Fakturační role pro smlouvy se zákazníkem Microsoftu – Azure
description: Přečtěte si o fakturačních rolích pro fakturační účty smluv se zákazníky Microsoftu v Azure.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/05/2021
ms.author: banders
ms.openlocfilehash: 00ac61567502984759c5db9837060c86aaee378d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593582"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Vysvětlení rolí pro správu smluv se zákazníky Microsoftu v Azure

Při správě fakturačního účtu smlouvy se zákazníkem Microsoftu používejte role popsané v následujících částech. Tyto role existují vedle předdefinovaných rolí, které v Azure slouží k řízení přístupu k prostředkům. Další informace najdete v tématu [Předdefinované role v Azure](../../role-based-access-control/built-in-roles.md).

Tento článek se týká fakturačního účtu smlouvy se zákazníky Microsoftu. Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu.

## <a name="billing-role-definitions"></a>Definice fakturačních rolí

Následující tabulka popisuje fakturační role, které slouží ke správě fakturačního účtu, fakturačních profilů a oddílů faktury.

|Role|Popis|
|---|---|
|Vlastník fakturačního účtu|Spravuje všechny aspekty fakturačního účtu.|
|Přispěvatel fakturačního účtu|Spravuje všechno kromě oprávnění k fakturačnímu účtu.|
|Čtenář fakturačního účtu|Může zobrazit všechno ve fakturačním účtu, ale jenom pro čtení.|
|Vlastník fakturačního profilu|Spravuje všechny aspekty fakturačního profilu.|
|Přispěvatel fakturačního profilu|Spravuje všechno kromě oprávnění k fakturačnímu profilu.|
|Čtenář fakturačního profilu|Může zobrazit všechno ve fakturačním profilu, ale jenom pro čtení.|
|Správce faktur|Zobrazení faktur pro fakturační profil|
|Vlastník oddílu faktur|Spravuje všechno v určitém oddílu faktury.|
|Přispěvatel oddílu faktur|Spravuje všechno kromě oprávnění k oddílu faktury.|
|Čtenář oddílu faktur|Může zobrazit všechno v oddílu faktury, ale jenom pro čtení.|
|Tvůrce předplatného Azure|Vytváří předplatná Azure.|

## <a name="billing-account-roles-and-tasks"></a>Role a úlohy související s fakturačním účtem

Fakturační účet umožňuje spravovat fakturaci vaší organizace. Fakturační účet slouží k uspořádání nákladů, monitorování poplatků a faktur a řízení přístupu k fakturaci ve vaší organizaci. Další informace najdete v článku [Vysvětlení fakturačního účtu](../understand/mca-overview.md#your-billing-account).

Následující tabulky ukazují, jakou roli potřebujete k různým úkonům v kontextu fakturačního účtu.

### <a name="manage-billing-account-permissions-and-properties"></a>Správa oprávnění a vlastností fakturačního účtu

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazení stávajících oprávnění k fakturačnímu účtu|✔|✔|✔|
|Udělování oprávnění k zobrazení a správě fakturačního účtu ostatním|✔|✘|✘|
|Zobrazení vlastností fakturačního účtu, jako je název společnosti, adresa a další|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Správa fakturačních profilů pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazení všech fakturačních profilů v účtu|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Správa faktur pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazení všech faktur v účtu|✔|✔|✔|
|Stahování faktur, souborů s informacemi o poplatcích a využití Azure, ceníků a daňových dokumentů v účtu|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Správa oddílů faktur pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazení všech oddílů faktur v účtu|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Správa transakcí pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazení všech fakturačních transakcí pro účet|✔|✔|✔|
|Zobrazení všech zakoupených produktů v účtu|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Správa předplatných pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazení všech předplatných Azure ve fakturačním účtu|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Role a úlohy související s fakturačním profilem

Fakturační profil umožňuje spravovat faktury a způsoby platby. Za předplatná Azure a další produkty zakoupené prostřednictvím fakturačního profilu se generuje měsíční faktura. Způsoby platby slouží k úhradě této faktury. Další informace najdete v článku [Vysvětlení fakturačních profilů](../understand/mca-overview.md#billing-profiles).

Následující tabulky ukazují, jakou roli potřebujete k různým úkonům v kontextu fakturačního profilu.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Správa oprávnění, vlastností a zásad fakturačního profilu

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení stávajících oprávnění k fakturačnímu profilu|✔|✔|✔|✔|✔|✔|✔|
|Udělování oprávnění k zobrazení a správě fakturačního profilu ostatním|✔|✘|✘|✘|✘|✘|✘|
|Zobrazení vlastností fakturačního profilu, jako je číslo objednávky, předvolby zasílání faktur e-mailem a další|✔|✔|✔|✔|✔|✔|✔|
|Aktualizace vlastností fakturačního profilu |✔|✔|✘|✘|✘|✘|✘|
|Zobrazení zásad použitých ve fakturačním profilu, například povolení nákupů rezervací v Azure, povolení nákupů z Azure Marketplace a další|✔|✔|✔|✔|✔|✔|✔|
|Uplatňování zásad ve fakturačním profilu |✔|✔|✘|✘|✘|✘|✘|
|Správa objednávek rezervací |✔|✔|✘|✘|✘|✘|✘|
|Zobrazit objednávky rezervací |✔|✔|✔|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Správa faktur pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení všech faktur ve fakturačním profilu|✔|✔|✔|✔|✔|✔|✔|
|Stahování faktur, souborů s informacemi o poplatcích a využití Azure, ceníků a daňových dokumentů pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Správa oddílů faktur pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení všech oddílů faktur pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|
|Vytváření nových oddílů faktur pro fakturační profil|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Správa transakcí pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení všech fakturačních transakcí pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Správa způsobů platby pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení způsobů platby pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|
|Sledování zůstatku kreditů Azure pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Správa předplatných pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení všech předplatných Azure pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Role a úlohy související s oddíly faktur

Oddíly faktury slouží k uspořádání nákladů na faktuře. Můžete vytvořit oddíly, pomocí kterých uspořádáte náklady podle oddělení, vývojového prostředí nebo podle jiných požadavků organizace. Dalším uživatelům můžete udělit oprávnění k vytváření předplatných Azure pro daný oddíl. V daném oddílu faktury se pak budou zobrazovat všechny poplatky za využití a nákupy v rámci daných předplatných. Další informace najdete v článku, který [vysvětluje oddíly faktury](../understand/mca-overview.md#invoice-sections).

Následující tabulky ukazují, jakou roli potřebujete k různým úkonům v kontextu oddílů faktur.

### <a name="manage-invoice-section-permissions-and-properties"></a>Správa oprávnění a vlastností oddílů faktur

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu |
|---|---|---|---|---|---|---|---|
|Zobrazení všech oprávnění k oddílu faktury|✔|✔|✔|✔|✔|✔|✔|
|Udělování oprávnění k zobrazení a správě oddílu faktury ostatním|✔|✘|✘|✘|✘|✘|✘|
|Zobrazení vlastností oddílu faktury|✔|✔|✔|✔|✔|✔|✔|
|Aktualizace vlastností oddílu faktury|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Správa produktů souvisejících s oddílem faktury

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení všech produktů zakoupených v rámci oddílu faktury|✔|✔|✔|✘|✔|✔|✔|
|Správa fakturace pro produkty v rámci oddílu faktury, například storno, vypnutí automatického obnovení a další|✔|✔|✘|✘|✘|✘|✘|
|Změna oddílu faktury pro produkty|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Správa předplatných pro oddíl faktury

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení všech předplatných Azure pro oddíl faktury|✔|✔|✔|✘|✔|✔|✔|
|Změna oddílu faktury pro předplatná|✔|✔|✘|✘|✘|✘|✘|
|Žádost o roli vlastníka fakturace pro předplatná od uživatelů v jiných fakturačních účtech|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Role a úlohy související s fakturací předplatného

Následující tabulka ukazuje, jakou roli potřebujete k různým úkonům v kontextu předplatného.

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|
|---|---|---|---|---|
|Vytváří předplatná Azure.|✔|✔|✘|✔|
|Aktualizace nákladového centra pro předplatné|✔|✔|✘|✘|
|Změna oddílu faktury pro předplatné|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Správa fakturačních rolí na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. Vyberte **Řízení přístupu (IAM)** v příslušném rozsahu, ve kterém chcete udělit přístup, například v rozsahu fakturačního účtu, fakturačního profilu nebo oddílu faktury.

4. Na stránce Řízení přístupu (IAM) se zobrazí seznam uživatelů a skupin přiřazených k jednotlivým rolím v daném rozsahu.

   ![Snímek obrazovky se seznamem správců fakturačního účtu](./media/understand-mca-roles/billing-list-admins.png)

5. Pokud chcete určitému uživateli udělit přístup, v horní části stránky vyberte **Přidat**. V rozevíracím seznamu Role vyberte roli. Zadejte e-mailovou adresu uživatele, kterému chcete udělit přístup. Přiřaďte roli výběrem možnosti **Uložit**.

   ![Snímek obrazovky, který znázorňuje přidání správce k fakturačnímu účtu](./media/understand-mca-roles/billing-add-admin.png)

6. Pokud chcete určitému uživateli odebrat přístup, vyberte uživatele s přiřazenou rolí, kterou chcete odebrat. Vyberte Odebrat.

   ![Snímek obrazovky, který znázorňuje odebrání správce z fakturačního účtu](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory
Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

Informace o fakturačním účtu najdete v následujících článcích:

- [Začínáme s fakturačními účty pro smlouvu se zákazníkem Microsoftu](../understand/mca-overview.md)
- [Vytvoření předplatného Azure pro fakturační účet pro smlouvu se zákazníkem Microsoftu](create-subscription.md)
