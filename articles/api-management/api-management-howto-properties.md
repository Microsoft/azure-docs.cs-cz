---
title: Jak používat s názvem hodnoty v zásadách Azure API Management
description: Další informace o použití hodnoty s názvem v rámci zásad Azure API Management.
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
ms.openlocfilehash: 829d6bc6cb3f8e78d065d7aaca4937634e7349c8
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437061"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Jak používat s názvem hodnoty v zásadách Azure API Management
Zásady služby API Management jsou vynikající funkcí systému, který chcete změnit chování rozhraní API prostřednictvím konfigurace webu Azure Portal. Zásady představují kolekci příkazů, které se postupně provádí na základě požadavku nebo odezvy z rozhraní API. Příkazy zásad lze sestavit pomocí literálu textové hodnoty. výrazy zásad a s názvem hodnoty. 

Každá instance služby API Management má vlastnosti kolekce párů klíč/hodnota, která je volána s názvem hodnoty, které jsou globální instanci služby. Tyto hodnoty s názvem slouží ke správě konstantní řetězcové hodnoty na všech zásad a konfigurace rozhraní API. Každá vlastnost může mít následující atributy:

| Atribut | Typ | Popis |
| --- | --- | --- |
| Zobrazované jméno |řetězec |Alfanumerický řetězec, který se používá jako odkaz na vlastnost v zásadách |
| Hodnota |řetězec |Hodnota vlastnosti Nesmí být prázdný nebo obsahovat jen mezerové znaky. |
|Tajný kód|Boolean|Určuje, zda hodnota je tajný kód a měla šifrovat, nebo ne.|
| Značky |pole řetězců |Volitelné značky, pokud je zadaný, můžete použít k filtrování seznamu vlastností. |

![Pojmenované hodnoty](./media/api-management-howto-properties/named-values.png)

Hodnoty vlastností můžou obsahovat řetězcových literálů a [výrazy zásad](https://msdn.microsoft.com/library/azure/dn910913.aspx). Například hodnota `ExpressionProperty` je výraz zásad, která vrací řetězec obsahující aktuální datum a čas. Vlastnost `ContosoHeaderValue` je označen jako tajný klíč, takže jeho hodnota se nezobrazí.

| Název | Hodnota | Tajný kód | Značky |
| --- | --- | --- | --- |
| ContosoHeader |trackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>Přidávat a upravovat vlastnosti

![Přidat vlastnost](./media/api-management-howto-properties/add-property.png)

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. Vyberte **pojmenované hodnoty**.
3. Stisknutím klávesy **+ přidat**.

  Název a hodnota jsou požadované hodnoty. Pokud je tato hodnota vlastnosti tajného klíče, zkontrolujte, zda že je tento tajný zaškrtávací políčko. Zadejte jeden nebo více volitelných značek Nápověda k uspořádání pojmenovaných hodnot, a klikněte na Uložit.
4. Klikněte na možnost **Vytvořit**.

Jakmile se vytvoří vlastnost, můžete ho upravit kliknutím na vlastnost. Pokud změníte název vlastnosti, všechny zásady, které odkazují na tuto vlastnost se automaticky aktualizují na použití nového názvu.

Informace o úpravě vlastností pomocí rozhraní REST API najdete v tématu [upravit vlastnost pomocí rozhraní REST API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Chcete-li odstranit vlastnost

Chcete-li odstranit vlastnost, klikněte na tlačítko **odstranit** vedle vlastnosti, která má odstranit.

> [!IMPORTANT]
> Pokud se všechny zásady odkazuje vlastnost, nebude možné úspěšně ho odstranit, dokud neodeberete vlastnost ze všech zásad, které ji používají.
> 
> 

Informace o odstranění vlastnosti pomocí rozhraní REST API najdete v tématu [odstranit vlastnost pomocí rozhraní REST API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-named-values"></a>K vyhledávání a filtrování hodnot s názvem

**Pojmenované hodnoty** karta obsahuje vyhledávání a filtrování funkce, které pomáhají při správě pojmenovaných hodnot. Chcete-li vlastnost seznam můžete filtrovat podle názvu vlastnosti, zadejte hledaný termín v **hledat vlastnost** textového pole. Chcete-li zobrazit všechny pojmenované hodnoty, zrušte **hledat vlastnost** textového pole a stiskněte klávesu enter.

Chcete-li vlastnost seznam můžete filtrovat podle hodnoty značek, zadejte jednu nebo více značek do **filtr podle značek** textového pole. Chcete-li zobrazit všechny pojmenované hodnoty, zrušte **filtr podle značek** textového pole a stiskněte klávesu enter.

## <a name="to-use-a-property"></a>Chcete-li použít vlastnost

Pokud chcete použít vlastnost v zásadách, umístěte název vlastnosti uvnitř pár dvojitých složených závorek, jako je `{{ContosoHeader}}`, jak je znázorněno v následujícím příkladu:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

V tomto příkladu `ContosoHeader` slouží jako název v záhlaví `set-header` zásady, a `ContosoHeaderValue` slouží jako hodnotu této hlavičky. Když tyto zásady se vyhodnotí během požadavku nebo odpovědi ke službě API Management gateway `{{ContosoHeader}}` a `{{ContosoHeaderValue}}` se nahradí jejich odpovídajícími hodnotami vlastností.

Pojmenované hodnoty můžete použít jako kompletní atribut nebo element hodnoty, jak je znázorněno v předchozím příkladu, ale můžete také měly být vložen do nebo kombinaci s část textového literálu výrazu, jak je znázorněno v následujícím příkladu: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Pojmenované hodnoty můžete také obsahovat výrazy zásad. V následujícím příkladu `ExpressionProperty` se používá.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Při vyhodnocování těchto zásad `{{ExpressionProperty}}` nahrazuje s hodnotou: `@(DateTime.Now.ToString())`. Vzhledem k tomu, že je hodnota výrazu zásady, je výraz vyhodnocen a zásady pokračuje v provádění.

Můžete ho otestovat si na portálu pro vývojáře pomocí volání operace, která má zásady s pojmenované hodnoty v rozsahu. V následujícím příkladu je volána operace s dvěma předchozí příklad `set-header` zásad pomocí pojmenovaných hodnot. Všimněte si, že odpověď obsahuje dva vlastní hlavičky, které byly konfigurovány pomocí zásad pojmenovaných hodnot.

![Portál pro vývojáře][api-management-send-results]

Když se podíváte na [trasování pro inspekci API](api-management-howto-api-inspector.md) volání, která zahrnuje předchozí dvě ukázkové zásady s pojmenované hodnoty, zobrazí se dvě `set-header` zásady s hodnotami vlastností vložen a také výraz zásady vyhodnocení vlastnosti, který obsahoval výraz zásady.

![Trasování API Inspectoru][api-management-api-inspector-trace]

Zatímco hodnoty vlastností mohou obsahovat výrazy zásad, hodnoty vlastností nesmí obsahovat jiné pojmenované hodnoty. Pokud text, který obsahuje odkaz na vlastnost se používá pro hodnotu vlastnosti, jako `Property value text {{MyProperty}}`, tento odkaz na vlastnost nenahradí a bude součástí hodnoty vlastnosti.

## <a name="next-steps"></a>Další postup
* Další informace o práci se zásadami
  * [Zásady ve službě API Management](api-management-howto-policies.md)
  * [referenční příručce o zásadách](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Výrazy zásad](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

