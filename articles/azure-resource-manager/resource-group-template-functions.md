---
title: Funkce šablon Správce prostředků | Microsoft Docs
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager k načtení hodnot, práci s řetězci a čísly a načtení informací o nasazení.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: 04358f21af79838f894dc50d5d9c165da67a332c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894166"
---
# <a name="azure-resource-manager-template-functions"></a>Azure Resource Manager funkce šablon

Tento článek popisuje všechny funkce, které můžete použít v šabloně Azure Resource Manager. Informace o používání funkcí v šabloně najdete v tématu [syntaxe šablony](template-expressions.md).

Chcete-li vytvořit vlastní funkce, přečtěte si téma [uživatelsky definované funkce](resource-group-authoring-templates.md#functions).

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

* [skupin](resource-group-template-functions-array.md#array)
* [COALESCE](resource-group-template-functions-array.md#coalesce)
* [spojuje](resource-group-template-functions-array.md#concat)
* [zobrazí](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [obsahovat](resource-group-template-functions-array.md#empty)
* [první](resource-group-template-functions-array.md#first)
* [průnik](resource-group-template-functions-array.md#intersection)
* [JSON](resource-group-template-functions-array.md#json)
* [posledního](resource-group-template-functions-array.md#last)
* [časový](resource-group-template-functions-array.md#length)
* [dlouhé](resource-group-template-functions-array.md#min)
* [počet](resource-group-template-functions-array.md#max)
* [oblasti](resource-group-template-functions-array.md#range)
* [přímo](resource-group-template-functions-array.md#skip)
* [nezbytná](resource-group-template-functions-array.md#take)
* [sjednocovací](resource-group-template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Funkce porovnání
Správce prostředků poskytuje několik funkcí pro porovnávání v šablonách.

* [rovná](resource-group-template-functions-comparison.md#equals)
* [tolik](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [zvýšen](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Funkce hodnoty nasazení
Správce prostředků poskytuje následující funkce pro získání hodnot z částí šablony a hodnot souvisejících s nasazením:

* [prostředí](resource-group-template-functions-deployment.md#deployment)
* [parameters](resource-group-template-functions-deployment.md#parameters)
* [proměnné](resource-group-template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Logické funkce
Správce prostředků poskytuje následující funkce pro práci s logickými podmínkami:

* [ani](resource-group-template-functions-logical.md#and)
* [logick](resource-group-template-functions-logical.md#bool)
* [Přestože](resource-group-template-functions-logical.md#if)
* [mění](resource-group-template-functions-logical.md#not)
* [ani](resource-group-template-functions-logical.md#or)

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
Správce prostředků poskytuje následující funkce pro práci s celými čísly:

* [přidávání](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [značek](resource-group-template-functions-numeric.md#div)
* [Plovák](resource-group-template-functions-numeric.md#float)
* [hmot](resource-group-template-functions-numeric.md#int)
* [dlouhé](resource-group-template-functions-numeric.md#min)
* [počet](resource-group-template-functions-numeric.md#max)
* [střední](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [jednotk](resource-group-template-functions-numeric.md#sub)

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
Správce prostředků poskytuje následující funkce pro získání hodnot prostředků:

* [extensionResourceId](resource-group-template-functions-resource.md#extensionresourceid)
* [listAccountSas](resource-group-template-functions-resource.md#list)
* [Klíče listkey](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [seznamu](resource-group-template-functions-resource.md#list)
* [dodavateli](resource-group-template-functions-resource.md#providers)
* [odkaz](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [Prostředku](resource-group-template-functions-resource.md#resourceid)
* [předplatné](resource-group-template-functions-resource.md#subscription)
* [subscriptionResourceId](resource-group-template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](resource-group-template-functions-resource.md#tenantresourceid)

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

* [Base](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [spojuje](resource-group-template-functions-string.md#concat)
* [zobrazí](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [obsahovat](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [první](resource-group-template-functions-string.md#first)
* [formátovat](resource-group-template-functions-string.md#format)
* [hlavních](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [posledního](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [časový](resource-group-template-functions-string.md#length)
* [newGuid](resource-group-template-functions-string.md#newguid)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [náhrady](resource-group-template-functions-string.md#replace)
* [přímo](resource-group-template-functions-string.md#skip)
* [split](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [řetezce](resource-group-template-functions-string.md#string)
* [podřetězec](resource-group-template-functions-string.md#substring)
* [nezbytná](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [sklon](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [identifikátor URI](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)
* [utcNow](resource-group-template-functions-string.md#utcnow)

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně Azure Resource Manager najdete v tématu [vytváření šablon Azure Resource Manager](resource-group-authoring-templates.md) .
* Chcete-li sloučit více šablon, přečtěte si téma [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md)
* Informace o iteraci zadaného počtu výskytů při vytváření typu prostředku najdete v tématu [vytvoření více instancí prostředků v Azure Resource Manager](resource-group-create-multiple.md)
* Pokud chcete zjistit, jak nasadit šablonu, kterou jste vytvořili, přečtěte si téma [nasazení aplikace pomocí šablony Azure Resource Manager](resource-group-template-deploy.md) .
