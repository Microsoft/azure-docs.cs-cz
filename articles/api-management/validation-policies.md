---
title: Zásady ověřování v Azure API Management | Microsoft Docs
description: Přečtěte si o zásadách, které můžete použít v Azure API Management k ověření požadavků a odpovědí.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 3f91ca21512b8cddcac7fe71fa3eec07e1a8745a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720079"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>Zásady API Management k ověřování žádostí a odpovědí

Tento článek poskytuje referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](./api-management-policies.md).

Pomocí zásad ověřování můžete ověřit požadavky a odpovědi rozhraní API na OpenAPI schématu a chránit před ohroženími zabezpečení, jako je například vkládání hlaviček nebo datové části. I když se nejedná o náhradu za bránu firewall webových aplikací, zásady ověřování poskytují flexibilitu při reakci na další třídu hrozeb, na kterou se nevztahují zabezpečovací produkty, které jsou závislé na statických předdefinovaných pravidlech.

## <a name="validation-policies"></a>Zásady ověřování

- [Validate Content](#validate-content) – ověřuje velikost nebo schéma JSON pro požadavek nebo tělo odpovědi na schéma rozhraní API. 
- [Parametry ověření](#validate-parameters) – ověří parametry záhlaví požadavku, dotazu nebo cesty proti schématu rozhraní API.
- [Validate Headers](#validate-headers) – ověřuje hlavičky odpovědí proti schématu rozhraní API.
- [Ověřit stavový kód](#validate-status-code) – ověří stavové kódy HTTP v odpovědích proti schématu rozhraní API.

> [!NOTE]
> Maximální velikost schématu rozhraní API, které může zásada ověřování používat, je 4 MB. Pokud schéma tento limit překročí, zásady ověřování vrátí chyby za běhu. Pokud ho chcete zvýšit, obraťte se prosím na [podporu](https://azure.microsoft.com/support/options/). 

## <a name="actions"></a>Akce

Každá zásada ověřování obsahuje atribut, který určuje akci, kterou API Management provede při ověřování entity v žádosti rozhraní API nebo v reakci na schéma rozhraní API. Pro elementy, které jsou zastoupené ve schématu rozhraní API a v závislosti na zásadách, je možné zadat akci pro prvky, které nejsou reprezentované ve schématu rozhraní API. Akce zadaná v podřízeném elementu zásady Přepisuje akci určenou pro svůj nadřazený prvek.

Dostupné akce:

| Akce         | Popis          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| ignorovat | Přeskočit ověření. |
| aby | Zablokuje zpracování žádosti nebo odpovědi, zaprotokoluje chybu ověření podrobností a vrátí chybu. Zpracování je přerušeno při zjištění první sady chyb. |
| zjistit | Protokoluje chyby ověřování, aniž by došlo k přerušení zpracování požadavků nebo odpovědí. |

## <a name="logs"></a>Protokoly

Podrobnosti o chybách ověřování během provádění zásad jsou protokolovány do proměnné v `context.Variables` zadaném v `errors-variable-name` atributu v kořenovém elementu zásad. Při konfiguraci v `prevent` akci blokuje chyba ověřování další zpracování žádostí nebo odpovědí a také se šíří na `context.LastError` vlastnost. 

Chcete-li prozkoumat chyby, použijte zásady [trasování](api-management-advanced-policies.md#Trace) k protokolování chyb z kontextových proměnných do [Application Insights](api-management-howto-app-insights.md).

## <a name="performance-implications"></a>Vliv na výkon

Přidání zásad ověřování může mít vliv na propustnost rozhraní API. Platí tyto obecné zásady:
* Čím větší je velikost schématu rozhraní API, tím nižší bude propustnost. 
* Čím větší je datová část v žádosti nebo odpovědi, tím nižší bude propustnost. 
* Velikost schématu rozhraní API má větší vliv na výkon než velikost datové části. 
* Ověřování proti schématu rozhraní API, které má velikost v megabajtech, může způsobit vypršení časových limitů požadavků nebo odpovědí za určitých podmínek. Tento efekt je výraznější ve  **spotřebě** a úrovních pro **vývojáře** služby. 

Pro vyhodnocení dopadu zásad ověřování na propustnost rozhraní API doporučujeme provést zátěžové testy s očekávanými provozními úlohami.

## <a name="validate-content"></a>Ověřit obsah

Tato `validate-content` zásada ověřuje velikost nebo schéma JSON pro požadavek nebo tělo odpovědi oproti schématu rozhraní API. Formáty jiné než JSON nejsou podporovány.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>Příklad

V následujícím příkladu je datová část JSON v požadavcích a odpovědích ověřována v režimu detekce. Zprávy s datovými částmi většími než 100 KB jsou blokovány. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>Elementy

| Název         | Popis                                                                                                                                   | Povinné |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ověřit – obsah | Kořenový element.                                                                                                                               | Yes      |
| obsah | Přidejte jeden nebo více těchto prvků pro ověření typu obsahu v žádosti nebo odpovědi a proveďte určenou akci.  | No |

### <a name="attributes"></a>Atributy

| Název                       | Popis                                                                                                                                                            | Povinné | Výchozí |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| Neurčený typ Content-Type-Action | [Akce](#actions) , která se má provést pro žádosti nebo odpovědi s typem obsahu, který není zadaný ve schématu rozhraní API. |  Yes     | –   |
| maximální velikost | Maximální délka těla žádosti nebo odpovědi v bajtech, která je zaregistrována v `Content-Length` hlavičce. Pokud je text požadavku nebo tělo odpovědi komprimováno, je tato hodnota dekomprimovaná délka. Maximální povolená hodnota: 102 400 bajtů (100 KB).  | Yes       | –   |
| velikost – překročení – akce | [Akce](#actions) , která se má provést pro žádosti nebo odpovědi, jejichž tělo překračuje velikost určenou v `max-size` . |  Yes     | –   |
| chyby – název proměnné | Název proměnné v nástroji `context.Variables` pro chyby ověření protokolu na.  |   Yes    | –   |
| typ | Typ obsahu, pro který se má provést ověření těla, u kterého se kontroluje v `Content-Type` záhlaví Tato hodnota rozlišuje malá a velká písmena. Pokud je prázdné, platí pro každý typ obsahu určený ve schématu rozhraní API. |   No    |  –  |
| ověřit jako | Ověřovací modul, který se použije k ověření těla žádosti nebo odpovědi s typem odpovídajícího obsahu. V současné době je jediná podporovaná hodnota "JSON".   |  Yes     |  –  |
| akce | [Akce](#actions) , která se má provést pro žádosti nebo odpovědi, jejichž tělo neodpovídá zadanému typu obsahu.  |  Yes      | –   |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="validate-parameters"></a>Ověřit parametry

Tato `validate-parameters` zásada ověřuje parametry hlaviček, dotazů nebo cest v požadavcích proti schématu rozhraní API.

> [!IMPORTANT]
> Pokud jste importovali rozhraní API pomocí rozhraní API pro správu před verzí `2021-01-01-preview` , `validate-parameters` nemusí tato zásada fungovat. Možná budete muset znovu naimportovat rozhraní API pomocí rozhraní API pro správu `2021-01-01-preview` nebo novější verze.


### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>Příklad

V tomto příkladu jsou všechny parametry dotazů a cest ověřovány v režimu prevence a hlavičkách v režimu detekce. Ověřování je přepsáno pro několik parametrů záhlaví:

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
</validate-parameters>
```

### <a name="elements"></a>Elementy

| Název         | Popis                                                                                                                                   | Povinné |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ověřit – parametry | Kořenový element. Určuje výchozí akce ověřování pro všechny parametry v požadavcích.                                                                                                                              | Yes      |
| záhlaví | Přidejte tento prvek pro přepsání výchozích ověřovacích akcí pro parametry hlaviček v požadavcích.   | No |
| query | Přidejte tento prvek pro přepsání výchozích ověřovacích akcí pro parametry dotazu v požadavcích.  | No |
| program | Přidejte tento prvek pro přepsání výchozích akcí ověření pro parametry cesty URL v požadavcích.  | No |
| parameter | Přidejte jeden nebo více prvků pro pojmenované parametry pro přepsání konfigurace na vyšší úrovni akcí ověření. | No |

### <a name="attributes"></a>Atributy

| Název                       | Popis                                                                                                                                                            | Povinné | Výchozí |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| zadaný parametr-parametru-action | [Akce](#actions) , která se má provést pro parametry požadavku zadané ve schématu rozhraní API <br/><br/> V případě, že je k dispozici v `headers` `query` prvku,, nebo `path` , Přepisuje hodnota `specified-parameter-action` v `validate-parameters` elementu.  |  Yes     | –   |
| Neurčený parametr-Action | [Akce](#actions) , která se má provést pro parametry žádosti, které nejsou zadané ve schématu rozhraní API. <br/><br/>V případě, že je k dispozici v `headers` `query` prvku nebo, Přepisuje hodnota `unspecified-parameter-action` v `validate-parameters` elementu. |  Yes     | –   |
| chyby – název proměnné | Název proměnné v nástroji `context.Variables` pro chyby ověření protokolu na.  |   Yes    | –   |
| name | Název parametru pro přepsání akce ověření pro. Tato hodnota rozlišuje malá a velká písmena.  | Yes | – |
| akce | [Akce](#actions) , která má být provedena pro parametr se shodným názvem. Pokud je parametr zadaný ve schématu rozhraní API, přepíše tato hodnota konfiguraci vyšší úrovně `specified-parameter-action` . Pokud parametr není zadán ve schématu rozhraní API, tato hodnota přepisuje konfiguraci vyšší úrovně `unspecified-parameter-action` .| Yes | – | 

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** všechny rozsahy

## <a name="validate-headers"></a>Ověřit hlavičky

Tato `validate-headers` zásada ověřuje hlavičky odpovědí proti schématu rozhraní API.

> [!IMPORTANT]
> Pokud jste importovali rozhraní API pomocí rozhraní API pro správu před verzí `2021-01-01-preview` , `validate-headers` nemusí tato zásada fungovat. Možná budete muset znovu naimportovat rozhraní API pomocí rozhraní API pro správu `2021-01-01-preview` nebo novější verze.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>Příklad

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>Elementy

| Název         | Popis                                                                                                                                   | Povinné |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ověřit – hlavičky | Kořenový element. Určuje výchozí akce ověřování pro všechny hlavičky v odpovědích.                                                                                                                              | Yes      |
| header | Přidejte jeden nebo více prvků pro pojmenovaná záhlaví k přepsání výchozích ověřovacích akcí pro hlavičky v odpovědích. | No |

### <a name="attributes"></a>Atributy

| Název                       | Popis                                                                                                                                                            | Povinné | Výchozí |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| zadáno – hlavička – akce | [Akce](#actions) , která se má provést pro hlavičky odpovědí zadané ve schématu rozhraní API  |  Yes     | –   |
| Neurčeno – hlavička – akce | [Akce](#actions) , která se má provést pro hlavičky odpovědí, které nejsou zadány ve schématu rozhraní API.  |  Yes     | –   |
| chyby – název proměnné | Název proměnné v nástroji `context.Variables` pro chyby ověření protokolu na.  |   Yes    | –   |
| name | Název záhlaví, pro které se má přepsat akce ověření. Tato hodnota rozlišuje malá a velká písmena. | Yes | – |
| akce | [Akce](#actions) , která se má provést pro hlavičku se shodným názvem Pokud je hlavička určena ve schématu rozhraní API, tato hodnota Přepisuje hodnotu `specified-header-action` v `validate-headers` elementu. V opačném případě Přepisuje hodnotu `unspecified-header-action` v elementu Validate-Headers. | Yes | – | 

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** odchozí, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="validate-status-code"></a>Ověřit stavový kód

Tato `validate-status-code` zásada ověřuje stavové kódy HTTP v odpovědích proti schématu rozhraní API. Pomocí této zásady lze zabránit úniku chyb back-endu, které mohou obsahovat trasování zásobníku. 

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>Příklad

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>Elementy

| Název         | Popis                                                                                                                                   | Povinné |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ověřit – stav-kód | Kořenový element.                                                                                                | Yes      |
| Stavový kód | Přidejte jeden nebo více prvků pro stavový kód HTTP, abyste popsali výchozí akci ověření pro stavové kódy v odpovědích. | No |

### <a name="attributes"></a>Atributy

| Název                       | Popis                                                                                                                                                            | Povinné | Výchozí |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| Neurčeno – stav-kód-akce | [Akce](#actions) , která se má provést pro stavové kódy HTTP v odpovědích, které nejsou zadané ve schématu rozhraní API.  |  Yes     | –   |
| chyby – název proměnné | Název proměnné v nástroji `context.Variables` pro chyby ověření protokolu na.  |   Yes    | –   |
| kód | Stavový kód HTTP pro přepsání akce ověření pro. | Yes | – |
| akce | [Akce](#actions) , která se má provést pro shodný stavový kód, který není určený ve schématu rozhraní API. Pokud je stavový kód zadán ve schématu rozhraní API, toto přepsání se neprojeví. | Yes | – | 

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** odchozí, zapnutá chyba

-   **Obory zásad:** všechny rozsahy


## <a name="validation-errors"></a>Chyby ověřování
V následující tabulce jsou uvedeny všechny možné chyby zásad ověřování. 

* **Podrobnosti** – lze použít k prozkoumání chyb. Není určeno k veřejnému sdílení.
* **Veřejná odpověď** – Chyba vrácená klientovi. Nevrací detaily implementace.

| **Název**                             | **Typ**                                                        | **Ověřovací pravidlo** | **Podrobnosti**                                                                                                                                       | **Veřejná odpověď**                                                                                                                       | **Akce**           |
|----|----|---|---|---|----|
| **ověřit – obsah** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |Částmi                                                     | SizeLimit           | Tělo žádosti má {Size} bajtů dlouhé a překračuje nakonfigurovaný limit {maxSize} bajtů.                                                       | Tělo žádosti má {Size} bajtů dlouhé a překračuje limit {maxSize} bajtů.                                                          | detekovat/zakázat |
||ResponseBody                                                    | SizeLimit           | Tělo odpovědi má velikost {Size} bajtů a překračuje nakonfigurovaný limit {maxSize} bajtů.                                                      | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| {messageContentType}                 | Částmi                                                     | Unspecified         | Nespecifikovaný typ obsahu {messageContentType} není povolený.                                                                                     | Nespecifikovaný typ obsahu {messageContentType} není povolený.                                                                             | detekovat/zakázat |
| {messageContentType}                 | ResponseBody                                                    | Unspecified         | Nespecifikovaný typ obsahu {messageContentType} není povolený.                                                                                     | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| | ApiSchema                                                       |                     | Schéma rozhraní API neexistuje nebo ho nebylo možné přeložit.                                                                                          | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
|                                      | ApiSchema                                                       |                     | Schéma rozhraní API neurčuje definice.                                                                                                        | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| {messageContentType}                 | Částmi/ResponseBody                                      | MissingDefinition   | Schéma rozhraní API neobsahuje definici {definition}, která je přidružená k typu obsahu {messageContentType}.                        | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| {messageContentType}                 | Částmi                                                     | IncorrectMessage    | Tělo požadavku není v souladu s definicí {definition}, která je přidružená k typu obsahu {messageContentType}.<br/><br/>{valError. Message} řádek: {valError. číslo řádku}, pozice: {valError. LinePosition}                  | Tělo požadavku není v souladu s definicí {definition}, která je přidružená k typu obsahu {messageContentType}.<br/><br/>{valError. Message} řádek: {valError. číslo řádku}, pozice: {valError. LinePosition}            | detekovat/zakázat |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | Tělo odpovědi nevyhovuje definici {definition}, která je přidružena k typu obsahu {messageContentType}.<br/><br/>{valError. Message} řádek: {valError. číslo řádku}, pozice: {valError. LinePosition}                                       | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
|                                      | Částmi                                                     | ValidationException | Tělo požadavku nelze ověřit pro typ obsahu {messageContentType}.<br/><br/>{Detail výjimky}                                                                | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
|                                      | ResponseBody                                                    | ValidationException | Tělo odpovědi nelze ověřit pro typ obsahu {messageContentType}.<br/><br/>{Detail výjimky}                                                                | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| **Validate-Parameter/Validate-Headers** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName}/{Header}           | QueryParameter / PathParameter / RequestHeader                  | Unspecified         | Neurčený parametr {path/parametr/Header dotazu} {paramName} není povolený.                                                               | Neurčený parametr {path/parametr/Header dotazu} {paramName} není povolený.                                                       | detekovat/zakázat |
| {Header}                         | ResponseHeader                                                  | Unspecified         | Nespecifikovaná hlavička {Header} není povolená.                                                                                                   | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
|                                      |ApiSchema                                                       |                     | Schéma rozhraní API neexistuje nebo ho nejde přeložit.                                                                                            | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
|                                       | ApiSchema                                                       |                     | Schéma rozhraní API neurčuje definice.                                                                                                          | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| paramName                          | QueryParameter / PathParameter / RequestHeader / ResponseHeader | MissingDefinition   | Schéma rozhraní API neobsahuje definici {definition}, která je přidružená k parametru {query/parametr/Header cesty} {paramName}.  | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| paramName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Požadavek nemůže obsahovat více hodnot pro {parametr dotazu/parametr/Header cesty} {paramName}.                                           | Požadavek nemůže obsahovat více hodnot pro {parametr dotazu/parametr/Header cesty} {paramName}.                                   | detekovat/zakázat |
| {Header}                         | ResponseHeader                                                  | IncorrectMessage    | Odpověď nemůže obsahovat více hodnot záhlaví {Header}.                                                                              | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| paramName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Hodnota {parametru dotazu/parametru/Header cesty} {paramName} nevyhovuje definici.<br/><br/>{valError. Message} řádek: {valError. číslo řádku}, pozice: {valError. LinePosition}                                          | Hodnota {parametru dotazu/parametru/Header cesty} {paramName} nevyhovuje definici.<br/><br/>{valError. Message} řádek: {valError. číslo řádku}, pozice: {valError. LinePosition}                              | detekovat/zakázat |
| {Header}                         | ResponseHeader                                                  | IncorrectMessage    | Hodnota záhlaví {Header} není v souladu s definicí.<br/><br/>{valError. Message} řádek: {valError. číslo řádku}, pozice: {valError. LinePosition}                                                                              | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| paramName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Hodnotu {parametr dotazu/parametr/Header cesty} {paramName} nelze analyzovat podle definice. <br/><br/>dodatečné. Zpráva                                | Hodnotu {parametr dotazu/parametr/Header cesty} {paramName} se nepovedlo analyzovat podle definice. <br/><br/>dodatečné. Zpráva                      | detekovat/zakázat |
| {Header}                         | ResponseHeader                                                  | IncorrectMessage    | Hodnotu záhlaví {Header} nebylo možné analyzovat podle definice.                                                                  | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| paramName                          | QueryParameter / PathParameter / RequestHeader                  | ValidationError     | {Parametr dotazu/parametr/Header cesty} {paramName} nelze ověřit.<br/><br/>{Detail výjimky}                                                                      | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| {Header}                         | ResponseHeader                                                  | ValidationError     | Záhlaví {Header} nelze ověřit.<br/><br/>{Detail výjimky}                                                                                                          | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |
| **ověřit – stav-kód**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {Status-Code}                        | StatusCode                                                      | Unspecified         | Stavový kód odpovědi {Status-Code} není povolen.                                                                                                | Požadavek nebylo možné zpracovat z důvodu vnitřní chyby. Obraťte se na vlastníka rozhraní API.                                                       | detekovat/zakázat |


V následující tabulce jsou uvedeny všechny možné hodnoty důvodu chyby ověřování spolu s možnými hodnotami zpráv:

|  **Důvod**         |    **Zpráva** |
|---|---|  
| Chybný požadavek       |     {Details} pro kontextovou proměnnou, {Public Response} pro klienta|
| Odpověď není povolena.  | {Details} pro kontextovou proměnnou, {Public Response} pro klienta |






## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

-   [Zásady v API Management](api-management-howto-policies.md)
-   [Transformační rozhraní API](transform-api.md)
-   [Odkaz na zásady](./api-management-policies.md) pro úplný seznam příkazů zásad a jejich nastavení
-   [Ukázky zásad](./policy-reference.md)
-   [Zpracování chyb](./api-management-error-handling-policies.md)
