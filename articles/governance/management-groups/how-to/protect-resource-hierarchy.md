---
title: Jak chránit hierarchii prostředků – zásady správného řízení Azure
description: Naučte se chránit svoji hierarchii prostředků pomocí nastavení hierarchie, která zahrnují nastavení výchozí skupiny pro správu.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 19d699b54a9979df1030c0f6e294d5a4492f2853
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469775"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Jak chránit hierarchii prostředků

Vaše prostředky, skupiny prostředků, předplatná, skupiny pro správu a tenant společně tvoří hierarchii prostředků. Nastavení v kořenové skupině pro správu, jako jsou například vlastní role Azure nebo přiřazení zásad Azure Policy, můžou mít vliv na všechny prostředky v hierarchii prostředků. Je důležité chránit hierarchii prostředků před změnami, které by mohly mít negativní vliv na všechny prostředky.

Skupiny pro správu teď mají nastavení hierarchie, které umožňuje správcům tenanta řídit toto chování. Tento článek obsahuje všechna dostupná nastavení hierarchie a jejich nastavení.

## <a name="rbac-permissions-for-hierarchy-settings"></a>Oprávnění RBAC pro nastavení hierarchie

Konfigurace nastavení hierarchie vyžaduje následující dvě operace RBAC v kořenové skupině pro správu:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Tyto operace umožňují uživateli číst a aktualizovat nastavení hierarchie. Operace neposkytují žádný jiný přístup k hierarchii skupiny pro správu ani k prostředkům v hierarchii. Obě tyto operace jsou k dispozici ve **Správci nastavení hierarchie**integrované role Azure.

## <a name="setting---default-management-group"></a>Nastavení – výchozí skupina pro správu

Ve výchozím nastavení je nové předplatné přidané v rámci tenanta přidáno jako člen skupiny pro správu root. Pokud jsou přiřazování zásad, řízení přístupu na základě role (RBAC) a další konstruktory zásad správného řízení přiřazeny ke skupině root management, tyto nové odběry se okamžitě projeví. Z tohoto důvodu mnoho organizací tyto konstrukce nepoužije v kořenové skupině pro správu, a to i v případě, že je to požadované místo pro jejich přiřazení. V ostatních případech je pro nové odběry žádoucí přísnější sada ovládacích prvků, ale neměla by být přiřazena ke všem předplatným. Toto nastavení podporuje jak případy použití.

Díky tomu, že je možné definovat výchozí skupinu pro správu pro nové odběry, můžete použít konstrukty zásad správného řízení v rámci organizace v kořenové skupině pro správu a použít samostatnou skupinu pro správu s přiřazením zásad nebo přiřazení rolí Azure, které jsou víc vhodné k novému předplatnému.

### <a name="set-default-management-group-in-portal"></a>Nastavit výchozí skupinu pro správu na portálu

Pokud chcete nakonfigurovat toto nastavení v Azure Portal, postupujte takto:

1. Pomocí panelu hledání vyhledejte a vyberte možnost skupiny pro správu.

1. V kořenové skupině pro správu vyberte **Podrobnosti** vedle názvu skupiny pro správu.

1. V části **Nastavení**vyberte **Nastavení hierarchie**.

1. Vyberte tlačítko **změnit výchozí skupinu pro správu** .

   > [!NOTE]
   > Pokud je tlačítko **změnit výchozí skupinu pro správu** zakázané, skupina pro správu, kterou si prohlížíte, není kořenová skupina pro správu nebo váš objekt zabezpečení nemá potřebná oprávnění ke změně nastavení hierarchie.

1. Vyberte skupinu pro správu z hierarchie a použijte tlačítko **Vybrat** .

### <a name="set-default-management-group-with-rest-api"></a>Nastavit výchozí skupinu pro správu pomocí REST API

Pro konfiguraci tohoto nastavení pomocí REST API se zavolá koncový bod [Nastavení hierarchie](/rest/api/resources/hierarchysettings) . K tomu použijte následující REST API identifikátor URI a formát textu. Nahraďte `{rootMgID}` ID vaší kořenové skupiny pro správu a `{defaultGroupID}` ID skupiny pro správu, která se má stát výchozí skupinou pro správu:

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Text požadavku

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Chcete-li nastavit výchozí skupinu pro správu zpět na kořenovou skupinu pro správu, použijte stejný koncový bod a nastavte **defaultManagementGroup** na hodnotu `/providers/Microsoft.Management/managementGroups/{rootMgID}` .

## <a name="setting---require-authorization"></a>Nastavení-vyžadovat autorizaci

Každý uživatel ve výchozím nastavení může vytvořit nové skupiny pro správu v rámci tenanta. Správci tenanta si můžou chtít poskytnout tato oprávnění jenom konkrétním uživatelům, aby zachovali konzistenci a shodu v hierarchii skupin pro správu. Pokud je tato možnost povolena, uživatel vyžaduje `Microsoft.Management/managementGroups/write` operaci pro kořenovou skupinu pro správu pro vytvoření nových podřízených skupin pro správu.

### <a name="set-require-authorization-in-portal"></a>Nastavit vyžadovat autorizaci na portálu

Pokud chcete nakonfigurovat toto nastavení v Azure Portal, postupujte takto:

1. Pomocí panelu hledání vyhledejte a vyberte možnost skupiny pro správu.

1. V kořenové skupině pro správu vyberte **Podrobnosti** vedle názvu skupiny pro správu.

1. V části **Nastavení**vyberte **Nastavení hierarchie**.

1. Přepněte **oprávnění vyžadovat pro vytváření nových skupin pro správu.** možnost na zapnuto.

   > [!NOTE]
   > Pokud **požadavek vyžaduje oprávnění k vytváření nových skupin pro správu.** přepínač je zakázaný, buď není zobrazená skupina pro správu kořenová skupina pro správu, nebo váš objekt zabezpečení nemá potřebná oprávnění ke změně nastavení hierarchie.

### <a name="set-require-authorization-with-rest-api"></a>Nastavit vyžadovat autorizaci pomocí REST API

Pro konfiguraci tohoto nastavení pomocí REST API se zavolá koncový bod [Nastavení hierarchie](/rest/api/resources/hierarchysettings) . K tomu použijte následující REST API identifikátor URI a formát textu. Tato hodnota je _logická_hodnota, a proto pro ni zadejte hodnotu **true** nebo **false** . Hodnota **true** povolí tuto metodu ochrany vaší hierarchie skupiny pro správu:

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Text požadavku

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Chcete-li nastavení vrátit zpět, použijte stejný koncový bod a nastavte **requireAuthorizationForGroupCreation** na hodnotu **false**.

## <a name="next-steps"></a>Další kroky

Další informace o řešeních pro správu najdete v následujících tématech:

- [Vytváření skupin pro správu pro organizaci prostředků Azure](../create.md)
- [Jak měnit, odstraňovat nebo spravovat skupiny pro správu](../manage.md)
