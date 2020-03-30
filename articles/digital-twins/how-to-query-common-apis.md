---
title: Běžné vzorce dotazů – Digitální dvojčata Azure | Dokumenty společnosti Microsoft
description: Seznamte se s několika běžnými vzory dotazů rozhraní API pro rozhraní API pro správu digitálních dvojčat Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 133c0e0dcc07afb85a0f3af9ae51d2207abac293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589109"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Jak se dotazovat Azure Digital Twins API pro běžné úkoly

Tento článek ukazuje vzorce dotazů, které vám pomohou spustit běžné scénáře pro instanci Azure Digital Twins. To předpokládá, že vaše digitální dvojčata instance je již spuštěna. Můžete použít libovolný klient REST, například Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Dotazy na mezery a typy

Tato část zobrazuje ukázkové dotazy, které vám poskytnou další informace o zřízených prostorech. Vytvořte ověřené požadavky GET HTTP s ukázkovými dotazy a nahraďte zástupné symboly hodnotami z vašeho nastavení. 

- Získejte mezery, které jsou kořenové uzly.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Získejte prostor podle názvu a zahrňte zařízení, senzory, vypočítané hodnoty a hodnoty senzorů. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Získejte mezery a informace o jejich zařízení/senzoru, jehož nadřazeným objektem je dané ID místa a které jsou na úrovni dvě až pět [vzhledem k danému prostoru](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Získejte místo s daným ID a zahrňte vypočítané hodnoty a hodnoty senzorů.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Získejte klíče vlastností pro určitý prostor.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Získejte mezery s klíčem vlastnosti s názvem *AreaInSqMeters* a jeho hodnota je 30. Můžete také provést operace řetězce, například získat mezery obsahující klíč vlastnosti s `name = X contains Y`.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Získejte všechny názvy s názvem *Teplota* a přidružené závislosti a ontologie.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Dotazy na role a přiřazení rolí

Tato část zobrazuje některé dotazy, které mají získat další informace o rolích a jejich přiřazeních. 

- Získejte všechny role podporované Azure Digital Twins.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Získejte všechna přiřazení rolí v instanci Digitální dvojčata. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Získejte přiřazení rolí na určité cestě.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Dotazy na zařízení

Tato část ukazuje některé příklady, jak můžete pomocí nastavení API pro správu získat konkrétní informace o vašich zařízeních. Všechna volání rozhraní API musí být ověřena požadavky HTTP GET.

- Získejte všechna zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Vyhledejte všechny stavy zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Získejte konkrétní zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Získejte všechna zařízení připojená ke kořenovému prostoru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Získejte všechna zařízení připojená k mezerám na úrovních 2 až 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Získejte všechna zařízení přímo připojena k určitému ID prostoru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Získejte všechna zařízení připojená k určitému prostoru a jeho potomkům.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Získejte všechna zařízení připojená k potomkům prostoru, s výjimkou tohoto prostoru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Získejte všechna zařízení připojená k přímým dětem prostoru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Získejte všechna zařízení připojená k jednomu z předchůdců prostoru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Získejte všechna zařízení připojená k potomkům prostoru, které jsou na úrovni menší nebo rovna 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Získejte všechna zařízení připojená k mezerám, které jsou na stejné úrovni jako prostor s ID *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Získejte připojovací řetězec zařízení ioT Hub pro vaše zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Získejte zařízení s daným ID hardwaru, včetně připojených senzorů.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Získejte senzory pro konkrétní datové typy, v tomto případě *Pohyb* a *teplota*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Dotazy na matchery a uživatelem definované funkce 

- Získejte všechny zřízené matchery a jejich ID.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Získejte podrobnosti o konkrétním matcheru, včetně mezer a uživatelem definované funkce, které jsou k němu přidruženy.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Vyhodnoťte matcher proti senzoru a povolte protokolování pro účely ladění. Vrácení této zprávy HTTP GET informuje o tom, zda matcher a senzor patří do datového typu. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Získejte ID uživatelem definovaných funkcí. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Získání obsahu konkrétní uživatelem definované funkce 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Dotazy pro uživatele

Tato část ukazuje některé ukázkové dotazy rozhraní API pro správu uživatelů v Azure Digital Twins. Vytvořte požadavek HTTP GET, který nahradí zástupné symboly hodnotami z vašeho nastavení. 

- Získejte všechny uživatele. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Získejte konkrétního uživatele.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět, jak se ověřit pomocí rozhraní API pro správu, přečtěte si [článek Ověřování pomocí rozhraní API](./security-authenticating-apis.md).

Další informace o koncových bodech rozhraní API najdete v článek [Použití funkce Digitální dvojčata Swagger](./how-to-use-swagger.md).
