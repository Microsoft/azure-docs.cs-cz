---
title: Přehled souhrnu výběrů-Azure Marketplace
description: Ve shrnutí výběr se zobrazí podrobnosti o peníze, které jste získali s vaší nabídkou. Také vám umožní zjistit, kdy obdržíte platby a kolik vám bude placeno.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: a872331238946de0d57e6d42164f1ce7fb1c7357
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83746208"
---
# <a name="payout-summary-overview"></a>Souhrnný přehled plateb

[Souhrn výběrů](./payout-summary.md) vám ukáže podrobnosti o peníze, které jste získali od Microsoftu. Také vám umožní zjistit, kdy obdržíte platby a kolik vám bude placeno.

Pokud prodáváte nabídky v Azure Marketplace, zobrazí se také informace o úspěšných výběrech v souhrnu výběrů. Další informace o platbě Azure Marketplace najdete v tématu [Azure Marketplace zásady účasti](https://docs.microsoft.com/legal/marketplace/participation-policy) a [Microsoft Azure Marketplace smlouvu vydavatele](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

> [!NOTE]
> Aby bylo možné výsledek vyhodnotit, vaše výtěžek musí dosáhnout [prahové hodnoty pro platbu](./payment-thresholds-methods-timeframes.md) $50. Podrobnosti o prahové hodnotě platby najdete v [Microsoft Azure Marketplace smlouvě vydavatele](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

Všechny sestavy a přehledy pro možnost publikování v režimu Transact jsou k dispozici v části analýza v partnerském centru, ke kterým se dostanete pomocí této ikony v pravém horním rohu portálu:

![Ukazuje ikonu Výběr v pravém horním rohu portálu partnerského centra.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Role a oprávnění

Jedná se o role a oprávnění pro přístup k sestavě výběr:

| Sestavy/stránky | Vlastník účtu | Manager | Vývojář | Obchodní Přispěvatel | Přispěvatel pro finance | Obchodník |
| --- | --- | --- | --- | --- | --- | --- |
| Sestava pořízení (včetně dat téměř v reálném čase) | Může zobrazit | Může zobrazit | Bez přístupu | Bez přístupu | Může zobrazit | Bez přístupu |
| Sestava nebo odpovědi zpětné vazby | Může zobrazit a odeslat názor. | Může zobrazit a odeslat názor. | Může zobrazit a odeslat názor. | Bez přístupu | Bez přístupu | Může zobrazit a odeslat názor. |
| --- | --- | --- | --- | --- | --- | --- |
| Sestava stavu (včetně dat téměř v reálném čase) | Může zobrazit | Může zobrazit | Může zobrazit | Může zobrazit | Bez přístupu | Bez přístupu |
| Sestava využití | Může zobrazit | Může zobrazit | Může zobrazit | Může zobrazit | Bez přístupu | Bez přístupu |
| Účet pro výběr | Může aktualizovat | Bez přístupu | Bez přístupu | Bez přístupu | Může aktualizovat | Bez přístupu |
| Daňový profil | Může aktualizovat | Bez přístupu | Bez přístupu | Bez přístupu | Může aktualizovat | Bez přístupu |
| Přehled plateb | Může zobrazit | Bez přístupu | Bez přístupu | Bez přístupu | Může zobrazit | Bez přístupu  |
| | | | | | | |

## <a name="payout-report-differences"></a>Výběr rozdílů sestav

Jedná se o rozdíly v sestavě výběr mezi portál partnerů cloudu (starý) a Partnerským centrem (nové):

| Portál Cloud Partner | Partnerské centrum |
| --- | --- |
| **Odkaz**:https://cloudpartner.azure.com/ | **Odkaz**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory ahttps://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigace**: vytváření sestav o výběrech poskytované ve výběrech Insights | **Navigace**: vytváření sestav o výběrech poskytované v partnerském centru – ikona výběr |
| **Rozsah**:<ul><li>Transakce pro položku na řádku je viditelná, pro probíhající shromažďování, shromažďování a platby.</li><li>Vytváření sestav – zobrazí všechny položky řádků po vytvoření objednávky nákupu, včetně probíhajícího shromažďování a fakturace probíhá, a stavu kolekce a položek řádků, které ještě nejsou způsobilé k placení.</li></ul> | **Rozsah**:<ul><li>Zobrazuje položky řádků poté, co se považují za oprávněné tržby.</li><li>Zákazníci se nejprve účtují do Microsoftu a potom prodejci uvidí, že sestava výběru začíná.</li><li>Sestava výběru nezobrazuje probíhající shromažďování a probíhá fakturace.</li></ul> |
| **Transakce není připravená na výběr**: probíhá fakturace. | **Transakce není připravená na výběr**: další odhadovaná platba: stav vyvýběru je v nezpracovaném stavu. |
| **Stav výběr**: není k dispozici | **Stav výběr**:<ul><li>Nezpracováno: zisk má nárok na platbu.</li><li>Nadcházející: vzdálení bude odesláno vydavateli v nejbližším měsíčním výběrovém.</li><li>Odesláno: platba byla odeslána do vaší banky.</li></ul> |
| | |

## <a name="payment-schedules"></a>Platební plány

Diskuzi o platebních plánech, včetně časových období, viditelnosti partnerů a o tom, kdy zákazník používá platební kartu nebo fakturu, najdete v části [Platební kalendáře](./payout-policy-details.md#payment-schedules) v tématu **Výběr podrobností** .

## <a name="transaction-history-download-export"></a>Export stažení Historie transakcí

Tato možnost poskytuje stažení každé položky řádku s možností přijetí, kterou vidíte na stránce Historie transakcí. To zahrnuje typ, datum, přidruženou částku transakce, zákazníka, produkt a další transakční údaje týkající se programu pobídek.

| Název sloupce | Description |
| --- | --- |
| earningId | Jedinečný identifikátor pro každý z nich |
| participantId | Primární identita partnera v rámci programu |
| participantIdType | ID programu pro pobídkové programy a prodejce, pokud je program určen pro aplikace ze Storu a Azure Marketplace |
| účastník | Název partnerského partnera |
| partnerCountryCode | Umístění/země/oblast pro partnerský partner |
| programName | Motivace/uložení názvu programu |
| transactionId | Jedinečný identifikátor pro transakci |
| transactionCurrency | Měna, ve které došlo k původní transakci zákazníka (nejedná se o měnu partnerského umístění) |
| transactionDate | Datum transakce Užitečné pro programy, kde mnoho transakcí přispívá k jednomu z nich |
| transactionExchangeRate | Směnný kurz použitý k zobrazení odpovídající částky transakce v USD |
| transactionAmount | Částka transakce v původní měně transakce na základě toho, které z nich se vygenerovaly |
| transactionAmountUSD | Částka transakce v amerických dolarech (USD) |
| páka | Obchodní pravidlo pro |
| earningRate | Míra motivace uplatňovaná na částku transakce pro vygenerování výnosu |
| quantity | Fakturované množství transakčních programů. Liší se podle programu |
| quantityType | Typ množství, například: fakturované množství, aktivní uživatelé (MAU) |
| earningType | Indikuje, že se jedná o poplatek, Rabat, Coop, prodej atd. |
| earningAmount | Peněžní částka v původní transakční měně |
| earningAmountUSD | Množství využité v USD |
| earningDate | Datum přijetí |
| calculationDate | Datum výpočtu v systému |
| earningExchangeRate | Směnný kurz použitý k zobrazení odpovídajících částek v USD |
| exchangeRateDate | Datum směnného kurzu použité k výpočtu EarningAmount USD |
| paymentAmountWOTax | Peněžní částka (bez daně) v platbě k měně za &quot; odeslané &quot; platby |
| paymentCurrency | Platíte do měny zvolené partnerem v platebním profilu. Zobrazuje se jenom pro odeslané platby. |
| paymentExchangeRate | Směnný kurz používaný k výpočtu paymentAmountWOTax v měně platby pomocí ExchangeRateDate |
| paymentId | Jedinečný identifikátor pro platbu Toto číslo je viditelné ve výpisu bank. |
| paymentStatus | Stav platby |
| paymentStatusDescription | Popis stavu platby |
| customerId | Bude vždy prázdné |
| customerName | Bude vždy prázdné |
| partNumber | Bude vždy prázdné |
| NázevVýrobku | Název produktu propojený s transakcí |
| productId | Jedinečný identifikátor produktu |
| parentProductId | Jedinečný identifikátor nadřazeného produktu Pokud pro transakci není k dispozici nadřazený produkt, pak ID nadřazeného produktu = ID produktu. |
| parentProductName | Název nadřazeného produktu Pokud pro transakci není k dispozici nadřazený produkt, pak název nadřazeného produktu = název produktu. |
| productType | Typ produktu (například aplikace, doplněk a hra) |
| invoiceNumber | Číslo faktury (jenom pro smlouvy Enterprise) |
| resellerId | Identifikátor prodejce |
| proprodejce | Jméno prodejce |
| transactionType | Typ transakce (například nákup, refundace, Storno a vrácení peněz) |
| localProviderSeller | Místní poskytovatel/prodejce záznamu |
| taxRemitted | Množství poplatků za daň (prodej, použití nebo DPH/GST). |
| taxRemitModel | Strana odpovědná za přebírání daní (prodej, použití nebo DPH/GST daně). |
| storeFee | Množství, které Microsoft zachovává jako poplatek za zpřístupnění aplikace nebo doplňku na komerčním webu Marketplace. |
| transactionPaymentMethod | Platební nástroj zákazníka, který se používá pro transakci (například karta, fakturaci mobilního dopravce a PayPal) |
| tpan | Síť AD třetí strany |
| customerCountry | Země nebo oblast zákazníka |
| customerCity | Město zákazníka |
| customerState | Stav zákazníka |
| customerZip | PSČ zákazníka, PSČ |
| TenantID | ID tenanta |
| externalReferenceId | Jedinečný identifikátor programu |
| externalReferenceIdLabel | Popisek jedinečného identifikátoru |
| transactionCountryCode | Kód země/oblasti, ve kterém se transakce stala |
| taxCountry | Země nebo oblast zákazníka |
| taxState | Stav zákazníka |
| taxCity | Město zákazníka |
| taxZipCode | Poštovní směrovací číslo zákazníka |
| LicensingProgramName | Název licenčního programu |
| Kód programu | Řetězec, který se má mapovat s názvem programu |
| earningAmountInLastPaymentCurrency | Peněžní částka v poslední platební měně (pole bude prázdné, pokud se nezaplatily žádné předchozí platby) |
| lastPaymentCurrency | Poslední platební měna (pole bude prázdné, pokud se nezaplatila žádná předchozí platba) |
| AssetId | Jedinečný identifikátor objednávek zákazníků pro vaši službu Marketplace Představuje položky nákupních řádků. Může existovat více prostředků. |
| OrderId | Má vztah k faktuře zákazníka. |
| LineItemId | Jednotlivý řádek na faktuře zákazníka |
| Země nebo oblast zákazníka | Název země nebo oblasti, kterou zadal zákazník. To může být jiné než země nebo oblast v předplatném Azure zákazníka. |
| Zákaznická EmailAddress | E-mailová adresa zadaná zákazníkem Může se lišit od e-mailové adresy v předplatném Azure zákazníka. |
| SkuId | ID skladové položky, jak je definováno během publikování. Nabídka může mít mnoho SKU, ale SKU může být přidruženo pouze k jedné nabídce. |

> [!NOTE]
> Všechny sestavy a přehledy pro možnost publikování v režimu Transact najdete v části analýza v partnerském centru.

## <a name="billing-questions-and-support"></a>Otázky a podpora fakturace
Pro podporu fakturace kontaktujte komerční [podporu vydavatele](https://partner.microsoft.com/support/v2/?stage=1)na webu Marketplace.

## <a name="next-step"></a>Další krok

- [Přehledy plateb](./payout-summary.md)
