---
title: Správa zájemců z komerčního tržiště Microsoftu
description: Přečtěte si o generování a přijímání potenciálních zákazníků od vašich Microsoft AppSource a Azure Marketplace nabídek.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 1d5f2248d94796d5e3ee76301642a95abddebfe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94489331"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Potenciální zákazníci z nabídky komerčního marketplace

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

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Pochopení údajů o potenciálních datech

Každý zájemce, kterého obdržíte během procesu pořízení zákazníka, má data v konkrétních polích. První pole, pro které se má zjistit `LeadSource` , je pole, které následuje po tomto formátu: nabídka **zdroje a akce**  |  .

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
- **Následná** akce: Nezapomeňte během 24 hodin pokračovat. Přímo poté, co zákazník nasadí testovací verzi, dostanete zájemce v aplikaci CRM zvolené možnosti. odešlete je e-mailem v době, kdy jsou pořád zahřívání. Požádejte o plánování telefonního hovoru, abyste lépe pochopili, jestli je váš produkt dobrým řešením pro svůj problém. Očekává se, že typická transakce vyžaduje několik následných volání.
- **Nurture**: nurture vašich zájemců, abyste vám pomohli dosáhnout vyššího ziskového rozpětí. Vrátit se změnami, ale bombard je. Doporučujeme, abyste před uzavřením e-mailu před zavřením přeposlali aspoň pár zákazníků. Nedávejte po prvním pokusu. Pamatujte na to, že tito zákazníci přímo využívají váš produkt a stráví čas v bezplatné zkušební verzi; jsou to skvělé potenciální zákazníky.

Po uvedení technického nastavení zahrňte tyto zájemce do své aktuální prodejní a marketingové strategie a provozní procesy. Uvažujete o tom, abychom lépe porozuměli celkovému procesu prodeje a chtěli byste úzce spolupracovat s vámi na zajištění vysoce kvalitních zájemců a dostatečného množství dat, aby bylo možné to úspěšně provést. Uvítáme vaše názory na to, jak můžeme optimalizovat a zdokonalovat zájemce, abychom vám poslali další data, abychom těmto zákazníkům pomohli zajistit úspěch. Dejte nám vědět, pokud máte zájem o [poskytování zpětné vazby](mailto:AzureMarketOnboard@microsoft.com) a návrhů, které umožní vašemu prodejnímu týmu lepší úspěšnost s komerčními zájemci na webu Marketplace.

## <a name="next-steps"></a>Další kroky

- [Nejčastější dotazy ke správě zájemců a řešení potíží](../lead-management-faq.md)