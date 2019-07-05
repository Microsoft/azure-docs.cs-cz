---
title: Získat fakturační vlastnictví předplatného Azure
description: Zjistěte, jak požádat o vlastnictví fakturace předplatného Azure od jiných uživatelů.
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
ms.openlocfilehash: 4c9a8c866a157757121e6a9dd07a0a8559937c5e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490850"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Získání vlastnictví předplatného Azure z jiných účtů fakturace

Můžete převzít vlastnictví předplatného Azure, pokud stávající fakturační vlastník opouští organizaci, nebo chcete úhrada předplatných pomocí fakturačnímu účtu. Převzetí vlastnictví přenese fakturační odpovědnosti předplatných k vašemu účtu.

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-for-access).

Chcete-li požádat o vlastnictví fakturace, musíte být **vlastník části faktury** nebo **Přispěvatel části faktury**. Další informace najdete v tématu [fakturovat část role úlohy](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Žádost o vlastnictví fakturace

1. Přihlaste se k [webu Azure portal](http://portal.azure.com) jako faktury části vlastníka nebo přispěvatele pro fakturační účet pro smlouvy zákazníka se společností Microsoft.

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure portal vyhledejte Správa nákladů a fakturace](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Vyberte **fakturovat oddíly** na levé straně. V závislosti na přístup budete muset vybrat fakturační účet nebo fakturační profil. Fakturace účtu nebo profil, vyberte **fakturovat oddíly**.
   
   ![Snímek obrazovky s výběrem části faktury](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Vyberte oddíl faktury ze seznamu. Po převzetí vlastnictví předplatná se účtuje na v této části faktury.

5. Vyberte **o převod** z dolní levé straně a pak vyberte **přidat**.
 
   ![Snímek obrazovky, který ukazuje výběr požadavků na přenos](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Zadejte e-mailová adresa uživatele, který požadujete vlastnictví fakturace z. Uživatel musí být účet správce na fakturační účet programu služeb Microsoft Online Service nebo vlastníkem účtu v rámci smlouvy Enterprise. Další informace najdete v tématu [zobrazit vaše fakturační účty na webu Azure portal](billing-view-all-accounts.md). Vyberte **odeslat žádost o převod**.

   ![Snímek obrazovky zobrazující posílá se žádost o přenos](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. Uživatel dostane e-mail s pokyny ke kontrole vašeho požadavku na převedení.

   ![Snímek obrazovky tohoto ukazuje revize přenos e-mail se žádostí o](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Schválit žádost o převod, uživatel vybere odkaz v e-mailu a postupuje podle pokynů.

    ![Snímek obrazovky tohoto ukazuje revize přenos e-mail se žádostí o](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Zkontrolovat stav žádosti o přenos

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure portal vyhledejte Správa nákladů a fakturace](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Vyberte **fakturovat oddíly** na levé straně. V závislosti na přístup budete muset vybrat fakturační účet nebo fakturační profil. Fakturace účtu nebo profil, vyberte **fakturovat oddíly**.
   
   ![Snímek obrazovky s výběrem části faktury](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Vyberte ze seznamu, pro který jste odeslali žádost o přenos části faktury.

5. Vyberte **o převod** ze strany vlevo. Na stránce žádosti o přenos se zobrazí následující informace:

    ![Snímek obrazovky zobrazující seznam požadavků na přenos](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Sloupec|Definice|
   |---------|---------|
   |Datum žádosti|Datum, kdy byla odeslána žádost o přenos|
   |Příjemce|E-mailovou adresu uživatele, že jste odeslali žádost o převod vlastnictví fakturace|
   |Datum vypršení platnosti|Datum vypršení platnosti požadavku|
   |Status|Stav žádosti o převedení|

    Žádost o převod může mít jednu z následujících stavů:

   |Status|Definice|
   |---------|---------|
   |V průběhu|Uživatel nepřijal žádost o přenos|
   |Zpracování|Uživatel schválit žádost o přenos. Fakturace za předplatné, které uživatel vybral se převádějí na vaši část faktury|
   |Dokončeno| Fakturace za předplatné, které uživatel vybral se přenese do vaší části faktury|
   |Bylo dokončeno s chybami|Zpracována žádost o ale fakturace pro některá předplatná, které uživatel vybral nebylo možné přenést|
   |Vypršela platnost|Uživatel nepřijal žádost včas a jeho platnost|
   |Zrušeno|Uživatelé s přístupem k žádosti o přenos bylo zrušeno žádost|
   |Odmítnuto|Uživatel odmítl žádost o přenos|

7. Vyberte žádost o přenos zobrazíte podrobnosti. Stránce s podrobnostmi o přenos zobrazí následující informace:
   
   ![Snímek obrazovky zobrazující seznam přenesené předplatných](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Sloupec  |Definice|
   |---------|---------|
   |ID žádosti o přenos|Jedinečné ID pro váš požadavek na převod. Pokud odešlete žádost o podporu sdílení ID s podporou Azure ohledně urychlení jejich zpracování vaší žádosti o podporu|
   |Datum žádosti o převod|Datum, kdy byla odeslána žádost o přenos|
   |Přenos žadatel|E-mailovou adresu uživatele, který poslal žádost o přenos|
   |Žádosti o převod vyprší| Datum, kdy vyprší platnost žádosti o přenos|
   |E-mailová adresa příjemce|E-mailovou adresu uživatele, že jste odeslali žádost o převod vlastnictví fakturace|
   |Odeslání příjemci odkaz na převod|Adresa url, který byl odeslán uživateli zkontrolovat žádost o přenos|

## <a name="supported-subscription-types"></a>Typy podporované předplatného

Můžete požádat o vlastnictví fakturace předplatného typů uvedené níže.

- [Akční balíček](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure v programu Open Licensing](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Sponzorství Azure Pass](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Bezplatná zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Vývoj/testování s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Nabídka sponzorství Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Smlouva Microsoft Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Předplatné MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Předplatitelé sady Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Předplatitelé sady Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Předplatitelé sady Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Předplatitelé sady Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Žádný kredit, který je k dispozici v rámci předplatného není k dispozici na nový účet po převodu.

\*\* Podporuje jenom pro předplatná v účtech, které jsou vytvořeny během registrace na webu Azure.


## <a name="additional-information"></a>Další informace

Následující část obsahuje další informace o přenos předplatného.

### <a name="no-service-downtime"></a>Žádný výpadek služby

Služby Azure v rámci předplatného zachovat spuštěna bez výpadku. Jsme pouze přechod fakturační vztah pro předplatná Azure, které uživatel vybere pro přenos.

### <a name="disabled-subscriptions"></a>Deaktivovaných předplatných

Nelze přenést deaktivovaných předplatných. Předplatné musí být v aktivním stavu k přenosu jejich vlastnictví fakturace.

### <a name="azure-resources-transfer"></a>Převést prostředky Azure

Všechny prostředky z předplatných, jako jsou virtuální počítače, disky a websites přenosu.

### <a name="azure-marketplace-products-transfer"></a>Přenos produkty Azure Marketplace

Produkty Azure Marketplace přenášet společně s jejich odpovídajících předplatných.

### <a name="azure-reservations-transfer"></a>Azure přenos rezervace

S předplatnými Azure rezervace nepřesouvejte automaticky. [Požádejte podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) přesunout rezervace.

### <a name="access-to-azure-services"></a>Přístup ke službám Azure

Přístup pro stávající uživatele, skupiny nebo instanční objekty, které byla přiřazena pomocí (Azure RBAC (řízení přístupu na základě role)) [.. /Role-Based-Access-Control/Overview.MD] nemá vliv během přechodu.

### <a name="azure-support-plan"></a>plán podpory Azure

Podpora Azure nepřenese předplatná. Pokud uživatel přenosů všech předplatných Azure, požádejte ho, aby zrušit jejich plán podpory.

### <a name="charges-for-transferred-subscription"></a>Poplatky za přenesené předplatného

Původní fakturační vlastníka z předplatných je zodpovědná za veškeré poplatky, které byly hlášeny až do chvíle, dokončení přenosu. Vaše faktura části je zodpovědná za hlášená z doby přenosu a vyšší poplatky za. Můžou existovat poplatky, které konal úplně před převodem, ale byla nahlášena později. Tyto poplatky se zobrazí na vaší faktuře oddílu.

### <a name="cancel-a-transfer-request"></a>Zrušit žádost o přenos

Žádost o přenos můžete zrušit, dokud požadavku je schváleny nebo odmítnuty. Pokud chcete zrušit žádost o přenos, přejděte [stránce s podrobnostmi o přenos](#check-the-transfer-request-status) a vyberte zrušit v dolní části stránky.

### <a name="software-as-a-service-saas-transfer"></a>Software jako služba (SaaS) přenosu

Předplatná se nepřevádějí produktech SaaS. Požádejte uživatele, aby [Azure kontakt podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) převést vlastnictví fakturace produktů SaaS. Spolu s vlastnictví fakturace uživatel může také převést vlastnictví prostředků. Vlastnictví prostředků umožňuje provádět operace správy, jako jsou odstranění a zobrazení podrobností o produktu. Uživatel musí být vlastníkem prostředku na produkt SaaS pro přenos vlastnictví prostředků.

## <a name="check-for-access"></a>Kontrolu přístupu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup

- Vlastnictví fakturace předplatného Azure se přenesou do vaší části faktury. Mějte přehled o poplatky za tato předplatná v [webu Azure portal](https://portal.azure.com).
- Ostatním uživatelům oprávnění k zobrazení a správě fakturace předplatného těchto služeb. Další informace najdete v tématu [fakturovat část role a úlohy](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
