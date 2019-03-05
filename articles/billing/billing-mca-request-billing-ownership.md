---
title: Získat fakturační vlastnictví předplatného Azure | Dokumentace Microsoftu
description: Zjistěte, jak požádat o vlastnictví fakturace předplatného Azure od jiných uživatelů.
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
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 01d34ef722034ef5b56c842bab97a3415a02b0bb
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341863"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>Získat fakturační vlastnictví předplatného Azure od jiných uživatelů

Můžete převzít vlastnictví předplatná Azure, je-li stávající fakturační vlastník opouští organizaci, nebo chcete úhrada předplatných pomocí fakturačnímu účtu.

Můžete odeslat žádost o převzít vlastnictví předplatného Azure z existující vlastníky v jiných účtech fakturace. Převzetí vlastnictví přenese fakturační odpovědnosti předplatných k vaší části faktury.

Chcete-li požádat o vlastnictví fakturace, musíte být **vlastník části faktury** nebo **Přispěvatel části faktury**. Další informace najdete v tématu [fakturovat část role úlohy](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="request-billing-ownership-in-the-azure-portal"></a>Požádat o vlastnictví fakturace na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure portal vyhledejte Správa nákladů a fakturace](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Přejděte do části faktury. V závislosti na přístup budete muset vybrat fakturační účet nebo fakturační profil. Fakturace účtu nebo profil, vyberte **fakturovat oddíly** a potom část faktury.
   <!-- Todo - Add a screenshot -->

4. Vyberte **o převod** ze strany vlevo.

5. V horní části stránky vyberte **přidat**.

6. Zadejte e-mailová adresa uživatele, který požadujete vlastnictví fakturace z. Uživatel musí být účet správce na fakturační účet programu služeb Microsoft Online Service nebo vlastníkem účtu v rámci smlouvy Enterprise. Další informace najdete v tématu [zobrazit vaše fakturační účty na webu Azure portal](billing-view-all-accounts.md).

   ![Snímek obrazovky zobrazující, přidáte nový požadavek na přenos](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. Vyberte **odeslat žádost o převod**.

8. Uživatel dostane e-mail s pokyny ke kontrole vašeho požadavku na převedení.

   ![Snímek obrazovky tohoto ukazuje revize přenos e-mail se žádostí o](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. Schválit žádost o převod, uživatel vybere odkaz v e-mailu a postupuje podle pokynů.

    ![Snímek obrazovky tohoto ukazuje revize přenos e-mail se žádostí o](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>Zkontrolujte stav vašeho požadavku na převedení na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure portal vyhledejte Správa nákladů a fakturace](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Přejděte do části faktury. V závislosti na přístup budete muset vybrat fakturační účet nebo fakturační profil. Fakturace účtu nebo profil, vyberte **fakturovat oddíly** a potom část faktury.
   <!-- Todo - Add a screenshot -->

4. Vyberte **o převod** ze strany vlevo.

5. Na stránce žádosti o přenos se zobrazí následující informace:

    ![Snímek obrazovky zobrazující seznam požadavků na přenos](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |Sloupec|Definice|
   |---------|---------|
   |Datum žádosti|Datum, kdy byla odeslána žádost o přenos|
   |Příjemce|E-mailovou adresu uživatele, že jste odeslali žádost o převod vlastnictví fakturace|
   |Datum konce platnosti|Datum vypršení platnosti požadavku|
   |Status|Stav žádosti o převedení|

    Žádost o převod může mít jednu z následujících stavů:

   |Status|Definice|
   |---------|---------|
   |Probíhá zpracování|Uživatel nepřijal žádost o přenos|
   |Zpracování|Uživatel schválit žádost o přenos. Fakturace za předplatné, které uživatel vybral se převádějí na vaši část faktury|
   |Dokončeno| Fakturace za předplatné, které uživatel vybral se přenese do vaší části faktury|
   |Bylo dokončeno s chybami|Zpracována žádost o ale fakturace pro některá předplatná, které uživatel vybral nebylo možné přenést|
   |Vypršela platnost|Uživatel nepřijal žádost včas a jeho platnost|
   |Zrušeno|Uživatelé s přístupem k žádosti o přenos bylo zrušeno žádost|
   |Odmítnuto|Uživatel odmítl žádost o přenos|

6. Vyberte žádost o přenos zobrazíte podrobnosti. Stránce s podrobnostmi o přenos zobrazí následující informace: <!-- Todo - Add a screenshot -->

   |Sloupec  |Definice|
   |---------|---------|
   |ID žádosti o přenos|Jedinečné ID pro váš požadavek na převod. Pokud odešlete žádost o podporu sdílení ID s podporou Azure ohledně urychlení jejich zpracování vaší žádosti o podporu|
   |Datum žádosti o převod|Datum, kdy byla odeslána žádost o přenos|
   |Přenos žadatel|E-mailovou adresu uživatele, který poslal žádost o přenos|
   |Žádosti o převod vyprší| Datum, kdy vyprší platnost žádosti o přenos|
   |E-mailová adresa příjemce|E-mailovou adresu uživatele, že jste odeslali žádost o převod vlastnictví fakturace|
   |Odeslání příjemci odkaz na převod|Adresa url, který byl odeslán uživateli zkontrolovat žádost o přenos|

## <a name="additional-information"></a>Další informace

Následující část obsahuje další informace o přenos předplatného.

### <a name="no-service-downtime"></a>Žádný výpadek služby

Služby Azure v rámci předplatného zachovat spuštěna bez výpadku. Jsme pouze přechod fakturační vztah pro předplatná Azure, které uživatel vybere pro přenos.

### <a name="disabled-subscriptions"></a>Zakázaná předplatná

Nelze přenést deaktivovaných předplatných. Předplatné musí být v aktivním stavu k přenosu jejich vlastnictví fakturace.

### <a name="azure-resources-transfer"></a>Převést prostředky Azure

Všechny prostředky z předplatných, jako jsou virtuální počítače, disky a websites přenosu.

### <a name="azure-marketplace-products-transfer"></a>Přenos produkty Azure Marketplace

Produkty Azure Marketplace přenášet společně s jejich odpovídajících předplatných.

### <a name="azure-reservations-transfer"></a>Azure přenos rezervace

S předplatnými Azure rezervace nepřesouvejte automaticky. [Požádejte podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) přesunout rezervace.

### <a name="access-to-azure-services"></a>Přístup ke službám Azure

Přístup k prostředkům Azure, která byla nastavena pomocí Azure RBAC (řízení přístupu na základě role) nemá vliv během přechodu.

### <a name="azure-support-plan"></a>plán podpory Azure

Podpora Azure nepřenese předplatná. Pokud uživatel přenosů všech předplatných Azure, požádejte ho, aby zrušit jejich plán podpory.

### <a name="charges-for-transferred-subscription"></a>Poplatky za přenesené předplatného

Původní fakturační vlastníka z předplatných je zodpovědná za veškeré poplatky, které byly hlášeny až do chvíle, dokončení přenosu. Vaše faktura části je zodpovědná za hlášená z doby přenosu a vyšší poplatky za. Můžou existovat poplatky, které konal úplně před převodem, ale byla nahlášena později. Tyto poplatky se zobrazí na vaší faktuře oddílu.

### <a name="supported-offers"></a>Podporované nabídky

Předplatné žádné typy nebo nabídky, s výjimkou nabízí CSP mohou být převedeny.

### <a name="cancel-a-transfer-request"></a>Zrušit žádost o přenos

Žádost o přenos můžete zrušit, dokud požadavku je schváleny nebo odmítnuty. Zrušit žádost o přenos [přejděte na stránku podrobností přenos](#check-status-of-your-transfer-request-in-the-Azure-portal) a vyberte zrušit v dolní části stránky.

### <a name="software-as-a-service-saas-transfer"></a>Software jako služba (SaaS) přenosu

Předplatná se nepřevádějí produktech SaaS. Požádejte uživatele, aby [Azure kontakt podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) převést vlastnictví fakturace produktů SaaS. Spolu s vlastnictví fakturace uživatel může také převést vlastnictví prostředků. Vlastnictví prostředků umožňuje provádět operace správy, jako jsou odstranění a zobrazení podrobností o produktu. Uživatel musí být vlastníkem prostředku na produkt SaaS pro přenos vlastnictví prostředků.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup

- Vlastnictví fakturace předplatného Azure se přenesou do vaší části faktury. Mějte přehled o poplatky za tato předplatná v [webu Azure portal](https://portal.azure.com).
- Ostatním uživatelům oprávnění k zobrazení a správě fakturace předplatného těchto služeb. Další informace najdete v tématu [fakturovat část role a úlohy](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).