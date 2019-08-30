---
title: Běžné vzory dotazů v Azure – digitální vlákna | Microsoft Docs
description: Seznamte se s běžnými způsoby dotazování rozhraní API pro správu digitálních vláken Azure.
author: kingdomofends
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: v-adgera
ms.openlocfilehash: 55b0676ce0a0dc6d4347ddcadf43b7b650f0f9a1
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172797"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Dotazování rozhraní API digitálních vláken Azure na běžné úlohy

V tomto článku se dozvíte o vzorech dotazů, které vám pomůžou při provádění běžných scénářů pro instanci digitálních vláken Azure. Předpokládá se, že instance digitálního vlákna je už spuštěná. Můžete použít libovolného klienta REST, jako je například post. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Dotazy na mezery a typy

V této části najdete Ukázkové dotazy, které vám pomohou získat další informace o zřízených prostorech. Pomocí ukázkových dotazů pořiďte ověřené požadavky GET HTTP a zástupné symboly nahraďte hodnotami z vašeho nastavení. 

- Získejte prostory, které jsou kořenovými uzly.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Získejte prostor podle názvu a uveďte zařízení, senzory, vypočítané hodnoty a hodnoty snímačů. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Získat mezery a jejich informace o zařízení/senzoru, jejichž nadřazeným objektem je dané ID prostoru a které jsou na úrovni dvě až pět [vzhledem k danému místu](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Získejte místo se zadaným ID a zahrňte hodnoty vypočtené a snímače.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Získá klíče vlastností pro určitý prostor.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Získá mezery s klíčem vlastnosti s názvem *AreaInSqMeters* a jeho hodnota je 30. Můžete také provádět operace s `name = X contains Y`řetězci, například získat mezery obsahující klíč vlastnosti.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Získejte všechny názvy s *teplotou* názvu a přidruženými závislostmi a ontologie.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Dotazy na role a přiřazení rolí

V této části jsou uvedeny některé dotazy, které vám pomohou získat další informace o rolích a jejich přiřazeních. 

- Získejte všechny role podporované pomocí digitálních vláken Azure.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Získejte všechna přiřazení rolí v instanci digitálního vlákna. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Získá přiřazení rolí na konkrétní cestě.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Dotazy na zařízení

V této části se dozvíte několik příkladů, jak můžete pomocí rozhraní API pro správu získat konkrétní informace o svých zařízeních. Všechna volání rozhraní API musí být ověřena získat požadavky HTTP.

- Získat všechna zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Vyhledá všechny stavy zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Získat konkrétní zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Získejte všechna zařízení připojená k kořenovému prostoru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Získejte všechna zařízení připojená k prostorům na úrovních 2 až 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Získejte všechna zařízení přímo připojená k určitému ID prostoru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Získejte všechna zařízení připojená k určitému prostoru a jeho následníkům.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Získejte všechna zařízení připojená k následníkům prostoru s výjimkou tohoto místa.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Získejte všechna zařízení připojená k přímým dětem místa.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Získejte všechna zařízení připojená k jednomu z nadřazených míst v prostoru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Získejte všechna zařízení připojená k následníkům prostoru, který je menší nebo roven 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Získejte všechna zařízení připojená k mezerám, které jsou na stejné úrovni jako místo s ID *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Získejte připojovací řetězec IoT Hub zařízení pro vaše zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Získá zařízení s daným ID hardwaru, včetně připojených senzorů.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Získejte senzory pro konkrétní datové typy, v tomto případě *pohyb* a *teplota*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Dotazy na shody a uživatelsky definované funkce 

- Získá všechny zřízené shody a jejich ID.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Získat podrobnosti o konkrétním shodě, včetně mezer a uživatelsky definované funkce, která je k ní přidružená.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Vyhodnotit shodu se senzorem a povolit protokolování pro účely ladění. Vrátí tuto zprávu HTTP GET s informacemi o tom, zda se ke stejnému datovému typu shoduje i senzor. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Získá ID uživatelsky definovaných funkcí. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Získá obsah konkrétní uživatelsky definované funkce. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Dotazy pro uživatele

V této části najdete několik ukázkových dotazů k rozhraní API pro správu uživatelů v digitálních Vlákenách Azure. Udělejte požadavek HTTP GET, který nahradí zástupné hodnoty hodnotami z vašeho nastavení. 

- Získá všechny uživatele. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Získá konkrétního uživatele.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Další postup

Pokud se chcete dozvědět, jak ověřit pomocí rozhraní API pro správu, přečtěte si [ověřování pomocí rozhraní API](./security-authenticating-apis.md).

Pokud chcete získat další informace o vašich koncových bodech rozhraní API, přečtěte si, [Jak používat digitální vlákna Swagger](./how-to-use-swagger.md).
