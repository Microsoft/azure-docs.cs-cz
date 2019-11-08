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
ms.topic: article
ms.date: 11/05/2019
ms.author: apimpm
ms.openlocfilehash: d11239aa49a53a90a38f2b5336d36cea6c97e9df
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824162"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Jak používat pojmenované hodnoty v zásadách Azure API Management

Zásady API Management představují výkonnou schopnost systému, která umožňuje Azure Portal změnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které se postupně provádí na základě požadavku nebo odezvy z rozhraní API. Příkazy zásad lze vytvořit pomocí textových hodnot literálů, výrazů zásad a pojmenovaných hodnot.

Každá instance API Management služby má kolekci vlastností párů klíč/hodnota, které se nazývají pojmenované hodnoty, které jsou globální pro instanci služby. Počet položek v kolekci není nijak omezen. Pojmenované hodnoty se dají použít ke správě konstantních řetězcových hodnot napříč všemi konfiguracemi a zásadami rozhraní API. Každá pojmenovaná hodnota může mít následující atributy:

| Atribut      | Typ            | Popis                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | řetězec          | Používá se pro odkazování pojmenované hodnoty v zásadách. Řetězec o 1 až 256 znaků. Jsou povoleny pouze písmena, číslice, tečky a spojovníky. |
| `Value`        | řetězec          | Skutečná hodnota. Nesmí být prázdný nebo obsahovat pouze prázdné znaky. Maximálně 4096 znaků.                                     |
| `Secret`       | Boolean         | Určuje, zda je hodnota tajná a měla by být zašifrovaná.                                                            |
| `Tags`         | pole řetězce | Slouží k filtrování seznamu pojmenovaných hodnot. Až 32 značek.                                                                                    |

![Pojmenované hodnoty](./media/api-management-howto-properties/named-values.png)

Pojmenované hodnoty mohou obsahovat řetězce literálů a [výrazy zásad](/azure/api-management/api-management-policy-expressions). Například hodnota `Expression` je výraz zásady, který vrací řetězec obsahující aktuální datum a čas. Pojmenovaná hodnota `Credential` je označena jako tajná, takže její hodnota se ve výchozím nastavení nezobrazí.

| Name (Název)       | Hodnota                      | Tajný kód | Značky          |
| ---------- | -------------------------- | ------ | ------------- |
| Hodnota      | 42                         | False  | důležitá – čísla |
| Přihlašovací údaj | ••••••••••••••••••••••     | True   | security      |
| Výraz | @ (DateTime. Now. ToString ()) | False  |               |

## <a name="to-add-and-edit-a-named-value"></a>Přidání a úprava pojmenované hodnoty

![Přidat pojmenovanou hodnotu](./media/api-management-howto-properties/add-property.png)

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. Vyberte **pojmenované hodnoty**.
3. Stiskněte **+ Přidat**.

    Název a hodnota jsou požadované hodnoty. Pokud je hodnota tajná, zaškrtněte políčko *Toto je tajné* . Zadejte jednu nebo více volitelných značek, které vám pomůžou s uspořádáním pojmenovaných hodnot, a klikněte na Uložit.

4. Klikněte na možnost **Vytvořit**.

Jakmile je pojmenovaná hodnota vytvořena, můžete ji upravit kliknutím na ni. Změníte-li název pojmenované hodnoty, budou automaticky aktualizovány všechny zásady, které odkazují na tuto pojmenovanou hodnotu, aby používaly nový název.

Informace o úpravě pojmenované hodnoty pomocí REST API najdete v tématu [Úprava pojmenované hodnoty pomocí REST API](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-named-value"></a>Odstranění pojmenované hodnoty

Pojmenovanou hodnotu odstraníte tak, že kliknete na **Odstranit** vedle pojmenované hodnoty, kterou chcete odstranit.

> [!IMPORTANT]
> Pokud je pojmenovaná hodnota odkazována pomocí jakýchkoli zásad, nebudete ji moci úspěšně odstranit, dokud neodeberete pojmenovanou hodnotu ze všech zásad, které ji používají.

Informace o odstranění pojmenované hodnoty pomocí REST API naleznete v tématu [odstranění pojmenované hodnoty pomocí REST API](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Hledání a filtrování pojmenovaných hodnot

Karta **pojmenované hodnoty** obsahuje možnosti vyhledávání a filtrování, které vám pomůžou se správou pojmenovaných hodnot. Chcete-li filtrovat seznam pojmenovaných hodnot podle názvu, zadejte hledaný termín do textového pole **Vlastnosti hledání** . Chcete-li zobrazit všechny pojmenované hodnoty, zrušte zaškrtnutí pole **Vlastnosti hledání** a stiskněte klávesu ENTER.

Chcete-li filtrovat seznam podle značky, zadejte do textového pole **filtrovat podle značek** jednu nebo více značek. Chcete-li zobrazit všechny pojmenované hodnoty, zrušte zaškrtnutí políčka **filtrovat podle značek** a stiskněte klávesu ENTER.

## <a name="to-use-a-named-value"></a>Použití pojmenované hodnoty

Chcete-li použít pojmenovanou hodnotu v zásadě, umístěte svůj název do dvojice Double složené závorky, jako je například `{{ContosoHeader}}`, jak je znázorněno v následujícím příkladu:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

V tomto příkladu se `ContosoHeader` používá jako název hlavičky v zásadách `set-header` a `ContosoHeaderValue` se používá jako hodnota této hlavičky. Pokud je tato zásada vyhodnocena během žádosti nebo odpovědi na bránu API Management, `{{ContosoHeader}}` a `{{ContosoHeaderValue}}` nahrazují jejich příslušné hodnoty.

Pojmenované hodnoty lze použít jako úplný atribut nebo hodnoty prvků, jak je znázorněno v předchozím příkladu, ale mohou být také vloženy do nebo kombinovány s částí textového výrazu literálu, jak je znázorněno v následujícím příkladu: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Pojmenované hodnoty mohou obsahovat také výrazy zásad. V následujícím příkladu je použita `ExpressionProperty`.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Když se tato zásada vyhodnotí, `{{ExpressionProperty}}` se nahradí její hodnotou: `@(DateTime.Now.ToString())`. Vzhledem k tomu, že hodnota je výrazem zásady, vyhodnocuje se výraz a zásada se provede s jeho spuštěním.

Tuto možnost můžete vyzkoušet na portálu pro vývojáře voláním operace, která má zásadu s pojmenovanými hodnotami v oboru. V následujícím příkladu je volána operace se dvěma předchozími příklady `set-header` zásad s pojmenovanými hodnotami. Všimněte si, že odpověď obsahuje dvě vlastní hlavičky, které byly nakonfigurovány pomocí zásad s pojmenovanými hodnotami.

![Portál pro vývojáře][api-management-send-results]

Pokud se podíváte na [trasování rozhraní API](api-management-howto-api-inspector.md) pro volání, které obsahuje dvě předchozí ukázkové zásady s pojmenovanými hodnotami, uvidíte dvě `set-header` zásady s vloženými pojmenovanými hodnotami i vyhodnocením výrazu zásady pro pojmenovanou hodnotu. byl obsažen výraz zásad.

![Trasování v inspektoru API][api-management-api-inspector-trace]

Pojmenované hodnoty můžou obsahovat výrazy zásad, ale nemůžou obsahovat jiné pojmenované hodnoty. Pokud se text obsahující odkaz s názvem hodnoty používá pro hodnotu, například `Text: {{MyProperty}}`, tento odkaz nebude vyřešen a nahrazen.

## <a name="next-steps"></a>Další kroky

-   Další informace o práci se zásadami
    -   [Zásady v API Management](api-management-howto-policies.md)
    -   [referenční příručce o zásadách](/azure/api-management/api-management-policies)
    -   [Výrazy zásad](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
