---
title: Konfigurace zákaznických zákazníků | Azure Marketplace
description: Nakonfigurujte zájemce zákazníka na komerčním webu Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 31dcc8c1e35b627b231dbe2a62998c8514d05a20
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902647"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Potenciální zákazníci z nabídky na webu Marketplace

Potenciální zákazníci zajímá nebo nasazují vaše nabídky z [Azure Marketplace](https://azuremarketplace.microsoft.com) nebo z [AppSource](https://appsource.microsoft.com). Po publikování vaší nabídky na webu Marketplace obdržíte potenciální zákazníky. V tomto článku se dozvíte, jak:

* Jak vaše nabídka Marketplace generuje zákazníky, což zajistí, že nebudete mít žádné obchodní příležitosti. 
* Připojte svůj CRM k vaší nabídce, abyste mohli spravovat potenciální zákazníky v jednom centrálním umístění.
* Seznamte se s daty vedoucích, která vám pošleme, takže se můžete podívat na zákazníky, kteří se vám dostali.

## <a name="generate-customer-leads"></a>Generování potenciálních zákazníků

Tady jsou místa, kde se vygeneruje zájemce:

1. Když zákazník souhlasí s sdílením svých informací po výběru možnosti "kontaktujte mě" na webu Marketplace. Tento zájemce je **počátečním zájmem** , kde můžeme sdílet informace o zákazníkovi, který vyjádřil zájem o získání vašeho produktu. Vedoucí je horní část trychtýře pořízení.

      ![Dynamics 365 kontaktujte mě](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Když zákazník vybere nabídku Get (získat) nebo "vytvořit" (ve [Azure Portal](https://portal.azure.com/)) k získání vaší nabídky, jedná se o **aktivního zájemce**, kde budeme sdílet informace o zákazníkovi, který zahájil nasazení vašeho produktu.

    ![SQL hned ho získat](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Vytvoření Windows serveru](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Zákazník vezme "zkušební jednotka" nebo spustí bezplatnou zkušební verzi vaší nabídky. Testovací jednotky nebo bezplatné zkušební verze představují urychlené příležitosti pro okamžité sdílení vaší firmy s potenciálními zákazníky bez jakýchkoli překážek vstupu.

    ![Testovací jednotka Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Testovací jednotka Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Připojení k systému CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Pochopení údajů o potenciálních datech

Každý zájemce, kterého obdržíte během procesu pořízení zákazníka, má data v konkrétních polích. První pole, pro které se má zjistit, `LeadSource` je pole, které následuje po tomto formátu: **Nabídka zdroj – akce** | 

**Zdroje**: Hodnota tohoto pole se vyplní na základě webu Marketplace, který vygeneroval zájemce. Možné hodnoty jsou `"AzureMarketplace"`, `"AzurePortal"`a `"AppSource (SPZA)"`.

**Akce**: Hodnota tohoto pole se naplní na základě akce, kterou zákazník zavedl na webu Marketplace, který vygeneroval zájemce. 

Možné hodnoty jsou:

- "INS" – instalace. Tato akce se provádí v Azure Marketplace nebo AppSource, když zákazník koupí váš produkt.
- "PLT" – představuje zkušební verzi LED pro partnery. Tato akce se provádí v AppSource, když zákazník používá možnost kontakt mi.
- "DNC" – nekontaktuje se. Tato akce se provádí na AppSource, když se partner, který jste provedli na stránce aplikace, vyžádá, aby kontaktoval. Sdílíme, že tento zákazník byl ve vaší aplikaci uveden výše, ale není potřeba ho kontaktovat.
- "Vytvořit" – Tato akce je pouze uvnitř Azure Portal a je generována, když zákazník koupí vaši nabídku na svůj účet.
- "StartTestDrive" – Tato akce je určena pouze pro testovací jednotky a je generována, když zákazník spustí testovací jednotku.

**Nabídky**: Na webu Marketplace můžete mít několik nabídek. Hodnota tohoto pole je vyplněna na základě nabídky, která zájemce vygenerovala. ID vydavatele a ID nabídky jsou odesílány v tomto poli a jsou to hodnoty, které jste zadali při publikování nabídky na webu Marketplace.

Následující příklady znázorňují příklady hodnot v očekávaném formátu `publisherid.offerid`: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Informace o zákazníkovi

Informace o zákazníkovi se odesílají prostřednictvím několika polí. Následující příklad ukazuje informace o zákaznících, které jsou obsaženy v rámci zájemce.

- FirstName Jan
- Polím Smith
- E-mail: JSmith\@Microsoft.com
- Hovor 1234567890
- Země: USA
- Podnikový Microsoft
- Hlava TECHNICKÝ ŘEDITEL

>[!Note]
>Ne všechna data v předchozím příkladu jsou vždy k dispozici pro každého zájemce. Vzhledem k tomu, že se dostanete k několika krokům, jak je uvedeno v části zájemci pro zákazníky, nejlepším způsobem, jak zájemce zvládnout, je zrušit duplicitu záznamů a přizpůsobit následná sledování. Tímto způsobem si jednotliví zákazníci dostanou příslušnou zprávu a vytváříte jedinečnou relaci.

## <a name="best-practices-for-lead-management"></a>Osvědčené postupy pro správu potenciálních zákazníků

1. *Proces* – definujte jasný prodejní proces s milníky, klíčovými ukazateli výkonu a jasným vlastnictvím týmu.
2. *Kvalifikace* – definovat předpoklady, které označují, zda byl zájemce plně kvalifikován. Zajistěte, aby prodejci prodeje nebo marketingu zajistily, že potenciální zákazníky budou pečlivě zajišťovat celý prodejní proces.
3. Nedělejte si nezapomněli pokračovat, očekává se, že typická transakce bude vyžadovat 5 až 12 následných volání.
4. *Nurture* – nurture vaše zájemce, abyste mohli získat přístup k vyššímu ziskovému rozpětí.

## <a name="leads-frequently-asked-questions"></a>Nejčastější dotazy k zájemcům

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Kde můžu získat pomoc při nastavování cíle realizace?

Můžete najít dokumentaci k tomuto [umístění](#connect-to-your-crm-system) nebo odeslat lístek podpory prostřednictvím aka.MS/marketplacepublishersupport a pak vybrat **"Vytvoření nabídky"** → **váš typ nabídka** → **Konfigurace řízení vedoucího**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Je nutné nakonfigurovat cíl zájemce, aby bylo možné publikovat nabídku na webu Marketplace?

Odpověď závisí na typu nabídky, kterou publikujete. SaaS a Dynamics 365 pro nabídky služby Customer Engagement nabízí seznam jako "kontaktní mě", všechny nabídky Dynamics 365 for Operations, 365 všechny nabídky služeb Dynamics for Operations a všechny konzultační nabídky služby vyžadují připojení k cíli realizace. Pokud váš typ nabídky není uveden, není vyžadován. Doporučuje se ale nakonfigurovat cíl zájemce, abyste nemuseli přijít o obchodní příležitosti.

### <a name="how-can-i-find-the-test-lead"></a>Jak můžu najít vedoucí test?

`“MSFT_TEST”` Vyhledejte v cílovém cíli, tady je ukázka vedoucího testu od Microsoftu:

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

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Mám živou nabídku, ale nevidím žádné zájemce?

Ujistěte se, že je připojení k cíli realizace platné. Po vyzkoušení vaší nabídky v partnerském centru vám pošleme vedoucího testu. Pokud se zobrazí vedoucí testu, pak je připojení platné. Můžete také otestovat své zájemce tím, že se pokusíte získat náhled nabídky během kroku Preview kliknutím na "získat IT", "kontakty mě" nebo "bezplatnou zkušební verzi" na výpisu na webu Marketplace.

Ujistěte se také, že hledáte správná data. Obsah v části [pochopení dat potenciálních zákazníků](#understand-lead-data) v tomto dokumentu popisuje údaje o potenciálních datech, které odesíláme do vašeho cíle potenciálního zákazníka.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Jsem nakonfigurovali Azure BLOB jako cíl pro zájem, proč se mi nezobrazuje potenciální zákazník?

Cíl zájemce Azure Blob už není podporovaný, takže nebudete mít žádné zájemce vygenerované vaší nabídkou. Přepněte na kteroukoli z dalších [možností cíle pro zájemce](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Obdržel jsem e-mail z webu Marketplace, proč nemůžu v mém CRM najít potenciálního zákazníka?

Je možné, že e-mailová doména koncového uživatele pochází z. edu. Z důvodů ochrany osobních údajů nepředávejte údaje o PII z domény. edu. Odešlete lístek podpory prostřednictvím aka.ms/marketplacepublishersupport.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Jak mám nastavit tabulku Azure jako cíl pro zájem, jak si můžu zobrazit potenciální zákazníky?

K datům zájemce uloženým v tabulce Azure můžete přistupovat z webu Azure Portal nebo si můžete stáhnout a nainstalovat [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zdarma a zobrazit tak data z tabulek vašeho účtu úložiště Azure.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Nakonfiguroval (a) jsem tabulku Azure jako cíl pro zájem, můžu dostávat oznámení, když web Marketplace pošle novému potenciálního zákazníka?

Ano, podle pokynů nastavte Microsoft flow, který odešle e-mail, pokud se do tabulky Azure v dokumentaci přidá zájemce. [](./commercial-marketplace-lead-management-instructions-azure-table.md)

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Mám nakonfigurované Salesforce jako cíl pro zájem, proč nemůžu najít potenciální zákazníky?

Zkontroluje, jestli je formulář "Web ke vedoucímu" povinným polem na základě rozevíracího seznamu. Pokud ano, přepněte pole na nepovinné textové pole.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Došlo k potížím s cílem mého zájemce a vynechali nějaké zájemce. Můžu se mi poslat do e-mailu?

Kvůli zásadám PII (soukromé údaje) nemůžeme sdílet informace o potenciálních osobách prostřednictvím nezabezpečeného e-mailu.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Nakonfiguroval (a) jsem tabulku Azure jako můj cíl pro zájem, jak dlouho se budou považovat za náklady?

Obecná data zájemce jsou nízká (< 1 GB pro téměř všechny vydavatele). Náklady budou záviset na počtu přijatých zájemců, pokud 1 000 zájemci obdrží za měsíc, náklady na 50 centů. Další informace o cenách služby Storage najdete v tématu [ceny služby Storage](https://azure.microsoft.com/pricing/details/storage/).

Pokud se Váš dotaz stále neodpoví, obraťte se na podporu prostřednictvím aka.ms/marketplacepublishersupport a pak vyberte možnost **Vytvoření nabídky** →. **váš typ nabízí** **konfiguraci správy potenciálních zákazníků.** 

## <a name="next-steps"></a>Další kroky

Po dokončení technického nastavení byste měli začlenit tyto zájemce do své aktuální prodejní & strategie a provozní procesy. Uvažujete o tom, abychom lépe porozuměli celkovému procesu prodeje a chtěli byste úzce spolupracovat s vámi na poskytování vysoce kvalitních zájemců a dostatečného množství dat, aby bylo možné to úspěšně provést. Uvítáme vaše názory na to, jak můžeme optimalizovat a zdokonalovat zájemce, abychom vám poslali další data, abychom těmto zákazníkům pomohli zajistit úspěch. Dejte nám vědět, pokud máte zájem o [poskytování zpětné vazby](mailto:AzureMarketOnboard@microsoft.com) a návrhů, které umožní vašemu prodejnímu týmu větší úspěšnost s zájemci na webu Marketplace.
