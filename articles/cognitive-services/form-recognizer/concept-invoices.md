---
title: Faktury – Nástroj pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s analýzou faktur pomocí rozhraní API pro rozpoznávání formulářů – využití a omezení.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 9a3a6bd6489baea90ed4143b42a09e7d697bbc50
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602440"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Předdefinovaný model faktury pro rozpoznávání formulářů

Nástroj pro rozpoznávání formulářů Azure může analyzovat a extrahovat informace z prodejních faktur pomocí předdefinovaných modelů faktur. Rozhraní API pro fakturaci umožňuje zákazníkům přebírat faktury v nejrůznějších formátech a vracet strukturovaná data pro automatizaci zpracování faktury. Kombinuje naše výkonné funkce [optického rozpoznávání znaků (OCR)](../computer-vision/concept-recognizing-text.md) s fakturací s porozuměním modelem obsáhlého učení pro extrakci klíčových informací z faktur v angličtině. Extrahuje text, tabulky a informace, jako je například zákazník, dodavatel, ID faktury, datum splatnosti faktury, celková hodnota, splatné částky faktury, částka daně, částka pro odeslání, fakturaci a další. Předem připravené fakturační rozhraní API je veřejně dostupné v nástroji pro rozpoznávání formulářů v 2.1 Preview.

## <a name="what-does-the-invoice-service-do"></a>K čemu služba faktur používá?

Rozhraní API pro fakturaci extrahuje klíčová pole z faktur a vrátí je v organizované strukturované odpovědi JSON. Faktury můžou být z nejrůznějších formátů a kvality, včetně telefonních snímků, naskenovaných dokumentů a digitálních souborů PDF. Rozhraní API pro fakturaci extrahuje strukturovaný výstup ze všech těchto faktur. 

![Příklad faktury contoso](./media/invoice-example.jpg)

## <a name="try-it-out"></a>Vyzkoušet

Chcete-li si vyzkoušet službu pro rozpoznávání formulářů, použijte nástroj uživatelské rozhraní online Sample:

> [!div class="nextstepaction"]
> [Vyzkoušet předem připravené modely](https://fott-preview.azurewebsites.net/)

Budete potřebovat předplatné Azure ([můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services)) a Recognzier koncový bod a klíč [formuláře](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) a klíč k vyzkoušení služby pro rozpoznávání formulářů. 

![Příklad analyzované faktury](./media/analyze-invoice.png)


### <a name="input-requirements"></a>Požadavky na vstup 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>Operace analyzovat fakturu

Operace [analyzovat fakturu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291) bere jako vstup obrázek nebo PDF z faktury a extrahuje hodnoty, které vás zajímají. Volání vrátí pole hlavičky odpovědi s názvem `Operation-Location` . `Operation-Location`Hodnota je adresa URL, která obsahuje ID výsledku, které se má použít v dalším kroku.

|Hlavička odpovědi| Adresa URL výsledku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>Operace získání výsledku analýzy faktury

Druhým krokem je zavolat operaci [získání výsledku analýzy faktury](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83) . Tato operace přijímá jako vstup ID výsledku, které bylo vytvořeno operací analyzovat fakturu. Vrátí odpověď JSON, která obsahuje pole **stav** s následujícími možnými hodnotami. Tuto operaci zavoláte iterativní, dokud se nevrátí s hodnotou **úspěch** . Použijte interval 3 až 5 sekund, abyste předešli překročení sazby za sekundu (RPS).

|Pole| Typ | Možné hodnoty |
|:-----|:----:|:----|
|status | řetězec | notStarted: operace analýzy nebyla spuštěna.<br /><br />běží: probíhá operace analýzy.<br /><br />Nepovedlo se: operace analýzy se nezdařila.<br /><br />úspěch: operace analýzy byla úspěšná.|

Pokud má pole **stav** hodnotu **úspěch** , bude odpověď JSON zahrnovat výsledky pro porozumění fakturám, tabulky extrahované a volitelné výsledky rozpoznávání textu, pokud je to požadováno. Výsledek porozumění faktuře je uspořádán jako slovník hodnot pojmenovaných polí, kde každá hodnota obsahuje extrahovaný text, normalizovanou hodnotu, ohraničovací rámeček, spolehlivost a odpovídající prvky slova. Výsledek rozpoznávání textu je uspořádán jako hierarchie řádků a slov s textem, ohraničujícím polem a informacemi o spolehlivosti.

### <a name="sample-json-output"></a>Ukázkový výstup JSON

Odpověď na operaci získání výsledku analýzy faktury bude strukturovaná reprezentace faktury se všemi extrahovanými informacemi. Podívejte se na [ukázku souboru faktury](./media/sample-invoice.jpg) a jeho strukturovaný výstup [vzorové faktury](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json).

Výstup JSON má 3 části: 
* `"readResults"` uzel obsahuje všechny rozpoznané znaky textu a výběru. Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. 
* `"pageResults"` uzel obsahuje tabulky a buňky extrahované s jejich ohraničujícími poli, spolehlivost a odkaz na řádky a slova v "readResults".
* `"documentResults"` uzel obsahuje hodnoty specifické pro fakturu, které model zjistil. Tady najdete všechna pole z faktury, jako je ID faktury, dodací adresa, fakturaci, zákazník, celkem a spousta dalších.

## <a name="example-output"></a>Příklad výstupu

Služba faktura bude extrahovat pole text, tabulky a 26 faktur. Níže jsou uvedená pole extrahovaná z faktury v odpovědi na výstup JSON (výstup níže používá tuto [ukázkovou fakturu](./media/sample-invoice.jpg)).  

|Název| Typ | Description | Text | Hodnota (standardní výstup) |
|:-----|:----|:----|:----| :----|
| CustomerName | řetězec | Fakturovaná zákazník | Společnost Microsoft Corp |  |
| CustomerId | řetězec | Referenční ID pro zákazníka | CID – 12345 |  |
| PurchaseOrder | řetězec | Referenční číslo nákupní objednávky | PO-3333 | |  |
| InvoiceId | řetězec | ID pro tuto konkrétní fakturu (často se jedná o číslo faktury) | INV-100 | |  |
| InvoiceDate | datum | Datum vydání faktury | 11/15/2019 | 
| DueDate | datum | Datum platby za tuto fakturu je splatnost. | 12/15/2019 | 2019-12-15 | 2019-11-15 |
| Dodavatel | řetězec | Dodavatel, který vytvořil tuto fakturu | SPOLEČNOST CONTOSO LTD. | |
| VendorAddress | řetězec | Poštovní adresa dodavatele | 123 456th St. New York, NY, 10001 | |
| VendorAddressRecipient | řetězec | Název přidružený k VendorAddress | Ústředí společnosti Contoso | |
| CustomerAddress | řetězec | Poštovní adresa pro zákazníka | 123 jiné St, Redmond, WA, 98052 | |
| CustomerAddressRecipient | řetězec | Název přidružený k CustomerAddress | Společnost Microsoft Corp | |
| BillingAddress | řetězec | Explicitní fakturační adresa pro zákazníka | 123 Bill St, Redmond, WA, 98052 | |
| BillingAddressRecipient | řetězec | Název přidružený k BillingAddress | Služby společnosti Microsoft | |
| ShippingAddress | řetězec | Explicitní dodací adresa pro zákazníka | 123 expedice St, Redmond, WA, 98052 | |
| ShippingAddressRecipient | řetězec | Název přidružený k ShippingAddress | Doručení Microsoftu | |
| Jedna | číslo | Pole mezisoučtu identifikované na této faktuře | 100,00 $ | 100 | 
| TotalTax | číslo | Celkové daňové pole uvedené na této faktuře | $10,00 | 10 |
| InvoiceTotal | číslo | Celkové nové poplatky přidružené k této faktuře | $110,00 | 110 |
| AmountDue |  číslo | Celková částka z důvodu dodavatele | $610,00 | 610 |
| ServiceAddress | řetězec | Explicitní adresa služby nebo adresa vlastnosti pro zákazníka | 123 Service St, Redmond, WA, 98052 | |
| ServiceAddressRecipient | řetězec | Název přidružený k ServiceAddress | Služby společnosti Microsoft | |
| RemittanceAddress | řetězec | Explicitní úhrada nebo platební adresa pro zákazníka | 123 úhrada St New York, NY, 10001 |  |
| RemittanceAddressRecipient | řetězec | Název přidružený k RemittanceAddress | Fakturace společnosti Contoso |  |
| ServiceStartDate | datum | První datum období služby (například období služby faktura za nástroj) | 14. 10. 2019 | 2019-10-14 |
| ServiceEndDate | datum | Koncové datum období služby (například období služby faktura za nástroj) | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | číslo | Explicitní dřív neplacený zůstatek | 500,00 $ | 500 |


## <a name="next-steps"></a>Další kroky

- Vyzkoušejte si vlastní faktury a ukázky v [ukázkovém uživatelském rozhraní pro rozpoznávání formulářů](https://fott-preview.azurewebsites.net/).
- Dokončete [rychlý Start klientské knihovny pro rozpoznávání formulářů](quickstarts/client-library.md) a začněte psát aplikaci pro zpracování faktury pomocí nástroje pro rozpoznávání formulářů v jazyce podle vašeho výběru.
- Nebo použijte rychlý Start k [extrakci dat faktury](./quickstarts/python-invoices.md) k implementaci extrakce dat faktury pomocí Pythonu a REST API.
## <a name="see-also"></a>Viz také

* [Co je služba Rozpoznávání formulářů?](./overview.md)
* [Referenční dokumentace REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)
