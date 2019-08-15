---
title: Získání vlastnictví fakturace předplatných Azure
description: Naučte se požadovat vlastnictví fakturace předplatných Azure od jiných uživatelů.
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
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019562"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Získání vlastnictví fakturace předplatných Azure z jiných účtů

Je možné převzít vlastnictví předplatných Azure, pokud existující vlastník fakturace opouští vaši organizaci, nebo chcete platit za předplatné prostřednictvím fakturačního účtu. Převzetí vlastnictví přenáší zodpovědnosti vyúčtování předplatných k vašemu účtu.

Tento článek se týká fakturačního účtu smlouvy Microsoft Customer Agreement. [Ověřte, jestli máte přístup k smlouvě o zákaznících Microsoftu](#check-for-access).

Chcete-li požádat o vlastnictví fakturace, musíte být přispěvatelem **oddílu faktury** nebo **přispěvatelem oddílu fakturace**. Další informace najdete v tématu [role oddílu fakturace – úlohy](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Požádat o vlastnictví fakturace

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako vlastník nebo přispěvatel oddílu faktury pro fakturační účet smlouvy Microsoft Customer Agreement.

2. Vyhledejte **cost management a fakturaci**.

   ![Snímek obrazovky zobrazující Azure Portal hledání cost management + fakturace](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Na levé straně vyberte **oddíly faktury** . V závislosti na vašem přístupu možná budete muset vybrat fakturační účet nebo Fakturační profil. Z fakturačního účtu nebo profilu vyberte **oddíly faktury**.
   
   ![Snímek obrazovky, který ukazuje výběr sekcí faktury](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. V seznamu vyberte část faktury. Jakmile převezmete vlastnictví předplatných, budou se fakturovat do této části.

5. V levé dolní části vyberte **požadavky na přenos** a pak vyberte **Přidat**.
 
   ![Snímek obrazovky zobrazující výběr požadavků na přenos](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Zadejte e-mailovou adresu uživatele, ze kterého požadujete vlastnictví fakturace. Uživatel musí být správcem účtu na fakturačním účtu programu Microsoft Online Service nebo na smlouva Enterprise vlastník účtu. Další informace najdete v tématu [zobrazení fakturačních účtů v Azure Portal](billing-view-all-accounts.md). Vyberte **Odeslat požadavek na přenos**.

   ![Snímek obrazovky, který ukazuje odeslání žádosti o přenos](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. Uživatel dostane e-mail s pokyny ke kontrole žádosti o přenos.

   ![Snímek obrazovky zobrazující e-mail s žádostí o přenos](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Pokud chcete žádost o přenos schválit, uživatel vybere odkaz v e-mailu a postupuje podle pokynů.

    ![Snímek obrazovky zobrazující e-mail s žádostí o přenos](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Zkontroluje stav žádosti o přenos.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **cost management a fakturaci**.

   ![Snímek obrazovky zobrazující Azure Portal hledání cost management + fakturace](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Na levé straně vyberte **oddíly faktury** . V závislosti na vašem přístupu možná budete muset vybrat fakturační účet nebo Fakturační profil. Z fakturačního účtu nebo profilu vyberte **oddíly faktury**.
   
   ![Snímek obrazovky, který ukazuje výběr sekcí faktury](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. V seznamu, pro který jste odeslali žádost o přenos, vyberte část faktura.

5. Vyberte **požadavky na přenos** z levé dolní části. Na stránce požadavky na přenos se zobrazí následující informace:

    ![Snímek obrazovky, který zobrazuje seznam požadavků na přenos](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Sloupec|Definice|
   |---------|---------|
   |Datum žádosti|Datum odeslání žádosti o přenos|
   |Příjemce|E-mailová adresa uživatele, kterému jste odeslali žádost o přenos vlastnictví fakturace|
   |Datum konce platnosti|Datum vypršení platnosti žádosti|
   |Stav|Stav žádosti o přenos|

    Požadavek na přenos může mít jeden z následujících stavů:

   |Stav|Definice|
   |---------|---------|
   |Probíhá zpracování|Uživatel nepřijal žádost o přenos.|
   |Zpracování|Uživatel schválil požadavek na přenos. Fakturace pro předplatná, která uživatel vybral, se přenáší do oddílu faktury|
   |Dokončeno| Faktura za předplatná, která uživatel vybral, se přenese do oddílu faktury.|
   |Dokončeno s chybami|Žádost se dokončila, ale fakturace pro některá předplatná, která se uživatel vybral, se nedal přenést.|
   |Vypršela platnost|Uživatel nepřijal žádost v čase a její platnost vypršela.|
   |Zrušeno|Někdo s přístupem k žádosti o přenos zrušil žádost.|
   |Odmítnuto|Uživatel odmítl žádost o přenos.|

7. Vyberte žádost o přenos pro zobrazení podrobností. Na stránce Podrobnosti o přenosu se zobrazí následující informace:
   
   ![Snímek obrazovky, který zobrazuje seznam přenesených předplatných](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Sloupec  |Definice|
   |---------|---------|
   |ID žádosti o přenos|Jedinečné ID žádosti o přenos Pokud odešlete žádost o podporu, nasdílejte ID s podporou Azure a urychlíte žádost o podporu.|
   |Požadován přenos|Datum odeslání žádosti o přenos|
   |Přenos vyžádaný|E-mailová adresa uživatele, který odeslal požadavek na přenos|
   |Vyprší platnost žádosti o přenos.| Datum, kdy vyprší platnost žádosti o přenos|
   |E-mailová adresa příjemce|E-mailová adresa uživatele, kterému jste odeslali žádost o přenos vlastnictví fakturace|
   |Odkaz na přenos odeslaný příjemci|Adresa URL, která byla odeslána uživateli za účelem kontroly žádosti o přenos|

## <a name="supported-subscription-types"></a>Podporované typy předplatného

Můžete si vyžádat vlastnictví fakturace pro níže uvedené typy předplatného.

- [Akční balíček](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure v rámci licenčního programu Open License](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass sponzorství](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Bezplatná zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure sponzorované nabídky](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Smlouva Microsoft Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Předplatitelé Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Předplatitelé Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise předplatitelé](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional předplatitelé](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\*Libovolný kredit dostupný v rámci předplatného nebude po převodu k dispozici v novém účtu.

\*\*Podporováno pouze pro odběry v účtech, které byly vytvořeny během registrace na webu Azure.


## <a name="additional-information"></a>Další informace

V následující části najdete další informace o přenosu předplatných.

### <a name="no-service-downtime"></a>Žádné výpadky služeb

Služby Azure v předplatném průběžně fungují bez přerušení. Provedeme převod pouze fakturačních vztahů pro předplatná Azure, která uživatel vybere k přenosu.

### <a name="disabled-subscriptions"></a>Zakázaná předplatná

Zakázaná předplatná nejde přenést. Aby bylo možné přenášet vlastnictví fakturace, musí být odběry v aktivním stavu.

### <a name="azure-resources-transfer"></a>Přenos prostředků Azure

Všechny prostředky z předplatných, jako jsou virtuální počítače, disky a přenosy z websites.

### <a name="azure-marketplace-products-transfer"></a>Přenos Azure Marketplace produktů

Azure Marketplace produktů se přenáší spolu s příslušnými předplatnými.

### <a name="azure-reservations-transfer"></a>Azure Reservations přenos

Azure Reservations se automaticky nepřesouvá s předplatnými. Pokud chcete přesunout rezervace, [kontaktujte podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

### <a name="access-to-azure-services"></a>Přístup ke službám Azure

Přístup pro existující uživatele, skupiny nebo instanční objekty, které byly přiřazeny pomocí služby (řízení přístupu na základě role) (Azure RBAC) [... /Role-based-Access-Control/Overview.MD] není ovlivněna během přechodu.

### <a name="azure-support-plan"></a>plán podpory Azure

Podpora Azure se nepřenáší s předplatnými. Pokud uživatel přenáší všechna předplatná Azure, požádejte ho, aby zrušil svůj plán podpory.

### <a name="charges-for-transferred-subscription"></a>Poplatky za přenesené předplatné

Původní vlastník fakturace předplatných je zodpovědný za všechny poplatky, které byly hlášeny až do okamžiku, kdy se přenos dokončil. Část vaší faktury zodpovídá za poplatky hlášené od okamžiku přenosu. Může dojít k nějakým poplatkům, které byly provedeny před přenosem, ale následně byly hlášeny. Tyto poplatky se zobrazí v části faktury.

### <a name="cancel-a-transfer-request"></a>Zrušení žádosti o přenos

Požadavek na přenos můžete zrušit, dokud není požadavek schválen nebo odmítnut. Žádost o přenos zrušíte tak, že přejdete na [stránku podrobnosti o přenosu](#check-the-transfer-request-status) a v dolní části stránky vyberete zrušit.

### <a name="software-as-a-service-saas-transfer"></a>Přenos softwaru jako služby (SaaS)

SaaS Products se nepřenáší s předplatnými. Požádejte uživatele, aby [kontaktoval podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby přenesl vlastnictví fakturace SaaS produktů. Společně s vlastnictvím fakturace může uživatel také přenášet vlastnictví prostředků. Vlastnictví prostředků umožňuje provádět operace správy, jako je odstranění a zobrazení podrobností produktu. Aby bylo možné přenést vlastnictví prostředku, musí být uživatel vlastníkem prostředku na SaaS produktu.

## <a name="check-for-access"></a>Vyhledat přístup
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktujte podporu

Pokud potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli rychle vyřešit problém.

## <a name="next-steps"></a>Další postup

- Vlastnictví fakturace předplatných Azure se přenese do oddílu faktury. Sledujte poplatky za tato předplatná v [Azure Portal](https://portal.azure.com).
- Poskytněte ostatním oprávnění k zobrazení a správě fakturace pro tyto odběry. Další informace najdete v tématu [role a úlohy oddílu fakturace](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
