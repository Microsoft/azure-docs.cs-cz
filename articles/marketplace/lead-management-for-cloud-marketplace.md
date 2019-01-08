---
title: Vedoucí správy pro cloudové Tržiště | Azure Marketplace a AppSource
description: Přehled různá témata související s publikováním nabídky a technických artefaktů na Azure Marketplace a AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: Marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/05/2018
ms.author: yijenj
ms.openlocfilehash: abaad7eabf92b6f3f53d7807af132959533552b9
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076956"
---
# <a name="lead-management-for-cloud-marketplace"></a>Vedoucí správy pro cloudové Tržiště


Zákazníci mají center jakoukoli firmu dobré. Obchodníci transformace dnešní produktu akvizice, nutné zaměřit se na připojení přímo se zákazníky a vytvářet relace. To je důvod, proč generování vysoce kvalitní zájemce je důležité nástroj pro prodejního cyklu. Po zobrazení vaší nabídky v [portál partnerů cloudu](https://cloudpartner.azure.com/), nejsou povolené jak prostřednictvím kódu programu dostávat kontaktní údaje zákazníků, ihned po vyjadřuje zájmu zákazníka nebo nasadí váš produkt v nástroje Marketplace. 



## <a name="what-are-leads-in-the-marketplace"></a>Co jsou vede na webu Marketplace?

Potenciální zákazníky jsou od zákazníků, kteří chtějí nebo nasazení produktů z webu Marketplace. Zda váš produkt je uvedená na webu Azure Marketplace nebo AppSource, bude moci přijímat potenciálních zákazníků od zákazníků až to nastavíte správně z CRM pro vaše listing(s) v portál Cloud Partner 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Jak se připojit váš systém CRM s portálem cloud partner

Pokud chcete začít, získávání potenciálních zákazníků, je vést Management connector na portál Cloud Partner navržený tak, aby ho můžete jednoduše připojit ke pomocí údajů CRM na seznam systému CRM, které jsou k dispozici. Teď dokáže snadno využít zájemců vygenerovaný na webu marketplace bez významných technického úsilí pro integraci s externím systémem.

Tady je podrobný návod, jak se připojit všech možných potenciálních zákazníků cíle:

**Dynamics CRM Online** - [kliknutím sem](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) pokyny o tom, jak nakonfigurovat pro získání zájemců Dynamics CRM Online.

**Marketo** - [kliknutím sem](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) se zobrazí pokyny pro nastavení konfigurace služby Marketo vést k získání potenciálních zákazníků.

**Salesforce** - [kliknutím sem](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) se zobrazí pokyny pro nastavení vaše instance Salesforce pro získání potenciálních zákazníků.

**Azure Table** – [kliknutím sem](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) se zobrazí pokyny pro nastavení vašeho účtu úložiště Azure pro získávání potenciálních zákazníků v Azure table.

**Koncový bod HTTPS** – [kliknutím sem](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) se zobrazí pokyny pro nastavení vašeho koncového bodu Https pro získání potenciálních zákazníků.

Jakmile správně nakonfigurovali cíl potenciálních zákazníků a jste nedosáhli publikování vaší nabídky, budeme ověření připojení a odeslat testovací zájemce. Při zobrazení nabídky předtím, než můžete vysílat živě, můžete také otestovat připojení zájemce pokusu o získání nabídky v prostředí ve verzi preview. Je důležité, abyste měli jistotu, že vašeho pobytu nastavení zájemce aktuální tak, aby neztratili všechny potenciální zákazníky, proto se ujistěte, že je tato připojení aktualizovat pokaždé, když se něco změnilo na vaší straně.


### <a name="what-are-the-next-steps"></a>Jaké jsou další kroky?

Po technické nastavení je na místě, by měl obsahovat těchto potenciálních zákazníků na aktuální prodejní a marketingové strategie a provozních procesů. Snažíme se chtějí lépe porozumět procesu celkové prodeje a chcete úzce spolupracovat s vámi na poskytování vysoce kvalitní zájemce a dostatek dat, které vám zajistí úspěch. Vítáme váš názor na jsme způsob optimalizace a Vylepšete si potenciální zákazníci, který jsme vám poslali s dalšími daty, abyste se mohli úspěšně tito zákazníci. Dejte nám vědět, pokud vás zajímá poskytuje zpětná vazba a návrhy pro prodejní tým mohl být větších úspěchů s Marketplace vede umožní.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Běžné chyby konfigurace potenciálních zákazníků při publikování na portál cloud partner 

**Nepovedlo se uložit zájemce do Dynamics CRM. Zkontrolujte nastavení účtu Dynamics CRM. LastCRMError: Nepovedlo se přihlásit do aplikace Dynamics CRM, LastCRMException:** 

> Pokud jste vybrali ověřování O365, zkontrolujte, jestli je platný uživatelský účet a heslo. Pokud jste vybrali AAD, zaškrtněte, pokud ID tenanta, ID aplikace a tajný klíč odpovídá aplikace co byla nastavena na AAD. Postupujte podle pokynů [tady](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics). Pokud uživatelské jméno/heslo účtu je platný, Zkontrolujte prosím, že má přístup k Dynamics 365 a má přiřazené licence (kroky 11-15, pokud používáte-li uživatel aplikace Office pomocí Azure Active Directory nebo nastavení zabezpečení). 

 
**Nepovedlo se uložit zájemce do Dynamics CRM. Uživatel oprávnění vytvářet pro atribut leadsourcecode v entitě potenciálních zákazníků** 

> Uživatel nebo aplikace chybí role zabezpečení, které chcete zapisovače webu Microsoft Marketplace potenciálních zákazníků. Postupujte podle kroků 11-15, pokud používáte Azure Active Directory nebo nastavení zabezpečení při použití uživatele s Office [tady](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics).

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: Klient nebyl nalezen. Tato instance může dojít, pokud nejsou žádná aktivní předplatná pro příslušného tenanta.**  

> Id adresáře, které jsou k dispozici v části Správa potenciálních zákazníků není platný adresář. Podrobnosti získání Id adresáře podle pokynů v kroku 2 (v části Azure Active Directory, z [zde](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Nepovedlo se uložit zájemce do Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser se nezdařilo - žádné role je přiřazená uživateli.**  

> Řešení: Přiřazení role zabezpečení na webu Microsoft Marketplace zájemce zapisovače. Postupujte podle pokynů [tady](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) v části Nastavení zabezpečení 

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: Aplikace s identifikátorem nebyla nalezena v adresáři** 

> Id aplikace poskytnuté v části Správa potenciálních zákazníků není platný adresář. Podrobnosti získání Id adresáře podle pokynů v kroku 8 (v části Azure Active Directory, z [tady](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: Identifikátor tenanta požadovaný není formát platných a není externí domény** 

> Id adresáře, které jsou k dispozici v části Správa potenciálních zákazníků není platný adresář. Podrobnosti získání Id adresáře podle pokynů v kroku 2 (v části Azure Active Directory, z [tady](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: Chyba při ověřování přihlašovacích údajů.: Je k dispozici tajný kód klienta je neplatný.** 

> Řešení: Přihlaste se k webu Azure Portal, zkontrolujte, jestli klíč aplikace se shoduje s co je portál partnerů cloudu. Vygenerujte heslo podle pokynů v kroku 10 (v části Azure Active Directory), z [tady](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Nepovedlo se uložit zájemce do Dynamics CRM. LastCRMError: Kanálu požadavku skončila platnost při čekání na odpověď po 00:02:00. Zvyšte hodnotu časového limitu předanou volání požadavku nebo zvyšte hodnotu SendTimeout na vazbě. Čas přidělený této operaci pravděpodobně částí delšího časového limitu.**  

> Řešení: Přihlaste se na portál Cloud Partner, prodejních míst v podrobnostech o >> zájemce cílové >> adresy URL, zkontrolujte, jestli je platnou instanci CRM dynamické

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Co jsou zájemci a proč jsou pro mě důležité jako vydavatele na webu Marketplace?** 

Potenciální zákazníci jsou zákazníci, kteří jsou nasazení produktů z webu Marketplace. Zda váš produkt je uvedená na [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) nebo [AppSource](https://appsource.microsoft.com/), bude moci přijímat zájemců zákazníky, kteří mají zájem o produktu, pokud mají nastavení cílové zájemce vaší nabídky.  


**Kde získám pomoc při nastavování mé určení potenciálních zákazníků?** 

Najdete zde dokumentaci: [Získání potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) nebo odeslat lístek podpory prostřednictvím aka.ms/marketplacepublishersupport vyberte nabídku typu a vést správy. 



**Je nutné nakonfigurovat cíl vedoucí k publikování nabídky na webu Marketplace?**

Ano, pokud publikujete aplikaci SaaS Kontaktujte mě, nebo Consulting Services.  
 


**Jak můžete ověřit správnost konfigurace potenciálních zákazníků?**

Po nastavení svoji nabídku a určení potenciálních zákazníků, publikování vaší nabídky. V kroku ověřování zájemce odešle Marketplace testu nový zájemce zájemce cíl nakonfigurovaný ve vaší nabídce. 


**Jak zjistím zájemce testu?**


Hledání pro "MSFT_TEST" v cíl potenciálních zákazníků, tady je ukázka testovací zájemce data: 

Společnost = MSFT_TEST_636573304831318844 

země = US 

Popis = MSFT_TEST_636573304831318844 

e-mailu = MSFT_TEST_636573304831318844@test.com

kódování UTF-8 = 

kódování UTF-8 = 

křestní_jméno = MSFT_TEST_636573304831318844 

Příjmení = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844 MSFT_TEST_636573304831318844 |<Offer Name> 

identifikátor objektu = 00Do0000000ZHog 

Telefon = 1234567890 

Název = MSFT_TEST_636573304831318844 

 

**Mám živé nabídky, ale mi nezobrazují všechny potenciální zákazníky?**

Jednotlivé potenciální zákazníky budou mít data předaná do polí v cíl vybraného zájemce, budou přicházet potenciální zákazníky v tomto formátu: **Zdroj akce | Nabídka** 

  *Zdroje:*

    “AzureMarketplace”, 
    “AzurePortal”, 
    “TestDrive”,  
    “SPZA” (acronym for AppSource) 

  *Akce:*

    “INS” – Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    “PLT” – Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    “DNC” – Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    “Create” – This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    “StartTestDrive” – This is for Test Drives only and is whenever a customer starts their test drive. 


  *Nabízí:*

    “checkpoint.check-point-r77-10sg-byol”, 
    “bitnami.openedxcypress”, 
    “docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a” 

 

  *Tady je ukázková data, informace o zákaznících*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Přečtěte si informace v části [vést informace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). 


**Konfiguroval jsem objektů BLOB v Azure jako cíl Moje potenciálních zákazníků, proč se mi nezobrazují zájemce?** 

Zájemce získá zapsat pouze při výběru úložiště Azure BLOB storage jako cíl potenciálních zákazníků. Přepnout do tabulek v Azure pro příjem zájemce reálném čase 


**E-mailu zobrazila se mi z webu Marketplace, proč nelze najít zájemce v mé CRM?**  

Je možné, že je koncový uživatel e-mailovou doménu z .edu. Z důvodů ochrany osobních údajů jsme z domény .edu nepředávejte data identifikovatelné osobní údaje. Vyplňte lístek podpory prostřednictvím aka.ms/marketplacepublishersupport 


 **Konfiguroval jsem tabulky/Azure BLOB Azure jako cíl Moje potenciálních zákazníků, jak lze zobrazit potenciální zákazníky?** 

Se dá dostat objektů blob nebo tabulek z webu Azure Portal nebo si můžete stáhnout a nainstalovat [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zdarma, chcete-li zobrazit svůj účet úložiště Azure tabulek/objektů BLOB. 


**Konfiguroval jsem Azure Table jako cíl Moje potenciálních zákazníků, může mohu dostat oznámení nového zájemce odesílají Marketplace?** 

Ano, postupujte podle pokynů k nastavení až Azure Table a funkce na dokumentaci [tady](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table). 



**Konfiguroval jsem Salesforce jako cíl Moje potenciálních zákazníků, proč nelze najít potenciální zákazníky?** 

Zkontrolujte, jestli webové vůdčí formuláře je povinné pole založené na rozevírací seznam. Pokud ano, přepněte na pole s textem nepovinné pole.  
 

**Došlo k nějakému problému s cílem Moje potenciálních zákazníků a jsem vynechalo některé potenciálních zákazníků. Je možné mít je mi odesílat e-mailem?** 

Z důvodu zásad identifikovatelné osobní údaje (soukromé údaje) jsme nelze sdílet informace o zájemcích nezabezpečené e-mailem. 



**Konfiguroval jsem, Azure Storage (tabulek/objektů BLOB) jako cíl Moje potenciálních zákazníků, kolik vás to bude stát?** 

Vedoucí obecné data jsou s nízkou (< 1 GB pro téměř všechny zdroje). Náklady závisí na počet zájemců přijme, pokud jsou v daném měsíci 1 000 zájemců, nás stojí přibližně 50 centů. 
