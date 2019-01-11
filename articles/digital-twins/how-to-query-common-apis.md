---
title: Azure digitální dvojče běžné vzory dotazů | Dokumentace Microsoftu
description: Další běžné vzory dotazů na rozhraní API pro správu Azure digitální dvojče.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: ff8638042fa10c939ff9c5fa7af99a660fcdc753
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198639"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Jak provádět dotazy digitální dvojče API služby Azure pro běžné úlohy

Tento článek popisuje vzory dotazů vám pomůže při realizaci běžné scénáře pro vaši instanci digitální dvojče Azure. To předpokládá, že je již spuštěna instance digitální dvojče. Můžete použít libovolného klienta REST, jako je Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Dotazy na mezery a typy

Tato část ukazuje ukázkové dotazy, chcete-li získat další informace o zřízené mezery. Proveďte ověřené žádosti GET HTTP s ukázkové dotazy, zástupné texty nahraďte hodnotami z vašeho nastavení. 

- Získejte prostory, které jsou kořenové uzly.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Načíst místo podle názvu a obsahovat zařízení, senzorů, vypočítané hodnoty a hodnoty čidel. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Získat prostorů a jejich zařízení a senzorů, jejichž nadřazený prvek je na dané místo ID a které jsou na úrovni přibližně 2 až 5 [vzhledem k dané místo](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Získání místa s daným ID a zahrnují vypočítané hodnoty snímačů a.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Získáte klíče vlastnosti pro určité místo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Získejte klíč vlastnosti s názvem prostory *AreaInSqMeters* a její hodnota je 30. Můžete také řetězec operace, například get prostory obsahující klíč vlastnosti s `name = X contains Y`.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Získat všechny názvy s názvem *teploty* a přidružené závislosti a ontologie.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Dotazy pro role a přiřazení rolí

Tato část uvádí některé dotazy, chcete-li získat další informace o rolích a jejich přiřazení. 

- Získáte všechny role, které podporuje Azure digitální dvojče.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Získáte všechna přiřazení rolí v digitální dvojče instanci. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Získáte přiřazení rolí na konkrétní cesty.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Dotazy na zařízení

Tato část uvádí některé příklady použití rozhraní API pro správu k získání konkrétních informací o vašich zařízeních. Všechna volání rozhraní API musí být ověřené žádosti GET HTTP.

- Získáte všechna zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Najdete všechny stavy zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Získání konkrétní zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Získáte všechna zařízení připojená k kořenové místa.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Získáte všechna zařízení připojená k mezery na úrovni 2 až 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Získat všechna zařízení připojená přímo na konkrétní místo ID.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Získáte všechna zařízení připojená k určité místo a jejích potomků.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Získáte všechna zařízení připojená k následníky mezeru, s výjimkou toto místo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Získáte všechna zařízení připojená k přímé podřízené objekty prostoru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Získáte všechna zařízení připojená k jednomu z nadřazených mezerou.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Získáte všechna zařízení připojená k následníky místo, které jsou menší než nebo roven hodnotě 5 úrovně.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Získat všechna zařízení připojená k prostory, které jsou na stejné úrovni jako prostor s ID *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Získání připojovacího řetězce zařízení služby IoT Hub pro vaše zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Získejte ID daného hardwaru, včetně připojených senzorů zařízení.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Získání senzorů pro konkrétní datové typy v tomto případě *pohybu* a *teploty*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Dotazy na procesy pro hledání shody a uživatelem definovaných funkcí 

- Získáte všechny zřízené procesy pro hledání shody a jejich ID.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Získáte podrobnosti o konkrétní předávaný, včetně mezer a uživatelem definované funkce, které s ním spojená.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Vyhodnocení předávaný proti senzoru a povolení protokolování pro účely ladění. Určuje, zda předávaný a senzor patří na datový typ říká vrátit tuto zprávu HTTP GET. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Získejte ID uživatelem definované funkce. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Získat obsah konkrétním uživatelem definované funkce 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Dotazy pro uživatele

Tato část ukazuje několik ukázkových dotazů rozhraní API pro správu uživatelů v digitální dvojče Azure. Ujistěte se, požadavek HTTP GET zástupné texty nahraďte hodnotami z vašeho nastavení. 

- Získáte všechny uživatele. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Získání konkrétního uživatele.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Další postup

Zjistěte, jak ověřování pomocí rozhraní API pro správu, přečtěte si téma [ověřování pomocí rozhraní API](./security-authenticating-apis.md).

Pokud chcete zobrazit všechny koncové body rozhraní API, přečtěte si [použití digitální dvojče Swagger](./how-to-use-swagger.md).
