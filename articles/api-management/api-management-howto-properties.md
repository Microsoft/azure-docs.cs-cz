---
title: Použití vlastností v zásadách Azure API Management
description: Další informace o použití vlastnosti zásad Azure API Management.
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
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: e0559380f6d686a4e559779c4271ea85106558d6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28197108"
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Použití vlastností v zásadách Azure API Management
Zásady služby API Management jsou vynikající funkcí systému, který povolí portálu Azure můžete změnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které se postupně provádí na základě požadavku nebo odezvy z rozhraní API. Příkazy zásad se dá vytvořit pomocí literálu textové hodnoty, výrazy zásad a vlastnosti. 

Každá instance služby API Management má vlastnosti kolekci dvojic klíč/hodnota, které jsou globální v instanci služby. Tyto vlastnosti můžete použít ke správě konstantní hodnoty řetězce ve všech konfigurací rozhraní API a zásady. Každá vlastnost může mít následující atributy:

| Atribut | Typ | Popis |
| --- | --- | --- |
| Zobrazované jméno |řetězec |Alfanumerický řetězec, který se používá jako odkaz na vlastnost v zásadách |
| Hodnota |řetězec |Hodnota vlastnosti Se nesmí být prázdný a skládat jenom z prázdných znaků. |
|Tajný kód|Boolean|Určuje, zda hodnota je tajný klíč a zašifrovat nebo ne.|
| Značky |Pole řetězců |Volitelné značky, pokud je zadaný, mohou být použity k filtrování seznamu vlastností. |

![Pojmenované hodnoty](./media/api-management-howto-properties/named-values.png)

Vlastnost hodnoty mohou obsahovat řetězcové literály a [výrazy zásad](https://msdn.microsoft.com/library/azure/dn910913.aspx). Například hodnota `ExpressionProperty` výrazu zásad, která vrací řetězec obsahující aktuální datum a čas. Vlastnost `ContosoHeaderValue` je označena jako tajný klíč, takže jeho hodnota se nezobrazí.

| Název | Hodnota | Tajný kód | Značky |
| --- | --- | --- | --- |
| ContosoHeader |trackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>Můžete přidávat a upravovat vlastnosti

![Přidat vlastnost](./media/api-management-howto-properties/add-property.png)

1. Vyberte **rozhraní API** z pod **API MANAGEMENT**.
2. Vyberte **s názvem hodnoty**.
3. Stiskněte klávesu **+ přidat**.

  Název a hodnotu jsou požadované hodnoty. Pokud hodnota této vlastnosti je tajný klíč, zkontrolujte, že toto je tajný zaškrtávací políčko. Zadejte jeden nebo více volitelné značky usnadní uspořádání vlastnosti, a klikněte na Uložit.
4. Klikněte na možnost **Vytvořit**.

Po vytvoření vlastnost ho můžete upravit kliknutím na vlastnost. Pokud změníte název vlastnosti, všechny zásady, které odkazují na tuto vlastnost se automaticky aktualizují na použití nového názvu.

Informace o úpravách vlastnosti pomocí rozhraní REST API najdete v tématu [upravit vlastnosti pomocí rozhraní REST API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>K odstranění vlastnosti

Chcete-li odstranit vlastnost, klikněte na tlačítko **odstranit** vedle vlastnost odstranit.

> [!IMPORTANT]
> Pokud se všechny zásady odkazuje vlastnost, nebude možné úspěšně ho odstranit, dokud neodeberete vlastnost ze všech zásad, které ho používají.
> 
> 

Informace o odstranění vlastnosti pomocí rozhraní REST API najdete v tématu [odstranit vlastnost pomocí rozhraní REST API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Vyhledávat a filtrovat vlastnosti

**s názvem hodnoty** karta zahrnuje vyhledávání a filtrování funkcí, které vám pomohou při správě vaší vlastnosti. Chcete-li filtrovat seznam vlastností podle názvu vlastnosti, zadejte hledaný termín v **vyhledávání vlastnost** textové pole. Chcete-li zobrazit všechny vlastnosti, zrušte **vyhledávání vlastnost** textové pole a stiskněte klávesu enter.

Chcete-li filtrovat seznam vlastností podle hodnoty značky, zadejte jednu nebo více značek do **filtrovat podle značky** textové pole. Chcete-li zobrazit všechny vlastnosti, zrušte **filtrovat podle značky** textové pole a stiskněte klávesu enter.

## <a name="to-use-a-property"></a>Chcete-li použít vlastnost

Chcete-li použít vlastnost v zásadách, označte název vlastnosti uvnitř pár dvojité složené závorky jako `{{ContosoHeader}}`, jak je znázorněno v následujícím příkladu:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

V tomto příkladu `ContosoHeader` slouží jako název záhlaví v `set-header` zásady, a `ContosoHeaderValue` slouží jako hodnotu této hlavičky. Pokud tato zásada se vyhodnotí během požadavku nebo odpovědi k bráně správy rozhraní API `{{ContosoHeader}}` a `{{ContosoHeaderValue}}` nahradí se jejich hodnoty odpovídající vlastnost.

Vlastnosti lze použít jako dokončení atribut nebo element hodnoty, jak je znázorněno v předchozím příkladu, ale můžete také měly být vložen do nebo v kombinaci s část výrazu literálovou, jak je znázorněno v následujícím příkladu:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Vlastnosti může také obsahovat výrazy zásad. V následujícím příkladu `ExpressionProperty` se používá.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Pokud tato zásada je vyhodnocena, `{{ExpressionProperty}}` se nahradí jeho hodnota: `@(DateTime.Now.ToString())`. Vzhledem k tomu, že hodnota je výraz zásady, je tento výraz vyhodnocen a zásady pokračuje v jeho spuštění.

Tuto funkci můžete otestovat v portálu pro vývojáře voláním operace, která má zásady s vlastností v oboru. V následujícím příkladu je volána operace dva předchozí příklad `set-header` zásady s vlastnostmi. Všimněte si, že odpověď obsahuje dva vlastní hlavičky, které byly nakonfigurovány pomocí vlastnosti zásad.

![Portál pro vývojáře][api-management-send-results]

Pokud si prohlédnete [trasování API Inspector](api-management-howto-api-inspector.md) pro volání, která zahrnuje předchozí dva ukázkové zásady s vlastnostmi, uvidíte dvě `set-header` zásad hodnoty vlastností vložit a také vyhodnocení výrazu zásad pro vlastnost, která obsahovala výraz zásady.

![Trasování API Inspector][api-management-api-inspector-trace]

Při hodnot vlastností mohou obsahovat výrazy zásad, hodnoty vlastností nesmí obsahovat další vlastnosti. Pokud text obsahující odkaz na vlastnost se používá pro hodnotu vlastnosti, jako `Property value text {{MyProperty}}`, že odkaz na vlastnost nebude nahrazen a budou zahrnuty jako součást hodnoty vlastnosti.

## <a name="next-steps"></a>Další postup
* Další informace o práci se zásadami
  * [Zásady ve službě API Management](api-management-howto-policies.md)
  * [referenční příručce o zásadách](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Výrazy zásad](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

