---
title: Přehled souhrnu výběrů, Azure Marketplace
description: Ve shrnutí výběr se zobrazí podrobnosti o peníze, které jste získali s vaší nabídkou. Také vám umožní zjistit, kdy obdržíte platby a kolik vám bude placeno.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 09/09/2020
ms.openlocfilehash: 708fc2c0783bdefa4ac4fa4b73f10733bba0bc04
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006920"
---
# <a name="payout-summary-overview"></a>Souhrnný přehled plateb

[Souhrn výběrů](./payout-summary.md) vám ukáže podrobnosti o peníze, které jste získali od Microsoftu. Také vám umožní zjistit, kdy obdržíte platby a kolik vám bude placeno.

Pokud prodáváte nabídky v Azure Marketplace, zobrazí se také informace o úspěšných výběrech v souhrnu výběrů. Další informace o platbě Azure Marketplace najdete v tématu o tom, [jak se zaplatí na komerčním webu Marketplace](./get-paid.md) a v rámci [smlouvy Microsoft Publisher](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> Aby bylo možné výsledek vyhodnotit, vaše výtěžek musí dosáhnout prahové hodnoty pro platbu $50. Podrobnosti najdete v článku o [smlouvě Microsoft Publisher](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

Chcete-li zobrazit podrobnosti výběru, přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home) a v pravém horním rohu obrazovky vyberte ikonu výběr:

![Ukazuje ikonu Výběr v pravém horním rohu portálu partnerského centra.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Role a oprávnění

Toto jsou role a oprávnění pro přístup k sestavě výběr:

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

## <a name="payment-schedules"></a>Platební plány

Diskuzi o platebních plánech, včetně časových období, viditelnosti partnerů a o tom, kdy zákazník používá platební kartu nebo fakturu, najdete v části [platební plány](./payout-policy-details.md#payment-schedules) v článku **Podrobnosti o výběrovém** seznamu.

## <a name="transaction-history-download-export"></a>Export stažení Historie transakcí

Tato možnost poskytuje stažení každé položky řádku s možností přijetí, kterou vidíte na stránce Historie transakcí. To zahrnuje typ, datum, přidruženou částku transakce, zákazníka, produkt a další transakční údaje týkající se programu pobídek.

| Název sloupce | Popis |
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
