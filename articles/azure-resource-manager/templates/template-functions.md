---
title: Funkce šablon
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager k načtení hodnot, práci s řetězci a čísly a načtení informací o nasazení.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c78e9481e6f2ba1f17389992aab2712719fc4c3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378402"
---
# <a name="arm-template-functions"></a>Funkce šablon ARM

Tento článek popisuje všechny funkce, které můžete použít v šabloně Azure Resource Manager (ARM). Informace o používání funkcí v šabloně najdete v tématu [syntaxe šablony](template-expressions.md).

Chcete-li vytvořit vlastní funkce, přečtěte si téma [uživatelsky definované funkce](template-syntax.md#functions).

Většina funkcí funguje při nasazení do skupiny prostředků, předplatného, skupiny pro správu nebo tenanta stejné. Ve všech oborech nelze použít několik funkcí. Jsou uvedeny v níže uvedených seznamech.

<a id="array" aria-hidden="true"></a>
<a id="concatarray" aria-hidden="true"></a>
<a id="contains" aria-hidden="true"></a>
<a id="createarray" aria-hidden="true"></a>
<a id="empty" aria-hidden="true"></a>
<a id="first" aria-hidden="true"></a>
<a id="intersection" aria-hidden="true"></a>
<a id="last" aria-hidden="true"></a>
<a id="length" aria-hidden="true"></a>
<a id="min" aria-hidden="true"></a>
<a id="max" aria-hidden="true"></a>
<a id="range" aria-hidden="true"></a>
<a id="skip" aria-hidden="true"></a>
<a id="take" aria-hidden="true"></a>
<a id="union" aria-hidden="true"></a>

## <a name="array-functions"></a>Funkce pro práci s poli

Správce prostředků poskytuje několik funkcí pro práci s poli.

* [array](template-functions-array.md#array)
* [spojuje](template-functions-array.md#concat)
* [zobrazí](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [empty](template-functions-array.md#empty)
* [první](template-functions-array.md#first)
* [průnik](template-functions-array.md#intersection)
* [posledního](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [min](template-functions-array.md#min)
* [počet](template-functions-array.md#max)
* [oblasti](template-functions-array.md#range)
* [přímo](template-functions-array.md#skip)
* [take](template-functions-array.md#take)
* [sjednocovací](template-functions-array.md#union)

<a id="coalesce" aria-hidden="true"></a>
<a id="equals" aria-hidden="true"></a>
<a id="less" aria-hidden="true"></a>
<a id="lessorequals" aria-hidden="true"></a>
<a id="greater" aria-hidden="true"></a>
<a id="greaterorequals" aria-hidden="true"></a>

## <a name="comparison-functions"></a>Funkce porovnání

Správce prostředků poskytuje několik funkcí pro porovnávání v šablonách.

* [COALESCE](template-functions-comparison.md#coalesce)
* [rovná se](template-functions-comparison.md#equals)
* [tolik](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true"></a>
<a id="parameters" aria-hidden="true"></a>
<a id="variables" aria-hidden="true"></a>

## <a name="date-functions"></a>Datové funkce

Správce prostředků poskytuje následující funkce pro práci s daty.

* [dateTimeAdd](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Funkce hodnoty nasazení

Správce prostředků poskytuje následující funkce pro získání hodnot z částí šablony a hodnot souvisejících s nasazením:

* [prostředí](template-functions-deployment.md#deployment)
* [environment (prostředí)](template-functions-deployment.md#environment)
* [ukazatelů](template-functions-deployment.md#parameters)
* [proměnné](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true"></a>
<a id="bool" aria-hidden="true"></a>
<a id="if" aria-hidden="true"></a>
<a id="not" aria-hidden="true"></a>
<a id="or" aria-hidden="true"></a>

## <a name="logical-functions"></a>Logické funkce

Správce prostředků poskytuje následující funkce pro práci s logickými podmínkami:

* [and](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [mění](template-functions-logical.md#not)
* [nebo](template-functions-logical.md#or)

<a id="add" aria-hidden="true"></a>
<a id="copyindex" aria-hidden="true"></a>
<a id="div" aria-hidden="true"></a>
<a id="float" aria-hidden="true"></a>
<a id="int" aria-hidden="true"></a>
<a id="minint" aria-hidden="true"></a>
<a id="maxint" aria-hidden="true"></a>
<a id="mod" aria-hidden="true"></a>
<a id="mul" aria-hidden="true"></a>
<a id="sub" aria-hidden="true"></a>

## <a name="numeric-functions"></a>Numerické funkce

Správce prostředků poskytuje následující funkce pro práci s celými čísly:

* [add](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [min](template-functions-numeric.md#min)
* [počet](template-functions-numeric.md#max)
* [střední](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [jednotk](template-functions-numeric.md#sub)

<a id="json" aria-hidden="true"></a>

## <a name="object-functions"></a>Objektové funkce

Správce prostředků poskytuje několik funkcí pro práci s objekty.

* [zobrazí](template-functions-object.md#contains)
* [empty](template-functions-object.md#empty)
* [průnik](template-functions-object.md#intersection)
* [JSON](template-functions-object.md#json)
* [length](template-functions-object.md#length)
* [sjednocovací](template-functions-object.md#union)

<a id="extensionResourceId" aria-hidden="true"></a>
<a id="listkeys" aria-hidden="true"></a>
<a id="list" aria-hidden="true"></a>
<a id="providers" aria-hidden="true"></a>
<a id="reference" aria-hidden="true"></a>
<a id="resourcegroup" aria-hidden="true"></a>
<a id="resourceid" aria-hidden="true"></a>
<a id="subscription" aria-hidden="true"></a>
<a id="subscriptionResourceId" aria-hidden="true"></a>
<a id="tenantResourceId" aria-hidden="true"></a>

## <a name="resource-functions"></a>Funkce prostředků

Správce prostředků poskytuje následující funkce pro získání hodnot prostředků:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [Klíče listkey](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [seznamu](template-functions-resource.md#list)
* [pickZones](template-functions-resource.md#pickzones)
* [dodavateli](template-functions-resource.md#providers)
* [odkaz](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) skupina prostředků se dá použít jenom v nasazeních do skupiny prostředků.
* [ResourceID](template-functions-resource.md#resourceid) -lze použít v jakémkoli oboru, ale platné parametry se mění v závislosti na rozsahu.
* [předplatné](template-functions-resource.md#subscription) – dá se použít jenom v nasazeních do skupiny prostředků nebo předplatného.
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true"></a>
<a id="base64tojson" aria-hidden="true"></a>
<a id="base64tostring" aria-hidden="true"></a>
<a id="concat" aria-hidden="true"></a>
<a id="containsstring" aria-hidden="true"></a>
<a id="datauri" aria-hidden="true"></a>
<a id="datauritostring" aria-hidden="true"></a>
<a id="emptystring" aria-hidden="true"></a>
<a id="endswith" aria-hidden="true"></a>
<a id="firststring" aria-hidden="true"></a>
<a id="guid" aria-hidden="true"></a>
<a id="indexof" aria-hidden="true"></a>
<a id="laststring" aria-hidden="true"></a>
<a id="lastindexof" aria-hidden="true"></a>
<a id="lengthstring" aria-hidden="true"></a>
<a id="padleft" aria-hidden="true"></a>
<a id="replace" aria-hidden="true"></a>
<a id="skipstring" aria-hidden="true"></a>
<a id="split" aria-hidden="true"></a>
<a id="startswith" aria-hidden="true"></a>
<a id="string" aria-hidden="true"></a>
<a id="substring" aria-hidden="true"></a>
<a id="takestring" aria-hidden="true"></a>
<a id="tolower" aria-hidden="true"></a>
<a id="toupper" aria-hidden="true"></a>
<a id="trim" aria-hidden="true"></a>
<a id="uniquestring" aria-hidden="true"></a>
<a id="uri" aria-hidden="true"></a>
<a id="uricomponent" aria-hidden="true"></a>
<a id="uricomponenttostring" aria-hidden="true"></a>

## <a name="string-functions"></a>Funkce řetězců

Správce prostředků poskytuje následující funkce pro práci s řetězci:

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [spojuje](template-functions-string.md#concat)
* [zobrazí](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [empty](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [první](template-functions-string.md#first)
* [formátovat](template-functions-string.md#format)
* [guid](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [posledního](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [náhrady](template-functions-string.md#replace)
* [přímo](template-functions-string.md#skip)
* [rozdělení](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [řetezce](template-functions-string.md#string)
* [podřetězec](template-functions-string.md#substring)
* [take](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [sklon](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [identifikátor URI](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně ARM najdete v tématu [vytváření šablon ARM](template-syntax.md) .
* Chcete-li sloučit více šablon, přečtěte si téma [použití propojených šablon s Azure Resource Manager](linked-templates.md)
* Informace o iteraci zadaného počtu výskytů při vytváření typu prostředku najdete v tématu [vytvoření více instancí prostředků v Azure Resource Manager](copy-resources.md).
* Pokud chcete zjistit, jak nasadit šablonu, kterou jste vytvořili, přečtěte si téma [nasazení aplikace pomocí šablon ARM](deploy-powershell.md) .
