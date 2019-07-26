---
title: Jak používat pojmenované hodnoty v zásadách Azure API Management
description: Naučte se používat pojmenované hodnoty v zásadách Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/22/2019
ms.author: apimpm
ms.openlocfilehash: 46f4e1b3df5f1c77a57d432297685d6d1a0a14a8
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405799"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Jak používat pojmenované hodnoty v zásadách Azure API Management

Zásady API Management představují výkonnou schopnost systému, která umožňuje Azure Portal změnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které se postupně provádí na základě požadavku nebo odezvy z rozhraní API. Příkazy zásad lze vytvořit pomocí textových hodnot literálů, výrazů zásad a pojmenovaných hodnot.

Každá instance API Management služby má kolekci vlastností párů klíč/hodnota, které se nazývají pojmenované hodnoty, které jsou globální pro instanci služby. Počet položek v kolekci není nijak omezen. Pojmenované hodnoty se dají použít ke správě konstantních řetězcových hodnot napříč všemi konfiguracemi a zásadami rozhraní API. Každá pojmenovaná hodnota může mít následující atributy:

| Atribut      | type            | Popis                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | řetězec          | Používá se pro odkazování na vlastnost v zásadách. Řetězec o 1 až 256 znaků. Jsou povoleny pouze písmena, číslice, tečky a spojovníky. |
| `Value`        | řetězec          | Skutečná hodnota. Nesmí být prázdný nebo obsahovat pouze prázdné znaky. Maximálně 4096 znaků.                                     |
| `Secret`       | boolean         | Určuje, zda je hodnota tajná a měla by být zašifrovaná.                                                            |
| `Tags`         | pole řetězce | Slouží k filtrování seznamu vlastností. Až 32 značek.                                                                                    |

![Pojmenované hodnoty](./media/api-management-howto-properties/named-values.png)

Pojmenované hodnoty mohou obsahovat řetězce literálů a [výrazy zásad](/azure/api-management/api-management-policy-expressions). Například hodnota `Expression` je výraz zásady, který vrací řetězec obsahující aktuální datum a čas. Pojmenovaná `Credential` hodnota je označena jako tajná, takže její hodnota se ve výchozím nastavení nezobrazí.

| Name       | Value                      | Secret | Tags          |
| ---------- | -------------------------- | ------ | ------------- |
| Value      | 42                         | False  | důležitá – čísla |
| Pověření | ••••••••••••••••••••••     | Pravda   | zabezpečení      |
| Výraz | @(DateTime.Now.ToString()) | False  |               |

## <a name="to-add-and-edit-a-property"></a>Přidání a úprava vlastnosti

![Přidat vlastnost](./media/api-management-howto-properties/add-property.png)

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. Vyberte **pojmenované hodnoty**.
3. Stiskněte **+ Přidat**.

    Název a hodnota jsou požadované hodnoty. Pokud je tato vlastnost tajná, zaškrtněte políčko Toto je tajné. Zadejte jednu nebo více volitelných značek, které vám pomůžou s uspořádáním pojmenovaných hodnot, a klikněte na Uložit.

4. Klikněte na možnost **Vytvořit**.

Jakmile je vlastnost vytvořena, můžete ji upravit kliknutím na vlastnost. Pokud změníte název vlastnosti, všechny zásady, které na tuto vlastnost odkazují, se automaticky aktualizují, aby používaly nový název.

Informace o úpravě vlastnosti pomocí REST API najdete v tématu [Úprava vlastnosti pomocí REST API](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-property"></a>Odstranění vlastnosti

Chcete-li odstranit vlastnost, klikněte na tlačítko **Odstranit** vedle vlastnosti, kterou chcete odstranit.

> [!IMPORTANT]
> Pokud je na vlastnost odkazováno pomocí všech zásad, nebudete ji moci úspěšně odstranit, dokud neodeberete vlastnost ze všech zásad, které ji používají.

Informace o odstranění vlastnosti pomocí REST API naleznete v tématu [odstranění vlastnosti pomocí REST API](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Hledání a filtrování pojmenovaných hodnot

Karta **pojmenované hodnoty** obsahuje možnosti vyhledávání a filtrování, které vám pomůžou se správou pojmenovaných hodnot. Chcete-li filtrovat seznam vlastností podle názvu vlastnosti, zadejte hledaný termín do textového pole **Vlastnosti hledání** . Chcete-li zobrazit všechny pojmenované hodnoty, zrušte zaškrtnutí pole **Vlastnosti hledání** a stiskněte klávesu ENTER.

Chcete-li filtrovat seznam vlastností podle hodnot značek, zadejte do textového pole **filtrovat podle značek** jednu nebo více značek. Chcete-li zobrazit všechny pojmenované hodnoty, zrušte zaškrtnutí políčka **filtrovat podle značek** a stiskněte klávesu ENTER.

## <a name="to-use-a-property"></a>Použití vlastnosti

Chcete-li použít vlastnost v zásadě, umístěte název vlastnosti do dvojice dvojitých závorek jako `{{ContosoHeader}}`, jak je znázorněno v následujícím příkladu:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

V tomto příkladu `ContosoHeader` se používá jako název záhlaví `set-header` v zásadě a `ContosoHeaderValue` používá se jako hodnota této hlavičky. Pokud je tato zásada vyhodnocena během žádosti nebo odpovědi na bránu `{{ContosoHeader}}` API Management a `{{ContosoHeaderValue}}` jsou nahrazeny odpovídajícími hodnotami vlastností.

Pojmenované hodnoty lze použít jako úplný atribut nebo hodnoty prvků, jak je znázorněno v předchozím příkladu, ale mohou být také vloženy do nebo kombinovány s částí textového výrazu literálu, jak je znázorněno v následujícím příkladu:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Pojmenované hodnoty mohou obsahovat také výrazy zásad. V následujícím příkladu `ExpressionProperty` je použit.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Když se tato zásada vyhodnotí, `{{ExpressionProperty}}` nahradí se její hodnotou:. `@(DateTime.Now.ToString())` Vzhledem k tomu, že hodnota je výrazem zásady, vyhodnocuje se výraz a zásada se provede s jeho spuštěním.

Tuto možnost můžete vyzkoušet na portálu pro vývojáře voláním operace, která má zásadu s pojmenovanými hodnotami v oboru. V následujícím příkladu je operace volána s použitím dvou předchozích ukázkových `set-header` zásad s pojmenovanými hodnotami. Všimněte si, že odpověď obsahuje dvě vlastní hlavičky, které byly nakonfigurovány pomocí zásad s pojmenovanými hodnotami.

![Vývojářský portál][api-management-send-results]

Pokud se podíváte na [trasování rozhraní API](api-management-howto-api-inspector.md) pro volání, které obsahuje dvě předchozí ukázkové zásady s pojmenovanými hodnotami, můžete zobrazit dvě `set-header` zásady s vloženými hodnotami vlastností a vyhodnocením výrazu zásady pro vlastnost, která byl obsažen výraz zásad.

![Trasování v inspektoru API][api-management-api-inspector-trace]

I když hodnoty vlastností mohou obsahovat výrazy zásad, hodnoty vlastností nemohou obsahovat jiné pojmenované hodnoty. Pokud se text obsahující odkaz na vlastnost používá pro hodnotu vlastnosti, například `Property value text {{MyProperty}}`, tento odkaz na vlastnost nebude nahrazen a bude zahrnut jako součást hodnoty vlastnosti.

## <a name="next-steps"></a>Další postup

-   Další informace o práci se zásadami
    -   [Zásady v API Management](api-management-howto-policies.md)
    -   [referenční příručce o zásadách](/azure/api-management/api-management-policies)
    -   [Výrazy zásad](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
