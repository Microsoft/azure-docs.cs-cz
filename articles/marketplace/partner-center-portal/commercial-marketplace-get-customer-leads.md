---
title: Správa zájemců z komerčního tržiště Microsoftu
description: Přečtěte si o generování a přijímání potenciálních zákazníků od vašich Microsoft AppSource a Azure Marketplace nabídek.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 89b73fd98ca773668d2eb53892d0c21397e9abf3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559576"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Potenciální zákazníci z komerční nabídky na webu Marketplace

Potenciální zákazníci zajímá nebo nasazují vaše nabídky z [Microsoft AppSource](https://appsource.microsoft.com) a [Azure Marketplace](https://azuremarketplace.microsoft.com). Po publikování vaší nabídky na komerčním webu Marketplace můžete dostávat potenciální zákazníky. V tomto článku se dozvíte o následujících konceptech řízení vedoucích:

* Jak vaše nabídka komerčního tržiště vygeneruje zájemce pro zákazníky, aby se zajistilo, že nebudete mít k 
* Jak připojit systém pro řízení vztahů se zákazníky (CRM) k vaší nabídce, abyste mohli spravovat potenciální zákazníky v jednom centrálním umístění.
* Data o zájemci vám pošleme, abyste se mohli podívat na zákazníky, kteří se na vás dostali.

## <a name="generate-customer-leads"></a>Generování potenciálních zákazníků

Tady jsou místa, kde se vygeneruje zájemce:

- Zákazník souhlasí s sdílením jejich informací po výběru **kontaktování** na komerčním webu Marketplace. Tento potenciální zákazník je *počátečním zájmem* . S vámi sdílíme informace o zákazníkovi, který vyjádřil zájem získat svůj produkt. Vedoucí je horní část trychtýře pořízení.

    ![Dynamics 365 kontaktujte mě](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Zákazník vybere **Get hned** (nebo vybere **vytvořit** v [Azure Portal](https://portal.azure.com/)) k získání vaší nabídky. Tento potenciální zákazník je *aktivním* zájemcem. Informace o zákazníkovi, který zahájil nasazení vašeho produktu, sdílíme s vámi.

    ![Tlačítko získat nyní SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Tlačítko pro vytvoření Windows serveru](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Zákazník vybere **testovou jednotku** nebo **bezplatnou zkušební verzi** a vyzkoušení vaší nabídky. Testovací jednotky nebo bezplatné zkušební verze představují urychlené příležitosti pro okamžité sdílení vaší firmy s potenciálními zákazníky bez jakýchkoli překážek vstupu.

    ![Tlačítko testovací jednotky Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Tlačítko bezplatné zkušební verze Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Připojení k systému CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Pochopení údajů o potenciálních datech

Každý zájemce, kterého obdržíte během procesu pořízení zákazníka, má data v konkrétních polích. První pole, pro které se má zjistit `LeadSource` , je pole, které následuje po tomto formátu: nabídka **zdroje a akce**  |  **Offer**.

**Zdroje**: hodnota pro toto pole je naplněná na základě tržiště, který vygeneroval zájemce. Možné hodnoty jsou `"AzureMarketplace"` , `"AzurePortal"` a `"AppSource (SPZA)"` .

**Akce**: hodnota pro toto pole je naplněná na základě akce, kterou zákazník zavedl na webu Marketplace, který zájemce vygeneroval.

Možné hodnoty:

- **"Ins"**: představuje *instalaci*. Tato akce je Azure Marketplace nebo AppSource, když zákazník získá svůj produkt.
- **"PLT"**: zastupuje *zkušební verzi, která se zavedla pro partnery*. Tato akce se provádí v AppSource, když zákazník vybere možnost **kontakt mi** .
- **"DNC"**: představuje *nekontaktování*. Tato akce se nachází v AppSource, když se partner, který byl křížově uvedený na stránce aplikace, vyžádá, aby kontaktoval. Sdílíme oznámení, že tento zákazník byl ve vaší aplikaci křížově uveden, ale není potřeba ho kontaktovat.
- **"Vytvořit"**: Tato akce je pouze uvnitř Azure Portal a je generována, když zákazník koupí vaši nabídku na svůj účet.
- **"StartTestDrive"**: Tato akce je určena pouze pro možnost **testovací jednotka** a je generována, když zákazník spustí testovací jednotku.

**Nabídky**: na komerčním webu Marketplace můžete mít několik nabídek. Hodnota tohoto pole je vyplněna na základě nabídky, která zájemce vygenerovala. ID vydavatele a ID nabídky jsou odesílány v tomto poli a jsou to hodnoty, které jste zadali při publikování nabídky na webu Marketplace.

Následující příklady znázorňují hodnoty v očekávaném formátu `publisherid.offerid` : 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informace o zákaznících

Informace o zákazníkovi se odesílají prostřednictvím několika polí. Následující příklad ukazuje informace o zákaznících, které jsou obsaženy v rámci zájemce:

- FirstName: Jan
- LastName: Smith
- E-mail: JSmith \@ Microsoft.com
- Telefon: 1234567890
- Země: US
- Společnost: Microsoft
- Název: technický ředitel

>[!NOTE]
>Ne všechna data v předchozím příkladu jsou vždy k dispozici pro každého zájemce. Vzhledem k tomu, že se dostanete k několika krokům, jak je uvedeno v části "generování zájemců zákazníků", nejlepším způsobem, jak zájemce zvládnout, je zrušit duplicitu záznamů a přizpůsobit následná sledování. Tímto způsobem každý zákazník získá příslušnou zprávu a Vy vytvoříte jedinečnou relaci.

## <a name="best-practices-for-lead-management"></a>Osvědčené postupy pro správu potenciálních zákazníků

Tady je několik doporučení pro řízení zájemců prostřednictvím prodejního cyklu:

- **Proces**: Definujte jasný prodejní proces s milníky, analýzou a jasným vlastnictvím týmu.
- **Kvalifikace**: Definujte předpoklady, které označují, zda byl zájemce plně kvalifikován. Zajistěte, aby prodejci prodeje nebo marketingu zabírali před úplným prodejem.
- **Následná**akce: Nezapomeňte během 24 hodin pokračovat. Přímo poté, co zákazník nasadí testovací verzi, dostanete zájemce v aplikaci CRM zvolené možnosti. odešlete je e-mailem v době, kdy jsou pořád zahřívání. Požádejte o plánování telefonního hovoru, abyste lépe pochopili, jestli je váš produkt dobrým řešením pro svůj problém. Očekává se, že typická transakce vyžaduje několik následných volání.
- **Nurture**: nurture vašich zájemců, abyste vám pomohli dosáhnout vyššího ziskového rozpětí. Vrátit se změnami, ale bombard je. Doporučujeme, abyste před uzavřením e-mailu před zavřením přeposlali aspoň pár zákazníků. Nedávejte po prvním pokusu. Pamatujte na to, že tito zákazníci přímo využívají váš produkt a stráví čas v bezplatné zkušební verzi; jsou to skvělé potenciální zákazníky.

## <a name="common-questions-about-lead-management"></a>Běžné otázky týkající se správy potenciálních zákazníků

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Kde můžu získat pomoc při nastavování cíle realizace?

Postupujte podle kroků v části [připojení k systému CRM](#connect-to-your-crm-system)nebo odešlete lístek podpory prostřednictvím [podpory a podpory partnerského centra](https://aka.ms/marketplacepublishersupport). Pak vyberte **Nabídka vytvořit**  >  **váš typ**pro  >  **správu správy potenciálních zákazníků**.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Je nutné nakonfigurovat cíl zájemce, aby bylo možné publikovat nabídku na komerčním webu Marketplace?

Odpověď závisí na typu nabídky, kterou publikujete. Software jako služba (SaaS) a Dynamics 365 Customer Engagement použijte **kontaktní mě** k vypsání všech Dynamics 365 pro nabídky finance a Operations, všech Dynamics 365 Business Central nabízí a všech nabídek služeb. V důsledku toho vyžadují připojení k cíli zájemce. Pokud váš typ nabídky není uveden, připojení k cíli zájemce není vyžadováno. Doporučujeme, abyste nakonfigurovali cíl zájemce, abyste nemuseli přijít o obchodní příležitosti.

### <a name="how-can-i-find-the-test-lead"></a>Jak můžu najít vedoucí test?

Vyhledejte `"MSFT_TEST"` v cíli zájemce. Tady je ukázka vedoucího testu od Microsoftu:

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Mám živou nabídku, ale proč mi nevidím nějaké potenciální zákazníky?

Ujistěte se, že je připojení k cíli realizace platné. Po výběru **publikovat** na vaší nabídce v partnerském centru vám pošleme vedoucího testu. Pokud se zobrazí vedoucí testu, připojení je platné. Můžete také otestovat své zájemce tím, že se v kroku Preview pokusíte získat náhled nabídky. Vyberte **získat hned**, **kontaktujte mě**nebo **bezplatnou zkušební verzi** na výpisu na komerčním webu Marketplace.

Ujistěte se také, že hledáte správná data. Obsah v části [pochopení údajů o potenciálních datech](#understand-lead-data) v tomto článku popisuje údaje o zájemcích, které jsme poslali do vašeho cíle potenciálního zákazníka.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Nakonfiguroval (a) jsem úložiště objektů BLOB v Azure jako cíl pro zájem, ale proč nevidím potenciálního zákazníka?

Azure Blob Storage už není podporovaný jako cíl pro realizace, takže nebudete mít žádné zájemce vygenerované vaší nabídkou. Přepněte na kteroukoli z dalších [možností cíle pro zájemce](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Dostal jsem e-mail z komerčního tržiště, ale proč nemůžu v mém CRM najít potenciálního zákazníka?

Je možné, že e-mailová doména koncového uživatele pochází z. edu. Z důvodů ochrany osobních údajů nemusíme předávat osobní údaje z domény. edu. Odešlete lístek podpory prostřednictvím [odborné pomoci a podpory pro partnerským centru](https://aka.ms/marketplacepublishersupport).

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Nakonfigurovali jsem tabulku Azure jako cíl pro realizace. Jak si můžu zobrazit potenciální zákazníky?

K datům zájemce uloženým v tabulce Azure můžete přistupovat z Azure Portal. Můžete si také stáhnout a nainstalovat [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zdarma a zobrazit tak data z tabulky vašeho účtu úložiště Azure.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Nakonfigurovali jsem tabulku Azure jako cíl pro realizace. Můžu dostávat oznámení při každém odeslání nového potenciálního zákazníka z obchodu Marketplace?

Ano. Postupujte podle pokynů v tématu [Konfigurace řízení zájemců pomocí tabulky Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) k nastavení služby Microsoft flow, která odešle e-mail, když se do tabulky Azure přidá zájemce.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Nakonfigurovali jste Salesforce jako cíl pro zájem, ale proč nemůžu najít potenciální zákazníky?

Zkontroluje, jestli je formulář Web-to-zájemce povinným polem na základě seznamu vyskladnění. Pokud je, přepněte pole na nepovinné textové pole.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Došlo k potížím s cílem mého zájemce a vynechali nějaké zájemce. Můžu se mi poslat do e-mailu?

V důsledku zásad osobních informací nemůžeme sdílet informace o potenciálních osobách prostřednictvím nezabezpečeného e-mailu.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Nakonfigurovali jsem tabulku Azure jako cíl pro realizace. Do jaké míry budou náklady?

Data generování zájemců jsou nízká. Téměř všem vydavatelům je méně než 1 GB. Náklady závisí na počtu přijatých zájemců. Například pokud 1 000 potenciální zákazníci obdrží za měsíc, náklady jsou přibližně 50 centů. Další informace o cenách služby Storage najdete v tématu [Azure Storage přehledu cen](https://azure.microsoft.com/pricing/details/storage/).

Pokud na vaši otázku neodpovíte, kontaktujte podpora Microsoftu prostřednictvím [centra pro partnery a pomoc a podporu](https://aka.ms/marketplacepublishersupport). Pak vyberte **Nabídka vytvořit**  >  **váš typ**pro  >  **správu správy potenciálních zákazníků**.

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Přijímám e-mailová oznámení, když se přijímají Noví zájemci zákazníků. Jak můžu nakonfigurovat někoho jiného, aby přijímal tyto e-maily?

Přihlaste se k nabídce v partnerském centru a přejděte na stránku **nastavení nabídky** > **Správa zájemců**  >  **Upravit**. Aktualizujte e-mailové adresy v poli **kontaktní e-mail** .

## <a name="next-steps"></a>Další kroky

Po uvedení technického nastavení zahrňte tyto zájemce do své aktuální prodejní a marketingové strategie a provozní procesy. Uvažujete o tom, abychom lépe porozuměli celkovému procesu prodeje a chtěli byste úzce spolupracovat s vámi na zajištění vysoce kvalitních zájemců a dostatečného množství dat, aby bylo možné to úspěšně provést. Uvítáme vaše názory na to, jak můžeme optimalizovat a zdokonalovat zájemce, abychom vám poslali další data, abychom těmto zákazníkům pomohli zajistit úspěch. Dejte nám vědět, pokud máte zájem o [poskytování zpětné vazby](mailto:AzureMarketOnboard@microsoft.com) a návrhů, které umožní vašemu prodejnímu týmu lepší úspěšnost s komerčními zájemci na webu Marketplace.
