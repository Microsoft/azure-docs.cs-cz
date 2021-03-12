---
title: Přidávání dat do protokolů auditu pomocí vlastních hlaviček – Azure API pro FHIR
description: Tento článek popisuje, jak přidat data do protokolů auditu pomocí vlastních hlaviček protokolu HTTP v Azure API pro FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 937be72bfec96119474e7effe9ba88a2cf253444
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017975"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>Přidávání dat do protokolů auditu pomocí vlastních hlaviček protokolu HTTP

V rozhraní API pro prostředky pro interoperabilitu s rychlým zdravotním prostředím (FHIR) Azure může uživatel chtít zahrnout do protokolů Další informace, které pocházejí z volajícího systému.

Například pokud je uživatel rozhraní API ověřený externím systémem, tento systém přepošle volání rozhraní FHIR API. Ve vrstvě rozhraní API FHIR byly ztraceny informace o původním uživateli, protože bylo volání předáno. Může být nutné protokolovat a uchovávat tyto informace o uživateli pro účely auditování nebo správy. Volající systém může poskytnout identitu uživatele, umístění volajícího nebo jiné potřebné informace v hlavičkách protokolu HTTP, které budou přeneseny spolu s přesměrováním volání.

Tento tok dat vidíte v následujícím diagramu:

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="Diagram vlastních hlaviček":::

Vlastní hlavičky můžete použít k zachycení několika typů informací. Například:

* Informace o identitě nebo autorizaci
* Původ volajícího
* Původní organizace
* Podrobnosti o klientském systému (elektronický záznam o stavu, portál pacient)

> [!IMPORTANT]
> Mějte na paměti, že informace odesílané ve vlastních hlavičkách jsou uloženy v systému interního protokolování společnosti Microsoft po dobu 30 dnů po jejich zpřístupnění v Azure log monitoring. Před přidáním informací do vlastních hlaviček doporučujeme šifrovat všechny informace. Pomocí zákaznických hlaviček byste neměli předávat žádné informace o FÍ.

Pro hlavičky HTTP je nutné použít následující zásady vytváření názvů: X-MS-AZUREFHIR-AUDIT- \<name> .

Tyto hlavičky HTTP jsou součástí kontejneru objektů a dat přidaných do protokolu. Například:

* X-MS-AZUREFHIR-AUDIT-USERID: 1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ: 1234

Tyto informace se pak při přidání do sloupce vlastnosti v protokolu zaserializovat do formátu JSON. Například:

```json
{ "X-MS-AZUREFHIR-AUDIT-USERID" : "1234",
"X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "XXXX",
"X-MS-AZUREFHIR-AUDIT-XYZ" : "1234" }
```
 
Stejně jako u všech hlaviček protokolu HTTP se může stejný název záhlaví opakovat s různými hodnotami. Například:

* X-MS-AZUREFHIR-AUDIT-USERLOCATION: nemocnice
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: Nouzový

Po přidání do protokolu jsou hodnoty kombinovány se seznamem odděleným čárkami. Například:

{"X-MS-AZUREFHIR-AUDIT-USERLOCATION": "nemocnice a nouze"}
 
Můžete přidat maximálně 10 jedinečných hlaviček (opakování stejné hlavičky s různými hodnotami se počítají jenom jako jedna). Celková maximální délka hodnoty pro jednu hlavičku je 2048 znaků.

Pokud používáte knihovnu rozhraní API klienta Firefly C#, kód vypadá nějak takto:

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak přidat data do protokolů auditu pomocí vlastních hlaviček v rozhraní Azure API pro FHIR. V dalším kroku se dozvíte další informace o dalších nastaveních, která můžete nakonfigurovat v rozhraní API Azure pro FHIR.
 
>[!div class="nextstepaction"]
>[Další nastavení](azure-api-for-fhir-additional-settings.md)
