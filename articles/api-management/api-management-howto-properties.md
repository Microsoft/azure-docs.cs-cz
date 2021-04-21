---
title: Jak používat pojmenované hodnoty v zásadách Azure API Management
description: Naučte se používat pojmenované hodnoty v zásadách Azure API Management. Pojmenované hodnoty mohou obsahovat literálové řetězce, výrazy zásad a tajné klíče uložené v Azure Key Vault.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: a970eb23c171522907b6066454d9ca15d85f0835
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812259"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Použití pojmenovaných hodnot v zásadách Azure API Management

[Zásady API Management](api-management-howto-policies.md) představují výkonnou schopnost systému, která umožňuje vydavateli změnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které se postupně provádí na základě požadavku nebo odezvy z rozhraní API. Příkazy zásad lze vytvořit pomocí textových hodnot literálů, výrazů zásad a pojmenovaných hodnot.

*Pojmenované hodnoty* jsou globální kolekce párů název/hodnota v každé instanci API Management. Počet položek v kolekci není nijak omezen. Pojmenované hodnoty se dají použít ke správě konstantních hodnot řetězců a tajných kódů napříč všemi konfiguracemi a zásadami rozhraní API. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Pojmenované hodnoty v Azure Portal":::

## <a name="value-types"></a>Typy hodnot

|Typ  |Description  |
|---------|---------|
|Podobě     |  Řetězcový literál nebo výraz zásady     |
|Tajný kód     |   Řetězcový literál nebo výraz zásad, který je zašifrovaný pomocí API Management      |
|[Trezor klíčů](#key-vault-secrets)     |  Identifikátor tajného kódu uloženého v trezoru klíčů Azure.      |

Jednoduché hodnoty nebo tajné klíče můžou obsahovat [výrazy zásad](./api-management-policy-expressions.md). Například výraz `@(DateTime.Now.ToString())` vrátí řetězec obsahující aktuální datum a čas.

Podrobnosti o atributech pojmenované hodnoty naleznete v [odkazu API Management REST API](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate).

## <a name="key-vault-secrets"></a>Tajné kódy trezoru klíčů

Tajné hodnoty mohou být uloženy buď jako šifrované řetězce v API Management (vlastní tajné klíče), nebo odkazem na tajné kódy v [Azure Key Vault](../key-vault/general/overview.md). 

Doporučuje se používat tajné klíče trezoru klíčů, protože pomáhá vylepšit API Management zabezpečení:

* Tajné kódy uložené v trezorech klíčů je možné opakovaně používat napříč službami.
* Podrobné [zásady přístupu](../key-vault/general/security-features.md#privileged-access) se dají použít u tajných klíčů.
* Tajné kódy, které jsou v trezoru klíčů aktualizované, se automaticky otočí v API Management. Po aktualizaci v trezoru klíčů se pojmenovaná hodnota v API Management v rámci 4 hodin aktualizuje. Tajný kód můžete také aktualizovat ručně pomocí Azure Portal nebo prostřednictvím REST API pro správu.

### <a name="prerequisites-for-key-vault-integration"></a>Předpoklady pro integraci trezoru klíčů

1. Postup vytvoření trezoru klíčů najdete v tématu [rychlý Start: vytvoření trezoru klíčů pomocí Azure Portal](../key-vault/general/quick-create-portal.md).
1. Povolte [spravovanou identitu](api-management-howto-use-managed-service-identity.md) přiřazenou systémem nebo uživatelem v instanci API Management.
1. Přiřaďte [zásady přístupu trezoru klíčů](../key-vault/general/assign-access-policy-portal.md) ke spravované identitě s oprávněními k získání a výpisu tajných kódů z trezoru. Postup přidání zásady:
    1. Na portálu přejděte do svého trezoru klíčů.
    1. Vyberte **nastavení > zásady přístupu > + přidat zásady přístupu**.
    1. Vyberte **oprávnění tajného klíče** a pak vyberte **získat** a **seznam**.
    1. V části **Vybrat objekt zabezpečení** vyberte název prostředku spravované identity. Pokud používáte identitu přiřazenou systémem, je objekt zabezpečení názvem vaší instance API Management.
1. Vytvořte nebo importujte tajný klíč do trezoru klíčů. Viz [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure Portal](../key-vault/secrets/quick-create-portal.md).

Pokud chcete použít tajný klíč trezoru klíčů, [přidejte nebo upravte pojmenovanou hodnotu](#add-or-edit-a-named-value)a zadejte typ **trezoru klíčů**. Vyberte tajný klíč z trezoru klíčů.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Přidat nebo upravit pojmenovanou hodnotu

### <a name="add-a-key-vault-secret"></a>Přidání tajného klíče trezoru klíčů

Viz [předpoklady pro integraci trezoru klíčů](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Při použití tajného klíče trezoru klíčů v API Management buďte opatrní, abyste neodstranili tajný klíč, Trezor klíčů ani spravovanou identitu, která se používá pro přístup k trezoru klíčů.

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
1. V části **rozhraní API** vyberte **pojmenované hodnoty**  >  **+ Přidat**.
1. Zadejte identifikátor **názvu** a zadejte **Zobrazovaný název** , který se použije k odkazování na vlastnost v zásadách.
1. V **typ hodnoty** vyberte **Trezor klíčů**.
1. Zadejte identifikátor tajného klíče trezoru klíčů (bez verze) nebo zvolte **Vybrat** a vyberte tajný klíč z trezoru klíčů.
    > [!IMPORTANT]
    > Pokud zadáte identifikátor tajného klíče trezoru klíčů sami, ujistěte se, že nemá informace o verzi. V opačném případě se tajný kód v API Management po aktualizaci trezoru klíčů automaticky neotočí.
1. V **klientské identitě** vyberte spravovanou identitu přiřazenou systémem nebo existující uživatelem přiřazenou identitu. Naučte se [přidávat nebo upravovat spravované identity ve službě API Management](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Identita potřebuje oprávnění k získání a výpisu tajných kódů z trezoru klíčů. Pokud jste ještě nenakonfigurovali přístup k trezoru klíčů, API Management vás vyzve, aby mohl automaticky konfigurovat identitu s potřebnými oprávněními.
1. Přidejte jednu nebo více volitelných značek, které vám pomůžou uspořádat pojmenované hodnoty a pak je **Uložit**.
1. Vyberte **Vytvořit**.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Přidat tajnou hodnotu trezoru klíčů":::

### <a name="add-a-plain-or-secret-value"></a>Přidat jednoduchou nebo tajnou hodnotu

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
1. V části **rozhraní API** vyberte **pojmenované hodnoty**  >  **+ Přidat**.
1. Zadejte identifikátor **názvu** a zadejte **Zobrazovaný název** , který se použije k odkazování na vlastnost v zásadách.
1. V **typ hodnoty** vyberte možnost **prostý** nebo **tajný**.
1. Do **hodnoty** zadejte výraz řetězce nebo zásady.
1. Přidejte jednu nebo více volitelných značek, které vám pomůžou uspořádat pojmenované hodnoty a pak je **Uložit**.
1. Vyberte **Vytvořit**.

Jakmile se pojmenovaná hodnota vytvoří, můžete ji upravit tak, že vyberete název. Změníte-li zobrazovaný název, všechny zásady, které se na tuto pojmenovanou hodnotu odkazují, se automaticky aktualizují, aby používaly nový zobrazovaný název.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li začít používat rozhraní příkazového řádku Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Pojmenovanou hodnotu přidáte pomocí příkazu [AZ APIM NV Create](/cli/azure/apim/nv#az_apim_nv_create) :

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

Po vytvoření pojmenované hodnoty ji můžete aktualizovat pomocí příkazu [AZ APIM NV Update](/cli/azure/apim/nv#az_apim_nv_update) . Chcete-li zobrazit všechny pojmenované hodnoty, spusťte příkaz [AZ APIM NV list](/cli/azure/apim/nv#az_apim_nv_list) :

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Chcete-li zobrazit podrobné informace o pojmenované hodnotě, kterou jste pro tento příklad vytvořili, spusťte příkaz [AZ APIM NV show](/cli/azure/apim/nv#az_apim_nv_show) :

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Tento příklad je tajná hodnota. Předchozí příkaz nevrací hodnotu. Tuto hodnotu zobrazíte spuštěním příkazu [AZ APIM NV show-Secret](/cli/azure/apim/nv#az_apim_nv_show_secret) :

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Pojmenovanou hodnotu odstraníte pomocí příkazu [AZ APIM NV Delete](/cli/azure/apim/nv#az_apim_nv_delete) :

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>Použít pojmenovanou hodnotu

Příklady v této části používají pojmenované hodnoty uvedené v následující tabulce.

| Name               | Hodnota                      | Tajný kód | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Ne  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | Ano   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Ne  | 

Chcete-li použít pojmenovanou hodnotu v zásadě, umístěte svůj zobrazovaný název do dvojice dvojitých závorek jako `{{ContosoHeader}}` , jak je znázorněno v následujícím příkladu:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

V tomto příkladu `ContosoHeader` se používá jako název záhlaví v `set-header` zásadě a `ContosoHeaderValue` používá se jako hodnota této hlavičky. Když se tato zásada vyhodnotí během žádosti nebo odpovědi na bránu API Management `{{ContosoHeader}}` a `{{ContosoHeaderValue}}` nahradí se jejich příslušnými hodnotami.

Pojmenované hodnoty lze použít jako úplný atribut nebo hodnoty prvků, jak je znázorněno v předchozím příkladu, ale mohou být také vloženy do nebo kombinovány s částí textového výrazu literálu, jak je znázorněno v následujícím příkladu: 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

Pojmenované hodnoty mohou obsahovat také výrazy zásad. V následujícím příkladu `ExpressionProperty` je použit výraz.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Když se tato zásada vyhodnotí, `{{ExpressionProperty}}` nahradí se její hodnotou `@(DateTime.Now.ToString())` . Vzhledem k tomu, že hodnota je výrazem zásady, vyhodnocuje se výraz a zásada se provede s jeho spuštěním.

Můžete ji otestovat ve Azure Portal nebo [portálu pro vývojáře](api-management-howto-developer-portal.md) voláním operace, která má zásadu s pojmenovanými hodnotami v oboru. V následujícím příkladu je operace volána s použitím dvou předchozích ukázkových `set-header` zásad s pojmenovanými hodnotami. Všimněte si, že odpověď obsahuje dvě vlastní hlavičky, které byly nakonfigurovány pomocí zásad s pojmenovanými hodnotami.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="Test odpovědi rozhraní API":::

Pokud se podíváte na výstupní [trasování API](api-management-howto-api-inspector.md) pro volání, které obsahuje dvě předchozí ukázkové zásady s pojmenovanými hodnotami, uvidíte dvě `set-header` zásady s vloženými pojmenovanými hodnotami i vyhodnocením výrazu zásady pro pojmenovanou hodnotu, která obsahovala výraz zásad.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="Trasování v inspektoru API":::

> [!CAUTION]
> Pokud zásada odkazuje na tajný kód v Azure Key Vault, bude hodnota z trezoru klíčů viditelná pro uživatele, kteří mají přístup k předplatným, která jsou povolená pro [trasování požadavků rozhraní API](api-management-howto-api-inspector.md).

Pojmenované hodnoty můžou obsahovat výrazy zásad, ale nemůžou obsahovat jiné pojmenované hodnoty. Pokud se text obsahující odkaz s názvem hodnoty používá pro hodnotu, například `Text: {{MyProperty}}` , tento odkaz nebude vyřešen a nahrazen.

## <a name="delete-a-named-value"></a>Odstranění pojmenované hodnoty

Pokud chcete odstranit pojmenovanou hodnotu, vyberte název a pak v místní nabídce (**...**) vyberte **Odstranit** .

> [!IMPORTANT]
> Pokud je pojmenovaná hodnota odkazována pomocí zásad API Management, nemůžete ji odstranit, dokud neodeberete pojmenovanou hodnotu ze všech zásad, které ji používají.

## <a name="next-steps"></a>Další kroky

-   Další informace o práci se zásadami
    -   [Zásady v API Management](api-management-howto-policies.md)
    -   [referenční příručce o zásadách](./api-management-policies.md)
    -   [Výrazy zásad](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

