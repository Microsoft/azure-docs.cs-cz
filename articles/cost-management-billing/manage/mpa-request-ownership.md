---
title: Získání vlastnictví fakturace předplatných Azure pro smlouvu s partnerem Microsoftu (MPA)
description: Přečtěte si, jak požádat ostatní uživatele o udělení vlastnictví fakturace předplatných Azure pro Smlouvu s partnerem Microsoftu (MPA).
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: bb2a58b76445f2cfd14a905de1fb66dbff3eee7e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371010"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Získání vlastnictví fakturace předplatných Azure pro vlastní účet se smlouvou MPA

Aby bylo možné nabídnout jednu kombinovanou fakturu za spravované služby a využití Azure, poskytovatel CSP (Cloud Solution Provider) může převzít vlastnictví fakturace předplatných Azure od svých zákazníků se smlouvou Direct Enterprise (EA).

Tato funkce je k dispozici pouze pro partnery CSP s přímým vyúčtováním a certifikací [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp). Vztahují se na ni zásady správného řízení a zásady Azure a u určitých zákazníků může vyžadovat přezkoumání a schválení.

Abyste si mohli vyžádat vlastnictví fakturace, musíte mít roli **Globální správce** nebo **Agent správy**. Další informace najdete v tématu [Partnerské centrum – Přiřazování uživatelských rolí a oprávnění](https://docs.microsoft.com/partner-center/permissions-overview).

Tento článek se týká fakturačních účtů pro smlouvu s partnerem Microsoftu. Tyto účty se vytváří pro poskytovatele CSP (Cloud Solution Provider), aby mohli spravovat fakturaci za své zákazníky v novém komerčním prostředí. Nové prostředí je k dispozici pouze pro partnery s alespoň jedním zákazníkem, který přijal smlouvu se zákazníkem Microsoftu (MCA) a má plán Azure. [Ověřte si, jestli máte přístup ke smlouvě s partnerem Microsoftu](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Požadavky

1. Navažte se zákazníkem [vztah na úrovni prodejce](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer). Projděte si [přehled regionální autorizace CSP](https://docs.microsoft.com/partner-center/regional-authorization-overview) a zajistěte, aby tenanti zákazníka i partnera byli ve stejné autorizované oblasti.
1. [Ověřte, že zákazník přijal smlouvu se zákazníkem Microsoftu](https://docs.microsoft.com/partner-center/confirm-customer-agreement).
1. Nastavte pro zákazníka [plán Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan). Pokud zákazník nakupuje přes několik prodejců, je potřeba nastavit plán Azure pro každou kombinaci zákazníka a prodejce.

## <a name="request-billing-ownership"></a>Podání žádosti o vlastnictví fakturace

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů agenta správy CSP v tenantovi CSP.
1. Vyhledejte **Cost Management a fakturace**.  
    ![Snímek obrazovky znázorňující hledání položky Cost Management + Billing na webu Azure Portal za účelem odeslání žádosti o vlastnictví fakturace](./media/mpa-request-ownership/search-cmb.png)
1. Na levé straně vyberte **Zákazníci** a pak v seznamu vyberte zákazníka.  
    [![Screenshot znázorňující výběr zákazníků](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. Vlevo dole vyberte **Žádosti o převod** a pak vyberte **Přidat novou žádost**.  
    [![Screenshot znázorňující výběr možnosti Žádosti o převod](./media/mpa-request-ownership/mpa-select-transfer-requests.png)](./media/mpa-request-ownership/mpa-select-transfer-requests.png#lightbox)
1. Zadejte e-mailovou adresu uživatele v organizaci zákazníka, který obdrží žádost o převod. Tento uživatel musí být vlastníkem účtu ve smlouvě Enterprise. Vyberte možnost **Poslat žádost o převod**.  
    [![Screenshot znázorňující výběr možnosti Poslat žádost o převod](./media/mpa-request-ownership/mpa-send-transfer-requests.png)](./media/mpa-request-ownership/mpa-send-transfer-requests.png#lightbox)
1. Tento uživatel obdrží e-mail s pokyny ke kontrole vaší žádosti o převod.  
    ![Snímek obrazovky znázorňující e-mail pro kontrolu žádosti o převod](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)
1. Uživatel může žádost o převod schválit tím, že vybere odkaz v e-mailu a bude postupovat podle zobrazených pokynů.  
    [![Screenshot obrazovky znázorňující e-mail pro kontrolu žádosti o převod](./media/mpa-request-ownership/review-transfer-requests.png)](./media/mpa-request-ownership/review-transfer-requests.png#lightbox) Uživatel může vybrat fakturační účet, ze kterého chce převést produkty Azure. Po jeho výběru se zobrazí produkty, které je možné převést. **Poznámka:** Zablokovaná předplatná nejde převést a zobrazují se v seznamu Produkty Azure, které se nedají převést. Po dokončení výběru produktů Azure, které se mají převést, vyberte **Ověřit**.
1. V oblasti pro **výsledek ověření převodu** se zobrazí dopad produktů Azure, které se budou převádět. Možné stavy:
    * **Úspěšně dokončeno:** Ověření pro tento produkt Azure prošlo a produkt je možné převést.
    * **Upozornění:** Pro vybraný produkt Azure bylo vydáno upozornění. Přestože je možné tento produkt převést, bude to mít určitý dopad, o kterém by uživatel měl vědět pro případ, že by chtěl provést nějaké zmírňující akce. Příklad: Převáděné předplatné Azure s výhodou využívá RI. Po dokončení převodu už předplatné nebude tuto výhodu mít. Pro zajištění maximálních úspor zajistěte přidružení RI k jinému předplatnému, které může využívat jeho výhody. Uživatel se také může rozhodnout přejít zpátky na stránku výběru a zrušit výběr tohoto předplatného Azure.
    * **Failed:** Vybraný produkt Azure nejde převést kvůli chybě. Pokud uživatel chce převést ostatní vybrané produkty Azure, musí přejít zpátky na stránku výběru a zrušit výběr tohoto produktu.  
    ![Screenshot zobrazující prostředí ověřování](./media/mpa-request-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Kontrola stavu žádosti o převod

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.  
    ![Snímek obrazovky znázorňující hledání položky Cost Management + Billing na webu Azure Portal za účelem získání stavu převodu](./media/mpa-request-ownership/billing-search-cost-management-billing.png)
1. Na levé straně vyberte **Zákazníci**.  
    [![Screenshot znázorňující výběr zákazníků](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. V seznamu vyberte zákazníka, za kterého jste odeslali žádost o převod.
1. Vlevo dole vyberte **Žádosti o převod**. Zobrazí se stránka žádostí o převod, která obsahuje následující informace: [![Screenshot znázorňující seznam žádostí o převod](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png#lightbox)

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
   |Zpracování|Uživatel schválil žádost o převod. Fakturace předplatných, která uživatel vybral, se převádí na váš účet.|
   |Dokončeno| Fakturace předplatných, která uživatel vybral, se převedla na váš účet.|
   |Dokončeno s chybami|Žádost byla dokončena, ale fakturaci některých předplatných, která uživatel vybral, nebylo možné převést.|
   |Platnost vypršela|Uživatel nepřijal žádost včas a její platnost vypršela.|
   |Zrušeno|Žádost o převod byla zrušena některým z uživatelů, kteří k ní mají přístup.|
   |Odmítnuto|Žádost o převod byla uživatelem zamítnuta.|

1. Výběrem žádosti o převod zobrazte její podrobnosti. Zobrazí se stránka s podrobnostmi o převodu, která obsahuje následující informace: [![Screenshot znázorňující seznam převedených předplatných](./media/mpa-request-ownership/mpa-transfer-completed.png)](./media/mpa-request-ownership/mpa-transfer-completed.png#lightbox)

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

* [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
* [Smlouva Microsoft Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* Předplatné pro vývoj/testování musíte nejdřív prostřednictvím lístku podpory převést na nabídku EA Enterprise. Po převodu se bude předplatné Enterprise pro vývoj/testování účtovat pomocí sazeb za průběžné platby. Žádné slevy nabízené prostřednictvím nabídky Enterprise pro vývoj/testování v rámci smlouvy EA zákazníka nebudou partnerovi CSP k dispozici.

## <a name="additional-information"></a>Další informace

V této části najdete doplňkové informace o převodu předplatných.

### <a name="no-service-downtime"></a>Žádné výpadky služby

Služby Azure v předplatném poběží i nadále bez přerušení. Dochází jen ke změně fakturačního vztahu pro předplatná Azure, která uživatel vybere.

### <a name="disabled-subscriptions"></a>Zakázaná předplatná

Zakázaná předplatná není možné převádět. Aby bylo možné převádět vlastnictví fakturace, musí být předplatná v aktivním stavu.

### <a name="azure-resources-transfer"></a>Převod prostředků Azure

Převádí se všechny prostředky zahrnuté v předplatném, jako jsou virtuální počítače, disky a weby.

### <a name="azure-marketplace-products-transfer"></a>Převod produktů na Azure Marketplace

Produkty z Azure Marketplace dostupné pro předplatná spravovaná poskytovateli CSP (Cloud Solution Provider) se převedou spolu s odpovídajícími předplatnými. Předplatná obsahující produkty z Azure Marketplace, které nejsou povolené pro poskytovatele CSP, není možné převést.

### <a name="azure-reservations-transfer"></a>Přenos rezervací Azure

Rezervace Azure se s předplatným automaticky nepřevádí. Buď si můžete rezervace v EA nechat pro jiná předplatná, nebo [rezervace zrušte](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations) a partner si je může znovu koupit v CSP.

### <a name="access-to-azure-services"></a>Přístup ke službám Azure

Převod nebude mít vliv na přístup stávajících uživatelů, skupin nebo instančních objektů přidělených pomocí [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md). Partner k předplatným nezíská nový přístup Azure RBAC.

Pokud partneři chtějí získat přístup k předplatným, měli by se spojit se zákazníkem. Aby partneři mohli vytvářet lístky podpory, musí získat přístup k funkci [AOBO (Admin on Behalf Of)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) nebo ke službě [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider).

### <a name="azure-support-plan"></a>plán podpory Azure

Podpora Azure se společně s předplatným nepřevádí. Pokud uživatel převede všechna předplatná Azure, požádejte ho o zrušení plánu podpory. Po převodu za podporu zodpovídá partner CSP. Zákazník by se s jakýmikoli žádostmi o podporu měl obracet na partnera CSP.  

### <a name="charges-for-transferred-subscription"></a>Poplatky za převedené předplatné

Původní vlastník fakturace předplatných nese odpovědnost za veškeré poplatky, které byly zaznamenané až do chvíle, kdy došlo k dokončení převodu. Za poplatky zaznamenané po převodu zodpovídáte vy. Některé poplatky také mohou vzniknout ještě před převodem, ale zaznamenají se až po něm. Tyto poplatky se zobrazí na vaší faktuře.

### <a name="cancel-a-transfer-request"></a>Zrušení žádosti o převod

Žádost o převod můžete zrušit až do chvíle, dokud ji uživatel neschválí nebo nezamítne. Zrušíte ji tak, že přejdete na [stránku s podrobnostmi o převodu](#check-the-transfer-request-status) a ve spodní části vyberete možnost Zrušit.

### <a name="software-as-a-service-saas-transfer"></a>Převod produktů SaaS (Software jako služba)

Produkty SaaS se společně s předplatným nepřevádí. Pokud chcete vlastnictví fakturace produktů SaaS převést, požádejte uživatele, aby [kontaktoval podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Uživatel může současně s vlastnictvím fakturace převést i vlastnictví prostředků. To vám umožní provádět operace správy, jako je odstraňování a prohlížení podrobností o produktu. Aby bylo možné toto vlastnictví převést, musí být uživatel vlastníkem prostředků pro daný produkt SaaS.

### <a name="additional-approval-for-certain-customers"></a>Další schválení pro určité zákazníky

Některé žádosti zákazníků o převod můžou kvůli povaze aktuální struktury podnikové registrace zákazníka vyžadovat další proces kontrolu ze strany Microsoftu. Partnerům se zobrazí oznámení o takových požadavcích při pokusu o odeslání pozvánky uživatelům. Partneři musí během tohoto procesu kontroly spolupracovat se svým manažerem pro partnerský vývoj a se zákaznickým týmem zákazníka.

### <a name="azure-subscription-directory"></a>Adresář předplatných Azure

Adresář převáděných předplatných Azure musí odpovídat adresáři zákazníka vybranému při navazování vztahu CSP.

Pokud se tyto dva adresáře neshodují, předplatná není možné převést. Musíte buď navázat nový vztah se zákazníkem na úrovni prodejce CSP výběrem adresáře příslušných předplatných Azure, nebo změnit adresář předplatných Azure tak, aby odpovídal adresáři vztahu CSP se zákazníkem. Další informace najdete v tématu [Přidružení existujícího předplatného k adresáři Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory).

### <a name="ea-subscription-in-the-non-orgnization-directory"></a>Předplatné EA v adresáři mimo organizaci

Předplatná EA z adresářů mimo organizaci se dají přenášet v případě, že pro příslušný adresář existuje vztah prodejce s CSP. Pokud pro adresář neexistuje vztah prodejce, musíte zajistit, aby v adresáři byl uživatel organizace jako *globální správce*, který může přijímat partnerské vztahy. Část názvu domény v uživatelském jméně musí tvořit buď počáteční výchozí název domény [název domény]. onmicrosoft.com, nebo ověřený nefederovaný název vlastní domény, například contoso.com.  

Pokud chcete do adresáře přidat nového uživatele, přečtěte si téma [Rychlé zprovoznění: Přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Kontrola přístupu ke smlouvě s partnerem Microsoftu

[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

* Vlastnictví fakturace předplatných Azure se převede na vás. Poplatky za tato předplatná můžete sledovat na webu [Azure Portal](https://portal.azure.com).
* Pokud chcete získat přístup k převedeným předplatným Azure, spojte se se zákazníkem. [Přidání nebo odebrání přiřazení rolí Azure pomocí webu Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
