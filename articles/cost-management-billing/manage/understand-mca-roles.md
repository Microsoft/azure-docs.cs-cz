---
title: Fakturační role pro smlouvy se zákazníkem Microsoftu – Azure
description: Přečtěte si o fakturačních rolích pro fakturační účty smluv se zákazníky Microsoftu v Azure.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: e334a423fd11aa3a357d52099a792dcc905aedeb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011659"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Vysvětlení rolí pro správu smluv se zákazníky Microsoftu v Azure

Při správě fakturačního účtu smlouvy se zákazníkem Microsoftu používejte role popsané v následujících částech. Tyto role existují vedle předdefinovaných rolí, které v Azure slouží k řízení přístupu k prostředkům. Další informace najdete v tématu [Předdefinované role v Azure](../../role-based-access-control/built-in-roles.md).

Tento článek se týká fakturačního účtu smlouvy se zákazníky Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

Podívejte se na video [Správa přístupu ke svému fakturačnímu účtu MCA](https://www.youtube.com/watch?v=9sqglBlKkho) , kde zjistíte, jak můžete řídit přístup k fakturačnímu účtu Microsoft Customer Agreement (MCA).

>[!VIDEO https://www.youtube.com/embed/9sqglBlKkho]

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
|Správce faktur|Může zobrazovat a platit faktury pro určitý fakturační profil.|
|Vlastník oddílu faktur|Spravuje všechno v určitém oddílu faktury.|
|Přispěvatel oddílu faktur|Spravuje všechno kromě oprávnění k oddílu faktury.|
|Čtenář oddílu faktur|Může zobrazit všechno v oddílu faktury, ale jenom pro čtení.|
|Tvůrce předplatného Azure|Vytváří předplatná Azure.|

## <a name="billing-account-roles-and-tasks"></a>Role a úlohy související s fakturačním účtem

Když se zaregistrujete do Azure, vytvoří se vám fakturační účet. Slouží ke správě faktur a plateb a sledování nákladů. Role u fakturačního účtu mají nejvyšší úroveň oprávnění a uživatelé v těchto rolích získají přehled o nákladech a fakturačních údajích pro celý účet. Přiřaďte tyto role pouze uživatelům, kteří potřebují zobrazovat faktury, a sledujte náklady na celý účet, jako je třeba finanční oddělení a účetní týmy. Další informace najdete v článku [Vysvětlení fakturačního účtu](../understand/mca-overview.md#your-billing-account).

Následující tabulky ukazují, jakou roli potřebujete k různým úkonům v kontextu fakturačního účtu.

### <a name="manage-billing-account-permissions-and-properties"></a>Správa oprávnění a vlastností fakturačního účtu

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazit přiřazení rolí pro fakturační účet|✔|✔|✔|
|Udělování oprávnění k zobrazení a správě fakturačního účtu ostatním|✔|✘|✘|
|Zobrazení vlastností fakturačního účtu, jako je adresa, smlouvy a další|✔|✔|✔|
|Aktualizace vlastností fakturačního účtu, jako jsou prodávané – na, zobrazované jméno a další|✔|✔|✘|

### <a name="manage-billing-profiles-for-billing-account"></a>Správa fakturačních profilů pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazit všechny fakturační profily pro účet|✔|✔|✔|
|Vytvořit nové fakturační profily|✔|✔|✘|

### <a name="manage-invoices-for-billing-account"></a>Správa faktur pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazit všechny faktury pro účet|✔|✔|✔|
|Platby faktur pomocí platební karty|✔|✔|✘|
|Stáhněte si faktury, soubory využití Azure, ceníky a daňové doklady.|✔|✔|✔|

### <a name="manage-products-for-billing-account"></a>Správa produktů pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazit všechny zakoupené produkty pro účet|✔|✔|✔|
|Správa fakturace pro produkty, jako je Storno, vypnutí automatického obnovování a další|✔|✔|✘|

### <a name="manage-subscriptions-for-billing-account"></a>Správa předplatných pro fakturační účet

|Úkol|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu|
|---|---|---|---|
|Zobrazit všechna předplatná Azure vytvořená pro fakturační účet|✔|✔|✔|
|Vytvoření nových předplatných Azure|✔|✔|✘|
|Zrušit předplatná Azure|✘|✘|✘|

## <a name="billing-profile-roles-and-tasks"></a>Role a úlohy související s fakturačním profilem

Každý fakturační účet má alespoň jeden Fakturační profil. Váš první Fakturační profil se nastaví při registraci k používání Azure. Pro fakturační profil se vygeneruje měsíční faktura a všechny jeho přidružené poplatky se účtují z předchozího měsíce. Můžete nastavit další fakturační profily podle svých potřeb. Uživatelé s rolemi ve fakturačním profilu můžou zobrazovat náklady, nastavovat rozpočet a spravovat a hradit faktury. Přiřaďte tyto role uživatelům, kteří zodpovídají za správu rozpočtových a platebních faktur pro fakturační profil, jako jsou členové týmů pro správu podniku ve vaší organizaci. Další informace najdete v článku [Vysvětlení fakturačních profilů](../understand/mca-overview.md#billing-profiles).

Následující tabulky ukazují, jakou roli potřebujete k různým úkonům v kontextu fakturačního profilu.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Správa oprávnění, vlastností a zásad fakturačního profilu

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazit přiřazení rolí pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|
|Udělování oprávnění k zobrazení a správě fakturačního profilu ostatním|✔|✘|✘|✘|✔|✘|✘|
|Zobrazení vlastností fakturačního profilu, jako je číslo nákupu, fakturace a dalších|✔|✔|✔|✔|✔|✔|✔|
|Aktualizace vlastností fakturačního profilu |✔|✔|✘|✘|✔|✔|✘|
|Zobrazit zásady použité na fakturačním profilu, jako jsou nákupy rezervací Azure, nákupy Azure Marketplace a další|✔|✔|✔|✔|✔|✔|✔|
|Uplatňování zásad ve fakturačním profilu |✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-invoices-for-billing-profile"></a>Správa faktur pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení všech faktur ve fakturačním profilu|✔|✔|✔|✔|✔|✔|✔|
|Platby faktur pomocí platební karty|✔|✔|✘|✔|✔|✘|✘|
|Stahování faktur, souborů s informacemi o poplatcích a využití Azure, ceníků a daňových dokumentů pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Správa oddílů faktur pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení všech oddílů faktur pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|
|Vytváření nových oddílů faktur pro fakturační profil|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-billing-profile"></a>Spravovat produkty pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazit všechny produkty zakoupené pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|
|Správa fakturace pro produkty, jako je Storno, vypnutí automatického obnovování a další|✔|✔|✘|✘|✔|✔|✘|
|Změnit fakturační profil pro produkty|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-payment-methods-for-billing-profile"></a>Správa způsobů platby pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení způsobů platby pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|
|Správa způsobů platby, jako je třeba výměna kreditní karty, odpojení kreditní karty a další|✔|✔|✘|✘|✔|✔|✘|
|Sledování zůstatku kreditů Azure pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Správa předplatných pro fakturační profil

|Úkol|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu|Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu
|---|---|---|---|---|---|---|---|
|Zobrazení všech předplatných Azure pro fakturační profil|✔|✔|✔|✔|✔|✔|✔|
|Vytvoření nových předplatných Azure|✔|✔|✘|✘|✔|✔|✘|
|Zrušit předplatná Azure|✘|✘|✘|✘|✘|✘|✘|
|Změna fakturačního profilu pro předplatná Azure|✔|✔|✘|✘|✔|✔|✘|

## <a name="invoice-section-roles-and-tasks"></a>Role a úlohy související s oddíly faktur

Každý Fakturační profil ve výchozím nastavení obsahuje jednu část faktury. Můžete vytvořit další oddíly faktury k seskupení nákladů na fakturu fakturačního profilu.  Uživatelé s rolemi v části faktura můžou řídit, kdo vytvoří předplatná Azure a provede další nákupy. Role přiřazujete uživatelům, kteří nastavují prostředí Azure pro týmy ve vaší organizaci, například technickým vedoucím nebo architektům. Další informace najdete v článku, který [vysvětluje oddíly faktury](../understand/mca-overview.md#invoice-sections).

Následující tabulky ukazují, jakou roli potřebujete k různým úkonům v kontextu oddílů faktur.

### <a name="manage-invoice-section-permissions-and-properties"></a>Správa oprávnění a vlastností oddílů faktur

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu |Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Zobrazit přiřazení rolí pro fakturu – oddíl|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Udělování oprávnění k zobrazení a správě oddílu faktury ostatním|✔|✘|✘|✘|✔|✘|✘|✘|✔|✘|✘|
|Zobrazení vlastností oddílu faktury|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Aktualizace vlastností oddílu faktury|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-invoice-section"></a>Správa produktů souvisejících s oddílem faktury

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu |Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Zobrazit všechny produkty koupené pro část faktury|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Správa fakturace pro produkty, jako je Storno, vypnutí automatického obnovování a další|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Změna oddílu faktury pro produkty|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Správa předplatných pro oddíl faktury

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu |Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Zobrazení všech předplatných Azure pro oddíl faktury|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Vytváří předplatná Azure.|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Zrušit předplatná Azure|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|
|Oddíl změny faktury pro předplatné Azure|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Žádost o roli vlastníka fakturace pro předplatná od uživatelů v jiných fakturačních účtech|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Role a úlohy související s fakturací předplatného

Následující tabulka ukazuje, jakou roli potřebujete k různým úkonům v kontextu předplatného.

|Úlohy|Vlastník oddílu faktur|Přispěvatel oddílu faktur|Čtenář oddílu faktur|Tvůrce předplatného Azure|Vlastník fakturačního profilu|Přispěvatel fakturačního profilu|Čtenář fakturačního profilu |Správce faktur|Vlastník fakturačního účtu|Přispěvatel fakturačního účtu|Čtenář fakturačního účtu 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Vytvoření předplatných|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Aktualizace nákladového centra pro předplatné|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Změna oddílu faktury pro předplatné|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Změnit fakturační profil předplatného|✘|✘|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Zrušit předplatná Azure|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|

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
