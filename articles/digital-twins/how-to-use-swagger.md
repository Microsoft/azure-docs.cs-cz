---
title: Jak používat referenční dokumentaci Swagger - Azure Digital Twins | Dokumenty společnosti Microsoft
description: Principy použití referenční dokumentace Azure Digital Twins Swagger.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023289"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Referenční dokumentace Azure Digital Twins Swagger

Každá zřízená instance Azure Digital Twins obsahuje vlastní automaticky generovanou referenční dokumentaci Swagger.

[Swagger](https://swagger.io/)neboli [OpenAPI](https://www.openapis.org/)sjednocuje komplexní informace o rozhraní API do interaktivního referenčního prostředku a referenčního prostředku bez jazykového režimu. Swagger poskytuje kritické referenční materiál, o kterém datové části JSON, metody HTTP a konkrétní koncové body, které chcete použít k provádění operací proti rozhraní API.

## <a name="swagger-summary"></a>Swagger shrnutí

Swagger poskytuje interaktivní souhrn vašeho rozhraní API, který zahrnuje:

* Informace o rozhraní API a objektovém modelu.
* Koncové body rozhraní REST API, které určují požadovanou datové části požadavku, záhlaví, parametry, cesty kontextu a metody HTTP.
* Testování funkcí rozhraní API.
* Příklad informací o odezvě použitých k ověření a potvrzení odpovědí HTTP.
* Informace o kódu chyby.

Swagger je pohodlný nástroj, který vám pomůže s vývojem a testováním volání na azure digital twins management API.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Referenční materiál

Automaticky generovaný referenční materiál Swagger poskytuje rychlý přehled důležitých konceptů, dostupné koncové body rozhraní API pro správu a popis každého objektového modelu, který pomáhá s vývojem a testováním.

Stručný souhrn popisuje rozhraní API.

[![Swagger shrnutí a API přehled informací](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

V seznamu jsou také uvedeny objektové modely rozhraní API pro správu.

[![Swagger modely uvedené v dolní části Swagger UI](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Můžete vybrat každý uvedený objektový model pro podrobnější souhrn klíčových atributů.

[![Swagger modely rozšířené číst obsah modelů](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Generované objektové modely Swagger jsou vhodné pro čtení všech [dostupných objektů](./concepts-objectmodel-spatialgraph.md)Azure Digital Twins a rozhraní API . Vývojáři můžou tento prostředek použít při vytváření řešení v Azure Digital Twins.

## <a name="endpoint-summary"></a>Souhrn koncového bodu

Swagger také poskytuje důkladný přehled všech koncových bodů, které tvoří api pro správu.

Každý uvedený koncový bod také obsahuje požadované informace o požadavku, jako jsou:

* Požadované parametry.
* Požadované datové typy parametrů.
* Metoda HTTP pro přístup k prostředku.

[![Koncové body Swagger zobrazené v uzdu Swagger](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Vyberte jednotlivé zdroje, chcete-li zobrazit jejich další obsah, abyste získali podrobnější přehled.

## <a name="use-swagger-to-test-endpoints"></a>Testování koncových bodů pomocí swaggeru

Jednou z výkonných funkcí, které Swagger poskytuje, je možnost otestovat koncový bod rozhraní API přímo prostřednictvím rozhraní dokumentace.

Po výběru konkrétního koncového bodu se zobrazí tlačítko **Vyzkoušet.**

[![Swagger Vyzkoušejte to tlačítko](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Rozbalte tuto část tak, aby se pro každý povinný a volitelný parametr zoátrala vstupní pole. Zadejte správné hodnoty a vyberte **Spustit**.

[![Swagger Vyzkoušejte to výsledek příklad](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Po provedení testu můžete ověřit data odpovědi.

## <a name="swagger-response-data"></a>Údaje o odezvě Swagger

Každý uvedený koncový bod také obsahuje data těla odezvy k ověření vývoje a testů. Mezi tyto příklady patří stavové kódy a JSON pro úspěšné požadavky HTTP.

[![Příklad odpovědi Swagger JSON](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Příklady také zahrnují kódy chyb, které pomáhají ladit nebo zlepšit neúspěšné testy.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 povolení

> [!NOTE]
> * Uživatelský objekt zabezpečení, který vytvořil prostředek Azure Digital Twins, bude mít přiřazení role Správce prostoru a bude moct vytvořit další přiřazení rolí pro ostatní uživatele. Tito uživatelé a jejich role mohou být oprávněni volat rozhraní API.

1. Podle pokynů na [úvodním panelu](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) vytvořte a nakonfigurujte aplikaci Služby Azure Active Directory. Případně můžete znovu použít existující registraci aplikace.

1. Přidejte do registrace aplikace Azure Active Directory následující identifikátor **URI přesměrování:**

    [![Zaregistrovat adresu URL přesměrování Swagger v AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name (Název)  | Nahradit hodnotou | Příklad |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Adresa URL dokumentace rozhraní API pro správu nalezená na portálu  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Zaškrtněte políčko **Implicitní udělení** > **tokenů přístupu,** aby bylo možné použít implicitní grantový tok OAuth 2.0. Vyberte **konfigurovat**a potom **uložit**.

1. Zkopírujte **ID klienta** aplikace Azure Active Directory.

Po dokončení registrace služby Azure Active Directory:

1. Na stránce naparování vyberte tlačítko **Autorizovat.**

    [![Vyberte tlačítko Autorizovat Swagger](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Vložte ID aplikace do **pole client_id.**

    [![Swagger client_id pole](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Poté budete přesměrováni na následující modální úspěch.

    [![Swagger přesměrování modální](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Chcete-li se dozvědět více o interaktivně testování žádostí chráněných OAuth 2.0, přečtěte si [oficiální dokumentaci](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Další kroky

- Další informace o objektových modelech Azure Digital Twins a grafu prostorové inteligence [načtete na článek Principy objektových modelů Azure Digital Twins](./concepts-objectmodel-spatialgraph.md).

- Informace o ověření pomocí rozhraní API pro správu najdete v textu [Authenticate with API](./security-authenticating-apis.md).
