---
title: Pochopit, jak použít Azure digitální dvojče odkaz Swagger | Dokumentace Microsoftu
description: Ke zjištění, jak používat Azure digitální dvojče Swagger referenční dokumentaci.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 1746e1d53be01e6c40b5d1948c666960970b75a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922383"
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

![Horní swagger][1]

Správa API objektové modely jsou také uvedeny.

![Modely swagger][2]

Můžete vybrat jednotlivých uvedených objektový model pro podrobnější přehled klíčových atributů.

![Swagger model][3]

Vygenerovaný objektové modely Swaggeru je pohodlné zobrazíte všechny dostupné Azure digitální dvojče [objekty a rozhraní API](./concepts-objectmodel-spatialgraph.md). Při vytváření řešení na Azure digitální dvojče mohou vývojáři použít tento prostředek.

## <a name="endpoint-summary"></a>Souhrn koncového bodu

Swagger obsahuje také důkladný přehled všechny koncové body, které tvoří rozhraní API pro správu.

Každý koncový bod uvedené také zahrnuje požadovaná žádost o informace, jako:

* Požadované parametry.
* Povinný parametr datové typy.
* Metoda HTTP pro přístup k prostředku.

![Swagger koncových bodů][4]

Pokud chcete zobrazit podrobnější přehled, vyberte každého prostředku.

## <a name="use-swagger-to-test-endpoints"></a>Využití Swaggeru k testování koncových bodů

Jednou výkonné funkce, které poskytuje Swaggeru je schopnost testování koncového bodu přímo prostřednictvím uživatelského rozhraní v dokumentaci k rozhraní API.

Když vyberete určitý koncový bod, zobrazí se **vyzkoušet**.

![Zkuste swagger][5]

Rozbalte tuto část, abyste vyvolali vstupní pole pro každý parametr požadované a volitelné. Zadejte správné hodnoty a vyberte **Execute**.

![Pokusili swagger][6]

Po spuštění testu můžete ověřit data odpovědi.

## <a name="swagger-response-data"></a>Data odpovědi swagger

Každý koncový bod uvedené také zahrnuje data těla odpovědi k ověření pro vývoj a testování. Tyto příklady: stavový kód a JSON, které chcete zobrazit pro úspěšné požadavky HTTP.

![Odpověď swagger][7]

Příklady také kódy chyb umožňující ladit nebo vylepšit selhání testů.

## <a name="swagger-oauth-20-authorization"></a>Swagger autorizace OAuth 2.0

Další informace o interaktivním testování požadavků, které jsou chráněné pomocí OAuth 2.0, najdete v článku [oficiální dokumentaci](https://swagger.io/docs/specification/authentication/oauth2/).

> [!NOTE]
> Uživatel instančního objektu, který vytvořil digitální dvojče Azure bude mít přiřazení rolí správce místa a budete moct vytvořit přiřazení dalších rolí pro ostatní uživatele.

1. Postupujte podle kroků v [v tomto rychlém startu](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) k vytvoření aplikace Azure AD typu ***webovou aplikaci nebo API***. Nebo můžete znovu použít existující registraci aplikace.

2. Přidejte následující adresa url odpovědi k registraci aplikace:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Název  | Nahradit hodnotou | Příklad: |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Vaši adresu URL dokumentace k rozhraní REST API pro správu nalézt v portálu  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

3. Udělení oprávnění pro vaši aplikaci pro přístup k digitální dvojče Azure. V části **požadovaná oprávnění**, zadejte `Azure Digital Twins` a vyberte **delegovaná oprávnění**. Potom vyberte **udělit oprávnění**.

    ![Registrace aplikace Azure AD přidat rozhraní api](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

4. Konfigurace manifestu aplikace povolit implicitní tok OAuth 2.0. Klikněte na tlačítko **Manifest** otevřete manifest aplikace pro vaši aplikaci. Nastavte *oauth2AllowImplicitFlow* k `true`.

    ![Azure AD implicitní tok](../../includes/media/digital-twins-permissions/aad-app-allow-implicit-flow.png)

5. Zkopírujte ID aplikace Azure AD.

6. Klikněte na tlačítko Autorizovat na stránce swagger.

    ![Povolit tlačítko swagger](../../includes/media/digital-twins-permissions/swagger-select-authorize-btn.png)

7. Do pole client_id vložte ID aplikace.

    ![Pole client_id swagger](../../includes/media/digital-twins-permissions/swagger-auth-form.png)

    ![Swagger udělení oprávnění aplikace](../../includes/media/digital-twins-permissions/swagger-grant-application-permissions.png)

8. Teď byste měli vidět nositele předaný ověřovací token v hlavičce autorizace a identita přihlášeného uživatele zobrazí ve výsledku.

    ![Token výsledek swagger](../../includes/media/digital-twins-permissions/swagger-token-example.png)

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o Azure digitální dvojče objektové modely a Prostorové řady grafu, přečtěte si [Principy Dvojčat digitální Azure objektu modely](./concepts-objectmodel-spatialgraph.md).

- Zjistěte, jak ověřování pomocí rozhraní API pro správu, přečtěte si téma [ověřování pomocí rozhraní API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
