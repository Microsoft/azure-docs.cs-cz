---
title: Konfigurace podmínek shody sady pravidel pro Azure front-end Standard/Premium
description: Tento článek poskytuje seznam různých podmínek shody, které jsou k dispozici pro sadu pravidel služby Azure front-Premium Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: 9e8defa9e929d21f210c48ffbd3b22e44195c17d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061617"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Podmínky shody sady pravidel služby Azure front-Premium Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

V sadě Azure front-Premium [Rule](concept-rule-set.md)Standard/Premium se pravidlo skládá z nuly nebo více podmínek shody a akce. Tento článek poskytuje podrobné popisy podmínek shody, které můžete použít v sadě pravidel služby Azure front-Premium Standard/Premium.

První část pravidla je podmínka shody nebo sada podmínek shody. Pravidlo může sestávat z až deseti podmínek shody. Podmínka shody identifikuje konkrétní typy požadavků, pro které jsou provedeny definované akce. Pokud použijete více podmínek shody, podmínky shody jsou seskupeny pomocí logiky a. Pro všechny podmínky shody, které podporují více hodnot, nebo je použita logika.

Podmínku shody můžete použít k těmto akcím:

* Vyfiltruje požadavky na základě konkrétní IP adresy, země nebo oblasti.
* Vyfiltruje požadavky podle informací v záhlaví.
* Vyfiltruje požadavky z mobilních zařízení nebo stolních zařízení.
* Vyfiltruje požadavky z názvu souboru žádosti a přípony souboru.
* Vyfiltruje požadavky z adresy URL požadavku, protokolu, cesty, řetězce dotazu, parametrů post atd.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="device-type"></a><a name="IsDevice"></a> Typ zařízení

Pomocí podmínky shody **typů zařízení** Identifikujte požadavky, které byly provedeny z mobilního zařízení nebo stolního zařízení.  

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-------|------------------|
| Operátor | <ul><li>V Azure Portal: `Equal` , `Not Equal`</li><li>V šablonách ARM: `Equal` ; použijte `negateCondition` vlastnost k určení _nerovnosti_ . |
| Hodnota | `Mobile`, `Desktop` |

### <a name="example"></a>Příklad

V tomto příkladu se shodují všechny požadavky zjištěné jako přicházející z mobilního zařízení.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="Snímek obrazovky portálu ukazující podmínku shody typu zařízení":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Post – argumenty

Použijte podmínku pro shodu **argumentů post** k identifikaci požadavků na základě argumentů uvedených v těle žádosti post. Jedna podmínka shody odpovídá jednomu argumentu z těla žádosti POST. Můžete zadat více hodnot, které mají být shodné, které budou kombinovány pomocí operátoru OR nebo Logic.

> [!NOTE]
> Podmínka shody **argumentů post** funguje s `application/x-www-form-urlencoded` typem obsahu.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-|-|
| Post – argumenty | Řetězcová hodnota představující název argumentu POST. |
| Operátor | Libovolný operátor ze [seznamu standardních operátorů](#operator-list). |
| Hodnota | Jeden nebo více hodnot typu řetězec nebo celé číslo představující hodnotu argumentu POST, který se má shodovat. Pokud je zadáno více hodnot, vyhodnocují se pomocí logiky nebo. |
| Transformace případu | `Lowercase`, `Uppercase` |

### <a name="example"></a>Příklad

V tomto příkladu se shodují všechny žádosti POST `customerName` , kde je v těle žádosti uveden argument a kde hodnota `customerName` začíná písmenem `J` nebo `K` . K převodu vstupních hodnot na velká písmena používáme transformaci velkých a malých písmen, aby byly hodnoty začínající na `J` , `j` , `K` a `k` všechny spárovány.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="Snímek obrazovky portálu ukazující podmínku pro shodu argumentů post":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> Řetězec dotazu

Použijte podmínku shody **řetězce dotazu** k identifikaci požadavků, které obsahují konkrétní řetězec dotazu. Můžete zadat více hodnot, které mají být shodné, které budou kombinovány pomocí operátoru OR nebo Logic.

> [!NOTE]
> Celý řetězec dotazu je porovnán jako jeden řetězec bez úvodní `?` .

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-|-|
| Operátor | Libovolný operátor ze [seznamu standardních operátorů](#operator-list). |
| Řetězec dotazu | Jeden nebo více hodnot typu řetězec nebo celé číslo představující hodnotu řetězce dotazu, který se má shodovat. Nepoužívejte na `?` začátku řetězce dotazu. Pokud je zadáno více hodnot, vyhodnocují se pomocí logiky nebo. |
| Transformace případu | `Lowercase`, `Uppercase` |

### <a name="example"></a>Příklad

V tomto příkladu odpovídáme všem žádostem, kde řetězec dotazu obsahuje řetězec `language=en-US` . Chceme, aby podmínka shody byla citlivá na velká a malá písmena, a proto jsme tento případ nemohli transformovat.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="Snímek obrazovky portálu zobrazující podmínku shody řetězce dotazu":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> Vzdálená adresa

Podmínka shody **vzdálených adres** identifikuje požadavky na základě umístění žadatele nebo IP adresy. Můžete zadat více hodnot, které mají být shodné, které budou kombinovány pomocí operátoru OR nebo Logic.

* Při zadávání bloků IP adres použijte zápis CIDR. To znamená, že syntaxe pro blok IP adres je základní IP adresa, za kterou následuje lomítko a velikost předpony. Například:
    * **Příklad protokolu IPv4**: `5.5.5.64/26` odpovídá všem žádostem, které přicházejí z adres 5.5.5.64 prostřednictvím 5.5.5.127.
    * **Příklad IPv6**: `1:2:3:/48` odpovídá všem žádostem, které přicházejí z adres 1:2:3:0:0:0:0:0 až 1:2:3: ffff: ffff: ffff: ffff: FFFF.
* Když zadáte víc IP adres a bloků IP adres, použije se logika nebo.
    * **Příklad protokolu IPv4**: Pokud PŘIDÁTE dvě IP adresy `1.2.3.4` a `10.20.30.40` , bude podmínka shodná se všemi požadavky, které dorazí buď na adresu 1.2.3.4 nebo 10.20.30.40.
    * **Příklad IPv6**: Pokud PŘIDÁTE dvě IP adresy `1:2:3:4:5:6:7:8` a `10:20:30:40:50:60:70:80` , bude se tato podmínka shodovat s všemi požadavky, které přicházejí z adresy 1:2:3:4:5:6:7:8 nebo 10:20:30:40:50:60:70:80.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-|-|
| Operátor | <ul><li>V Azure Portal: `Geo Match` , `Geo Not Match` , `IP Match` nebo `IP Not Match`</li><li>V šablonách ARM: `GeoMatch` , `IPMatch` ; použijte `negateCondition` vlastnost k určení _geografické neshody_ nebo _IP adresy se neshodují_ .</li></ul> |
| Hodnota | <ul><li>Pro `IP Match` operátory nebo `IP Not Match` : zadejte jeden nebo víc rozsahů IP adres. Pokud je zadáno více rozsahů IP adres, vyhodnocují se pomocí logiky nebo.</li><li>Pro `Geo Match` operátory nebo `Geo Not Match` : Zadejte jedno nebo více umístění za použití jejich kódu země.</li></ul> |

### <a name="example"></a>Příklad

V tomto příkladu odpovídáme všem žádostem, u kterých požadavek nepochází z USA.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="Snímek obrazovky portálu ukazující podmínku pro vzdálené shody adres":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> Text žádosti

Podmínka shody **těla žádosti** identifikuje požadavky na základě konkrétního textu, který se zobrazí v těle žádosti. Můžete zadat více hodnot, které mají být shodné, které budou kombinovány pomocí operátoru OR nebo Logic.

> [!NOTE]
> Pokud tělo žádosti přesáhne 64 KB, bude se pro podmínku shody **těla žádosti** brát v úvahu jenom prvních 64 KB.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-|-|
| Operátor | Libovolný operátor ze [seznamu standardních operátorů](#operator-list). |
| Hodnota | Jeden nebo více hodnot typu řetězec nebo celé číslo představující hodnotu textu textu požadavku, který se má shodovat. Pokud je zadáno více hodnot, vyhodnocují se pomocí logiky nebo. |
| Transformace případu | `Lowercase`, `Uppercase` |

### <a name="example"></a>Příklad

V tomto příkladu odpovídáme všem požadavkům, kde tělo žádosti obsahuje řetězec `ERROR` . Před vyhodnocením shody převádíme tělo požadavku na velká písmena, takže `error` a další variace případ budou také aktivovat tuto podmínku shody.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="Snímek obrazovky portálu zobrazující podmínku shody textu požadavku":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> Název souboru žádosti

Podmínka shody **názvů souborů žádosti** identifikuje požadavky, které obsahují zadaný název souboru v adrese URL požadavku. Můžete zadat více hodnot, které mají být shodné, které budou kombinovány pomocí operátoru OR nebo Logic.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-|-|
| Operátor | Libovolný operátor ze [seznamu standardních operátorů](#operator-list). |
| Hodnota | Jeden nebo více hodnot typu řetězec nebo celé číslo představující hodnotu názvu souboru požadavku, která se má shodovat. Pokud je zadáno více hodnot, vyhodnocují se pomocí logiky nebo. |
| Transformace případu | `Lowercase`, `Uppercase` |

### <a name="example"></a>Příklad

V tomto příkladu odpovídáme všem žádostem, kde je název souboru žádosti `media.mp4` . Název souboru před vyhodnocením shody Transformujte na malá písmena, takže `MEDIA.MP4` a ostatní variace případ budou taky aktivovat tuto podmínku shody.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="Snímek obrazovky portálu zobrazující podmínku shody názvu souboru požadavku":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> Žádost o příponu souboru

Podmínka shody **přípon souborů žádosti** identifikuje požadavky, které obsahují zadanou příponu souboru, do názvu souboru v adrese URL požadavku. Můžete zadat více hodnot, které mají být shodné, které budou kombinovány pomocí operátoru OR nebo Logic.

> [!NOTE]
> Nezahrnujte počáteční období. Můžete například namísto `html` použít `.html`.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-|-|
| Operátor | Libovolný operátor ze [seznamu standardních operátorů](#operator-list). |
| Hodnota | Jeden nebo více hodnot typu řetězec nebo celé číslo představující hodnotu přípony souboru požadavku, která se má shodovat. Nezahrnujte počáteční období. Pokud je zadáno více hodnot, vyhodnocují se pomocí logiky nebo. |
| Transformace případu | `Lowercase`, `Uppercase` |

### <a name="example"></a>Příklad

V tomto příkladu odpovídáme všem požadavkům, kde je přípona souboru žádosti `pdf` nebo `docx` . Před vyhodnocením shody převedeme příponu souboru požadavku na malá písmena, takže `PDF` , `DocX` a další variace případ budou aktivovat taky tuto podmínku shody.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="Snímek obrazovky portálu zobrazující podmínku shody přípony souboru požadavku":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> Hlavička žádosti

Podmínka shody **hlaviček požadavku** určuje požadavky, které v žádosti obsahují konkrétní hlavičku. Tuto podmínku shody můžete použít ke kontrole, zda hlavička existuje bez ohledu na její hodnotu nebo zda má být zkontrolováno, zda hlavička odpovídá zadané hodnotě. Můžete zadat více hodnot, které mají být shodné, které budou kombinovány pomocí operátoru OR nebo Logic.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-|-|
| Název hlavičky | Řetězcová hodnota představující název argumentu POST. |
| Operátor | Libovolný operátor ze [seznamu standardních operátorů](#operator-list). |
| Hodnota | Jeden nebo více hodnot typu řetězec nebo celé číslo představující hodnotu hlavičky požadavku, která se má shodovat. Pokud je zadáno více hodnot, vyhodnocují se pomocí logiky nebo. |
| Transformace případu | `Lowercase`, `Uppercase` |

### <a name="example"></a>Příklad

V tomto příkladu odpovídáme všem požadavkům, kde požadavek obsahuje hlavičku s názvem `MyCustomHeader` , bez ohledu na její hodnotu.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="Snímek obrazovky portálu zobrazující podmínku shody hlaviček požadavku":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> Request – metoda

Podmínka shody **metody žádosti** identifikuje požadavky, které používají zadanou metodu požadavku HTTP. Můžete zadat více hodnot, které mají být shodné, které budou kombinovány pomocí operátoru OR nebo Logic.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-|-|
| Operátor | <ul><li>V Azure Portal: `Equal` , `Not Equal`</li><li>V šablonách ARM: `Equal` ; použijte `negateCondition` vlastnost k určení _nerovnosti_ . |
| Request – metoda | Jedna nebo více metod HTTP z: `GET` , `POST` , `PUT` , `DELETE` , `HEAD` , `OPTIONS` , `TRACE` . Pokud je zadáno více hodnot, vyhodnocují se pomocí logiky nebo. |

### <a name="example"></a>Příklad

V tomto příkladu odpovídáme všem požadavkům, kde požadavek používá `DELETE` metodu.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="Snímek obrazovky portálu zobrazující podmínku shody metody požadavku":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> Cesta požadavku

Podmínka shody **cest požadavku** identifikuje požadavky, které obsahují zadanou cestu v adrese URL požadavku. Můžete zadat více hodnot, které mají být shodné, které budou kombinovány pomocí operátoru OR nebo Logic.

> [!NOTE]
> Cesta je částí adresy URL za názvem hostitele a lomítkem. Například v adrese URL `https://www.contoso.com/files/secure/file1.pdf` je cesta `files/secure/file1.pdf` .

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-|-|
| Operátor | Libovolný operátor ze [seznamu standardních operátorů](#operator-list). |
| Hodnota | Jeden nebo více hodnot typu řetězec nebo celé číslo představující hodnotu cesty požadavku, která se má shodovat. Nepoužívejte počáteční lomítko. Pokud je zadáno více hodnot, vyhodnocují se pomocí logiky nebo. |
| Transformace případu | `Lowercase`, `Uppercase` |

### <a name="example"></a>Příklad

V tomto příkladu porovnáváme všechny žádosti, kde začíná cesta k souboru žádosti `files/secure/` . Před vyhodnocením shody převádíme příponu souboru na malá písmena, takže požadavky na `files/SECURE/` a další variace případ budou taky aktivovat tuto podmínku shody.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="Snímek obrazovky portálu zobrazující podmínku shody cesty požadavku":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> Protokol žádosti

Podmínka shody **protokolu žádosti** identifikuje požadavky, které používají zadaný protokol (http nebo https).

> [!NOTE]
> *Protokol* se někdy také označuje jako *schéma*.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-|-|
| Operátor | <ul><li>V Azure Portal: `Equal` , `Not Equal`</li><li>V šablonách ARM: `Equal` ; použijte `negateCondition` vlastnost k určení _nerovnosti_ . |
| Request – metoda | `HTTP`, `HTTPS` |

### <a name="example"></a>Příklad

V tomto příkladu odpovídáme všem žádostem, kde požadavek používá `HTTP` protokol.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="Snímek obrazovky portálu zobrazující podmínku shody protokolu požadavku":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> Adresa URL požadavku

Identifikuje požadavky, které odpovídají zadané adrese URL. Vyhodnotí se celá adresa URL, včetně protokolu a řetězce dotazu, ale ne fragmentu. Můžete zadat více hodnot, které mají být shodné, které budou kombinovány pomocí operátoru OR nebo Logic.

> [!TIP]
> Když použijete tuto podmínku pravidla, nezapomeňte protokol zahrnout. Například použijte `https://www.contoso.com` místo pouh `www.contoso.com` .

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-|-|
| Operátor | Libovolný operátor ze [seznamu standardních operátorů](#operator-list). |
| Hodnota | Jeden nebo více hodnot typu řetězec nebo celé číslo představující hodnotu adresy URL požadavku, která se má shodovat. Pokud je zadáno více hodnot, vyhodnocují se pomocí logiky nebo. |
| Transformace případu | `Lowercase`, `Uppercase` |

### <a name="example"></a>Příklad

V tomto příkladu odpovídáme všem žádostem, kde adresa URL požadavku začíná `https://api.contoso.com/customers/123` . Před vyhodnocením shody převádíme příponu souboru na malá písmena, takže požadavky na `https://api.contoso.com/Customers/123` a další variace případ budou taky aktivovat tuto podmínku shody.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="Snímek obrazovky portálu zobrazující podmínku shody adresy URL požadavku":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> Seznam operátorů

Pro pravidla, která přijímají hodnoty ze seznamu standardních operátorů, platí následující operátory:

| Operátor                   | Popis                                                                                                                    | Podpora šablon ARM                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Všechny                        | Odpovídá, pokud existuje libovolná hodnota, bez ohledu na to, co je to.                                                                     | `operator`: `Any`                                               |
| Je rovno                      | Odpovídá, pokud hodnota přesně odpovídá zadanému řetězci.                                                                   | `operator`: `Equal`                                             |
| Contains                   | Odpovídá, pokud hodnota obsahuje zadaný řetězec.                                                                          | `operator`: `Contains`                                          |
| Je menší než                  | Odpovídá, pokud je délka hodnoty menší než zadané celé číslo.                                                       | `operator`: `LessThan`                                          |
| Větší než               | Odpovídá, pokud je délka hodnoty větší než zadané celé číslo.                                                    | `operator`: `GreaterThan`                                       |
| Menší než nebo rovno         | Odpovídá, pokud je délka hodnoty menší nebo rovna zadanému celému číslu.                                           | `operator`: `LessThanOrEqual`                                   |
| Je větší než nebo rovno      | Odpovídá, pokud délka hodnoty je větší nebo rovna zadanému celému číslu.                                        | `operator`: `GreaterThanOrEqual`                                |
| Začíná na                | Odpovídá, pokud hodnota začíná zadaným řetězcem.                                                                       | `operator`: `BeginsWith`                                        |
| Končí na                  | Odpovídá, pokud hodnota končí zadaným řetězcem.                                                                         | `operator`: `EndsWith`                                          |
| Regulární                      | Odpovídá, pokud hodnota odpovídá zadanému regulárnímu výrazu. [Další podrobnosti najdete níže.](#regular-expressions)        | `operator`: `RegEx`                                             |
| Ne žádné                    | Odpovídá, pokud není k dispozici žádná hodnota.                                                                                                | `operator`: `Any` a `negateCondition` : `true`                |
| Není rovno                  | Odpovídá, pokud hodnota neodpovídá zadanému řetězci.                                                                    | `operator`: `Equal` a `negateCondition` : `true`              |
| Neobsahuje               | Odpovídá, pokud hodnota neobsahuje zadaný řetězec.                                                                  | `operator`: `Contains` a `negateCondition` : `true`           |
| Není menší než              | Odpovídá, pokud délka hodnoty není menší než zadané celé číslo.                                                   | `operator`: `LessThan` a `negateCondition` : `true`           |
| Není větší než           | Odpovídá, pokud délka hodnoty není větší než zadané celé číslo.                                                | `operator`: `GreaterThan` a `negateCondition` : `true`        |
| Není menší než nebo rovno     | Odpovídá, pokud délka hodnoty není menší nebo rovna zadanému celému číslu.                                       | `operator`: `LessThanOrEqual` a `negateCondition` : `true`    |
| Není větší než nebo rovno | Odpovídá, pokud délka hodnoty není větší než nebo rovna zadanému celému číslu.                                    | `operator`: `GreaterThanOrEqual` a `negateCondition` : `true` |
| Nezačíná na            | Odpovídá, pokud hodnota nezačíná zadaným řetězcem.                                                               | `operator`: `BeginsWith` a `negateCondition` : `true`         |
| Nekončí na              | Odpovídá, pokud hodnota nekončí zadaným řetězcem.                                                                 | `operator`: `EndsWith` a `negateCondition` : `true`           |
| Není RegEx                  | Odpovídá, pokud hodnota neodpovídá zadanému regulárnímu výrazu. [Další podrobnosti najdete níže.](#regular-expressions) | `operator`: `RegEx` a `negateCondition` : `true`              |

> [!TIP]
> Pro číselné operátory, které jsou *menší než* a *větší než nebo rovno*, je použití porovnání založeno na délce. Hodnota v podmínce Match by měla být celé číslo, které určuje délku, kterou chcete porovnat.

### <a name="regular-expressions"></a><a name="regular-expressions"></a> Regulární výrazy

Regulární výrazy nepodporují následující operace:

* Zpětná reference a zachycení dílčích výrazů.
* Libovolné kontrolní výrazy s nulovou šířkou.
* Reference k podrutinám a rekurzivní vzory.
* Podmíněné vzory.
* Řízení operací zpětného navrácení.
* `\C`Direktiva s jedním bajtem.
* `\R`Direktiva shody nového řádku.
* `\K`Začátek direktivy resetování shody
* Popisky a vložený kód.
* Seskupení atomie a kvantifikátory possessive.

## <a name="next-steps"></a>Další kroky

* Další informace o [sadě pravidel](concept-rule-set.md)
* Naučte se [Konfigurovat svoji první sadu pravidel](how-to-configure-rule-set.md).
* Přečtěte si další informace o [akcích sady pravidel](concept-rule-set-actions.md).
