---
title: Shrnutí výplaty komerčního tržiště | Azure Marketplace
description: Přehled výplaty zobrazuje podrobnosti o penězích, které jste svou nabídkou vydělali. Také vám dá vědět, kdy budete dostávat platby a kolik vám bude zaplaceno.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 678dc8b058d0ae0694dafeb4222b2fc9f10ecda7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288695"
---
# <a name="payout-reporting"></a>Výplatní hlášení

[**Souhrn výplaty**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) zobrazuje podrobnosti o penězích, které jste vydělali u společnosti Microsoft. Také vám dá vědět, kdy budete dostávat platby a kolik vám bude zaplaceno.

Pokud nabídky prodáváte na Azure Marketplace, uvidíte také informace o úspěšných výplatách v **souhrnu výplat**. Další informace o platbách na Azure Marketplace najdete v [zásadách účasti na Webu Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkId=722436) a ve smlouvě s [vydavatelem webu Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Aby byl váš výnos způsobilý k výplatě, musí dosáhnout [hranice platby](payment-thresholds-methods-timeframes.md) 50 USD. Podrobnosti o limitu platby najdete na této stránce a najdete v tématu [Smlouva s vydavatelem na webu Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Role a oprávnění k přístupu k výplatní sestavě](#roles-and-permission-to-access-the-payout-report)
- [Sestava výplat: rozdíl mezi portálem partnerů cloudu a partnerským centrem](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Typy odběratelů](#customer-types)
- [Spoluvztah mezi výplatou a využitím](#corelation-between-payout-and-usage)
- [Historie transakcí ke stažení](#transaction-history-download-export)
- [Otázky k fakturaci a podpora](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Role a oprávnění k přístupu k výplatní sestavě

| Sestavy/stránky    | Vlastník účtu    | Manager  | Developer | Obchodní přispěvatel |  Finanční přispěvatel | Marketingu |
|------------------|------------------|----------|-----------|----|----|-----|
| Sestava pořízení (včetně dat téměř v reálném čase) | Může zobrazit | Může zobrazit | Bez přístupu | Bez přístupu | Může zobrazit | Bez přístupu |
| Zpráva/odpovědi o zpětné vazbě | Může zobrazit a odeslat zpětnou vazbu | Může zobrazit a odeslat zpětnou vazbu | Může zobrazit a odeslat zpětnou vazbu | Bez přístupu | Bez přístupu | Může zobrazit a odeslat zpětnou vazbu |
| Zpráva o stavu (včetně dat téměř v reálném čase) | Může zobrazit | Může zobrazit | Může zobrazit | Může zobrazit | Bez přístupu | Bez přístupu |
| Sestava využití | Může zobrazit | Může zobrazit | Může zobrazit | Může zobrazit | Bez přístupu | Bez přístupu |
| Výplatní účet | Může aktualizovat | Bez přístupu | Bez přístupu | Bez přístupu | Může aktualizovat | Bez přístupu |
| Daňový profil | Může aktualizovat | Bez přístupu | Bez přístupu | Bez přístupu | Může aktualizovat | Bez přístupu |
| Přehled plateb | Může zobrazit | Bez přístupu | Bez přístupu | Bez přístupu | Může zobrazit | Bez přístupu |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Sestava výplat: rozdíl mezi portálem partnerů cloudu a partnerským centrem

| | Portál Cloud Partner | Partnerské centrum |
|---------|---------|---------|
| Odkazy | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)A[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Navigace | Přehledy výplat poskytovaných v přehledech výplaty | Přehledy výplat v Partnerském centru – ikona výplaty |
| Rozsah | <ul> <li>Transakce na řádkovou položku je viditelná, pro probíhající vyzvednutí, shromážděné a placené </li> <li>Přehledy – zobrazí všechny řádkové položky po vytvoření nákupní objednávky, včetně probíhajícího výběru a probíhajícífakturace a stavu inkasa a řádkových položek, které ještě nejsou způsobilé k platbě. </li> </ul> | <ul> <li>Zobrazí řádkové položky, jakmile jsou považovány za způsobilé příjmy.</li> <li>Zákazníci nejprve zaplatí společnosti Microsoft a pak nemít na ošid, kteří uvidí, jak začíná výplatní sestava.</li> <li>Sestava výplaty nezobrazuje probíhající shromažďování a fakturaci.  </li> </ul>  |
| Transakce není připravena k výplatě | Probíhá fakturace | Další odhadovaná platba: Stav výplaty je v nezpracovaném stavu.  |
| Stav výplaty |  | Nezpracované: <br> Výdělek má nárok na platbu. Zůstane v tomto stavu po dobu chlazení, jak je definováno v programovépříručce pro motivační program. <br> <br> Nadcházející: <br> Čekající interní kontroly generované platebním příkazem před zpracováním platby. <br> <br> Odeslána: <br> Platba byla odeslána do vaší banky. |

## <a name="customer-types"></a>Typy odběratelů

### <a name="enterprise-agreement"></a>Podniková smlouva

Zákazníkům bez smlouvy Enterprise se měsíčně účtují licence na software marketplace. Zákazníkům se smlouvou Enterprise se fakturuje měsíčně prostřednictvím faktury, která je prezentována čtvrtletně.

### <a name="credit-cards-and-monthly-invoice"></a>Kreditní karty a měsíční faktura

Zákazníci mohou také platit kreditní kartou a měsíční fakturou. V takovém případě budou licenční poplatky za software účtovány měsíčně.

### <a name="csp-and-direct-pay-users"></a>Uživatelé CSP a Direct Pay

Například pokud zákazník nakupuje pomocí platební karty.

## <a name="corelation-between-payout-and-usage"></a>Spoluvztah mezi výplatou a využitím

|Popis    |    Datum  | Objednávky/využití  | Výplata |
|----------|----------|-----------|-------------|
|Období objednávky   | Srp 15, 2019 - Srp 30, 2019 | **Příkazy korelace atributy** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Použití** <br> <ul> <li>CustomerId </li> <li>Jméno zákazníka</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Odhadované rozšířené poplatky <br> Odhadovaná výplata (PC) </li> </ul> |  |
|Konec termínu (měsíc)   | 30. srpna 2019 | | |
|Datum fakturace | 1. září 2019 | | |
|Datum platby odběratele | 1. září 2019 | | |
|Doba úschovy (pouze kreditní karty, 30 dní) | 1. září 2019 – 30. | | **Pořadí atributů korelace:** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li> Jméno zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Jméno zákazníka</li> <li>OrderId</li> <li>LineItemId</li> <li>částka transakce</li> <li>VydělávatAmountInLastPaymentCurrency</li> </ul> <br> **Stav výplaty:** Nezpracované |
|Začátek období vyzvednutí | 1. září 2019 | | |
|Konec období vyzvednutí (maximálně 30 dní) | 30. září 2019 | | |
|Datum výpočtu výplaty (měsíčně 15.) | 1. října 2019 | | **Atributy korelace** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li>Jméno zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Jméno zákazníka</li> <li>OrderId</li> <li>LineItemId</li> <li>částka transakce</li> <li>VydělávatAmountInLastPaymentCurrency</li> </ul> <br> **Stav výplaty:** Nadcházející |
|Datum výplaty | 15. října 2019 | | **Atributy korelace** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li> Jméno zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Jméno zákazníka</li> <li>OrderId</li> <li>LineItemId</li> <li>částka transakce</li> <li>VydělávatAmountInLastPaymentCurrency</li> </ul> <br> **Stav výplaty:** Platba odeslána |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Smlouva enterprise (čtvrtletní/měsíční zákazníci)

| Popis |    Datum  | Využití | Výplata |
|----------|----------|---------|-----------|
|Období objednávky | Srp 15, 2019 - Srp 30, 2019 | **Příkazy korelace atributy** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Sestava využití** <br> <ul> <li>CustomerId </li> <li>Jméno zákazníka</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Odhadované rozšířené poplatky <br> Odhadovaná výplata (PC) </li> </ul> | |
|Konec termínu (čtvrtletí) | 30. září 2019 | | |
|Datum fakturace | 15. října 2019 | | |
|Doba úschovy (pouze kreditní karty, 30 dní) | neuvedeno | | |
|Začátek období vyzvednutí | 15. října 2019 | | |
|Pouze kreditní karty, 30 dní | 1. listopadu 2019 - 30. | | |
|Konec období vyzvednutí (maximálně, 90 dní) | 15. ledna 2020 | | |
|Datum platby odběratele | 30. prosince 2019 | | |
|Výpočet výplaty | 15. ledna 2020 | | |
|Datum výplaty | 15. února 2020 | | **Pro čtvrtletní zákazníky** <br> <br> **Sestava objednávek** <br> <ul><li>AssetId</li> <li>ID zákazníka</li> <li> Jméno zákazníka</li> </ul> <br> **Použití** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Jméno zákazníka</li> <li>OrderId</li> <li>LineItemId</li> <li>částka transakce</li> <li>VydělávatAmountInLastPaymentCurrency</li> </ul> <br> **Stav výplaty:** odesláno |

## <a name="transaction-history-download-export"></a>Historie transakcí stáhnout export

Tato možnost poskytuje stažení jednotlivých řádkových položek příjmů, které vidíte na stránce Historie transakcí, typ výdělku, datum, přidruženou částku transakce, zákazníka, produkt a další podrobnosti o transakcích, které se vztahují k programu Pobídky.

| Název sloupce     | Popis    |
|-------------|-------------------------------|
| vydělávatId                      | Jedinečný identifikátor pro každý výdělek                                                                                                       |
| participantId                  | Primární identita partnera vydělávajícího v rámci programu                                                                            |
| participantIdType              | Většinou ID programu pro motivační programy a prodejce IF pro programy Store a Azure Marketplace                                          |
| název účastníka                | Jméno partnera pro výdělek                                                                                                              |
| partnerCountryCode             | Místo/země partnera, který vydělává                                                                                                  |
| název programu                    | Název programu pobídky/úložiště                                                                                                             |
| transactionId                  | Jedinečný identifikátor transakce                                                                                                    |
| transactionCurrency            | Měna, ve které došlo k původní transakci odběratele (není měnou umístění partnera)                                     |
| datum transakce                | Datum transakce. Užitečné pro programy, kde mnoho transakcí přispívá k jednomu výdělku                                           |
| transactionExchangeRate        | Směnný kurz použitý k zobrazení odpovídající částky transakce v USD                                                                 |
| částka transakce              | Částka transakce v původní měně transakce, na základě které je generováno výdělku                                              |
| transactionAmountUSD           | Částka transakce v USD                                                                                                                |
| Páčku                          | Označuje obchodní pravidlo pro výdělek.                                                                                                  |
| vydělávatMíra výdělku                    | Motivační sazba použitá na částku transakce pro generování výdělku                                                                      |
| quantity                       | Liší se v závislosti na programu. Označuje fakturované množství pro transakční programy.                                                            |
| quantityTyp                   | Označuje typ množství, například: Fakturované množství, MAU.                                                                                     |
| vydělávatTyp                    | Označuje, zda se jedná o poplatek, slevu, coop, prodat atd.                                                                                          |
| vydělávatČástka                  | Částka příjmů v původní měně transakce                                                                                      |
| vydělávatAmountUSD               | Částka výdělku v USD                                                                                                                    |
| datum výdělek                    | Datum výdělku                                                                                                                      |
| výpočetDatum                | Datum výpočtu výdělek v systému                                                                                            |
| vydělávatSměnný kurz            | Směnný kurz použitý k zobrazení odpovídající částky V USD                                                                                  |
| exchangeRateDate               | Datum směnného kurzu použité k výpočtu částky výdělek USD                                                                                   |
| platbaAmountWOTax             | Částka příjmu (bez daně) v měně Zaplatit za "Odeslané" pouze pro "Odeslané"                                                                 |
| paymentCurrency                | Platba měně vybrané partnerem v profilu Platba. Zobrazeno pouze pro odeslané platby                                                   |
| paymentExchangeRate            | Směnný kurz použitý k výpočtu platbyAmountWOTax v měně platby pomocí DataData                                            |
| paymentId            | Jedinečný identifikátor platby Toto číslo je viditelné ve vašem bankovním výpisu.                                            |
| paymentStatus            | Stav platby                                            |
| paymentStatusDescription            | Popis stavu platby                                            |
| customerId                     | Bude vždy prázdné                                                                                                                     |
| customerName                   | Bude vždy prázdné                                                                                                                     |
| partNumber                     | Bude vždy prázdné                                                                                                                     |
| Productname                    | Název produktu spojený s transakcí                                                                                                       |
| productId                      | Jedinečný identifikátor produktu                                                                                                                |
| parentProductId                | Jedinečný identifikátor nadřazeného produktu Poznámka: Pokud pro transakci neexistuje nadřazený produkt, pak ID nadřazeného produktu = ID produktu. |
| parentProductName              | Název nadřazeného produktu. Poznámka: Pokud pro transakci neexistuje nadřazený produkt, potom název nadřazeného produktu = Název produktu.   |
| productType                    | Typ produktu (například Aplikace, Doplněk, Hra atd.)                                                                                        |
| fakturaČíslo                  | Číslo faktury (platí pouze pro EA)                                                                                                  |
| prodejceId                     | Identifikátor prodejce                                                                                                                      |
| název prodejce                   | Jméno prodejce                                                                                                                            |
| Transactiontype                | Typ transakce (například nákup, refundace, storno, zpětné vrácení peněz atd.)                                                               |
| localProviderSeller            | Místní poskytovatel/prodejce záznamů                                                                                                          |
| taxRemitted                    | Částka promkaného daně (daně z prodeje, použití nebo DPH/GST).                                                                                   |
| taxRemitModel                  | strana odpovědná za proplácení daní (daně z prodeje, použití nebo DPH/GST).                                                                    |
| storeFee                       | Částka, kterou si společnost Microsoft ponechala jako poplatek za zpřístupnění aplikace nebo doplňku ve Storu.                                            |
| transactionPaymentMethod       | Platební nástroj odběratele použitý pro transakci (například karta, fakturace mobilního operátora, PayPal atd.)                                |
| tpan                           | Označuje síť pro reklamy třetích stran.                                                                                                     |
| zákazníkZemě                | Země zákazníka                                                                                                                         |
| customerCity                   | Zákaznické město                                                                                                                            |
| customerState                  | Stav odběratele                                                                                                                           |
| customerZip                    | PSČ zákazníka                                                                                                                 |
| ID tenanta                       |                                                                                                                                          |
| externalReferenceId            | Jedinečný identifikátor programu                                                                                                        |
| externalReferenceIdLabel       | Označení jedinečného identifikátoru                                                                                                                  |
| transactionCountryCode       | Kód země, ve kterém došlo k transakci                                                                                                                  |
| taxZemě       | Prodáno do země zákazníka                                                                                                                  |
| taxState       | Prodáno státu zákazníka                                                                                                                  |
| taxCity       | Prodáno do zákaznického města                                                                                                                  |
| taxZipCode       | Prodáno zákazníkovi Zip                                                                                                                  |
| Název programu LicensingProgram       |                                                                                                                   |
| Kód programu       | Řetězec pro mapování s názvem programu                                                                                                                   |
| vydělávatAmountInLastPaymentCurrency       | Částka příjmů v poslední měně platby (pole bude prázdné, pokud nebyly zaplaceny žádné předchozí platby)                                                                                                                   |
| lastPaymentCurrency       | Měna poslední platby (pole bude prázdné, pokud nebyla zaplacena žádná předchozí platba)                                                                                                                   |
| AssetId       | Jedinečný identifikátor objednávek zákazníků pro vaši službu Marketplace.  Představuje transakční řádkové položky nákupu. Může existovat více datových zdrojů.                                                                                                                   |
| OrderId       | se vztahuje k faktuře odběratele                                                                                                                   |
| LineItemId       | individuální řádek na faktuře odběratele                                                                                                                   |
| Země zákazníka       | Název země poskytnutý zákazníkem.  To se může lišit od země v předplatném Azure zákazníka.                                                                                                                   |
| E-mailová adresa zákazníka       | E-mailová adresa poskytnutá koncovým zákazníkem.  To se může lišit od e-mailové adresy v předplatném Azure zákazníka.                                                                                                                   |
| SkuId       | ID skladové položky definované během publikování. Nabídka může mít mnoho skladových položk, ale skladová položka může být přidružena pouze k jedné nabídce.                                                                                                                   |

>[!Note]
>Všechny přehledy a přehledy pro možnost publikování transgrafu jsou k dispozici prostřednictvím části Přehledy v části Portál pro partnery cloudnebo analytics v Centru partnerů.

## <a name="billing-questions-and-support"></a>Otázky k fakturaci a podpora

Chcete-li získat pomoc s dotazy týkajícími se fakturace, obraťte se na [podporu vydavatele komerčního trhu](https://aka.ms/marketplacepublishersupport).
