---
title: Konfigurace zájemců zákazníků | Azure Marketplace
description: Konfigurace zákazníků vede na komerčním trhu.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 05b166b2ec46900d9e3972025efb581d9619ec6a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252602"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Potenciální zákazníci vaší nabídky na marketplace

Zájemci jsou zákazníci, kteří mají zájem o vaše nabídky nebo o jejich nasazení z [Azure Marketplace](https://azuremarketplace.microsoft.com) nebo z [AppSource](https://appsource.microsoft.com). Jakmile bude vaše nabídka zveřejněna na trhu, obdržíte potenciální zákazníky. Tento článek vysvětlí:

* Jak vaše nabídka na trhu generuje zákazníky, což zajišťuje, že nezmeškáte obchodní příležitosti. 
* Připojte svůj CRM k vaší nabídce, abyste mohli spravovat své potenciální zákazníky na jednom centrálním místě.
* Seznamte se s údaji o potenciálních zákaznících, které vám posíláme, abyste mohli sledovat zákazníky, kteří vás kontaktovali.

## <a name="generate-customer-leads"></a>Generovat zájemce zákazníků

Zde jsou místa, kde je zájemce generován:

1. Když zákazník souhlasí se sdílením svých informací po výběru možnosti "Kontaktujte mě" z trhu. Tento zájemce je **počátečním zájemcem,** kde sdílíme informace o zákazníkovi, který projevil zájem o získání vašeho produktu. Zájemce je horní část akviziční cesty.

      ![Dynamics 365 Kontaktujte mě](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Když zákazník vybere "Získat nyní" nebo "Vytvořit" (na [portálu Azure)](https://portal.azure.com/)získat vaši nabídku, tento zájemce je **aktivní zájemce**, kde sdílíme informace o zákazníkovi, který začal nasazovat váš produkt.

    ![SQL Get it Now](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Vytvoření systému Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Zákazník provede "Testovací jízdu" nebo spustí "bezplatnou zkušební verzi" vaší nabídky. Testovací jízdy nebo bezplatné zkušební verze jsou zrychlené příležitosti pro okamžité sdílení vaší firmy s potenciálními zákazníky bez překážek vstupu.

    ![Testovací jízda Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Testovací jízda Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Připojení k crm systému

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Principy dat zájemců

Každý zájemce, který obdržíte během procesu získávání zákazníků, má data v konkrétních polích. První pole, na které `LeadSource` je třeba dávat pozor, je pole, které následuje po tomto formátu:**Nabídka** **akce** | zdroje .

**Zdroje:** Hodnota tohoto pole je naplněna na základě tržiště, které vygenerovalo zájemce. Možné hodnoty `"AzureMarketplace"` `"AzurePortal"`jsou `"AppSource (SPZA)"`, a .

**Akce**: Hodnota tohoto pole je vyplněna na základě akce, kterou zákazník provedl na trhu a která vygenerovala zájemce. 

Možné hodnoty:

- "INS" - instalace. Tato akce se provádí na Azure Marketplace nebo AppSource, když si zákazník koupí váš produkt.
- "PLT" - Zkratka pro Partner Led Trial. Tato akce je na AppSource, když zákazník používá možnost Kontaktujte mě.
- "DNC" - Nekontaktujte. Tato akce se na AppSource, když partner, který byl kříž uvedený na stránce aplikace dostane požádán, aby byl kontaktován. Sdílíme informace o tom, že tento zákazník byl ve vaší aplikaci uveden na kříži, ale není třeba ho kontaktovat.
- "Vytvořit" – Tato akce je jenom uvnitř portálu Azure a je generována, když zákazník zakoupí vaši nabídku na svůj účet.
- "StartTestDrive" – Tato akce je pouze pro testovací jednotky a je generována, když zákazník spustí testovací jednotku.

**Nabídky**: Můžete mít více nabídek na trhu. Hodnota pro toto pole je naplněna na základě nabídky, která vygenerovala zájemce. ID vydavatele i ID nabídky jsou v tomto poli odesílány a jsou to hodnoty, které jste zadali při publikování nabídky na trhu.

Následující příklady ukazují příklady hodnot `publisherid.offerid`v očekávaném formátu : 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Informace o zákaznících

Informace o zákazníkovi jsou odesílány prostřednictvím více polí. Následující příklad ukazuje informace o zákazníkovi, které jsou obsaženy v zájemci.

- Jméno: Jan
- Příjmení: Novák
- E-mail:\@jsmith microsoft.com
- Telefon: 1234567890
- Země: USA
- Společnost: Microsoft
- Název: CTO

>[!Note]
>Pro každého zájemce nejsou vždy k dispozici všechna data v předchozím příkladu. Vzhledem k tomu, že zájemce získáte z několika kroků, jak je uvedeno v části Zájemci zákazníků, je nejlepším způsobem, jak s zájemci zacházet, deduplikovat záznamy a přizpůsobit následná opatření. Tímto způsobem každý zákazník dostává příslušnou zprávu a vytváříte jedinečný vztah.

## <a name="best-practices-for-lead-management"></a>Doporučené postupy pro vedení

1. *Proces* – Definujte jasný prodejní proces s milníky, kku a jasným vlastnictvím týmu.
2. *Kvalifikace* – Definujte požadavky, které označují, zda byl zájemce plně kvalifikován. Ujistěte se, že obchodní nebo marketingoví zástupci pečlivě zařadí zájemce, než je provedete celým prodejním procesem.
3. *Sledování* - Nezapomeňte sledovat, očekávejte, že typická transakce bude vyžadovat 5 až 12 následných hovorů
4. *Pečujte* - Pečujte o své zájemce, abyste se dostali na cestu k vyšší ziskové marži.

## <a name="leads-frequently-asked-questions"></a>Nejčastější dotazy zájemců

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Kde mohu získat pomoc s nastavením cíle pro potenciální zákazníky?

Dokumentaci najdete [zde](#connect-to-your-crm-system) nebo odešlete lístek podpory prostřednictvím aka.ms/marketplacepublishersupport pak vyberte **"vytvoření nabídky"** → **typ nabídky** → **"konfigurace správy potenciálních zákazníků".**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Je nutné nakonfigurovat cíl zájemce, abych mohl zveřejnit nabídku na trhu?

Odpověď závisí na typu nabídky, kterou publikujete. SaaS a Dynamics 365 for Customer Engagement nabízí seznam jako "Kontaktujte mě", všechny nabídky Dynamics 365 pro operace, všechny nabídky Dynamics 365 Business Central a všechny nabídky konzultačních služeb vyžadují připojení k cíli zájemce. Pokud váš typ nabídky nebyl uveden, není vyžadován. Doporučujeme však nakonfigurovat cíl zájemce, abyste nezmeškali obchodní příležitosti.

### <a name="how-can-i-find-the-test-lead"></a>Jak najdu testovacího stopovace?

Vyhledejte `"MSFT_TEST"` v destinaci zájemce ukázkový test od společnosti Microsoft:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Mám živou nabídku, ale nevidím žádné stopy?

Zkontrolujte, zda je připojení k cíli zájemce platné. Po zveřejnění nabídky v Partnerském centru vám zašleme testovacího zájemce. Pokud se zobrazí testovací zájemce, připojení je platné. Připojení pro potenciální zákazníky můžete také otestovat tím, že se pokusíte získat náhled nabídky během kroku náhledu kliknutím na "get it now", "contacts me" nebo "free trial" na výpisu na trhu.

Také se ujistěte, že hledáte správná data. Obsah v části [Porozumět údajům o zájemcích](#understand-lead-data) v tomto dokumentu popisuje údaje o zájemcích, které odesíláme do cíle zájemce.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Nakonfiguroval jsem Azure BLOB jako cíl pro potenciální zákazníky, proč nevidím zájemce?

Cíl zájemců o objekt blob Azure už není podporovaný, takže vám chybí všichni zákazníci, kteří jsou generováni vaší nabídkou. Přepněte na některou z dalších [možností cíle zájemce](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Obdržel(a) jsem e-mail od marketplace, proč nemohu najít zájemce v crm?

Je možné, že e-mailová doména koncového uživatele pochází z .edu. Z důvodu ochrany osobních údajů nepředáváme soukromé identifikovatelné informace z domény .edu. Odešlete lístek podpory prostřednictvím aka.ms/marketplacepublishersupport.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Nakonfiguroval jsem Azure Table jako cíl pro potenciální zákazníky, jak můžu zobrazit zájemce?

K datům o potenciálních zákaznících uloženým v tabulce Azure můžete přistupovat z webu Azure Portal nebo si můžete zdarma stáhnout a nainstalovat [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) a zobrazit data tabulk vašeho účtu úložiště Azure.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Nakonfiguroval jsem Azure Table jako cíl pro potenciální zákazníky, můžu dostat upozornění při každém odeslání nového zájemce marketplacem?

Ano, podle pokynů nastavte tok Microsoftu, který odešle e-mail, pokud je zájemce přidán do tabulky Azure v dokumentaci [zde](./commercial-marketplace-lead-management-instructions-azure-table.md).

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Nakonfiguroval jsem salesforce jako cíl pro potenciální zákazníky, proč nelze zájemce najít?

Zkontrolujte, zda je formulář "web pro zájemce" povinným polem založeným na seznamu. Pokud ano, přepněte toto pole na nepovinné textové pole.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Byl tam problém s mým hlavním cílem, a já jsem vynechal některé vede. Mohu si je nechat poslat e-mailem?

Vzhledem k zásadám pro identifikaci soukromých informací nemůžeme sdílet informace o potenciálních zákaznících prostřednictvím nezabezpečených e-mailů.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Nakonfiguroval jsem Azure Table jako cíl pro potenciální zákazníky, kolik to bude stát?

Data olověné genje nízká (<1 GB pro téměř všechny vydavatele). Náklady budou záviset na počtu přijatých zájemců, pokud bude za měsíc přijato 1 000 zájemců, bude to stát kolem 50 centů. Další informace o cenách úložiště najdete v [tématu ceny za úložiště](https://azure.microsoft.com/pricing/details/storage/).

Pokud vaše otázka stále není zodpovězena, kontaktujte podporu prostřednictvím aka.ms/marketplacepublishersupport a pak vyberte **možnost "vytvoření nabídky"** → **typ nabídky** → **"konfigurace správy potenciálních zákazníků".** 

### <a name="i-am-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-who-to-receive-these-emails"></a>Dostávám e-mailová oznámení, když jsou přijati noví zákazníci zájemci. Jak mohu nakonfigurovat, kdo bude tyto e-maily dostávat?

Získejte přístup ke své nabídce v Centru partnerů a přejděte na stránku **Nastavení nabídky** ->**úpravy** **správy** -> zájemců . Aktualizujte e-mailové adresy v poli **Kontaktní e-mail.**

## <a name="next-steps"></a>Další kroky

Jakmile je technické nastavení zavedeno, měli byste tyto potenciální zákazníky začlenit do aktuální prodejní & marketingové strategie a provozních procesů. Máme zájem lépe porozumět vašemu celkovému prodejnímu procesu a chceme s vámi úzce spolupracovat na poskytování vysoce kvalitních potenciálních zákazníků a dostatečného množství dat, abyste byli úspěšní. Uvítáme vaši zpětnou vazbu o tom, jak můžeme optimalizovat a vylepšovat potenciální zákazníky, které vám posíláme, s dalšími údaji, které pomohou těmto zákazníkům uspět. Dejte nám vědět, pokud máte zájem [o poskytnutí zpětné vazby](mailto:AzureMarketOnboard@microsoft.com) a návrhů, které vašemu prodejnímu týmu umožní být úspěšnější s zájemci marketplace.
