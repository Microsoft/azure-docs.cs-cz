---
title: Spustit překlad
titleSuffix: Azure Cognitive Services
description: Spusťte požadavek na překlad dokumentu ve službě překladu dokumentů.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 1d167962e22953a4a9ca69ece347f8427b9218c9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836187"
---
# <a name="start-translation"></a>Spustit překlad

Pomocí tohoto rozhraní API můžete spustit hromadnou (dávkovou) žádost o překlady se službou překladu dokumentů. Každý požadavek může obsahovat více dokumentů a musí obsahovat zdrojový a cílový kontejner pro každý dokument.

Předpona a filtr přípon (pokud jsou zadány) se používají k filtrování složek. Předpona se použije pro dílčí cestu za názvem kontejneru.

V požadavku může být obsažena paměť Glossaries/Translation, kterou služba používá při překladu dokumentu.

Pokud je Glosář neplatný nebo nedosažitelný během překladu, je ve stavu dokumentu uvedena chyba. Pokud soubor se stejným názvem už v cílovém umístění existuje, bude přepsán. TargetUrl pro každý cílový jazyk musí být jedinečný.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `POST` požadavek na:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Přečtěte si, jak najít [vlastní název domény](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Všechny požadavky rozhraní API na službu překladu dokumentů vyžadují vlastní koncový bod domény**.
> * Koncový bod, který se nachází na vašich Azure Portal _klíčů prostředků a koncových_ bodech globálního překladatele, nemůžete použít `api.cognitive.microsofttranslator.com` k převedení požadavků HTTP na překlad dokumentů.

## <a name="request-headers"></a>Hlavičky požadavku

Hlavičky požadavku jsou:

|Hlavičky|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Požadovaná hlavička žádosti|

## <a name="request-body-batch-submission-request"></a>Text žádosti: žádost o odeslání dávky

|Název|Typ|Description|
|--- |--- |--- |
|vztahují|BatchRequest[]|BatchRequest uvedená níže. Vstupní seznam dokumentů nebo složek obsahujících dokumenty. Typy médií: "Application/JSON", "text/JSON", "Application/* + JSON".|

### <a name="inputs"></a>Vstupy

Definice pro vstupní požadavek dávkového převodu

|Název|Typ|Vyžadováno|Popis|
|--- |--- |--- |--- |
|source|SourceInput[]|Ano|Inputs. Source uvedená níže. Zdroj vstupních dokumentů.|
|storageType|StorageInputType[]|Ano|Inputs. storageType je uveden níže. Typ úložiště zdrojového řetězce vstupních dokumentů|
|cíle|TargetInput[]|Ano|vstupy. Target uvedené níže. Umístění cíle pro výstup.|

**Inputs. Source**

Zdroj vstupních dokumentů.

|Název|Typ|Vyžadováno|Popis|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Ne|DocumentFilter [] je uveden níže.|
|Filter. prefix|řetězec|Ne|Řetězec předpony rozlišující velká a malá písmena pro filtrování dokumentů ve zdrojové cestě pro překlad. Pokud například používáte identifikátor URI objektu BLOB služby Azure Storage, použijte předponu k omezení podsložek pro překlad.|
|Filter. přípona|řetězec|Ne|Řetězec přípony rozlišující velká a malá písmena pro filtrování dokumentů ve zdrojové cestě pro překlad. To se nejčastěji používá pro přípony souborů.|
|language|řetězec|Ne|Kód jazyka, pokud není zadán, bude provedeno automatické rozpoznávání dokumentu.|
|sourceUrl|řetězec|True|Umístění složky/kontejneru nebo jednoho souboru s vašimi dokumenty|
|storageSource|StorageSource|Ne|StorageSource uvedená níže.|
|storageSource. Azureblobu|řetězec|Ne||

**vstupy. storageType**

Typ úložiště zdrojového řetězce vstupních dokumentů

|Název|Typ|
|--- |--- |
| – soubor|řetězec|
|složky|řetězec|

**vstupy. Target**

Cíl pro dokončené přeložené dokumenty

|Název|Typ|Vyžadováno|Popis|
|--- |--- |--- |--- |
|category|řetězec|Ne|Kategorie/vlastní systém pro požadavek na překlad|
|glossaries|Glosář []|Ne|Glosář uvedený níže. Seznam glosáře|
|Glossaries. Format|řetězec|Ne|Formátovat.|
|glossaries.glossaryUrl|řetězec|True (Pokud používáte Glossaries)|Umístění glosáře. Použijeme příponu souboru k extrakci formátování, pokud není zadán parametr format. Pokud se dvojice jazyk překladu v glosáři nenachází, nebude použita.|
|glossaries.storageSource|StorageSource|Ne|StorageSource uvedené výš.|
|targetUrl|řetězec|True|Umístění složky nebo kontejneru do vašich dokumentů.|
|language|řetězec|True|Kód cílového jazyka se dvěma písmeny. Podívejte se [na seznam kódů jazyka](../../language-support.md).|
|storageSource|StorageSource []|Ne|StorageSource [] je uveden výše.|
|verze|řetězec|Ne|Verze.|

## <a name="example-request"></a>Příklad požadavku

Následují příklady požadavků Batch.

**Překlad všech dokumentů v kontejneru**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Překlad všech dokumentů v kontejneru s použitím Glossaries**

Ujistěte se, že jste vytvořili adresu URL glosáře & token SAS pro konkrétní objekt BLOB nebo dokument (ne pro kontejner).

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
     "glossaries": [
                        {
                            "glossaryUrl": https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D,
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**Překlad konkrétní složky v kontejneru**

Ujistěte se, že jste zadali název složky (rozlišuje velká a malá písmena) jako předponu ve filtru – i když je token SAS pro kontejner stále k dispozici.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D,
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Překlad konkrétního dokumentu v kontejneru**

* Ujistěte se, že jste zadali "storageType": "File"
* Ujistěte se, že jste vytvořili zdrojovou adresu URL & token SAS pro konkrétní objekt BLOB nebo dokument (ne pro kontejner).
* Ujistěte se, že jste zadali název cílového souboru jako součást cílové adresy URL – i když je token SAS pro kontejner stále k dispozici.
* Vzorový požadavek níže ukazuje jeden dokument, který je přeložen do dvou cílových jazyků.

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>Stavové kódy odpovědí

Níže jsou uvedené možné stavové kódy HTTP, které požadavek vrátí.

|Stavový kód|Description|
|--- |--- |
|202|Přijata. Úspěšná žádost a služba vytvoří požadavek Batch. Operation-Location hlaviček bude označovat adresu URL stavu s ID operace. HeadersOperation-Location: String|
|400|Chybný požadavek. Neplatný požadavek Ověřte vstupní parametry.|
|401|Přístupu. Zkontrolujte prosím své přihlašovací údaje.|
|429|Frekvence požadavků je příliš vysoká.|
|500|Došlo k vnitřní chybě serveru.|
|503|Služba není momentálně k dispozici.  Zkuste to později.|
|Další stavové kódy|<ul><li>Příliš mnoho žádostí</li><li>Server je dočasně nedostupný.</li></ul>|

## <a name="error-response"></a>Chybová odezva

|Název|Typ|Description|
|--- |--- |--- |
|kód|řetězec|Výčty obsahující chybové kódy vysoké úrovně. Možné hodnoty:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Neautorizováno</li></ul>|
|zpráva|řetězec|Získá chybovou zprávu vysoké úrovně.|
|innerError|InnerErrorV2|Nový formát vnitřní chyby, který je v souladu s pokyny pro Cognitive Services rozhraní API. Obsahuje požadované vlastnosti ErrorCode, zpráva a volitelné vlastnosti cíl, podrobnosti (dvojice klíč-hodnota), vnitřní chyba (může být vnořený).|
|vnořen. ErrorCode|řetězec|Načte řetězec chyby kódu.|
|innerError. Message|řetězec|Získá chybovou zprávu vysoké úrovně.|

## <a name="examples"></a>Příklady

### <a name="example-successful-response"></a>Příklad úspěšné odpovědi

Následující informace jsou vráceny v úspěšné odpovědi.

ID úlohy najdete v hlavičce odpovědi metody POST Operation-Location hodnotě adresy URL. Posledním parametrem adresy URL je ID úlohy operace (řetězec následující po "/Operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Příklad chybové odpovědi

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o použití překladu dokumentů a klientské knihovny, postupujte podle našeho rychlého startu.

> [!div class="nextstepaction"]
> [Začínáme s překladem dokumentů](../get-started-with-document-translation.md)
