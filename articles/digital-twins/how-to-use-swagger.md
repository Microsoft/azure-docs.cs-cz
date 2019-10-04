---
title: Vysvětlení použití referenčních Swagger služby Azure Digital revlákens | Microsoft Docs
description: Pochopíte, jak používat referenční dokumentaci Swagger pro Azure Digital.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 06cbdf6fbba89b6666e6fe3162fd591e93dffecd
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958874"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Referenční dokumentace k sadě Swagger pro Azure Digital revlákens

Každá zřízená instance digitálních vláken Azure obsahuje svou vlastní automaticky generovanou dokumentaci k Swagger.

[Swagger](https://swagger.io/)nebo [openapi](https://www.openapis.org/)JEDNOTKÁM komplexního rozhraní API v interaktivním a jazykovém nezávislá referenčním prostředku. Swagger poskytuje důležité referenční materiály, které se týkají datových částí JSON, metod HTTP a konkrétních koncových bodů, které se mají použít k provádění operací s rozhraním API.

## <a name="swagger-summary"></a>Shrnutí Swagger

Swagger poskytuje interaktivní souhrn vašich rozhraní API, včetně:

* Informace o rozhraní API a objektovém modelu
* REST API koncové body, které určují požadovanou datovou část požadavku, záhlaví, parametry, cesty kontextu a metody HTTP.
* Testování funkcí rozhraní API.
* Příklad informací o odpovědi, které slouží k ověření a potvrzení odpovědí HTTP.
* Informace o kódu chyby.

Swagger je pohodlný nástroj, který vám pomůže s vývojem a testováním volání rozhraní API pro správu digitálních vláken Azure.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Referenční materiál

Automaticky vygenerovaný referenční materiál Swagger poskytuje rychlý přehled o důležitých konceptech, dostupných koncových bodech rozhraní API pro správu a popis jednotlivých objektových modelů, které pomáhají při vývoji a testování.

Stručné shrnutí popisují rozhraní API.

[@no__t – 1Swagger nahoru](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Jsou uvedeny také modely objektů rozhraní API pro správu.

[modely @no__t – 1Swagger](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Každý uvedený objektový model můžete vybrat pro podrobnější souhrn klíčových atributů.

[@no__t – model 1Swagger](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Vygenerované objektové modely Swagger jsou vhodné k zobrazení všech dostupných [objektů a rozhraní API](./concepts-objectmodel-spatialgraph.md)pro Azure Digital revlákens. Vývojáři můžou tento prostředek použít při vytváření řešení pro digitální vlákna Azure.

## <a name="endpoint-summary"></a>Souhrn koncového bodu

Swagger také poskytuje podrobný přehled všech koncových bodů, které tvoří rozhraní API pro správu.

Každý uvedený koncový bod obsahuje také požadované informace o požadavku, například:

* Požadované parametry.
* Požadované datové typy parametrů.
* Metoda HTTP pro přístup k prostředku.

[@no__t – koncové body 1Swagger](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Pokud chcete zobrazit podrobnější přehled, vyberte jednotlivé prostředky.

## <a name="use-swagger-to-test-endpoints"></a>Použití Swagger k testování koncových bodů

Jednou z výkonných funkcí Swagger je schopnost otestovat koncový bod rozhraní API přímo prostřednictvím uživatelského rozhraní dokumentace.

Po výběru konkrétního koncového bodu se zobrazí výzva **vyzkoušet si ho**.

[@no__t – 1Swagger try](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Rozbalením této části zobrazíte vstupní pole pro každý povinný a volitelný parametr. Zadejte správné hodnoty a vyberte **provést**.

[@no__t – pokus o 1Swagger](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Po provedení testu můžete ověřit data odpovědi.

## <a name="swagger-response-data"></a>Data odpovědi Swagger

Každý uvedený koncový bod obsahuje také data textu odpovědi pro ověření vývoje a testů. Tyto příklady zahrnují stavové kódy a JSON, které chcete zobrazit pro úspěšné požadavky HTTP.

[@no__t – odpověď 1Swagger](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Příklady také obsahují chybové kódy, které vám pomůžou ladit nebo vylepšit selhání testů.

## <a name="swagger-oauth-20-authorization"></a>Autorizace pro Swagger 2,0 OAuth

> [!NOTE]
> * Objekt zabezpečení uživatele, který vytvořil prostředek digitálních vláken Azure, bude mít přiřazenou roli správce prostoru a bude moct vytvořit další přiřazení rolí pro ostatní uživatele. Tito uživatelé a jejich role mohou být autorizováni pro volání rozhraní API.

1. Postupujte podle kroků v [tomto rychlém](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) startu nebo si [Zaregistrujte aplikaci Azure Digital revlákens s Azure Active Directory starším](./how-to-use-legacy-aad.md) a vytvořte a nakonfigurujte aplikaci Azure AD. Alternativně můžete znovu použít stávající registraci aplikace.

1. Do registrace aplikace přidejte následující adresu URL odpovědi:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name  | Nahradit | Příklad |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Adresa URL vašeho REST API pro správu se našla na portálu.  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Zkopírujte ID vaší aplikace Azure AD.

Po dokončení registrace Azure Active Directory:

1. Na stránce Swagger vyberte tlačítko **autorizovat** .

    [@no__t – 1Select tlačítko pro autorizaci Swagger](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Vložte ID aplikace do pole **client_id** .

    [pole ![Swagger client_id](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Pak budete přesměrováni na následující modální pole úspěch.

    [@no__t – modální 1Swagger přesměrování](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Další informace o interaktivním testování žádostí chráněných přes OAuth 2,0 najdete v [oficiální dokumentaci](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Další kroky

- Pokud si chcete přečíst další informace o objektových modelech digitálních vláken Azure a grafu prostorové logiky, přečtěte si téma [informace o objektových modelech digitálních vláken Azure](./concepts-objectmodel-spatialgraph.md).

- Pokud se chcete dozvědět, jak ověřit pomocí rozhraní API pro správu, přečtěte si téma [ověřování pomocí rozhraní API](./security-authenticating-apis.md).