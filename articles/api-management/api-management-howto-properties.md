---
title: Jak používat pojmenované hodnoty v zásadách správy rozhraní Azure API
description: Přečtěte si, jak používat pojmenované hodnoty v zásadách správy rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 4362d0875ac2c20fc6963d404f86898a12387dad
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260917"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Jak používat pojmenované hodnoty v zásadách správy rozhraní Azure API

Zásady správy rozhraní API jsou výkonné funkce systému, které umožňují portálu Azure změnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které se postupně provádí na základě požadavku nebo odezvy z rozhraní API. Příkazy zásad lze sestavit pomocí literálových textových hodnot, výrazů zásad a pojmenovaných hodnot.

Každá instance služby API Management má kolekci párů klíč/hodnota, která se nazývá pojmenované hodnoty, které jsou globální pro instanci služby. Neexistuje žádné uložené omezení počtu položek v kolekci. Pojmenované hodnoty lze použít ke správě konstantních řetězcových hodnot ve všech konfiguracích a zásadách rozhraní API. Každá pojmenovaná hodnota může mít následující atributy:

| Atribut      | Typ            | Popis                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | řetězec          | Používá se pro odkazování na pojmenovanou hodnotu v zásadách. Řetězec o jednom až 256 znacích. Povolena jsou pouze písmena, čísla, tečka a pomlčka. |
| `Value`        | řetězec          | Skutečná hodnota. Nesmí být prázdný nebo se skládat pouze z prázdných míst. Maximálně 4096 znaků.                                        |
| `Secret`       | Boolean         | Určuje, zda je hodnota tajný klíč a měla by být zašifrována či nikoli.                                                               |
| `Tags`         | pole řetězce | Slouží k filtrování seznamu pojmenovaných hodnot. Až 32 značek.                                                                                    |

![Pojmenované hodnoty](./media/api-management-howto-properties/named-values.png)

Pojmenované hodnoty mohou obsahovat literálové řetězce a [výrazy zásad](/azure/api-management/api-management-policy-expressions). Například hodnota `Expression` je výraz zásady, který vrací řetězec obsahující aktuální datum a čas. Pojmenovaná `Credential` hodnota je označena jako tajný klíč, takže její hodnota není ve výchozím nastavení zobrazena.

| Name (Název)       | Hodnota                      | Tajný kód | Značky          |
| ---------- | -------------------------- | ------ | ------------- |
| Hodnota      | 42                         | False  | vitální čísla |
| Přihlašovací údaj | ••••••••••••••••••••••     | True   | security      |
| Expression | @(DateTime.Now.toString()) | False  |               |

> [!NOTE]
> Místo pojmenovaných hodnot uložených ve službě API Management můžete použít hodnoty uložené ve službě [Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) jak ukazuje tento [příklad](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml).

## <a name="to-add-and-edit-a-named-value"></a>Přidání a úprava pojmenované hodnoty

![Přidání pojmenované hodnoty](./media/api-management-howto-properties/add-property.png)

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. Vyberte **pojmenované hodnoty**.
3. Stiskněte **klávesu +Přidat**.

    Název a hodnota jsou požadované hodnoty. Pokud je hodnota tajným tajemstvím, zaškrtněte _políčko Toto je tajný_ klíč. Zadejte jednu nebo více volitelných značek, které vám pomohou s uspořádáním pojmenovaných hodnot, a klikněte na Uložit.

4. Klikněte na **Vytvořit**.

Po vytvoření pojmenované hodnoty ji můžete upravit kliknutím na ní. Pokud změníte název pojmenované hodnoty, všechny zásady, které odkazují na pojmenovanou hodnotu, budou automaticky aktualizovány, aby používaly nový název.

Informace o úpravách pojmenované hodnoty pomocí rozhraní REST API naleznete v tématu [Úprava pojmenované hodnoty pomocí rozhraní REST API](/rest/api/apimanagement/2019-12-01/property?patch).

## <a name="to-delete-a-named-value"></a>Odstranění pojmenované hodnoty

Pokud chcete odstranit pojmenovanou hodnotu, klikněte vedle pojmenované hodnoty odstranit na **Odstranit.**

> [!IMPORTANT]
> Pokud je pojmenovaná hodnota odkazována všemi zásadami, nebude možné ji úspěšně odstranit, dokud neodeberete pojmenovanou hodnotu ze všech zásad, které ji používají.

Informace o odstranění pojmenované hodnoty pomocí rozhraní REST API naleznete v tématu [Odstranění pojmenované hodnoty pomocí rozhraní REST API](/rest/api/apimanagement/2019-12-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Hledání a filtrování pojmenovaných hodnot

Karta **Pojmenované hodnoty** obsahuje funkce vyhledávání a filtrování, které vám pomohou spravovat pojmenované hodnoty. Chcete-li filtrovat seznam pojmenovaných hodnot podle názvu, zadejte hledaný termín do textového pole **vlastnosti Hledat.** Chcete-li zobrazit všechny pojmenované hodnoty, zrušte zaškrtnutí textového pole **vlastnosti Hledat** a stiskněte klávesu ENTER.

Chcete-li seznam filtrovat podle značky, zadejte jednu nebo více tagů do textového pole **Filtrovat podle tagů.** Chcete-li zobrazit všechny pojmenované hodnoty, zrušte zaškrtnutí textového pole **Filtr podle tagů** a stiskněte klávesu ENTER.

## <a name="to-use-a-named-value"></a>Použití pojmenované hodnoty

Chcete-li použít pojmenovanou hodnotu v zásadě, umístěte `{{ContosoHeader}}`její název do dvojitédvojice závorek, jako je , jak je znázorněno v následujícím příkladu:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

V tomto `ContosoHeader` příkladu se používá jako název `set-header` záhlaví `ContosoHeaderValue` v zásadě a používá se jako hodnota této hlavičky. Když je tato zásada vyhodnocena během `{{ContosoHeader}}` požadavku `{{ContosoHeaderValue}}` nebo odpovědi na bránu pro správu rozhraní API a jsou nahrazeny příslušnými hodnotami.

Pojmenované hodnoty lze použít jako úplné hodnoty atributů nebo prvků, jak je znázorněno v předchozím příkladu, ale mohou být také vloženy do části literálu textového výrazu nebo s ním kombinovány, jak je znázorněno v následujícím příkladu:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Pojmenované hodnoty mohou také obsahovat výrazy zásad. V následujícím příkladu `ExpressionProperty` se používá.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Při vyhodnocení této `{{ExpressionProperty}}` zásady je nahrazena `@(DateTime.Now.ToString())`její hodnota: . Vzhledem k tomu, že hodnota je výraz zásady, výraz je vyhodnocen a zásada pokračuje s jeho provádění.

Můžete otestovat na portálu pro vývojáře voláním operace, která má zásady s pojmenovanými hodnotami v oboru. V následujícím příkladu je operace volána `set-header` se dvěma předchozími ukázkovými zásadami s pojmenovanými hodnotami. Všimněte si, že odpověď obsahuje dvě vlastní záhlaví, které byly nakonfigurovány pomocí zásad s pojmenovanými hodnotami.

![Portál pro vývojáře][api-management-send-results]

Pokud se podíváte na [trasování inspektoru rozhraní API](api-management-howto-api-inspector.md) pro volání, které obsahuje `set-header` dvě předchozí ukázkové zásady s pojmenovanými hodnotami, zobrazí se dvě zásady s vloženými pojmenovanými hodnotami a také vyhodnocení výrazu zásad pro pojmenovanou hodnotu, která obsahovala výraz zásady.

![Trasování inspektoru rozhraní API][api-management-api-inspector-trace]

Zatímco pojmenované hodnoty mohou obsahovat výrazy zásad, nemohou obsahovat jiné pojmenované hodnoty. Pokud text obsahující pojmenovanou hodnotu odkaz se `Text: {{MyProperty}}`používá pro hodnotu, jako je například , že odkaz nebude vyřešen a nahrazen.

## <a name="next-steps"></a>Další kroky

-   Další informace o práci se zásadami
    -   [Zásady ve správě rozhraní API](api-management-howto-policies.md)
    -   [referenční příručce o zásadách](/azure/api-management/api-management-policies)
    -   [Výrazy zásad](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
