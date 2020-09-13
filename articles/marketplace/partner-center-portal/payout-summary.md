---
title: Sestavy výběrů – komerční tržiště Microsoftu
description: V sestavách výběr se zobrazí podrobnosti o peníze, které jste získali s vaší nabídkou, včetně objemu platby a o tom, kdy budete platit.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 09/09/2020
ms.openlocfilehash: 62de9380578ca5a97f7b72f9939d5c2dd7392c0d
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004217"
---
# <a name="payout-summaries"></a>Přehledy plateb

Souhrn výběrů vám ukáže podrobnosti o peníze, které jste získali od Microsoftu. Také vám umožní zjistit, kdy obdržíte platby a kolik vám bude placeno.

Pokud prodáváte produkty v Azure Marketplace, zobrazí se také informace o úspěšných výběrech v souhrnu výběrů. Další informace o platbě Azure Marketplace najdete v tématu o tom, [jak se zaplatí na komerčním webu Marketplace](./get-paid.md) a v rámci [smlouvy Microsoft Publisher](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> Aby bylo možné výsledek vyhodnotit, vaše výtěžek musí dosáhnout prahové hodnoty pro platbu $50. Podrobnosti najdete v článku o [smlouvě Microsoft Publisher](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> Pokud hledáte podporu týkající se vydaných výběrů, včetně konfigurace účtů pro výběr, chybějících výběrů, podržení výběrů nebo cokoli jiného, obraťte se [na podporu.](https://developer.microsoft.com/windows/support)

## <a name="access-the-payout-summary-pages"></a>Přístup k souhrnným stránkám výběrů

Chcete-li otevřít jednu ze stránek souhrnu výběr:

1. V pravém horním rohu vyberte ikonu výběr.
2. Vyberte **historii transakcí**, **platby**nebo **exportovat data**.

## <a name="payments-page"></a>Stránka platby

Součty na této stránce reprezentují všechny programy, které se účastníte. Můžete je filtrovat podle ID účastníka, programu, ID platby a typu příjmu. Částky jsou uvedené v USD. Zaplacené částky jsou uvedené také v měně příjemce.

| Oblast  | Popis |
| --- | --- |
| Celkem placené letos   | Celková částka za tento rok, ve Spojených dolarech, pro všechny vaše programy       |
| Další odhadovaná platba | Jedna další platba přichází vám (i v případě, že už brzy někdo přijde), v amerických dolarech. |
| Poslední platba           | Částka (v amerických dolarech), název programu a program vaší poslední platby           |
| Platby podle zdroje     | Množství plateb (v amerických dolarech) reprezentované programem za posledních 12 měsíců           |
| Platby               | Vyberte **placené** nebo **nevyřízené** a pak podle potřeby seřaďte. Další podrobnosti o konkrétním **zobrazení**výběru platby. Pokud si chcete stáhnout kopii příkazu platební platby, vyberte **Stáhnout**. Vzhledem k tomu, že se zobrazí data historie transakcí, může se stát, že se nezobrazuje související tržby hned po dobu 24 hodin. |
|||

Pokud chcete exportovat všechna data na této stránce, vyberte **exportovat** a postupujte podle pokynů na stránce Exportovat data.

## <a name="transaction-history-page"></a>Stránka historie transakcí

Na této stránce se zobrazí všechny vaše tržby, včetně data, typu a příjmu každého z nich. Můžete vybrat časové období, které chcete zobrazit, a můžete filtrovat podle ID registrace, programu, ID platby, typu trvání, páky a stavu. Data jsou k dispozici pro aktuální fiskální rok (1. června 30) a předchozí dva fiskální roky.

Chcete-li zobrazit další podrobnosti o objektu k dispozici, vyberte šipku dolů na pravé straně stránky. Tím se zobrazí páčka, objem výnosů a produkt. Pokud některá z těchto dat není k dispozici, ale potřebujete k ní přístup, obraťte se na [podporu](https://developer.microsoft.com/windows/support). Pokud je procento výsledkem úpravy a nikoli transakcí, pole produktu se nezobrazí.

Pokud chcete na této stránce Exportovat data transakcí, vyberte **exportovat** a postupujte podle pokynů na stránce Exportovat data. Soubory exportované ze stránky historie transakcí zobrazují data v měně transakce, výnosy v transakčních měnách i v amerických dolarech a placené hodnoty v hodnotě Pay to Currency.

## <a name="payment-status"></a>Stav platby

| Stav vystavení | Důvod | Je vyžadována akce partnera? |
| --- | --- | --- |
| Nezpracované              | Zisk má nárok na platbu. Zůstane v tomto stavu pro období chlazení, jak je definováno v Průvodci programu pro program k Pobídkě. | No                                                         |
| Nadcházející                 | Platební objednávka vygenerovala nevyřízené interní recenze před zpracováním platby.                                                               | No                                                         |
| Nevyřízená daňová faktura      | Vaše daňová faktura je neúplná nebo neplatná.                                                                                                  | Abyste mohli platit, musíte si nejdřív aktualizovat svou daňovou fakturu. |
| Zamítnuto během revize   | Platba byla během revize odmítnuta.                                                                                                     | Podrobnosti získáte od [podpory Microsoftu](https://developer.microsoft.com/windows/support) .                      |
| Neúspěšný                   | Platba se nezdařila z důvodu chyby systému Microsoft.                                                                                         | Podrobnosti získáte od [podpory Microsoftu](https://developer.microsoft.com/windows/support)  .                      |
| Rozpracované              | Platba probíhá.                                                                                                                 | No                                                         |
| Nesprávná platba        | Recouping platby probíhá.                                                                                                       | No                                                         |
| Odesláno                     | Platba byla odeslána do vaší banky.                                                                                                     | No                                                         |
| Opětovného zpracování             | V platbě došlo k systémové chybě společnosti Microsoft a probíhá její zpracování.                                                                  | No                                                         |
| Reversed                 | Platba byla stornována vaší bankou a bude odeslána znovu v dalším platebním cyklu.                                                     | No                                                         |
| Daňová faktura odmítnuta     | Vaše daňová faktura byla během revize odmítnuta. Všechny probíhající platby budou pozastaveny, dokud se nedokončí kontrola daňové faktury.                 | Podrobnosti získáte od [podpory Microsoftu](https://developer.microsoft.com/windows/support)  .                      |
| Daňová faktura pod kontrolou | Vaše daňové faktury se přezkoumávají. Po schválení daňové faktury se vaše platba uvolní.                                   | No                                                         |
| Zamítnuto                 | Platba byla odmítnuta vaší bankou.                                                                                                      | Podrobnosti získáte od své banky.                             |
|||

## <a name="export-data-page"></a>Stránka pro export dat

Pomocí těchto pokynů exportujte data.

Stránka exportovat data se neaktualizuje sama na sebe. Je možné, že budete muset stránku aktualizovat ručně, aby se zobrazila nejnovější data.

Výsledkem vašeho filtru může být chyba, že **není k dispozici žádná data** . To pravděpodobně znamená, že jste opustili výchozí časové období, které jste vybrali po dobu tří měsíců, a pak si vybrali ID platby z vybírání, které je mimo tuto dobu. Rozbalte své časové období a zkuste to znovu.

## <a name="payments"></a>Platby

![Exportovat platby](./media/pc-export-payments.png)

Tato možnost nabízí stažení plateb, které jste obdrželi v bance, pro daný program, přidruženou daň a agregovanou částku. Tato sestava se používá pro mnoho programů partnerského centra, takže některé sloupce mohou být pro vaši sestavu nerelevantní. Tyto sloupce jsou označeny níže.

| Název sloupce | Popis |
| --- | --- |
| participantID            | Primární identita partnera v rámci programu                                                                             |
| participantIDType        | Obvykle ID programu pro pobídkové programy a ID prodejce pro aplikace ze Storu                                                                |
| účastník          | Název partnerského partnera                                                                                                               |
| programName              | Motivace/uložení názvu programu                                                                                                              |
| vytvořené                   | Množství realizované v měně za tento program/participantID                                                                       |
| earnedUSD                | Množství získané pro ID programu/účastníka v USD                                                                                      |
| withheldTax              | Množství sražené daně v měně za program/participantID                                                               |
| salesTax                 | Celková částka DPH v platbě za program/participantID (platí jenom pro motivační program)                   |
| serviceFeeTax            | Celková částka serviceFeeTax v platbě k měně pro program/participantID (platí pro programy pro Store a Azure Marketplace) |
| totalPayment             | Celková platba v místní měně s výjimkou srážkové daně a zahrnutí DPH (Pokud je k dispozici) pro program/participantID   |
| currencyCode             | Plaťte podle kódu měny                                                                                                                      |
| paymentMethod            | Metoda použitá pro placení partnera, například přenos elektronické banky, dobropis                                                     |
| paymentID                | Jedinečný identifikátor pro platbu Toto číslo je obvykle viditelné v bankovním výpisu (platí pouze pro platby SAP).              |
| paymentStatus            | Stav platby                                                                                                                            |
| paymentStatusDescription | Popisný popis stavu platby                                                                                                    |
| paymentDate              | Platba data byla odeslána od společnosti Microsoft.                                                                                                      |
|||

## <a name="transaction-history"></a>Historie transakcí

![Exportovat historii transakcí](./media/pc-export-transaction.png)

Tato možnost poskytuje stažení každé položky řádku pro přijetí dat, která se zobrazí na stránce Historie transakcí, typ, datum, přidružená částka transakce, zákazník, produkt a další transakční údaje, které se vztahují k vašim programům.

| Název sloupce | Popis | Použitelnost pro motivaci/uložení/Azure Marketplace |
| --- | --- | --- |
| earningId                      | Jedinečný identifikátor pro každý z nich                                                                                                       | Vše                                                            |
| participantId                  | Primární identita partnera v rámci programu                                                                            | Vše                                                            |
| participantIdType              | Převážně ID programu pro pobídkové programy a prodejce, pokud je to pro aplikace ze Storu a Azure Marketplace                                          | Vše                                                            |
| účastník                | Název partnerského partnera                                                                                                              | Vše                                                            |
| partnerCountryCode             | Umístění/země/oblast pro partnerský partner                                                                                                  | Vše                                                            |
| programName                    | Motivace/uložení názvu programu                                                                                                             | Vše                                                            |
| transactionId                  | Jedinečný identifikátor pro transakci                                                                                                    | Vše                                                            |
| transactionCurrency            | Měna, ve které došlo k původní transakci zákazníka (nejedná se o měnu umístění partnera)                                     | Vše                                                            |
| transactionDate                | Datum transakce Užitečné pro programy, kde mnoho transakcí přispívá k jednomu z nich                                           | Vše                                                            |
| transactionExchangeRate        | Datum směnného kurzu použité k zobrazení odpovídající částky transakce v USD                                                                 | Vše                                                            |
| transactionAmount              | Částka transakce v původní měně transakce na základě toho, které z nich se vygenerovaly                                              | Vše                                                            |
| transactionAmountUSD           | Částka transakce v USD                                                                                                                | Vše                                                            |
| páka                          | Označuje obchodní pravidlo pro vyloučení.                                                                                                  | Vše                                                            |
| earningRate                    | Míra motivace uplatňovaná na částku transakce pro vygenerování výnosu                                                                      | Vše                                                            |
| quantity                       | Liší se v závislosti na programu. Indikuje fakturované množství transakčních programů.                                                            | Vše                                                            |
| quantityType                   | Indikuje typ množství, například fakturované množství, MAU                                                                             | Vše                                                            |
| earningType                    | Indikuje, že se jedná o poplatek, Rabat, co-op, prodej atd.                                                                                          | Vše                                                            |
| earningAmount                  | Peněžní částka v původní transakční měně                                                                                      | Vše                                                            |
| earningAmountUSD               | Množství využité v USD                                                                                                                    | Vše                                                            |
| earningDate                    | Datum přijetí                                                                                                                      | Vše                                                            |
| calculationDate                | Datum výpočtu v systému                                                                                            | Vše                                                            |
| earningExchangeRate            | Směnný kurz použitý k zobrazení odpovídajících částek v USD                                                                                  | Vše                                                            |
| exchangeRateDate               | Datum směnného kurzu použité k výpočtu EarningAmount USD                                                                                   | Vše                                                            |
| paymentAmountWOTax             | Celková částka (bez daně) v platbě k měně pro jenom odeslané platby                                                                 | Vše                                                            |
| paymentCurrency                | Platíte do měny zvolené partnerem v platebním profilu. Zobrazuje se jenom pro odeslané platby.                                                   | Vše                                                            |
| paymentExchangeRate            | Směnný kurz používaný k výpočtu paymentAmountWOTax v měně platby pomocí ExchangeRateDate                                            | Vše                                                            |
| claimId                        | Jedinečný identifikátor pro deklaraci identity                                                                                                              | Motivace – jenom některé programy                                |
| planId                         | Jedinečný identifikátor pro plán                                                                                                               | Motivace – jenom některé programy                                |
| paymentId                      | Jedinečný identifikátor pro platbu Toto číslo je obvykle viditelné ve výpisu bank.                                                 | Pouze platby SAP                                              |
| paymentStatus                  | Stav platby                                                                                                                           | Vše                                                            |
| paymentStatusDescription       | Popisný popis stavu platby                                                                                                   | Vše                                                            |
| customerId                     | Bude vždy prázdné                                                                                                                     | Pouze pobídkové programy (výjimka: OEM) a Azure Marketplace |
| customerName                   | Bude vždy prázdné                                                                                                                     | Pouze pobídkové programy (výjimka: OEM) a Azure Marketplace |
| partNumber                     | Bude vždy prázdné                                                                                                                     | Některé aplikace a Azure Marketplace pro motivaci a ukládání        |
| NázevVýrobku                    | Název produktu propojený s transakcí                                                                                                       | Vše                                                            |
| productId                      | Jedinečný identifikátor produktu                                                                                                                | Uložení a Azure Marketplace                                    |
| parentProductId                | Jedinečný identifikátor nadřazeného produktu Pokud pro transakci není k dispozici nadřazený produkt, pak ID nadřazeného produktu = ID produktu. | Uložení a Azure Marketplace                                    |
| parentProductName              | Název nadřazeného produktu Pokud pro transakci není k dispozici nadřazený produkt, pak název nadřazeného produktu = název produktu.   | Uložení a Azure Marketplace                                    |
| productType                    | Typ produktu, jako je aplikace, doplněk nebo hra                                                                                        | Uložení a Azure Marketplace                                    |
| invoiceNumber                  | Číslo faktury (platí jenom pro EA)                                                                                                  | Motivace a Azure Marketplace – pouze některé programy           |
| subscriptionId                 | Identifikátor předplatného přidružený k zákazníkovi                                                                                         | Motivace – jenom některé programy                                 |
| subscriptionStartDate          | Počáteční datum odběru                                                                                                                  | Motivace – jenom některé programy                                 |
| subscriptionEndDate            | Datum ukončení předplatného                                                                                                                    | Motivace – jenom některé programy                                 |
| resellerId                     | Identifikátor prodejce                                                                                                                      | Motivace – jenom některé programy                                 |
| proprodejce                   | Jméno prodejce                                                                                                                            | Motivace – jenom některé programy                                 |
| distributorId                  | Identifikátor distributora                                                                                                                   | Motivace – jenom některé programy                                 |
| Distributor                | Název distributora                                                                                                                         | Motivace – jenom některé programy                                 |
| agreementNumber                | Číslo smlouvy                                                                                                                         | Motivace – jenom některé programy                                 |
| agreementStartDate             | Počáteční datum smlouvy                                                                                                                     | Motivace – jenom některé programy                                 |
| agreementEndDate               | Koncové datum smlouvy                                                                                                                       | Motivace – jenom některé programy                                 |
| workload                       | Úloha                                                                                                                                 | Motivace – jenom některé programy                                 |
| transactionType                | Typ transakce, například nákup, refundace, Storno nebo vrácení peněz                                                               | Uložení a Azure Marketplace                                    |
| localProviderSeller            | Místní poskytovatel/prodejce záznamu                                                                                                          | Jenom úložiště                                                     |
| taxRemitted                    | Množství poplatků za daň (prodej, použití nebo DPH/GST)                                                                                   | Uložení a Azure Marketplace                                    |
| taxRemitModel                  | Strana odpovědná za přebírání daní (prodej, použití nebo DPH/GST daně)                                                                    | Jenom úložiště                                                     |
| storeFee                       | Množství, které Microsoft zachovává jako poplatek za zpřístupnění aplikace nebo doplňku ve Storu                                           | Jenom úložiště                                                     |
| transactionPaymentMethod       | Platební nástroj zákazníka, který se používá pro transakci, jako je karta, fakturace mobilních dopravců nebo PayPal                                | Uložení a Azure Marketplace                                    |
| tpan                           | Indikuje síť AD třetí strany.                                                                                                     | Store – pouze inzeráty                                               |
| customerCountry                | Země nebo oblast zákazníka                                                                                                                         | Uložení a Azure Marketplace                                    |
| customerCity                   | Město zákazníka                                                                                                                            | Uložení a Azure Marketplace                                    |
| customerState                  | Stav zákazníka                                                                                                                           | Uložení a Azure Marketplace                                    |
| customerZip                    | PSČ zákazníka, PSČ                                                                                                                 | Uložení a Azure Marketplace                                    |
| purchaseTypeCode               | Bude vždy prázdné                                                                                                                     | Pobídkový program – CRI                                        |
| purchaseOrderType              | Bude vždy prázdné                                                                                                                     | Pobídkový program – CRI                                        |
| purchaseOrderCoverageStartDate | Bude vždy prázdné                                                                                                                     | Pobídkový program – CRI                                        |
| purchaseOrderCoverageEndDate   | Bude vždy prázdné                                                                                                                     | Pobídkový program – CRI                                        |
| programOfferingLevel           |                                                                                                                                          | Pobídkový program – CRI                                        |
| TenantID                       |                                                                                                                                          | Motivační program                                             |
| externalReferenceId            | Jedinečný identifikátor programu                                                                                                        | Programy přímých plateb (motivace a obchod)                      |
| externalReferenceIdLabel       | Popisek jedinečného identifikátoru                                                                                                                  | Programy přímých plateb (motivace a obchod)                      |
|||

## <a name="historical-statements"></a>Historické příkazy

![Exportovat historické příkazy](./media/pc-export-statements.png)

Historie transakcí od 1. července 1 2019 se zpracovává samostatně. Příkazy budou používat následující pole místo aktuálních.

> [!NOTE]
> Starší historie transakcí obsahuje sloupec s názvem "vyhrazeno", který odpovídá sloupci "příjmy" v moderní historii, s tím rozdílem, že vylučuje všechny příjmy se stavem = "odeslání platby".

> [!NOTE]
> Filtry, jako jsou 3M, 6 min nebo 12M, se nebudou vztahovat na oddíl **historická prohlášení** .

| Název pole | Popis |
| --- | --- |
| Zdroj výnosů          | Zdroj výnosů na základě toho, kde transakce proběhla, například Microsoft Store, úložiště Windows Phone, Windows Store 8 nebo inzerce                  |
| ID objednávky                | Jedinečný identifikátor objednávky Toto ID umožňuje identifikovat nákupní transakce s příslušnými transakcemi, které nepatří do nákupních transakcí, jako jsou refundace nebo vratek. Obě budou mít stejné ID objednávky. V případě rozděleného za poplatek, u kterého bylo použito více způsobů platby pro jeden nákup, vám také umožní propojit transakce nákupu. |
| ID transakce          | Jedinečný identifikátor transakce.                                                                                                                                          |
| Datum a čas transakce   | Datum a čas, kdy došlo k transakci (UTC).                                                                                                                       |
| ID nadřazeného produktu       | Jedinečný identifikátor nadřazeného produktu Pokud pro transakci není k dispozici nadřazený produkt, pak ID nadřazeného produktu = ID produktu.                                |
| ID produktu              | Jedinečný identifikátor produktu                                                                                                                                              |
| Název nadřazeného produktu     | Název nadřazeného produktu Pokud pro transakci není k dispozici nadřazený produkt, pak název nadřazeného produktu = název produktu.                                  |
| Název produktu            | Název produktu                                                                                                                                                    |
| Typ produktu            | Typ produktu, jako je aplikace, doplněk nebo hra                                                                                                                       |
| Množství                | Když je zdroj výnosů Microsoft Store pro firmy, množství představuje počet zakoupených licencí. U všech ostatních zdrojů příjmů bude množství vždy 1. I v případě, že jedna transakce je rozdělena na dvě řádkové položky, protože byly použity dvě různé metody platby, zobrazí se v každé položce řádku množství 1. |
| Transaction Type (Typ transakce)        | Typ transakce, například nákup, refundace, Storno nebo vrácení peněz                                                                                              |
| Způsob platby          | Platební nástroj zákazníka, který se používá pro transakci, jako je karta, fakturace mobilních dopravců nebo PayPal                                                               |
| Země nebo oblast        | Země nebo oblast, kde se transakce stala                                                                                                                          |
| Místní poskytovatel/prodejce | Místní poskytovatel/prodejce záznamu                                                                                                                                        |
| Transakční měna    | Měna transakce                                                                                                                                            |
| Částka transakce      | Množství transakce                                                                                                                                              |
| DPH poukázané            | Množství poplatků za daň (prodej, použití nebo DPH/GST)                                                                                                                  |
| Čisté příjmy            | Množství transakce méně se propoukázané.                                                                                                                                   |
| Poplatek za Store               | Procento čistých příjmů uchovávaných Microsoftem jako poplatek za zpřístupnění aplikace nebo doplňku ve Storu                                                      |
| Výsledek aplikace            | Čisté příjmy minus poplatek za Store                                                                                                                                       |
| Odmítnuté daně          | Množství srážky daně z příjmu (zahrnuté do **rezervovaného** souboru CSV)                                                                                                |
| Platba                 | Aplikace pokračuje bez jakýchkoli příslušných srážkových daní za příjem (částka uvedená v transakční měně). Nezahrnuto do **rezervovaného** souboru CSV.                               |
| Míra FX                 | Směnný kurz, který se používá k převodu transakční měny na platební měnu                                                                                         |
| Měna platby        | Měna, ve které se platby provedly                                                                                                                                       |
| Převedená platba       | Částka platby převedená na platební měnu pomocí míry FX                                                                                                         |
| Model daňové úhrady         | Strana odpovědná za přebírání daní (prodej, použití nebo DPH/GST daně)                                                                                                   |
| Datum a čas nároku   | Datum a čas, kdy transakce pokračuje v nároku na výběr (UTC). Když je vytvořen výběr, zahrnuje transakci transakce s datem a časem nároku před datem vytvoření výběr (zahrnuto pouze do **rezervovaného** souboru CSV). |
| Poplatky                 | Zobrazuje rozpis všech podrobností o poplatcích, které jsou agregované ve sloupci částka transakce (zahrnuté jenom pro Azure Marketplace; nezahrnuté do **rezervovaného** souboru CSV). |
|||

## <a name="next-step"></a>Další krok

- [Podrobnosti zásad plateb](./payout-policy-details.md)
