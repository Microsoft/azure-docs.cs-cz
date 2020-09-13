---
title: Správa zájemců pro komerční tržiště | Azure Marketplace a AppSource
description: Přehled různých témat souvisejících s publikováním nabídek a technických artefaktů pro Azure Marketplace a AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/14/2020
ms.openlocfilehash: e3924e609091f4d0975901024f20db74efb6aac5
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032703"
---
# <a name="lead-management-for-the-commercial-marketplace"></a>Správa zájemců pro komerční tržiště

Zákazníci jsou centrem jakékoli dobré firmy. V transformaci dnešního nákupu produktů se pracovníky marketingu musí soustředit na přímé připojení k zákazníkům a vytváření relací. Z tohoto důvodu je generování vysoce kvalitních potenciálních zákazníků důležitým nástrojem pro váš prodejní cyklus. Po výpisu vaší nabídky v [partnerském centru](https://partner.microsoft.com/)jsou k dispozici nástroje, které vám umožní programově získat kontaktní informace zákazníka hned, co zákazník vyjádří zájem, nebo nasadí váš produkt na webu Marketplace. 

## <a name="what-are-leads-in-the-commercial-marketplace"></a>Co jsou zájemci na komerčním webu Marketplace?

Zájemci pocházejí od zákazníků, kteří si zajímá nebo nasazují vaše produkty z komerčního tržiště. Po připojení CRM k vašim výpisům v partnerském centru budete moci dostávat potenciální zákazníky Azure Marketplace a AppSource.

## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Jak připojit systém CRM k partnerskému centru

Konektor pro správu zájemců v partnerském centru je navržený tak, aby se mohl snadno připojit k informacím CRM do seznamu dostupných systémů CRM. Teď můžete snadno využít potenciální zákazníky vygenerované na webu Marketplace bez významného vývojového úsilí pro integraci s externím systémem.

Tady jsou podrobné pokyny, jak propojit jednotlivé možné cíle potenciálních zákazníků:

**Dynamics CRM Online** – informace o tom, jak nakonfigurovat Dynamics CRM online za účelem získání zájemců, najdete v tématu [Konfigurace služby Customer management for Dynamics 365 for Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) .

**Marketo** – informace o nastavení konfigurace potenciálních zákazníků služby Marketo pro získání zájemců najdete v tématu [Konfigurace správy zájemců ve Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) .

**Salesforce** – pokyny k nastavení instance Salesforce pro získání zájemců najdete v tématu [Konfigurace správy zájemců pro Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) .

**Tabulka Azure** – pokyny k nastavení účtu úložiště Azure pro získání zájemců v tabulce Azure najdete v tématu [Konfigurace správy zájemců pomocí tabulky Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) .

**Koncový bod HTTPS** – informace o nastavení koncového bodu HTTPS pro získání zájemců najdete v tématu [Konfigurace správy zájemců pomocí koncového bodu https](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) .

Po správném nakonfigurování cíle potenciálního zákazníka a vyžádání žádosti o publikování vaší nabídky ověříme připojení a pošleme vám testovacího vedoucího. Při prohlížení nabídky před tím, než budete připraveni, můžete také otestovat své zájemce tím, že se pokusíte získat nabídku sami v prostředí verze Preview. Je důležité zajistit, aby vaše nastavení potenciálních zákazníků zůstalo v aktuálním stavu, takže nepřijdete o žádné zájemce, proto nezapomeňte aktualizovat tato připojení, kdykoli se na konci změnila nějaká změna.

### <a name="what-are-the-next-steps"></a>Jaké jsou další kroky?

Po dokončení technického nastavení byste měli tyto zájemce začlenit do svého aktuálního & marketingové strategie a provozní procesy. Uvažujete o tom, jak se seznámit s vaším celkovým procesem prodeje a chceme úzce spolupracovat s vámi na poskytování vysoce kvalitních zájemců a dostatečného množství dat, abyste mohli úspěšně probíhat. Uvítáme vaše názory na to, jak můžeme optimalizovat a zdokonalovat zájemce, abychom vám poslali další data, abychom těmto zákazníkům pomohli zajistit úspěch. Dejte nám vědět, pokud máte zájem o poskytování zpětné vazby a návrhů, které umožní vašemu prodejnímu týmu lepší úspěšnost s komerčními zájemci na webu Marketplace.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a><a id="publishing-config-errors"></a> Běžné chyby konfigurace potenciálních zákazníků během publikování v partnerském centru

**Nepovedlo se uložit zájemce do Dynamics CRM. Ověřte nastavení účtu Dynamics CRM. LastCRMError: nepovedlo se přihlásit k Dynamics CRM, LastCRMException:** 

> Pokud byla vybrána možnost ověřování Microsoft 365, ověřte, zda je uživatelský účet a heslo platné. Pokud jste vybrali Azure Active Directory, podívejte se, jestli ID tenanta, ID aplikace a tajný klíč aplikace odpovídají obsahu, který jste nastavili v Azure Active Directory. Postupujte podle [pokynů.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) Pokud je uživatelské jméno/heslo účtu platné, ujistěte se prosím, že má přístup k Dynamics 365 a má přiřazenou licenci (kroky 11-15 při použití Azure Active Directory nebo nastavení zabezpečení při použití uživatele Office). 

**Nepovedlo se uložit zájemce do Dynamics CRM. Uživatel nemá oprávnění k vytvoření pro atribut leadsourcecode v entitě potenciálního zákazníka.** 

> V aplikaci nebo uživateli chybí role zabezpečení pro Microsoft Marketplace zapisovače potenciálních zákazníků. Pokud [používáte uživatele Office, postupujte](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)podle kroků 11-15, pokud používáte Azure Active Directory, nebo nastavení zabezpečení.

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: tenant se nenašel. Tato instance může nastat, pokud pro tenanta nejsou k dispozici žádná aktivní předplatná.**  

> ID adresáře, které je uvedené v části Správa zájemců, není platný adresář. Získejte prosím ID adresáře podle pokynů v kroku 2 (v části [Azure Active Directory).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)

**Nepovedlo se uložit zájemce do Dynamics CRM. LastCRMError: SecLib:: RetrievePrivilegeForUser se nezdařilo – uživateli nejsou přiřazeni žádné role.**  

> Řešení: Přiřaďte roli zabezpečení pro Microsoft Marketplace zapisovače potenciálních zákazníků. Postupujte podle [pokynů v části](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) nastavení zabezpečení.

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: aplikace s identifikátorem nebyla v adresáři nalezena.** 

> ID aplikace uvedené v části Správa zájemců není platný adresář. Získejte prosím ID adresáře podle pokynů v kroku 8 (v části Azure Active Directory, [odsud).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: požadovaný identifikátor tenanta není platný a není platný formát externí domény.** 

> ID adresáře, které je uvedené v části Správa zájemců, není platný adresář. Získejte prosím ID adresáře podle pokynů v kroku 2 (v části Azure Active Directory, [odsud).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: Chyba při ověřování přihlašovacích údajů. byl zadán neplatný tajný klíč klienta.** 

> Řešení: Přihlaste se do Azure Portal a ověřte, jestli se klíč aplikace shoduje s obsahem partnerského centra. Vygenerujte prosím heslo na základě instrukcí v kroku 10 (v části Azure Active Directory), z [tohoto místa](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Nepovedlo se uložit zájemce do Dynamics CRM. LastCRMError: při čekání na odpověď po 00:02:00 vypršel časový limit kanálu požadavků. Zvyšte hodnotu časového limitu předaného pro volání a vyžádejte si nebo zvyšte hodnotu SendTimeout na vazbě. Čas přidělený této operaci byl pravděpodobně částí delšího časového limitu.**  

> Řešení: Přihlaste se k partnerskému centru, vyhledejte nastavení nabídky >> potenciální zákazníci >> adrese URL a ověřte, jestli se jedná o platnou instanci Dynamic CRM.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Co jsou to zájemce a proč jsou důležité pro mě jako Vydavatel na webu Marketplace?** 

Zájemci jsou zákazníci, kteří nasazují vaše produkty z webu Marketplace. Bez ohledu na to, jestli je váš produkt uvedený na [Azure Marketplace](https://azuremarketplace.microsoft.com) nebo [AppSource](https://appsource.microsoft.com/), budete moci dostávat potenciální zákazníky, kteří mají zájem o produkt, pokud jste si v nabídce nastavili cíl realizace.  

**Kde můžu získat pomoc při nastavování cíle realizace?** 

Dokumentaci můžete najít na adrese [získat zájemce zákazníka](./partner-center-portal/commercial-marketplace-get-customer-leads.md) nebo odeslat lístek podpory v [nápovědě a podpoře](https://aka.ms/marketplacepublishersupport). Vyberte nabídku typ a Správa potenciálních zákazníků. 

**Je nutné nakonfigurovat cíl zájemce, aby bylo možné publikovat nabídku na webu Marketplace?**

Ano, pokud publikujete aplikaci SaaS s kontaktními osobami nebo konzultační služby.  

**Jak si můžu ověřit, jestli je správně nastavená konfigurace potenciálního zákazníka?**

Po nastavení nabídky a cíle zájemce publikujte vaši nabídku. V kroku potvrzení zájemce odešle Marketplace test vedoucího k cíli potenciálního zákazníka, který je ve vaší nabídce nakonfigurovaný. 

**Jak můžu najít vedoucí test?**

Ve svém cílovém cíli vyhledejte "MSFT_TEST", zde je ukázka dat vedoucího testu: 

```text
company = MSFT_TEST_636573304831318844 

country = US 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|\<Offer Name> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 
```

**Mám živou nabídku, ale nevidím žádné zájemce?**

Každý z potenciálních zákazníků bude mít data předaná v rámci vybraného cíle realizace, zájemci budou mít tento formát: **zdroj – akce | Nabídka**

- *Prostředky*
  - AzureMarketplace
  - AzurePortal
  - TestDrive  
  - SPZA (zkratka pro AppSource)

- *Činností*
  - "INS" – zaznamenání instalace. Jedná se o Azure Marketplace nebo AppSource pokaždé, když zákazník narazí na tlačítko, aby získal váš produkt.
  - "PLT" – představuje zkušební verzi LED pro partnery. Jedná se o AppSource pokaždé, když zákazník narazí na tlačítko Contact mi.
  - "DNC" – zaznamená, že se nekontaktují. Toto je na AppSource vždy, když se partner, který jste provedli na stránce aplikace, vyžádá, aby kontaktoval. Sdílíme hlavní hlavu, že tento zákazník byl v aplikaci uveden výše, ale není potřeba ho kontaktovat.
  - "Vytvořit" – Toto je uvnitř Azure Portal jenom a pokaždé, když zákazník koupí vaši nabídku na svůj účet.
  - "StartTestDrive" – Jedná se pouze o testovací jednotky a je pokaždé, když zákazník spustí svoji testovací jednotku.

- *Rozsah*
  - "Checkpoint. Check-Point-r77-10sg-BYOL";
  - "Bitnami. openedxcypress",
  - "Docusign. 3701c77e-1cfa-4c56-91e6-3ed0b622145a"

*Tady jsou ukázková data informací o zákaznících.*

```json
{ 
"FirstName":"John",
"LastName":"Smith",
"Email":"jsmith@microsoft.com",
"Phone":"1234567890",
"Country":"US",
"Company":"Microsoft",
"Title":"CTO"
}
```

Další informace najdete v části [informace o zájemci](./partner-center-portal/commercial-marketplace-get-customer-leads.md). 

**Jsem nakonfigurovali Azure BLOB jako cíl pro zájem, proč se mi nezobrazuje potenciální zákazník?** 

Zájemce se napíše jenom v případě, že jako cíl pro zájemce vyberete úložiště objektů BLOB v Azure. Přepněte do tabulky Azure, abyste získali potenciálního zákazníka v reálném čase.

**Obdržel jsem e-mail z webu Marketplace, proč nemůžu v mém CRM najít potenciálního zákazníka?**  

Je možné, že e-mailová doména koncového uživatele pochází z. edu. Z důvodů ochrany osobních údajů nemusíme z domény. edu předávat osobní identifikovatelné údaje. Odešlete lístek podpory na [pomoc a podporu](https://aka.ms/marketplacepublishersupport).

**Jak mám nakonfigurované Azure Table/Azure BLOB jako cíl pro zájem, jak si můžu zobrazit potenciální zákazníky?** 

K objektu BLOB nebo tabulce můžete přistupovat z Azure Portal, nebo můžete stáhnout a nainstalovat [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zdarma a zobrazit tak tabulky a objekty blob účtu Azure Storage. 

**Nakonfiguroval (a) jsem tabulku Azure jako cíl pro zájem, můžu dostávat oznámení, když web Marketplace pošle novému potenciálního zákazníka?** 

Ano, postupujte podle pokynů pro nastavení funkce Azure Table + v dokumentaci [zde](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md). 

**Mám nakonfigurované Salesforce jako cíl pro zájem, proč nemůžu najít potenciální zákazníky?**

Zkontroluje, jestli je formulář Web to zájemce povinným polem na základě rozevíracího seznamu. Pokud ano, přepněte pole na nepovinné textové pole.  
 
**Došlo k potížím s cílem mého zájemce a vynechali nějaké zájemce. Můžu se mi poslat do e-mailu?** 

Kvůli zásadám ochrany osobních údajů nemůžeme sdílet informace o potenciálních zákazníků prostřednictvím nezabezpečeného e-mailu. 

**Nakonfigurovali jsem Azure Storage (objekt BLOB/tabulka) jako cíl pro realizace, kolik to bude?** 

Data generování potenciálních zákazníků jsou nízká (<1 GB pro téměř všechny vydavatele). Náklady budou záviset na počtu přijatých zájemců, pokud 1 000 zájemci obdrží za měsíc, náklady na 50 centů. 
