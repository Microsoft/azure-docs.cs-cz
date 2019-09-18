---
title: Získání vlastnictví fakturace předplatných Azure
description: Přečtěte si, jak požádat ostatní uživatele o udělení vlastnictví fakturace předplatných Azure.
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
ms.openlocfilehash: 110bd975de1d865c2aa3d3b088c98cdc8b9e45ad
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "69019562"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Získání vlastnictví fakturace předplatných Azure od ostatních účtů

Převzetí vlastnictví předplatných Azure může být vhodné, pokud stávající vlastník fakturace odchází ze společnosti, nebo pokud chcete předplatná hradit ze svého fakturačního účtu. Po převzetí vlastnictví se odpovědnosti spojené s fakturací předplatných převedou na váš účet.

Tento článek se týká fakturačních účtů smluv se zákazníkem Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-for-access).

Pokud chcete požádat o vlastnictví fakturace, musíte být **vlastníkem oddílu faktur** nebo **přispěvatelem oddílu faktur**. Podrobnosti najdete v části [Role a úlohy související s oddíly faktur](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Podání žádosti o vlastnictví fakturace

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako vlastník nebo přispěvatel oddílu faktury pro fakturační účet spojený se smlouvou se zákazníkem Microsoftu.

2. Vyhledejte položku **Správa nákladů a fakturace**.

   ![Snímek obrazovky znázorňující hledání položky Správa nákladů a fakturace na webu Azure Portal](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Na levé straně vyberte **Oddíly faktury**. V závislosti na úrovni vašeho přístupu možná budete muset vybrat fakturační účet nebo fakturační profil. Ve fakturačním účtu nebo profilu vyberte **Oddíly faktury**.
   
   ![Snímek obrazovky znázorňující výběr možnosti Oddíly faktury](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. V seznamu vyberte oddíl faktury. Jakmile převezmete vlastnictví předplatných, budou se účtovat do tohoto oddílu.

5. Vlevo dole vyberte **Žádosti o převod** a potom zvolte **Přidat**.
 
   ![Snímek obrazovky znázorňující výběr možnosti Žádosti o převod](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Zadejte e-mailovou adresu uživatele, o jehož vlastnictví fakturace žádáte. Uživatel musí být správcem účtu pro fakturační účet programu služby Microsoft Online, nebo vlastníkem účtu souvisejícího se smlouvou Enterprise. Další informace najdete v tématu věnovaném [zobrazení fakturačních účtů na webu Azure Portal](billing-view-all-accounts.md). Vyberte možnost **Poslat žádost o převod**.

   ![Snímek obrazovky znázorňující výběr možnosti Poslat žádost o převod](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. Tento uživatel obdrží e-mail s pokyny ke kontrole vaší žádosti o převod.

   ![Snímek obrazovky znázorňující e-mail pro kontrolu žádosti o převod](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Uživatel může žádost o převod schválit tím, že vybere odkaz v e-mailu a bude postupovat podle zobrazených pokynů.

    ![Snímek obrazovky znázorňující e-mail pro kontrolu žádosti o převod](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Kontrola stavu žádosti o převod

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte položku **Správa nákladů a fakturace**.

   ![Snímek obrazovky znázorňující hledání položky Správa nákladů a fakturace na webu Azure Portal](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Na levé straně vyberte **Oddíly faktury**. V závislosti na úrovni vašeho přístupu možná budete muset vybrat fakturační účet nebo fakturační profil. Ve fakturačním účtu nebo profilu vyberte **Oddíly faktury**.
   
   ![Snímek obrazovky znázorňující výběr možnosti Oddíly faktury](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. V seznamu vyberte oddíl faktury, pro který jste poslali žádost o převod.

5. Vlevo dole vyberte **Žádosti o převod**. Zobrazí se stránka žádostí o převod, která obsahuje následující informace:

    ![Snímek obrazovky znázorňující seznam žádostí o převod](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Sloupec|Definice|
   |---------|---------|
   |Datum žádosti|Datum odeslání žádosti o převod|
   |Příjemce|E-mailová adresa uživatele, kterému jste odeslali žádost o převod vlastnictví fakturace|
   |Datum vypršení platnosti|Datum, kdy platnost žádosti vyprší|
   |Status|Stav žádosti o převod|

    Žádost o převod může mít jeden z následujících stavů:

   |Status|Definice|
   |---------|---------|
   |Probíhá|Uživatel žádost o převod dosud neschválil.|
   |Zpracování|Uživatel schválil žádost o převod. Fakturace předplatných, která uživatel vybral, se převádí do vašeho oddílu faktury.|
   |Dokončeno| Fakturace předplatných, která uživatel vybral, se převedla do vašeho oddílu faktury.|
   |Dokončeno s chybami|Žádost byla dokončena, ale fakturaci některých předplatných, která uživatel vybral, nebylo možné převést.|
   |Platnost vypršela|Uživatel nepřijal žádost včas a její platnost vypršela.|
   |Zrušeno|Žádost o převod byla zrušena některým z uživatelů, kteří k ní mají přístup.|
   |Odmítnuto|Žádost o převod byla uživatelem zamítnuta.|

7. Výběrem žádosti o převod zobrazte její podrobnosti. Zobrazí se stránka s podrobnostmi o převodu, která obsahuje následující informace:
   
   ![Snímek obrazovky znázorňující seznam převedených předplatných](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Sloupec  |Definice|
   |---------|---------|
   |ID žádosti o převod|Jedinečné ID vaší žádosti o převod. Pokud odešlete žádost o podporu, sdělte toto ID podpoře Azure, abyste žádost urychlili.|
   |Datum žádosti o převod|Datum, kdy byla žádost o převod odeslána|
   |Uživatel, který požádal o převod|E-mailová adresa uživatele, který žádost o převod odeslal|
   |Platnost žádosti o převod vyprší| Datum, kdy platnost žádosti o převod vyprší|
   |E-mailová adresa příjemce|E-mailová adresa uživatele, kterému jste odeslali žádost o převod vlastnictví fakturace|
   |Odkaz pro převod zaslaný příjemci|Adresa URL odeslaná uživateli za účelem kontroly žádosti o převod|

## <a name="supported-subscription-types"></a>Podporované typy předplatného

O vlastnictví fakturace můžete požádat pro níže uvedené typy předplatných.

- [Akční balíček](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure v programu Open Licensing](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Sponzorství Azure Pass](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Bezplatná zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Plán Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Sponzorovaná nabídka Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Smlouva Microsoft Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Předplatitelé sady Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Předplatitelé sady Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Předplatitelé sady Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Předplatitelé sady Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Po převodu nebude mít nový účet k dispozici žádný kredit, který předplatné obsahovalo.

\*\* Podporuje se jen u předplatných v účtech vytvořených během registrace na webu Azure.


## <a name="additional-information"></a>Další informace

V této části najdete doplňkové informace o převodu předplatných.

### <a name="no-service-downtime"></a>Žádné výpadky služby

Služby Azure v předplatném poběží i nadále bez přerušení. Dochází jen ke změně fakturačního vztahu pro předplatná Azure, která uživatel vybere.

### <a name="disabled-subscriptions"></a>Zakázaná předplatná

Zakázaná předplatná není možné převádět. Aby bylo možné převádět vlastnictví fakturace, musí být předplatná v aktivním stavu.

### <a name="azure-resources-transfer"></a>Převod prostředků Azure

Převádí se všechny prostředky zahrnuté v předplatném, jako jsou virtuální počítače, disky a weby.

### <a name="azure-marketplace-products-transfer"></a>Převod produktů na Azure Marketplace

Produkty na Azure Marketplace se převádí společně s příslušnými předplatnými.

### <a name="azure-reservations-transfer"></a>Přenos rezervací Azure

Rezervace Azure se s předplatným automaticky nepřevádí. Pokud je chcete přesunout, [obraťte se na podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="access-to-azure-services"></a>Přístup ke službám Azure

Na přístup stávajících uživatelů, skupin nebo instančních objektů přidělených pomocí Azure RBAC (řízení přístupu na základě role)[../role-based-access-control/overview.md] nemá převod vliv.

### <a name="azure-support-plan"></a>plán podpory Azure

Podpora Azure se společně s předplatným nepřevádí. Pokud uživatel převede všechna předplatná Azure, požádejte ho o zrušení plánu podpory.

### <a name="charges-for-transferred-subscription"></a>Poplatky za převedené předplatné

Původní vlastník fakturace předplatných nese odpovědnost za veškeré poplatky, které byly zaznamenané až do chvíle, kdy došlo k dokončení převodu. Poplatky zaznamenané po převodu spadají do vašeho oddílu faktury. Některé poplatky také mohou vzniknout ještě před převodem, ale zaznamenají se až po něm. Ty se zobrazí ve vašem oddílu faktury.

### <a name="cancel-a-transfer-request"></a>Zrušení žádosti o převod

Žádost o převod můžete zrušit až do chvíle, dokud ji uživatel neschválí nebo nezamítne. Zrušíte ji tak, že přejdete na [stránku s podrobnostmi o převodu](#check-the-transfer-request-status) a ve spodní části vyberete možnost Zrušit.

### <a name="software-as-a-service-saas-transfer"></a>Převod produktů SaaS (Software jako služba)

Produkty SaaS se společně s předplatným nepřevádí. Pokud chcete vlastnictví fakturace produktů SaaS převést, požádejte uživatele, aby [kontaktoval podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Uživatel může současně s vlastnictvím fakturace převést i vlastnictví prostředků. To vám umožní provádět operace správy, jako je odstraňování a prohlížení podrobností o produktu. Aby bylo možné toto vlastnictví převést, musí být uživatel vlastníkem prostředků pro daný produkt SaaS.

## <a name="check-for-access"></a>Ověření přístupu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

- Vlastnictví fakturace předplatných Azure se převede do vašeho oddílu faktury. Poplatky za tato předplatná můžete sledovat na webu [Azure Portal](https://portal.azure.com).
- Oprávnění k zobrazení a správě fakturace těchto předplatných můžete udělit i dalším uživatelům. Podrobnosti najdete v části [Role a úlohy související s oddíly faktury](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
