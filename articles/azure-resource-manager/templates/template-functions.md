---
title: Funkce šablon
description: Popisuje funkce, které se mají použít v šabloně Azure Resource Manager k načtení hodnot, práci s řetězci a číselnými kódy a načtení informací o nasazení.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: fbd82f89ed9a97a3f376a9ed6eaa8ae3760759ff
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982374"
---
# <a name="arm-template-functions"></a>Funkce šablony ARM

Tento článek popisuje všechny funkce, které můžete použít v šabloně Azure Resource Manager (ARM). Informace o používání funkcí v šabloně naleznete v [tématu syntaxe šablony](template-expressions.md).

Chcete-li vytvořit vlastní funkce, viz [Uživatelem definované funkce](template-syntax.md#functions).

Většina funkcí funguje stejně při nasazení do skupiny prostředků, předplatného, skupiny pro správu nebo klienta. Několik funkcí nelze použít ve všech oborech. Jsou uvedeny v níže uvedených seznamech.

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Funkce pole a objektu

Správce prostředků poskytuje několik funkcí pro práci s poli a objekty.

* [Pole](template-functions-array.md#array)
* [Coalesce](template-functions-array.md#coalesce)
* [Concat](template-functions-array.md#concat)
* [Obsahuje](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [empty](template-functions-array.md#empty)
* [První](template-functions-array.md#first)
* [Průsečíku](template-functions-array.md#intersection)
* [Json](template-functions-array.md#json)
* [Poslední](template-functions-array.md#last)
* [Délka](template-functions-array.md#length)
* [Min](template-functions-array.md#min)
* [Max](template-functions-array.md#max)
* [Rozsah](template-functions-array.md#range)
* [Přeskočit](template-functions-array.md#skip)
* [vzít](template-functions-array.md#take)
* [Unie](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Funkce porovnání

Resource Manager poskytuje několik funkcí pro porovnávání v šablonách.

* [equals](template-functions-comparison.md#equals)
* [less](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>Datové funkce

Správce prostředků poskytuje následující funkce pro práci s daty.

* [dateTimeAdd](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Funkce hodnoty nasazení

Správce prostředků poskytuje následující funkce pro získávání hodnot z částí šablony a hodnot souvisejících s nasazením:

* [Nasazení](template-functions-deployment.md#deployment)
* [Prostředí](template-functions-deployment.md#environment)
* [Parametry](template-functions-deployment.md#parameters)
* [Proměnné](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Logické funkce

Správce prostředků poskytuje následující funkce pro práci s logickými podmínkami:

* [A](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [Ne](template-functions-logical.md#not)
* [Nebo](template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Numerické funkce

Správce prostředků poskytuje následující funkce pro práci s celáčísla:

* [add](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [Min](template-functions-numeric.md#min)
* [Max](template-functions-numeric.md#max)
* [Mod](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [Dílčí](template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Funkce prostředků

Správce prostředků poskytuje následující funkce pro získávání hodnot prostředků:

* [rozšířeníResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [seznam kláves](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [seznam*](template-functions-resource.md#list)
* [Poskytovatelů](template-functions-resource.md#providers)
* [Odkaz](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) - lze použít pouze v nasazení ch a) do skupiny prostředků.
* [resourceId](template-functions-resource.md#resourceid) - lze použít v libovolném oboru, ale platné parametry se mění v závislosti na oboru.
* [předplatné](template-functions-resource.md#subscription) – lze použít pouze v nasazení do skupiny prostředků nebo předplatného.
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Funkce řetězců

Správce prostředků poskytuje následující funkce pro práci s řetězci:

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [Obsahuje](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [empty](template-functions-string.md#empty)
* [Endswith](template-functions-string.md#endswith)
* [První](template-functions-string.md#first)
* [Formát](template-functions-string.md#format)
* [Identifikátor guid](template-functions-string.md#guid)
* [Indexof](template-functions-string.md#indexof)
* [Poslední](template-functions-string.md#last)
* [Lastindexof](template-functions-string.md#lastindexof)
* [Délka](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [oložek vlevo](template-functions-string.md#padleft)
* [Nahradit](template-functions-string.md#replace)
* [Přeskočit](template-functions-string.md#skip)
* [Rozdělit](template-functions-string.md#split)
* [Startswith](template-functions-string.md#startswith)
* [Řetězec](template-functions-string.md#string)
* [Podřetězec](template-functions-string.md#substring)
* [vzít](template-functions-string.md#take)
* [Tolower](template-functions-string.md#tolower)
* [Toupper](template-functions-string.md#toupper)
* [Trim](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [Uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Další kroky

* Popis oddílů v šabloně ARM naleznete v tématu [Authoring ARM templates](template-syntax.md)
* Pokud chcete sloučit víc šablon, přečtěte si informace [o použití propojených šablon pomocí Azure Resource Manageru.](linked-templates.md)
* Chcete-li itrerate zadaný počet časů při vytváření typu prostředku, najdete v [tématu vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).
* Postup nasazení vytvořené šablony najdete v [tématu Nasazení aplikace pomocí šablon ARM.](deploy-powershell.md)
