---
title: Shrnutí výběrů na komerčním webu Marketplace | Azure Marketplace
description: Ve shrnutí výběr se zobrazí podrobnosti o peníze, které jste získali s vaší nabídkou. Také vám umožní zjistit, kdy obdržíte platby a kolik vám bude placeno.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 6ee6b6f325ba58ecaa3c3acb5d5ded173262bafb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269897"
---
# <a name="payout-reporting"></a>Vytváření sestav o výběrech

[**Souhrn výběrů**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) vám ukáže podrobnosti o peníze, které jste získali od Microsoftu. Také vám umožní zjistit, kdy obdržíte platby a kolik vám bude placeno.

Pokud prodáváte nabídky v Azure Marketplace, zobrazí se také informace o úspěšných výběrech v **souhrnu výběrů**. Další informace týkající se Azure Marketplace platby najdete v článku [zásady pro účast Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkId=722436) a [Microsoft Azure Marketplace smlouvu vydavatele](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Aby bylo možné výsledek vyhodnotit, vaše výtěžek musí dosáhnout [prahové hodnoty pro platbu](payment-thresholds-methods-timeframes.md) $50. Podrobnosti o prahové hodnotě platby najdete na této stránce a Projděte si [smlouvu o Microsoft Azure Marketplace vydavatele](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Role a oprávnění pro přístup k sestavě výběr](#roles-and-permission-to-access-the-payout-report)
- [Výběr sestavy: rozdíl mezi portál partnerů cloudu a Partnerským centrem](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Typy zákazníků](#customer-types)
- [Vztah mezi výběrem a využitím](#corelation-between-payout-and-usage)
- [Stažení Historie transakcí](#transaction-history-download-export)
- [Otázky a podpora fakturace](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Role a oprávnění pro přístup k sestavě výběr

| Sestavy/stránky    | Vlastník účtu    | Manager  | Vývojář | Obchodní Přispěvatel |  Přispěvatel pro finance | Obchodník |
|------------------|------------------|----------|-----------|----|----|-----|
| Sestava pořízení (včetně dat téměř v reálném čase) | Může zobrazit | Může zobrazit | Bez přístupu | Bez přístupu | Může zobrazit | Bez přístupu |
| Sestava nebo odpovědi zpětné vazby | Může zobrazit a odeslat názor. | Může zobrazit a odeslat názor. | Může zobrazit a odeslat názor. | Bez přístupu | Bez přístupu | Může zobrazit a odeslat názor. |
| Sestava stavu (včetně dat téměř v reálném čase) | Může zobrazit | Může zobrazit | Může zobrazit | Může zobrazit | Bez přístupu | Bez přístupu |
| Sestava využití | Může zobrazit | Může zobrazit | Může zobrazit | Může zobrazit | Bez přístupu | Bez přístupu |
| Účet pro výběr | Může aktualizovat | Bez přístupu | Bez přístupu | Bez přístupu | Může aktualizovat | Bez přístupu |
| Daňový profil | Může aktualizovat | Bez přístupu | Bez přístupu | Bez přístupu | Může aktualizovat | Bez přístupu |
| Přehled plateb | Může zobrazit | Bez přístupu | Bez přístupu | Bez přístupu | Může zobrazit | Bez přístupu |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Výběr sestavy: rozdíl mezi portál partnerů cloudu a Partnerským centrem

| | Portál Cloud Partner | Partnerské centrum |
|---------|---------|---------|
| Odkazy | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory) a [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Navigace | Vytváření sestav ve výběrech poskytovaných ve výběrech Insights | Sestavy výběrů poskytované v partnerském centru – ikona výběr |
| Scope | <ul> <li>Transakce pro položku na řádku je viditelná, pro probíhající shromažďování, shromažďování a platby. </li> <li>Vytváření sestav – zobrazí všechny položky řádků po vytvoření objednávky nákupu, včetně probíhajícího shromažďování a fakturace probíhá, a stavu kolekce a položek řádků, které ještě nejsou způsobilé k placení. </li> </ul> | <ul> <li>Zobrazuje položky řádku, jakmile se považují za oprávněné tržby.</li> <li>Zákazníci se nejprve účtují do Microsoftu a potom prodejci uvidí, že sestava výběru začíná.</li> <li>Sestava výběru nebude zobrazovat probíhající shromažďování a probíhá fakturace.  </li> </ul>  |
| Transakce není připravená na výběr. | Probíhá fakturace | Další odhadovaná platba: stav vyvýběru je v nezpracovaném stavu.  |
| Stav výběr |  | Nezpracované <br> Zisk má nárok na platbu. Zůstane v tomto stavu pro období chlazení, jak je definováno v Průvodci programu pro program k Pobídkě. <br> <br> Nadcházející <br> Platební objednávka – vygenerované interní recenze, které čekají na vyřízení, než se zpracuje platba <br> <br> Odesláno: <br> Platba byla odeslána do vaší banky. |

## <a name="customer-types"></a>Typy zákazníků

### <a name="enterprise-agreement"></a>Smlouva Enterprise

Zákazníkům, kteří nemají smlouva Enterprise v místě, se účtují měsíčně pro licence na tržiště pro software Marketplace. Zákazníci s smlouva Enterprise se účtují měsíčně prostřednictvím faktury, která se prezentuje čtvrtletně.

### <a name="credit-cards-and-monthly-invoice"></a>Kreditní karty a měsíční faktury

Zákazníci můžou také platit pomocí platební karty a faktury za měsíc. V takovém případě se licenční poplatky za software účtují měsíčně.

### <a name="csp-and-direct-pay-users"></a>Uživatelé CSP a přímé platby

Například pokud zákazník nakupuje pomocí platební karty.

## <a name="corelation-between-payout-and-usage"></a>Vztah mezi výběrem a využitím

|Popis    |    Datum  | Objednávky/použití  | Platební |
|----------|----------|-----------|-------------|
|Období objednávky   | 15. srpna 2019 – srpna 30, 2019 | **Pořadí atributů korelace** <br> <ul> <li>Seskup</li> <li>ID</li> </ul> <br> **Použití** <br> <ul> <li>ID </li> <li>Název zákazníka</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Odhadované rozšířené náklady <br> Odhadovaný výběr (PC) </li> </ul> |  |
|Termín ukončení (měsíc)   | 30. srpna 2019 | | |
|Datum fakturace | Září 1, 2019 | | |
|Datum platby zákazníka | Září 1, 2019 | | |
|V úschově období (jenom kreditní karty, 30 dní) | Září 1, 2019 – září 30, 2019 | | **Pořadí atributů korelace:** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li> Název zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>ID</li> <li>Název zákazníka</li> <li>Seskup</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stav výběr:** Nezpracované |
|Začátek období shromažďování | Září 1, 2019 | | |
|Konec období shromažďování (maximum, 30 dní) | Září 30, 2019 | | |
|Výběr data výpočtu (měsíčně na 15.) | Říjen 1, 2019 | | **Korelační atributy** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li>Název zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>ID</li> <li>Název zákazníka</li> <li>Seskup</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stav výběr:** Nadcházející |
|Datum vyvýběru | 15. října 2019 | | **Korelační atributy** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li> Název zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>ID</li> <li>Název zákazníka</li> <li>Seskup</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stav výběr:** Platba odeslána |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Smlouva Enterprise (čtvrtletní/měsíční zákazníci)

| Popis |    Datum  | Využití | Platební |
|----------|----------|---------|-----------|
|Období objednávky | 15. srpna 2019 – srpna 30, 2019 | **Pořadí atributů korelace** <br> <ul> <li>Seskup</li> <li>ID</li> </ul> <br> **Sestava využití** <br> <ul> <li>ID </li> <li>Název zákazníka</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Odhadované rozšířené náklady <br> Odhadovaný výběr (PC) </li> </ul> | |
|Termín ukončení (čtvrtletí) | Září 30, 2019 | | |
|Datum fakturace | 15. října 2019 | | |
|V úschově období (jenom kreditní karty, 30 dní) | neuvedeno | | |
|Začátek období shromažďování | 15. října 2019 | | |
|Pouze kreditní karty, 30 dní | 1\. listopadu 2019 – 30. listopadu 2019 | | |
|Konec období shromažďování (maximum, 90 dní) | 15. ledna 2020 | | |
|Datum platby zákazníka | 30. prosince 2019 | | |
|Výpočet výběr | 15. ledna 2020 | | |
|Datum vyvýběru | 15. února 2020 | | **Pro zákazníky s čtvrtletním základem** <br> <br> **Sestava objednávek** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li> Název zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>ID</li> <li>Název zákazníka</li> <li>Seskup</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stav Vyvýběru:** odesláno |

## <a name="transaction-history-download-export"></a>Export stažení Historie transakcí

Tato možnost nabízí stažení každé položky řádku k přijetí, kterou vidíte na stránce Historie transakcí, typu, datu, přidruženém množství transakce, zákazníkovi, produktu a dalších transakčních detailech vztahujících se k programu pobídek.

| Název sloupce     | Popis    |
|-------------|-------------------------------|
| earningId                      | Jedinečný identifikátor pro každý z nich                                                                                                       |
| participantId                  | Primární identita partnera v rámci programu                                                                            |
| participantIdType              | Převážně ID programu pro pobídkové programy a prodejce, pokud je to pro aplikace ze Storu a Azure Marketplace                                          |
| účastník                | Název partnerského partnera                                                                                                              |
| partnerCountryCode             | Umístění/země pro partnerský partner                                                                                                  |
| programName                    | Motivace/uložení názvu programu                                                                                                             |
| transactionId                  | Jedinečný identifikátor pro transakci                                                                                                    |
| transactionCurrency            | Měna, ve které došlo k původní transakci zákazníka (nejedná se o měnu partnerského umístění)                                     |
| transactionDate                | Datum transakce Užitečné pro programy, kde mnoho transakcí přispívá k jednomu z nich                                           |
| transactionExchangeRate        | Směnný kurz použitý k zobrazení odpovídající částky transakce v USD                                                                 |
| transactionAmount              | Částka transakce v původní měně transakce na základě toho, které z nich se vygenerovaly                                              |
| transactionAmountUSD           | Částka transakce v USD                                                                                                                |
| páka                          | Označuje obchodní pravidlo pro vyloučení.                                                                                                  |
| earningRate                    | Míra motivace uplatňovaná na částku transakce pro vygenerování výnosu                                                                      |
| quantity                       | Liší se v závislosti na programu. Indikuje fakturované množství transakčních programů.                                                            |
| quantityType                   | Indikuje typ množství, například fakturované množství, MAU.                                                                                     |
| earningType                    | Uvádí, zda se jedná o poplatek, slevu, Coop, prodej atd.                                                                                          |
| earningAmount                  | Peněžní částka v původní transakční měně                                                                                      |
| earningAmountUSD               | Množství využité v USD                                                                                                                    |
| earningDate                    | Datum přijetí                                                                                                                      |
| calculationDate                | Datum výpočtu v systému                                                                                            |
| earningExchangeRate            | Směnný kurz použitý k zobrazení odpovídajících částek v USD                                                                                  |
| exchangeRateDate               | Datum směnného kurzu použité k výpočtu EarningAmount USD                                                                                   |
| paymentAmountWOTax             | Celková částka (bez daně) v platbě k měně pro jenom odeslané platby                                                                 |
| paymentCurrency                | Platíte do měny zvolené partnerem v platebním profilu. Zobrazuje se jenom pro odeslané platby.                                                   |
| paymentExchangeRate            | Směnný kurz používaný k výpočtu paymentAmountWOTax v měně platby pomocí ExchangeRateDate                                            |
| paymentId            | Jedinečný identifikátor pro platbu Toto číslo je viditelné ve výpisu bank.                                            |
| paymentStatus            | Stav platby                                            |
| paymentStatusDescription            | Popisný popis stavu platby                                            |
| ID                     | Bude vždy prázdné                                                                                                                     |
| customerName                   | Bude vždy prázdné                                                                                                                     |
| partNumber                     | Bude vždy prázdné                                                                                                                     |
| NázevVýrobku                    | Název produktu propojený s transakcí                                                                                                       |
| productId                      | Jedinečný identifikátor produktu                                                                                                                |
| parentProductId                | Jedinečný identifikátor nadřazeného produktu Poznámka: Pokud pro transakci není k dispozici nadřazený produkt, pak ID nadřazeného produktu = ID produktu. |
| parentProductName              | Název nadřazeného produktu Poznámka: Pokud pro transakci není k dispozici nadřazený produkt, pak název nadřazeného produktu = název produktu.   |
| productType                    | Typ produktu (například aplikace, doplněk, hra atd.)                                                                                        |
| invoiceNumber                  | Číslo faktury (platí jenom pro EA)                                                                                                  |
| resellerId                     | Identifikátor prodejce                                                                                                                      |
| proprodejce                   | Jméno prodejce                                                                                                                            |
| transactionType                | Typ transakce (například nákup, refundace, reverzní, vrácení peněz atd.)                                                               |
| localProviderSeller            | Místní poskytovatel/prodejce záznamu                                                                                                          |
| taxRemitted                    | Množství poplatků za daň (prodej, použití nebo DPH/GST).                                                                                   |
| taxRemitModel                  | Strana odpovědná za přebírání daní (prodej, použití nebo DPH/GST daně).                                                                    |
| storeFee                       | Množství, které Microsoft zachovává jako poplatek za zpřístupnění aplikace nebo doplňku ve Storu.                                            |
| transactionPaymentMethod       | Platební nástroj zákazníka, který se používá pro transakci (například karta, fakturaci mobilního dopravce, PayPal atd.)                                |
| tpan                           | Indikuje síť AD třetí strany.                                                                                                     |
| customerCountry                | Země zákazníka                                                                                                                         |
| customerCity                   | Město zákazníka                                                                                                                            |
| customerState                  | Stav zákazníka                                                                                                                           |
| customerZip                    | PSČ zákazníka, PSČ                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | Jedinečný identifikátor programu                                                                                                        |
| externalReferenceIdLabel       | Popisek jedinečného identifikátoru                                                                                                                  |
| transactionCountryCode       | Kód země, ve kterém se transakce stala                                                                                                                  |
| taxCountry       | Prodáno do země zákazníka                                                                                                                  |
| taxState       | Prodal stav zákazníka                                                                                                                  |
| taxCity       | Prodáváno zákazníkovi město                                                                                                                  |
| taxZipCode       | Prodáno do PSČ zákazníka                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Kód programu       | Řetězec, který se má mapovat s názvem programu                                                                                                                   |
| EarningAmountInLastPaymentCurrency       | Peněžní částka v poslední platební měně (pole bude prázdné, pokud se nezaplatily žádné předchozí platby)                                                                                                                   |
| lastPaymentCurrency       | Poslední platební měna (pole bude prázdné, pokud se nevyplatila žádná předchozí platba)                                                                                                                   |
| AssetId       | Jedinečný identifikátor objednávek zákazníků pro vaši službu Marketplace  Představuje položky transakčního nákupu v řádku. Může existovat více prostředků.                                                                                                                   |
| Seskup       | má vztah k faktuře zákazníka.                                                                                                                   |
| LineItemId       | jednotlivý řádek na faktuře zákazníka                                                                                                                   |
| Země zákazníka       | Název země, kterou zadal zákazník.  To může být jiné než země v předplatném Azure zákazníka.                                                                                                                   |
| Zákaznická EmailAddress       | E-mailová adresa poskytnutá koncovým zákazníkem  Může se lišit od e-mailové adresy v předplatném Azure zákazníka.                                                                                                                   |
| SkuId       | ID skladové položky, jak je definováno během publikování. Nabídka může mít mnoho SKU, ale SKU může být přidruženo pouze k jedné nabídce.                                                                                                                   |

>[!Note]
>Všechny sestavy a přehledy pro možnost publikování v režimu Transact jsou k dispozici prostřednictvím oddílu Insights v části portál partnerů cloudu nebo analýza v partnerském centru.

## <a name="billing-questions-and-support"></a>Otázky a podpora fakturace

Pokud potřebujete pomoc s fakturací na otázky, kontaktujte prosím [podporu komerčního vydavatele na webu Marketplace](https://aka.ms/marketplacepublishersupport).
