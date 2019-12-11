---
title: Shrnutí výběrů na komerčním webu Marketplace | Azure Marketplace
description: Ve shrnutí výběr se zobrazí podrobnosti o peníze, které jste získali s vaší nabídkou. Také vám umožní zjistit, kdy obdržíte platby a kolik vám bude placeno.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 0d05802c9d5d80f91913291d710b674369f0ff17
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980267"
---
# <a name="payout-reporting"></a>Vytváření sestav o výběrech

**Souhrn výběrů** vám ukáže podrobnosti o peníze, které jste získali od Microsoftu. Také vám umožní zjistit, kdy obdržíte platby a kolik vám bude placeno.

Pokud prodáváte nabídky v Azure Marketplace, zobrazí se také informace o úspěšných výběrech v **souhrnu výběrů**. Další informace týkající se Azure Marketplace platby najdete v článku [zásady pro účast Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkId=722436) a [Microsoft Azure Marketplace smlouvu vydavatele](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Aby bylo možné výsledek vyhodnotit, vaše výtěžek musí dosáhnout [prahové hodnoty pro platbu](payment-thresholds-methods-timeframes.md) $50. Podrobnosti o prahové hodnotě platby najdete na této stránce a Projděte si [smlouvu o Microsoft Azure Marketplace vydavatele](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Výběr sestavy: rozdíl mezi portál partnerů cloudu a Partnerským centrem](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Typy zákazníků](#customer-types)
- [Vztah mezi výběrem a využitím](#corelation-between-payout-and-usage)
- [Stažení Historie transakcí](#transaction-history-download-export)
- [Otázky a podpora fakturace](#billing-questions-and-support)

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Výběr sestavy: rozdíl mezi portál partnerů cloudu a Partnerským centrem

| | Portál Cloud Partner | Partnerské centrum |
|---------|---------|---------|
| Odkazy | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory a https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| Navigace | Vytváření sestav ve výběrech poskytovaných ve výběrech Insights | Sestavy výběrů poskytované v partnerském centru – ikona výběr |
| Rozsah | <ul> <li>Transakce pro položku na řádku je viditelná, pro probíhající shromažďování, shromažďování a platby. </li> <li>Vytváření sestav – zobrazí všechny položky řádků po vytvoření objednávky nákupu, včetně probíhajícího shromažďování a fakturace probíhá, a stavu kolekce a položek řádků, které ještě nejsou způsobilé k placení. </li> </ul> | <ul> <li>Zobrazuje položky řádku, jakmile se považují za oprávněné tržby.</li> <li>Zákazníci se nejprve účtují do Microsoftu a potom prodejci uvidí, že sestava výběru začíná.</li> <li>Sestava výběru nebude zobrazovat probíhající shromažďování a probíhá fakturace.  </li> </ul>  |
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

|Popis    |    Datum  | Objednávky/použití  | Výplata |
|----------|----------|-----------|-------------|
|Období objednávky   | 15. srpna 2019 – srpna 30, 2019 | **Pořadí atributů korelace** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Použití** <br> <ul> <li>CustomerId </li> <li>Název zákazníka</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Odhadované rozšířené náklady <br> Odhadovaný výběr (PC) </li> </ul> |  |
|Termín ukončení (měsíc)   | 30. srpna 2019 | | |
|Datum fakturace | Září 1, 2019 | | |
|Datum platby zákazníka | Září 1, 2019 | | |
|V úschově období (jenom kreditní karty, 30 dní) | Září 1, 2019 – září 30, 2019 | | **Pořadí atributů korelace:** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li> Název zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Název zákazníka</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stav výběr:** Nezpracované |
|Začátek období shromažďování | Září 1, 2019 | | |
|Konec období shromažďování (maximum, 30 dní) | Září 30, 2019 | | |
|Výběr data výpočtu (měsíčně na 15.) | Říjen 1, 2019 | | **Korelační atributy** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li>Název zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Název zákazníka</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stav výběr:** Nadcházející |
|Datum vyvýběru | 15. října 2019 | | **Korelační atributy** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li> Název zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Název zákazníka</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stav výběr:** Platba odeslána |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Smlouva Enterprise (čtvrtletní/měsíční zákazníci)

| Popis |    Datum  | Využití | Výplata |
|----------|----------|---------|-----------|
|Období objednávky | 15. srpna 2019 – srpna 30, 2019 | **Pořadí atributů korelace** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Sestava využití** <br> <ul> <li>CustomerId </li> <li>Název zákazníka</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Odhadované rozšířené náklady <br> Odhadovaný výběr (PC) </li> </ul> | |
|Termín ukončení (čtvrtletí) | Září 30, 2019 | | |
|Datum fakturace | 15. října 2019 | | |
|V úschově období (jenom kreditní karty, 30 dní) | – | | |
|Začátek období shromažďování | 15. října 2019 | | |
|Pouze kreditní karty, 30 dní | 1\. listopadu 2019 – 30. listopadu 2019 | | |
|Konec období shromažďování (maximum, 90 dní) | 15. ledna 2020 | | |
|Datum platby zákazníka | 30. prosince 2019 | | |
|Výpočet výběr | 15. ledna 2020 | | |
|Datum vyvýběru | 15. února 2020 | | **Pro zákazníky s čtvrtletním základem** <br> <br> **Sestava objednávek** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li> Název zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Název zákazníka</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stav Vyvýběru:** odesláno |

<!---
## Billing

Depending on the transaction option used, the publisher’s software license fees can be presented as follows:

* Free: No charge for software licenses.
* **Bring the own license (BYOL)**: Any applicable charges for software licenses are managed directly between the publisher and customer. Microsoft only passes through Azure infrastructure usage fees. (Virtual Machines and Azure Applications only).
* **Pay-as-you-go**: Software license fees are presented as a per-hour, per-core (VCPU) pricing rate based on the Azure infrastructure used. This only applies to Virtual Machines and Azure Applications. 
* **Subscription pricing**: Software license fees are presented as a monthly or annual recurring fee billed as a flat rate or per-seat. This only applies to SaaS Apps and Azure Applications - Managed Apps.

### Pay as you go

If you enable the Pay-As-You-Go option, then you have the following cost structure. 

* If you enable the Pay-As-You-Go option, then you have the following cost structure.

|Your license cost  | $1.00 per hour  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$1.14 per hour*       |

* In this scenario, Microsoft bills $1.14 per hour for use of your published VM image.

|Microsoft bills  | $1.14 per hour  |
|---------|---------|
|Microsoft pays you 80% of your license cost|   $0.80 per hour     |
|Microsoft keeps 20% of your license cost  |  $0.20 per hour       |
|Microsoft keeps 100% of the Azure usage cost | $0.14 per hour |

### Bring Your Own License (BYOL)

* If you enable the BYOL option, then you have the following cost structure.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

### SaaS App subscription

This option must be configured to sell through Microsoft and can be priced at a flat rate or per user on a monthly or annual basis.
•   If you enable the Sell through Microsoft option for a SaaS offer, then you have the following cost structure.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.
* Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020. In this scenario, Microsoft bills $100.00 for your software license and pays out $90.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost <br> \* Microsoft pays you 90% of your license cost for any qualified SaaS apps   |   $80.00 per month <br> \* $90.00 per month    |
|Microsoft keeps 20% of your license cost <br> \* Microsoft keeps 10% of your license cost for any qualified SaaS apps.  |  $20.00 per month <br> \* $10.00     |

**Reduced Marketplace Service Fee:** For certain SaaS Products that you publish on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%.  In order for your Product to qualify, at least one of your products must be designated by Microsoft as either IP co-sell ready or IP co-sell prioritized. To receive this reduced Marketplace Service Fee for the month, eligibility must be met at least five (5) business days before the end of the previous calendar month. Reduced Marketplace Service fee will not apply to VMs, Managed Apps or any other products made available through our Commercial Marketplace.  This Reduced Marketplace Service Fee will be available to qualified offers, with license charges collected by Microsoft between May 1, 2019 and June 30, 2020.  After that time, the Marketplace Service Fee will return to its normal amount.

### Customer invoicing, payment, billing, and collections

**Invoicing and payment**

Publisher can use the customer's preferred invoicing method to deliver subscription or PAYGO software license fees.

**Enterprise agreement** 

If the customer's preferred invoicing method is the Microsoft Enterprise Agreement, your software license fees will be billed using this invoicing method as an itemized cost, separate from any Azure-specific usage costs.

**Credit cards and monthly invoice** 

Customers can also pay using a credit card and a monthly invoice. In this case, your software license fees will be billed just like the Enterprise Agreement scenario, as an itemized cost, separate from any Azure-specific usage costs.



**Billing and collections** 

Publisher software license billing is presented using the customer selected method of invoicing and follows the invoicing timeline. Customers without an Enterprise Agreement in place are billed monthly for marketplace software licenses. Customers with an Enterprise Agreement are billed monthly via an invoice that is presented quarterly.

When subscription or Pay-as-You-Go pricing models are selected, Microsoft acts as the agent of the publisher and is responsible for all aspects of billing, payment, and collection.

### Publisher payout and reporting

* Any software licensing fees collected by Microsoft as an agent are subject to a 20% transaction fee unless otherwise specified and are deducted at the time of publisher payout.

* Customers typically purchase using the Enterprise Agreement or a credit-card enabled pay-as-you-go agreement. The agreement type determines billing, invoicing, collection, and payout timing.
--->

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
| množství                       | Liší se v závislosti na programu. Indikuje fakturované množství transakčních programů.                                                            | 
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
| customerId                     | Bude vždy prázdné                                                                                                                     |
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
| earningAmountInLastPaymentCurrency       | Peněžní částka v poslední platební měně (pole bude prázdné, pokud se nezaplatily žádné předchozí platby)                                                                                                                   |
| lastPaymentCurrency       | Poslední platební měna (pole bude prázdné, pokud se nevyplatila žádná předchozí platba)                                                                                                                   |
| AssetId       | Jedinečný identifikátor objednávek zákazníků pro vaši službu Marketplace  Představuje položky transakčního nákupu v řádku. Může existovat více prostředků.                                                                                                                   |
| OrderId       | má vztah k faktuře zákazníka.                                                                                                                   |
| LineItemId       | jednotlivý řádek na faktuře zákazníka                                                                                                                   |
| Země zákazníka       | Název země, kterou zadal zákazník.  To může být jiné než země v předplatném Azure zákazníka.                                                                                                                   |
| Zákaznická EmailAddress       | E-mailová adresa poskytnutá koncovým zákazníkem  Může se lišit od e-mailové adresy v předplatném Azure zákazníka.                                                                                                                   |
| SkuId       | ID skladové položky, jak je definováno během publikování. Nabídka může mít mnoho SKU, ale SKU může být přidruženo pouze k jedné nabídce.                                                                                                                   |

>[!Note]
>Všechny sestavy a přehledy pro možnost publikování v režimu Transact jsou k dispozici prostřednictvím oddílu Insights v části portál partnerů cloudu nebo analýza v partnerském centru.

## <a name="billing-questions-and-support"></a>Otázky a podpora fakturace

Pokud potřebujete pomoc s fakturací na otázky, kontaktujte prosím [podporu komerčního vydavatele na webu Marketplace](https://aka.ms/marketplacepublishersupport).
