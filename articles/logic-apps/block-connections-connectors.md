---
title: Blokovat připojení pro konkrétní konektory rozhraní API
description: Omezení vytváření a používání připojení rozhraní API v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 02d9852f6615c3926a02294e0e7eca50f2fbe9a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310041"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Blokovat připojení vytvořená pomocí konektorů v Azure Logic Apps

Pokud vaše organizace neumožňuje připojení k omezeným nebo neschváleným prostředkům pomocí jejich konektorů v Azure Logic Apps, můžete zablokovat možnost vytvářet a používat tato připojení v pracovních postupech aplikace logiky. Pomocí [Azure Policy](../governance/policy/overview.md)můžete definovat a vyhovět [zásadám](../governance/policy/overview.md#policy-definition) , které zabraňují vytváření a používání připojení pro konektory, které chcete blokovat. Z bezpečnostních důvodů můžete například chtít blokovat připojení ke konkrétním platformám sociálních médií nebo jiným službám a systémům.

V tomto tématu se dozvíte, jak nastavit zásadu, která blokuje konkrétní připojení pomocí Azure Portal, ale definice zásad můžete vytvořit jinými způsoby, například prostřednictvím Azure REST API, Azure PowerShell, Azure CLI a Azure Resource Manager šablon. Další informace najdete v tématu [kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md).

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud předplatné nemáte, [Vytvořte si bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* Referenční ID konektoru, který chcete blokovat. Další informace najdete v tématu [Vyhledání referenčního ID konektoru](#connector-reference-ID).

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>Najít referenční ID konektoru

Pokud již máte aplikaci logiky s připojením, které chcete blokovat, postupujte podle [kroků Azure Portal](#connector-ID-portal). Jinak postupujte podle těchto kroků:

1. Přejděte na [seznam konektorů Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors).

1. Vyhledejte referenční stránku pro konektor, který chcete blokovat.

   Například pokud chcete blokovat konektor Instagramu, který je zastaralý, přejít na tuto stránku:

   `https://docs.microsoft.com/connectors/instagram/`

1. Z adresy URL stránky zkopírujte a uložte referenční ID konektoru na konci bez lomítka ( `/` ), například `instagram` .

   Později při vytváření definice zásady použijete toto ID v příkazu podmínky definice, například:

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>portál Azure

1. V [Azure Portal](https://portal.azure.com)Najděte a otevřete aplikaci logiky.

1. V nabídce aplikace logiky vyberte **zobrazení kód aplikace** logiky, abyste mohli zobrazit definici JSON vaší aplikace logiky.

   ![Pokud chcete najít ID konektoru, otevřete zobrazení kód aplikace logiky.](./media/block-connections-connectors/code-view-connector-id.png)

1. Vyhledejte `parameters` objekt obsahující `$connections` objekt, který zahrnuje `{connection-name}` objekt pro každé připojení v aplikaci logiky a určuje informace o tomto připojení:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   Například pro konektor Instagramu vyhledejte `instagram` objekt, který identifikuje připojení Instagramu:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. Pro připojení, které chcete blokovat, vyhledejte `id` vlastnost a hodnotu, která je v tomto formátu: 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   Tady je například `id` vlastnost a hodnota pro připojení Instagramu:

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. Z `id` hodnoty vlastnosti zkopírujte a uložte referenční ID konektoru na konci, například `instagram` .

   Později při vytváření definice zásady použijete toto ID v příkazu podmínky definice, například:

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>Vytvořit zásadu pro blokování vytváření připojení

Pokud chcete zcela blokovat vytváření připojení v aplikaci logiky, postupujte takto:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Do vyhledávacího pole portálu zadejte `policy` a vyberte **zásady**.

   ![V Azure Portal vyhledejte a vyberte zásady.](./media/block-connections-connectors/find-select-azure-policy.png)

1. V nabídce **zásady** v části **vytváření obsahu** vyberte **definice**  >  **+ definice zásad**.

   ![Vyberte definice > + definice zásady.](./media/block-connections-connectors/add-new-policy-definition.png)

1. V části **definice zásad** zadejte informace o definici zásady na základě vlastností popsaných v tomto příkladu:

   ![Snímek obrazovky se zobrazením vlastností "definice zásad".](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Umístění definice** | Yes | <*Azure – předplatné – název*> | Předplatné Azure, které se má použít pro definici zásady <p><p>1. Pokud chcete najít předplatné, vyberte tlačítko se třemi tečkami (**...**). <br>2. v seznamu **odběr** vyhledejte a vyberte své předplatné. <br>3. Jakmile budete hotovi, vyberte **Vybrat**. |
   | **Název** | Yes | <*Policy – definice – název*> | Název, který se má použít pro definici zásady |
   | **Popis** | No | <*Policy – definice – název*> | Popis definice zásady |
   | **Kategorie** | Yes | **Logic Apps** | Název existující kategorie nebo nové kategorie pro definici zásady |
   | **Vynucení zásad** | Yes | **Povoleno** | Toto nastavení určuje, jestli se má při ukládání vaší práce povolit nebo zakázat definici zásady. |
   ||||

1. V části **pravidlo zásad** se pole pro úpravy JSON předem vyplní šablonou definic zásad. Nahraďte tuto šablonu [definicí zásad](../governance/policy/concepts/definition-structure.md) na základě vlastností popsaných v následující tabulce a pomocí této syntaxe:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | `mode` | `All` | Režim, který určuje typy prostředků, které zásada vyhodnocuje. <p><p>Tento scénář nastavuje `mode` `All` , který platí pro zásady skupiny prostředků Azure, odběry a všechny typy prostředků. <p><p>Další informace najdete v tématu [Struktura definice zásad – režim](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Podmínka, která určuje, kdy se má vyhovět pravidlo zásad <p><p>V tomto scénáři určuje, `{condition-to-evaluate}` zda je `api.id` hodnota shodná s hodnotou `Microsoft.Web/connections/api.id` `*managedApis/{connector-name}` , která určuje zástupnou hodnotu (*). <p><p>Další informace najdete v tématu [Struktura definice zásad – pravidlo zásad](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `field` | `Microsoft.Web/connections/api.id` | `field`Hodnota, která se má porovnat s podmínkou <p><p>V tomto scénáři `field` používá [*alias*](../governance/policy/concepts/definition-structure.md#aliases) `Microsoft.Web/connections/api.id` k přístupu k hodnotě ve vlastnosti konektoru `api.id` . |
   | `like` | `*managedApis/{connector-name}` | Logický operátor a hodnota, která se má použít pro porovnání `field` hodnoty <p><p>V tomto scénáři `like` operátor a zástupný znak (*) zajistí, že pravidlo funguje bez ohledu na oblast a řetězec, `*managedApis/{connector-name}` je hodnota, která má být shodná s `{connector-name}` identifikátorem ID konektoru, který chcete blokovat. <p><p>Předpokládejme například, že chcete blokovat vytváření připojení k platformám nebo databázím sociálních médií: <p><p>Službě `twitter` <br>Instagramu `instagram` <br>Přes `facebook` <br>Pinterestu `pinterest` <br>-SQL Server nebo Azure SQL: `sql` <p><p>Chcete-li najít Tato ID konektoru, viz výše v tomto tématu v části [Najít referenční ID konektoru](#connector-reference-ID) . |
   | `then` | `{effect-to-apply}` | Efekt, který se má použít, pokud `if` je splněna podmínka <p><p>V tomto scénáři je potřeba `{effect-to-apply}` Blokovat a podařit požadavek nebo operaci, které nevyhovují zásadám. <p><p>Další informace najdete v tématu [Struktura definice zásad – pravidlo zásad](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | `effect`Je zablokování žádosti, která má vytvořit zadané připojení. <p><p>Další informace najdete v tématu [vysvětlení Azure Policy efektů – Deny](../governance/policy/concepts/effects.md#deny). |
   ||||

   Předpokládejme například, že chcete blokovat vytváření připojení pomocí konektoru Instagramu. Tady je definice zásady, kterou můžete použít:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   Tady je způsob, jakým se zobrazuje pole **pravidlo zásad** :

   ![Snímek obrazovky s polem pravidlo zásad s příkladem pravidla zásad](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   V případě více konektorů můžete přidat další podmínky, například:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. Jakmile budete mít hotovo, vyberte **Uložit**. Po uložení definice zásady Azure Policy vygeneruje a přidá do definice zásady další hodnoty vlastností.

1. V dalším kroku přiřaďte definici zásady, kde chcete zásadu vynutila, a [Vytvořte přiřazení zásady](#create-policy-assignment).

Další informace o definicích zásad Azure najdete v těchto tématech:

* [Definice struktury zásad](../governance/policy/concepts/definition-structure.md)
* [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)
* [Azure Policy předdefinované definice zásad pro Azure Logic Apps](./policy-reference.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>Vytvořit zásadu pro blokování pomocí připojení

Když vytvoříte připojení v aplikaci logiky, toto připojení existuje jako samostatný prostředek Azure. Pokud odstraníte jenom aplikaci logiky, připojení se automaticky neodstraní a bude i nadále existovat, dokud ji neodstraníte. Může nastat situace, kdy připojení již existuje nebo kde je nutné vytvořit připojení pro použití mimo aplikaci logiky. Můžete pořád zablokovat možnost použít existující připojení v aplikaci logiky tak, že vytvoříte zásadu, která znemožní ukládat aplikace logiky s omezeným nebo neschváleným připojením.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Do vyhledávacího pole portálu zadejte `policy` a vyberte **zásady**.

   ![V Azure Portal vyhledejte a vyberte zásady.](./media/block-connections-connectors/find-select-azure-policy.png)

1. V nabídce **zásady** v části **vytváření obsahu** vyberte **definice**  >  **+ definice zásad**.

   ![Vyberte definice > + definice zásady.](./media/block-connections-connectors/add-new-policy-definition.png)

1. V části **definice zásad** zadejte informace o definici zásady na základě vlastností popsaných v tomto příkladu a pokračujte pomocí Instagramu jako příklad:

   ![Vlastnosti definice zásad](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Umístění definice** | Yes | <*Azure – předplatné – název*> | Předplatné Azure, které se má použít pro definici zásady <p><p>1. Pokud chcete najít předplatné, vyberte tlačítko se třemi tečkami (**...**). <br>2. v seznamu **odběr** vyhledejte a vyberte své předplatné. <br>3. Jakmile budete hotovi, vyberte **Vybrat**. |
   | **Název** | Yes | <*Policy – definice – název*> | Název, který se má použít pro definici zásady |
   | **Popis** | No | <*Policy – definice – název*> | Popis definice zásady |
   | **Kategorie** | Yes | **Logic Apps** | Název existující kategorie nebo nové kategorie pro definici zásady |
   | **Vynucení zásad** | Yes | **Povoleno** | Toto nastavení určuje, jestli se má při ukládání vaší práce povolit nebo zakázat definici zásady. |
   ||||

1. V části **pravidlo zásad** se pole pro úpravy JSON předem vyplní šablonou definic zásad. Nahraďte tuto šablonu [definicí zásad](../governance/policy/concepts/definition-structure.md) na základě vlastností popsaných v následující tabulce a pomocí této syntaxe:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | `mode` | `All` | Režim, který určuje typy prostředků, které zásada vyhodnocuje. <p><p>Tento scénář nastavuje `mode` `All` , který platí pro zásady skupiny prostředků Azure, odběry a všechny typy prostředků. <p><p>Další informace najdete v tématu [Struktura definice zásad – režim](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Podmínka, která určuje, kdy se má vyhovět pravidlo zásad <p><p>V tomto scénáři určuje, `{condition-to-evaluate}` zda výstup řetězce z `[string(field('Microsoft.Logic/workflows/parameters'))]` obsahuje řetězec, `{connector-name}` . <p><p>Další informace najdete v tématu [Struktura definice zásad – pravidlo zásad](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | Hodnota, která se má porovnat s podmínkou <p><p>V tomto scénáři `value` je výstup řetězce z `[string(field('Microsoft.Logic/workflows/parameters'))]` , který převede `$connectors` objekt uvnitř `Microsoft.Logic/workflows/parameters` objektu na řetězec. |
   | `contains` | `{connector-name}` | Logický operátor a hodnota, která se má použít pro porovnání s `value` vlastností <p><p>V tomto scénáři `contains` operátor zajišťuje, že pravidlo funguje bez ohledu na to, kde `{connector-name}` se zobrazí, kde řetězec, `{connector-name}` je ID konektoru, který chcete omezit nebo blokovat. <p><p>Předpokládejme například, že chcete blokovat použití připojení k platformám nebo databázím sociálních médií: <p><p>Službě `twitter` <br>Instagramu `instagram` <br>Přes `facebook` <br>Pinterestu `pinterest` <br>-SQL Server nebo Azure SQL: `sql` <p><p>Chcete-li najít Tato ID konektoru, viz výše v tomto tématu v části [Najít referenční ID konektoru](#connector-reference-ID) . |
   | `then` | `{effect-to-apply}` | Efekt, který se má použít, pokud `if` je splněna podmínka <p><p>V tomto scénáři je potřeba `{effect-to-apply}` Blokovat a podařit požadavek nebo operaci, které nedodržují zásady. <p><p>Další informace najdete v tématu [Struktura definice zásad – pravidlo zásad](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | `effect`Je `deny` nebo zablokuje požadavek na uložení aplikace logiky, která používá zadané připojení. <p><p>Další informace najdete v tématu [vysvětlení Azure Policy efektů – Deny](../governance/policy/concepts/effects.md#deny). |
   ||||

   Předpokládejme například, že chcete blokovat ukládání aplikací logiky, které používají připojení Instagramu. Tady je definice zásady, kterou můžete použít:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   Tady je způsob, jakým se zobrazuje pole **pravidlo zásad** :

   ![Pravidlo pro definici zásady](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. Jakmile budete mít hotovo, vyberte **Uložit**. Po uložení definice zásady Azure Policy vygeneruje a přidá do definice zásady další hodnoty vlastností.

1. V dalším kroku přiřaďte definici zásady, kde chcete zásadu vynutila, a [Vytvořte přiřazení zásady](#create-policy-assignment).

Další informace o definicích zásad Azure najdete v těchto tématech:

* [Definice struktury zásad](../governance/policy/concepts/definition-structure.md)
* [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)
* [Azure Policy předdefinované definice zásad pro Azure Logic Apps](./policy-reference.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>Vytvořit přiřazení zásad

Dál je potřeba přiřadit definici zásady, ve které chcete zásady vyhovět, například pro jednu skupinu prostředků, více skupin prostředků, tenanta Azure Active Directory (Azure AD) nebo předplatné Azure. Pro tuto úlohu použijte následující postup k vytvoření přiřazení zásady:

1. Pokud jste se odhlásili, přihlaste se zpátky do [Azure Portal](https://portal.azure.com). Do vyhledávacího pole portálu zadejte `policy` a vyberte **zásady**.

   ![V Azure Portal vyhledejte a vyberte zásady.](./media/block-connections-connectors/find-select-azure-policy.png)

1. V nabídce **zásady** v části **vytváření obsahu** vyberte **přiřazení**  >  **zásady přiřazení**.

   ![Vyberte přiřazení > přiřazení.](./media/block-connections-connectors/add-new-policy-assignment.png)

1. V části **základy** zadejte tyto informace pro přiřazení zásady:

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Rozsah** | Yes | Prostředky, u kterých chcete vynutilit přiřazení zásady. <p><p>1. vedle pole **obor** vyberte tlačítko se třemi tečkami (**...**). <br>2. v seznamu **předplatné** vyberte předplatné Azure. <br>3. v seznamu **Skupina prostředků** vyberte skupinu prostředků. <br>4. Jakmile budete hotovi, vyberte **Vybrat**. |
   | **Vyloučení** | No | Všechny prostředky Azure, které se mají vyloučit z přiřazení zásad. <p><p>1. klikněte na tlačítko se třemi tečkami (**...**) vedle pole **vyloučení** . <br>2. v seznamu **prostředků** vyberte prostředek > **Přidat do vybraného oboru**. <br>3. Jakmile budete hotovi, vyberte **Uložit**. |
   | **Definice zásady** | Yes | Název definice zásady, kterou chcete přiřadit a vykonat. Tento příklad pokračuje s příkladem zásady Instagramu, "Block Instagramu Connections". <p><p>1. Vyberte tlačítko se třemi tečkami (**...**) vedle pole **definice zásady** . <br>2. Vyhledejte a vyberte definici zásady pomocí filtru **typů** nebo **vyhledávacího** pole. <br>3. Jakmile budete hotovi, vyberte **Vybrat**. |
   | **Název přiřazení** | Yes | Název, který se má použít pro přiřazení zásady, pokud se liší od definice zásady |
   | **ID přiřazení** | Yes | Automaticky vygenerované ID pro přiřazení zásady |
   | **Popis** | No | Popis přiřazení zásady |
   | **Vynucení zásad** | Yes | Nastavení, které povoluje nebo zakazuje přiřazení zásady |
   | **Přiřadil** | No | Jméno osoby, která vytvořila a používala přiřazení zásady |
   ||||

   Například pokud chcete přiřadit zásadu ke skupině prostředků Azure pomocí příkladu Instagramu:

   ![Vlastnosti přiřazení zásad](./media/block-connections-connectors/policy-assignment-basics.png)

1. Až budete hotovi, vyberte **zkontrolovat + vytvořit**.

   Až zásadu vytvoříte, možná budete muset počkat až 15 minut, než se zásada uplatní. Změny mohou mít také podobné opožděné účinky.

1. Jakmile se zásada projeví, můžete [zásady testovat](#test-policy).

Další informace najdete v tématu [rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy](../governance/policy/assign-policy-portal.md).

<a name="test-policy"></a>

## <a name="test-the-policy"></a>Testování zásad

Pokud chcete vyzkoušet zásady, začněte vytvářet připojení pomocí konektoru s omezením Now v návrháři aplikace logiky. Když se Instagramu přihlásíte k Instagramu, zobrazí se tato chyba, že aplikaci logiky se nepovedlo vytvořit připojení:

![Chyba připojení kvůli použitým zásadám](./media/block-connections-connectors/connection-failure-message.png)

Tato zpráva obsahuje tyto informace:

| Description | Content |
|-------------|---------|
| Důvod selhání | `"Resource 'instagram' was disallowed by policy."` |
| Název přiřazení | `"Block Instagram connections"` |
| ID přiřazení | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| ID definice zásady | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Policy](../governance/policy/overview.md)