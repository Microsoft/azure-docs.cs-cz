---
title: Pochopení způsobu, jak používat Azure digitální dvojče Swagger | Dokumentace Microsoftu
description: Jak používat Azure digitální dvojče Swagger
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: adgera
ms.openlocfilehash: dbadc90a206937d4c9f1d7b75a872d93b1a8a587
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324088"
---
# <a name="how-to-use-azure-digital-twins-swagger"></a>Jak používat Azure digitální dvojče Swagger

Každá instance zřízené digitální dvojče Azure zahrnuje vlastní automaticky generované referenční dokumentaci Swagger.

[Swagger](https://swagger.io/) (nebo [OpenAPI](https://www.openapis.org/)) sjednotí komplexní informace o rozhraní API do prostředek referenční dokumentace pro interaktivní a jazykově nezávislé. Konkrétně Swagger poskytuje důležité referenční materiály, o které datové části JSON, metod HTTP a konkrétní koncové body používat k provedení operací k rozhraní API.

## <a name="swagger-summary"></a>Souhrn swagger

Swagger poskytuje interaktivní přehled vašeho rozhraní API, včetně:

* Informace o rozhraní API a objekt modelu.
* Koncových bodů rozhraní REST API pro zadání požadované datové části požadavku, záhlaví, parametrů, kontextu cesty a metody HTTP.
* Testování funkce rozhraní API.
* Příklad odpovědi informace pro ověřování a potvrzení odpovědi protokolu HTTP.
* Informace o kódu chyby.

Swagger se proto vhodný nástroj, který vám pomůže s vývoj a testování volání rozhraní API pro správu.

> [!TIP]
> Pro srovnání neoficiální Swagger ve verzi preview neposkytujeme k předvedení funkcí rozhraní API nastavit.
> Je hostovaný na [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Je možné otevřít vlastní, vygenerované, dokumentaci Swagger rozhraní API Management na:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Název vlastního atributu | Nahraďte |
| --- | --- |
| `yourInstanceName` | Název instance digitální dvojče Azure |
| `yourLocation` | Jaké oblasti serveru vaší instance je hostován aplikací |

## <a name="reference-material"></a>Referenční materiál

Automaticky generované referenční materiály, které popisuje důležité koncepty a objektové modely.

Stručný přehled popisuje rozhraní API:

![Horní swagger][1]

Základní rozhraní API objektové modely jsou také uvedeny:

![Modely swagger][2]

Klikněte na tlačítko do jednotlivých uvedených objektový Model pro podrobnější přehled klíčových atributů:

![Swagger model][3]

Vygenerovaný Swagger objektové modely jsou vhodné zobrazíte všechny dostupné Azure digitální dvojče [objekty a rozhraní API](./concepts-objectmodel-spatialgraph.md). To je skvělý prostředek pro vývojáře pro použití při vytváření řešení na Azure digitální dvojče.

## <a name="endpoint-summary"></a>Souhrn koncového bodu

Swagger obsahuje také důkladný přehled všechny koncové body, které tvoří rozhraní API.

Každý koncový bod uvedené také obsahuje informace o požadované žádosti o jako například:

* Požadované parametry.
* Povinný parametr datové typy.
* Metoda HTTP pro přístup k prostředku.

![Swagger koncových bodů][4]

Jednotlivé prostředky můžete kliknutí zobrazíte podrobnější přehled.

## <a name="using-swagger-to-test-endpoints"></a>K testování koncových bodů využívající Swagger

Jednou z Swagger poskytuje výkonné funkce je schopnost **vyzkoušet** nebo koncový bod rozhraní API přímo prostřednictvím uživatelského rozhraní v dokumentaci k testování.

Po kliknutí do určitého koncového bodu, zobrazí se vám **vyzkoušet** tlačítka:

![Zkuste swagger][5]

Rozbalením této části zobrazí vstupní pole pro každý parametr požadované a volitelné. Zadejte hodnoty odpovídajícím způsobem a klikněte na tlačítko **Execute**:

![Pokusili swagger][6]

Po spuštění testu, můžete ověřit data odpovědi.

## <a name="swagger-response-data"></a>Data odpovědi swagger

Každý koncový bod uvedené také zahrnuje data těla odpovědi k ověření pro vývoj a testování. Tyto příklady: požadovaný stavové kódy a JSON pro úspěšné požadavky HTTP.

![Odpověď swagger][7]

Příklady také kódy chyb umožňující ladit nebo vylepšit selhání testů.

## <a name="swagger-oauth-20-authorization"></a>Swagger autorizace OAuth 2.0

Testovat požadavků na prostředky rozhraní API OAuth 2.0, najdete v článku [oficiální dokumentaci](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Další postup

Další informace o Azure digitální dvojče objektové modely a Prostorové řady grafu, přečtěte si [v tomto článku](./concepts-objectmodel-spatialgraph.md).

Zjistěte, jak ověřování pomocí rozhraní API pro správu, přečtěte si téma [ověřování pomocí rozhraní API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.png
[2]: media/how-to-use-swagger/swagger_management_models.png
[3]: media/how-to-use-swagger/swagger_management_model.png
[4]: media/how-to-use-swagger/swagger_management_endpoints.png
[5]: media/how-to-use-swagger/swagger_management_try.png
[6]: media/how-to-use-swagger/swagger_management_tried.png
[7]: media/how-to-use-swagger/swagger_management_response.png
