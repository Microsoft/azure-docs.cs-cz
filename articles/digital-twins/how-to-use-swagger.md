---
title: Pochopit, jak použít Azure digitální dvojče odkaz Swagger | Dokumentace Microsoftu
description: Ke zjištění, jak používat Azure digitální dvojče Swagger referenční dokumentaci.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/29/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 9d4fd798309398dff38081cc66b4071c5dd1f5bf
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670790"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Digitální dvojče Swagger referenční dokumentace k Azure

Každá instance zřízené digitální dvojče Azure zahrnuje vlastní automaticky generované referenční dokumentaci Swagger.

[Swagger](https://swagger.io/), nebo [OpenAPI](https://www.openapis.org/), sjednotí komplexní informace o rozhraní API do prostředek referenční dokumentace pro interaktivní a jazykově nezávislé. Swagger obsahuje důležité referenční materiály, o které datové části JSON, metod HTTP a konkrétní koncové body používat k provedení operací k rozhraní API.

## <a name="swagger-summary"></a>Souhrn swagger

Swagger poskytuje interaktivní přehled vašeho rozhraní API, která zahrnuje:

* Informace o rozhraní API a objekt modelu.
* Koncových bodů rozhraní REST API, které zadejte požadované žádosti datových částí, záhlaví, parametry, kontext cesty a metody HTTP.
* Testování funkce rozhraní API.
* Informace o odpovědi příklad používá k ověření a potvrďte odpovědi protokolu HTTP.
* Informace o kódu chyby.

Swagger je praktický nástroj, který vám pomůže s vývoj a testování volání rozhraní API Správce Dvojčat digitální Azure.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Referenční materiál

Automaticky generované referenční materiál Swagger poskytuje rychlý přehled důležitých konceptů, k dispozici koncové body rozhraní API pro správu a popis jednotlivých objektový model pro vývoj a testování.

Stručný přehled popisuje rozhraní API.

[![Horní swagger](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Správa API objektové modely jsou také uvedeny.

[![Modely swagger](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Můžete vybrat jednotlivých uvedených objektový model pro podrobnější přehled klíčových atributů.

[![Swagger model](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Vygenerovaný objektové modely Swaggeru je pohodlné zobrazíte všechny dostupné Azure digitální dvojče [objekty a rozhraní API](./concepts-objectmodel-spatialgraph.md). Při vytváření řešení na Azure digitální dvojče mohou vývojáři použít tento prostředek.

## <a name="endpoint-summary"></a>Souhrn koncového bodu

Swagger obsahuje také důkladný přehled všechny koncové body, které tvoří rozhraní API pro správu.

Každý koncový bod uvedené také zahrnuje požadovaná žádost o informace, jako:

* Požadované parametry.
* Povinný parametr datové typy.
* Metoda HTTP pro přístup k prostředku.

[![Swagger koncových bodů](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Pokud chcete zobrazit podrobnější přehled, vyberte každého prostředku.

## <a name="use-swagger-to-test-endpoints"></a>Využití Swaggeru k testování koncových bodů

Jednou výkonné funkce, které poskytuje Swaggeru je schopnost testování koncového bodu přímo prostřednictvím uživatelského rozhraní v dokumentaci k rozhraní API.

Když vyberete určitý koncový bod, zobrazí se **vyzkoušet**.

[![Zkuste swagger](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Rozbalte tuto část, abyste vyvolali vstupní pole pro každý parametr požadované a volitelné. Zadejte správné hodnoty a vyberte **Execute**.

[![Pokusili swagger](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Po spuštění testu můžete ověřit data odpovědi.

## <a name="swagger-response-data"></a>Data odpovědi swagger

Každý koncový bod uvedené také zahrnuje data těla odpovědi k ověření pro vývoj a testování. Tyto příklady: stavový kód a JSON, které chcete zobrazit pro úspěšné požadavky HTTP.

[![Odpověď swagger](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Příklady také kódy chyb umožňující ladit nebo vylepšit selhání testů.

## <a name="swagger-oauth-20-authorization"></a>Swagger autorizace OAuth 2.0

> [!NOTE]
> * Uživatel instančního objektu, který vytvořil digitální dvojče Azure bude mít přiřazení rolí správce místa a budete moct vytvořit přiřazení dalších rolí pro ostatní uživatele. Tyto uživatele a jejich rolí můžete oprávnění k volání rozhraní API.

1. Postupujte podle kroků v [v tomto rychlém startu](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) nebo [registraci Azure digitální dvojče aplikace ve službě starší verze služby Azure Active Directory](./how-to-use-legacy-aad.md) vytvořit a nakonfigurovat aplikaci Azure AD. Alternativně můžete znovu použít existující registraci aplikace.

1. Přidejte následující adresa url odpovědi k registraci aplikace:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name  | Nahradit hodnotou | Příklad |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Vaši adresu URL dokumentace k rozhraní REST API pro správu nalézt v portálu  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Zkopírujte ID aplikace Azure AD.

Po dokončení registrace Azure Active Directory:

1. Vyberte **Authorize** tlačítko na stránce swagger.

    [![Vyberte Swaggeru povolit tlačítko](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Vložte do ID aplikace **client_id** pole.

    [![Pole client_id swagger](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Pak bude přesměrován na modální následující úspěch.

    [![Modální okno přesměrování swagger](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Další informace o interaktivním testování požadavků, které jsou chráněné pomocí OAuth 2.0, najdete v článku [oficiální dokumentaci](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o Azure digitální dvojče objektové modely a Prostorové řady grafu, přečtěte si [Principy Dvojčat digitální Azure objektu modely](./concepts-objectmodel-spatialgraph.md).

- Zjistěte, jak ověřování pomocí rozhraní API pro správu, přečtěte si téma [ověřování pomocí rozhraní API](./security-authenticating-apis.md).