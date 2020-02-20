---
title: Získání vlastnictví fakturace předplatných Azure
description: Přečtěte si, jak požádat ostatní uživatele o udělení vlastnictví fakturace předplatných Azure.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 10f1052f9acf9bf91c1d7fb0b64a1d3285487cf3
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200723"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Získání vlastnictví fakturace předplatných Azure od ostatních účtů

Převzetí vlastnictví předplatných Azure může být vhodné, pokud stávající vlastník fakturace odchází ze společnosti, nebo pokud chcete předplatná hradit ze svého fakturačního účtu. Po převzetí vlastnictví se odpovědnosti spojené s fakturací předplatných převedou na váš účet.

Tento článek se týká fakturačních účtů smluv se zákazníkem Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-for-access).

Pokud chcete požádat o vlastnictví fakturace, musíte být **vlastníkem oddílu faktur** nebo **přispěvatelem oddílu faktur**. Podrobnosti najdete v části [Role a úlohy související s oddíly faktur](understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Podání žádosti o vlastnictví fakturace

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako vlastník nebo přispěvatel oddílu faktury pro fakturační účet spojený se smlouvou se zákazníkem Microsoftu.

2. Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Správa nákladů a fakturace na webu Azure Portal](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Na stránce s rozsahy fakturace vyberte fakturační účet, který se má použít k platbě za využití předplatných. Tento fakturační účet by měl být typu **Smlouva se zákazníkem Microsoftu**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/mca-request-billing-ownership/list-of-scopes.png)

    > [!NOTE]
    >
    > Azure Portal si pamatuje poslední rozsah fakturace, který jste využili, a tento rozsah se zobrazí, když na stránku Správa nákladů a fakturace vrátíte příště. Pokud jste stránku Správa nákladů a fakturace navštívili dřív, stránka s rozsahy fakturace se vám nezobrazí. Pokud ano, zkontrolujte, že jste ve [správném rozsahu](#check-for-access). Pokud ne, [přepněte rozsah](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) a vyberte fakturační účet pro smlouvu se zákazníkem Microsoftu.

4. Na levé straně vyberte **Fakturační profily**.

    ![Snímek obrazovky znázorňující výběr fakturačních profilů](./media/mca-request-billing-ownership/mca-select-profiles.png)     

    > [!Note]
    >
    > Pokud se vám fakturační profily nezobrazují, nejste ve správném rozsahu fakturace. Musíte vybrat fakturační účet pro smlouvu se zákazníkem Microsoftu a potom vybrat Fakturační profily. Informace o postupu při změně rozsahu najdete v tématu věnovaném [přepínání rozsahů fakturace na webu Azure Portal](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. V seznamu vyberte **fakturační profil**. Jakmile převezmete vlastnictví předplatných, jejich využití se bude účtovat na tento fakturační profil.

6. Na levé straně vyberte **Oddíly faktury**.

    ![Snímek obrazovky znázorňující výběr možnosti Oddíly faktury](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)   

7. V seznamu vyberte oddíl faktury. Jakmile převezmete vlastnictví předplatných, jejich využití se přiřadí k tomuto oddílu faktury fakturačního profilu.

8. Vlevo dole vyberte **Žádosti o převod** a pak vyberte **Přidat novou žádost**.

    ![Snímek obrazovky znázorňující výběr možnosti Žádosti o převod](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)

9. Zadejte e-mailovou adresu uživatele, o jehož vlastnictví fakturace žádáte. Uživatel musí být správcem účtu pro fakturační účet programu služby Microsoft Online, nebo vlastníkem účtu souvisejícího se smlouvou Enterprise. Další informace najdete v tématu věnovaném [zobrazení fakturačních účtů na webu Azure Portal](view-all-accounts.md). Vyberte možnost **Poslat žádost o převod**.

    ![Snímek obrazovky znázorňující výběr možnosti Poslat žádost o převod](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)

10. Tento uživatel obdrží e-mail s pokyny ke kontrole vaší žádosti o převod.

    ![Snímek obrazovky znázorňující e-mail pro kontrolu žádosti o převod](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)

11. Uživatel může žádost o převod schválit tím, že vybere odkaz v e-mailu a bude postupovat podle zobrazených pokynů.

    ![Snímek obrazovky znázorňující e-mail pro kontrolu žádosti o převod](./media/mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Kontrola stavu žádosti o převod

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Správa nákladů a fakturace na webu Azure Portal](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Na stránce s fakturačními rozsahy vyberte fakturační účet, pro který se odeslala žádost o převod.

4. Na levé straně vyberte **Fakturační profily**.

    ![Snímek obrazovky znázorňující výběr fakturačních profilů](./media/mca-request-billing-ownership/mca-select-profiles.png)     

5. Vyberte **fakturační profil**, pro který se odeslala žádost o převod.

6. Na levé straně vyberte **Oddíly faktury**.

    ![Snímek obrazovky znázorňující výběr možnosti Oddíly faktury](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)   

7. V seznamu vyberte oddíl faktury, pro který se odeslala žádost o převod.

8. Vlevo dole vyberte **Žádosti o převod**. Zobrazí se stránka žádostí o převod, která obsahuje následující informace:

    ![Snímek obrazovky znázorňující seznam žádostí o převod](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

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

9. Výběrem žádosti o převod zobrazte její podrobnosti. Zobrazí se stránka s podrobnostmi o převodu, která obsahuje následující informace:

    ![Snímek obrazovky znázorňující seznam převedených předplatných](./media/mca-request-billing-ownership/mca-transfer-completed.png)

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
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

- Vlastnictví fakturace předplatných Azure se převede do vašeho oddílu faktury. Poplatky za tato předplatná můžete sledovat na webu [Azure Portal](https://portal.azure.com).
- Oprávnění k zobrazení a správě fakturace těchto předplatných můžete udělit i dalším uživatelům. Podrobnosti najdete v části [Role a úlohy související s oddíly faktury](understand-mca-roles.md#invoice-section-roles-and-tasks).
