---
title: Použití zásad Azure Firewall k definování hierarchie pravidel
description: Naučte se používat zásady Azure Firewall k definování hierarchie pravidel a prosazování dodržování předpisů.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: c290904c9f4bc7dba70dad9351dc45b676e0c236
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88893686"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>Použití zásad Azure Firewall k definování hierarchie pravidel

Správci zabezpečení musí spravovat brány firewall a zajišťovat dodržování předpisů v místních i cloudových nasazeních. Klíčovou součástí je schopnost poskytovat týmům aplikací flexibilitu při implementaci kanálů CI/CD a vytvářet tak pravidla brány firewall automatizovaným způsobem.

Zásady Azure Firewall umožňují definovat hierarchii pravidla a vyhovět dodržování předpisů:

- Poskytuje hierarchickou strukturu pro překrytí centrální základní zásady nad podřízenou zásadu týmového týmu aplikace. Základní zásady mají vyšší prioritu a běží před podřízenou zásadou.
- Pomocí vlastní definice řízení přístupu na základě role (RBAC) zabráníte nechtěnému odebrání základních zásad a zajistěte selektivní přístup ke skupinám kolekcí pravidel v rámci předplatného nebo skupiny prostředků. 

## <a name="solution-overview"></a>Přehled řešení

Kroky vysoké úrovně jsou v tomto příkladu:

1. Ve skupině prostředků týmu zabezpečení vytvořte základní zásady brány firewall. 
3. Definujte pravidla specifická pro zabezpečení v základních zásadách. Tím se přidá společná sada pravidel pro povolení nebo odmítnutí provozu.
4. Vytvořte zásady týmového týmu, které dědí základní zásady. 
5. Definujte v zásadách pravidla specifická pro aplikaci. Můžete také migrovat pravidla z již existujících bran firewall.
6. Vytvořte Azure Active Directory vlastní role, abyste poskytovali jemně odstupňovaný přístup ke skupině kolekcí pravidel a přidali role do oboru zásad brány firewall. V následujícím příkladu můžou členové prodejního týmu upravit skupiny kolekcí pravidel pro zásady brány firewall Sales Teams. Totéž platí pro databáze a technické týmy.
7. Přidružte zásadu k odpovídající bráně firewall. Brána firewall Azure může mít jenom jednu přiřazenou zásadu. K tomu je potřeba, aby každý tým aplikace měl svou vlastní bránu firewall.



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="Týmy a požadavky" border="false":::

### <a name="create-the-firewall-policies"></a>Vytvoření zásad brány firewall

- Základní zásady brány firewall.

Vytvořte zásady pro každý tým aplikace:

- Zásady brány firewall pro prodej. Zásady brány firewall pro prodej dědí základní zásady brány firewall.
- Zásada brány firewall databáze. Zásady brány firewall databáze dědí základní zásady brány firewall.
- Zásady brány firewall pro vývoj. Zásady pro technický firewall také dědí základní zásady brány firewall.

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="Týmy a požadavky" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>Vytvoření vlastních rolí pro přístup ke skupinám kolekcí pravidel 

Vlastní role jsou definovány pro každý tým aplikace. Role definuje operace a rozsah. Týmy aplikace můžou upravovat skupiny kolekcí pravidel pro příslušné aplikace.

Pro definování vlastních rolí použijte následující postup vysoké úrovně:

1. Získat předplatné:

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. Spusťte následující příkaz:

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. Použijte příkaz Get-AzRoleDefinition pro výstup role čtenář ve formátu JSON. 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. Otevřete ReaderSupportRole.jsv souboru v editoru.

   Následuje ukázka výstupu JSON. Informace o různých vlastnostech najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md).

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. Úpravou souboru JSON přidejte 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

   operace s vlastností **Actions**   . Nezapomeňte vložit čárku za operaci čtení. Tato akce umožní uživateli vytvořit a aktualizovat skupiny kolekcí pravidel.
6. V **AssignableScopes**přidejte své ID předplatného s následujícím formátem: 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   Musíte přidat explicitní ID předplatných, jinak tuto roli nebudete moct importovat do svého předplatného.
7. Odstraňte **Id**   řádek vlastnosti ID a změňte vlastnost IsDeleted **IsCustom**   na hodnotu true.
8. Změnit vlastnosti **Name**   a **Description**   na *AZFM autor skupiny kolekcí pravidel* a *Uživatelé v této roli můžou upravovat skupiny kolekcí pravidel zásad brány firewall* .

Váš soubor JSON by měl vypadat podobně jako v následujícím příkladu:

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. Chcete-li vytvořit novou vlastní roli, použijte příkaz New-AzRoleDefinition a zadejte soubor definice role JSON. 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>Výpis vlastních rolí

Chcete-li zobrazit seznam všech vlastních rolí, můžete použít příkaz Get-AzRoleDefinition:

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

Můžete také zobrazit vlastní role v Azure Portal. Přejděte do svého předplatného, vyberte **řízení přístupu (IAM)**, **role**.

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="Týmy a požadavky":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="Týmy a požadavky":::

Další informace najdete v tématu [kurz: Vytvoření vlastní role Azure pomocí Azure PowerShell](../role-based-access-control/tutorial-custom-role-powershell.md).

### <a name="add-users-to-the-custom-role"></a>Přidat uživatele do vlastní role

Na portálu můžete přidat uživatele do role Autoři skupin kolekcí pravidel AZFM a poskytnout přístup k zásadám brány firewall.

1. Na portálu vyberte zásady brány firewall aplikace pro aplikaci (například SalesAppPolicy).
2. Vyberte **Access Control**.
3. Vyberte **Přidat přiřazení role**.
4. Přidejte uživatele nebo skupiny uživatelů (například prodejní tým) do role.

Tento postup opakujte pro ostatní zásady brány firewall.

### <a name="summary"></a>Shrnutí

Zásady brány firewall s vlastní RBAC teď poskytují selektivní přístup ke skupinám kolekcí pravidel zásad brány firewall.

Uživatelé nemají oprávnění k těmto akcím:
- Odstraňte zásady Azure Firewall nebo brány firewall.
- Aktualizujte hierarchii zásad brány firewall nebo nastavení DNS nebo analýzu hrozeb.
- Aktualizujte zásady brány firewall, pokud nejsou členy skupiny AZFM pravidel skupiny pro vytváření.

Správci zabezpečení můžou pomocí základních zásad vymáhat guardrails a blokovat určité typy provozu (například ICMP) podle požadavků jejich podniku. 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [zásadách Azure firewall](policy-overview.md).

