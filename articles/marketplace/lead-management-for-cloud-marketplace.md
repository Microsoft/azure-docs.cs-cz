---
title: Vedení pro komerční trh | Azure Marketplace a AppSource
description: Přehled různých témat souvisejících s publikováním nabídek a technických artefaktů na Azure Marketplace a AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 09b02d043e970c68cdff0e3dc4f97328c9d74c84
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383785"
---
# <a name="lead-management-for-commercial-marketplace"></a>Vedení pro komerční trh


Zákazníci jsou centrem každého dobrého podnikání. Při transformaci dnešních akvizic produktů se obchodníci musí zaměřit na přímé spojení se zákazníky a budování vztahů. To je důvod, proč generování vysoce kvalitních zájemců je důležitým nástrojem pro váš prodejní cyklus. Po uvedení nabídky v [Partnerském centru](https://partner.microsoft.com/)jsou k dispozici nástroje, které vám umožňují programově přijímat kontaktní informace zákazníků ihned poté, co zákazník projeví zájem nebo nasadí váš produkt na tržiště. 



## <a name="what-are-leads-in-the-marketplace"></a>Co jsou potenciální zákazníci na trhu?

Potenciální zákazníci pocházejí od zákazníků, kteří mají zájem nebo nasazují vaše produkty z webu Marketplace. Ať už je váš produkt uvedený na Azure Marketplace nebo AppSource, budete moct přijímat potenciální zákazníky, jakmile se správně nastaví z vašeho CRM do vašeho zápisu v Centru partnerů. 


## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Jak propojit crm systém s Partnerským centrem

Chcete-li začít získat zájemce, je konektor pro správu potenciálních zákazníků v Centru partnerů navržen tak, aby jej bylo možné snadno připojit k informacím aplikace CRM do seznamu dostupných systémů CRM. Nyní můžete snadno využít potenciální zákazníky generované na trhu bez významného technického úsilí o integraci s externím systémem.

Zde jsou podrobné pokyny, jak připojit jednotlivé možné cíle zájemců:

**Dynamics CRM Online** – Informace o konfiguraci aplikace Dynamics CRM Online najdete v [tématu Konfigurace správy zájemců pro dynamics 365 for Customer Engagement,](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) kde najdete pokyny, jak nakonfigurovat aplikaci Dynamics CRM Online pro získávání zájemců.

**Marketo** - Viz [konfigurace řízení potenciálních zákazníků v Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) pokyny k nastavení Marketo Lead Configuration získat potenciální zákazníky.

**Salesforce** – Pokyny k nastavení instance Salesforce pro získání zájemců najdete v tématu [Konfigurace správy zájemců pro Salesforce.](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

**Azure Table** – na [webu konfigurace správy potenciálních zákazníků pomocí tabulky Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) najdete pokyny k nastavení účtu úložiště Azure pro získání zájemců v tabulce Azure.

**Https Koncový bod** – viz [konfigurace správy zájemců pomocí koncového bodu HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) pokyny k nastavení https koncového bodu pro získání zájemců.

Jakmile správně nakonfigurujete cíl zájemce a najdete v nabídce možnost Publikovat, ověříme připojení a zašleme vám testovacího zájemce. Když si nabídku prohlížíte před tím, než začnete žít, můžete také otestovat připojení zájemců tím, že se pokusíte získat nabídku sami v prostředí náhledu. Je důležité zajistit, aby nastavení zájemců zůstalo aktuální, abyste nepřišli o žádné potenciální zákazníky, proto se ujistěte, že tato připojení aktualizujete vždy, když se na vaší straně něco změní.

### <a name="what-are-the-next-steps"></a>Jaké jsou další kroky?

Jakmile je technické nastavení zavedeno, měli byste tyto potenciální zákazníky začlenit do aktuální prodejní & marketingové strategie a provozních procesů. Máme zájem lépe porozumět vašemu celkovému prodejnímu procesu a chceme s vámi úzce spolupracovat na poskytování vysoce kvalitních potenciálních zákazníků a dostatečného množství dat, abyste byli úspěšní. Uvítáme vaši zpětnou vazbu o tom, jak můžeme optimalizovat a vylepšovat potenciální zákazníky, které vám posíláme, s dalšími údaji, které pomohou těmto zákazníkům uspět. Dejte nám vědět, pokud máte zájem o poskytnutí zpětné vazby a návrhů, které vašemu prodejnímu týmu umožní být úspěšnější s zájemci marketplace.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a>Běžné chyby konfigurace zájemců během publikování v Centru partnerů

**Zájemce nelze uložit do aplikace Dynamics CRM. Zkontrolujte nastavení účtu Dynamics CRM. LastCRMError: Nelze se přihlásit k aplikaci Dynamics CRM, LastCRMException:** 

> Pokud bylo vybráno ověřování O365, zkontrolujte, zda je uživatelský účet a heslo platné. Pokud byl vybrán AAD, zkontrolujte, zda ID klienta, ID aplikace a tajný klíč aplikace odpovídá tomu, co bylo nastaveno na AAD. Postupujte podle pokynů [zde](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Pokud je uživatelské jméno/heslo účtu platné, ujistěte se, že má přístup k Dynamics 365 a má přiřazenou licenci (kroky 11–15, pokud používáte Azure Active Directory nebo Nastavení zabezpečení, pokud používáte uživatele Office). 

**Zájemce nelze uložit do aplikace Dynamics CRM. Uživatel nemá oprávnění k vytvoření atributu leadsourcecode v entitě zájemce.** 

> V aplikaci nebo uživateli chybí role zabezpečení pro autora hlavního autora webu Microsoft Marketplace. Pokud používáte Azure Active Directory nebo Nastavení zabezpečení, pokud používáte uživatele Office tady , postupujte [podle](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)kroků 11-15 .

**Zájemce nelze uložit do aplikace Dynamics CRM pomocí služby AAD. Výjimka:: Tenant nebyl nalezen. Tato instance může dojít, pokud neexistují žádné aktivní odběry pro klienta.**  

> ID adresáře uvedené v části správa zájemců není platným adresářem. Získejte ID adresáře na základě pokynů v kroku 2 (v části Azure Active Directory, [odtud](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Zájemce nelze uložit do aplikace Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser se nezdařilo - uživateli nejsou přiřazeny žádné role.**  

> Řešení: Přiřazení role zabezpečení autorovi hlavního autora webu Microsoft Marketplace. Postupujte podle pokynů [zde v](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) části Nastavení zabezpečení.

**Zájemce nelze uložit do aplikace Dynamics CRM pomocí služby AAD. Výjimka:: Aplikace s identifikátorem nebyla v adresáři nalezena.** 

> ID aplikace uvedené v části správa zájemců není platným adresářem. Získejte ID adresáře na základě pokynů v kroku 8 (v části Azure Active Directory, [odtud).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Zájemce nelze uložit do aplikace Dynamics CRM pomocí služby AAD. Výjimka:: Požadovaný identifikátor klienta není platný a není platný formát externí domény.** 

> ID adresáře uvedené v části správa zájemců není platným adresářem. Získejte ID adresáře na základě pokynů v kroku 2 (v části Azure Active Directory, [odtud).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Zájemce nelze uložit do aplikace Dynamics CRM pomocí služby AAD. Výjimka:: Chyba ověřování pověření.: Je k dispozici neplatný tajný klíč klienta.** 

> Řešení: Přihlaste se k portálu Azure, zkontrolujte, jestli klíč aplikace odpovídá tomu, co je v Centru partnerů. Vygenerujte heslo na základě instrukce v kroku 10 (v části Azure Active Directory), [odtud](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Zájemce nelze uložit do aplikace Dynamics CRM. LastCRMError: Časový čas kanálu požadavku při čekání na odpověď po 00:02:00. Zvyšte hodnotu časového omezení předanou volání request nebo zvyšte hodnotu SendTimeout na vazbě. Čas přidělený této operaci mohl být částí delšího časového času.**  

> Řešení: Přihlaste se do Centra partnerů, zkontrolujte podrobnosti >> o cíl hlavního >> URL, zkontrolujte, zda se jedná o platnou instanci dynamického CRM.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Co jsou potenciální zákazníci a proč jsou pro mě jako vydavatele na Marketplace důležití?** 

Zájemci jsou zákazníci, kteří nasazují vaše produkty z Webu Marketplace. Ať už je váš produkt uvedený na [Azure Marketplace](https://azuremarketplace.microsoft.com) nebo [AppSource](https://appsource.microsoft.com/), budete moct přijímat potenciální zákazníky, kteří mají o váš produkt zájem, pokud máte v nabídce nastavencíl pro potenciální zákazníky.  

**Kde mohu získat pomoc s nastavením cíle pro potenciální zákazníky?** 

Dokumentaci naleznete zde: [Získejte zájemce zákazníků](./partner-center-portal/commercial-marketplace-get-customer-leads.md) nebo odešlete lístek podpory prostřednictvím aka.ms/marketplacepublishersupport vybrat typ nabídky a správu zájemců. 

**Musím nakonfigurovat cíl zájemce, abych mohl publikovat nabídku na Marketplace?**

Ano, pokud publikujete aplikaci Contact Me SaaS nebo poradenské služby.  
 
**Jak mohu potvrdit, že konfigurace zájemce je správná?**

Po nastavení nabídky a hlavního cíle nabídku publikujte. V kroku ověření zájemce odešle Marketplace testovacího zájemce do cíle zájemce nakonfigurovaného ve vaší nabídce. 

**Jak najdu testovacího stopovace?**

Vyhledejte "MSFT_TEST" v cílové destinaci zájemce, tady jsou ukázková testovací data zájemce: 

společnost = MSFT_TEST_636573304831318844 

země = USA 

popis = MSFT_TEST_636573304831318844 

e-mail =MSFT_TEST_636573304831318844@test.com

kódování = UTF-8 

kódování = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844| \<Název nabídky> 

oid = 00Do000000ZHog 

telefon = 1234567890 

název = MSFT_TEST_636573304831318844 

**Mám živou nabídku, ale nevidím žádné stopy?**

Každý zájemce bude mít data předaná v polích ve vybraném cíli zájemce, zájemci budou mít v tomto formátu: **Zdrojová akce| Nabídka** 

  *Zdrojů:*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Akce:*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Nabízí:*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Zde jsou ukázková data informací o zákaznících*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Další informace naleznete v části [Informace o potenciálních odkazech](./partner-center-portal/commercial-marketplace-get-customer-leads.md). 

**Nakonfiguroval jsem Azure BLOB jako cíl pro potenciální zákazníky, proč nevidím zájemce?** 

Zájemce se zapíše jenom když jako cíl zájemce vyberete úložiště Azure BLOB. Přepněte do tabulky Azure a získejte zájemce v reálném čase.

**Obdržel(a) jsem e-mail od marketplace, proč nemohu najít zájemce v crm?**  

Je možné, že e-mailová doména koncového uživatele pochází z .edu. Z důvodu ochrany osobních údajů nepředáváme osobní data z domény .edu. Odešlete [lístek podpory](https://aka.ms/marketplacepublishersupport).

**Nakonfiguroval jsem Azure Table/Azure BLOB jako cíl pro potenciální zákazníky, jak můžu zobrazit zájemce?** 

K objektu blob nebo tabulce můžete přistupovat z webu Azure Portal nebo si můžete zdarma stáhnout a nainstalovat [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) a zobrazit tabulky nebo objekty BLOB účtu úložiště Azure. 

**Nakonfiguroval jsem Azure Table jako cíl pro potenciální zákazníky, můžu dostat upozornění při každém odeslání nového zájemce marketplacem?** 

Ano, podle pokynů nastavte Azure Table + Function v dokumentaci [zde](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md). 

**Nakonfiguroval jsem salesforce jako cíl pro potenciální zákazníky, proč nelze zájemce najít?** 

Zkontrolujte, zda je formulář pro zájemce na webu povinným polem založeným na seznamu přivýběru. Pokud ano, přepněte toto pole na nepovinné textové pole.  
 
**Byl tam problém s mým hlavním cílem, a já jsem vynechal některé vede. Mohu si je nechat poslat e-mailem?** 

Vzhledem k zásadám ochrany osobních údajů nemůžeme sdílet informace o potenciálních zákaznících prostřednictvím nezabezpečených e-mailů. 

**Nakonfiguroval jsem Azure Storage (BLOB/Table) jako cíl pro potenciální zákazníky, kolik to bude stát?** 

Data olověné genje nízká (<1 GB pro téměř všechny vydavatele). Náklady budou záviset na počtu přijatých zájemců, pokud bude za měsíc přijato 1 000 zájemců, bude to stát kolem 50 centů. 
