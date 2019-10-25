---
title: Ověřování pomocí spravovaných identit – Azure Logic Apps
description: Přístup k prostředkům v jiných Azure Active Directory tenantů bez přihlašování pomocí přihlašovacích údajů nebo tajných kódů pomocí spravované identity
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: fdc5340c9affa7137815577af842aa8b43a552a8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72799607"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Ověřování přístupu k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps

Pokud chcete získat přístup k prostředkům v jiných klientech Azure Active Directory (Azure AD) a ověřit vaši identitu bez přihlášení, vaše aplikace logiky může používat [spravovanou identitu](../active-directory/managed-identities-azure-resources/overview.md) přiřazenou systémem (dřív označovanou jako identita spravované služby nebo MSI), a ne přihlašovací údaje nebo tajné kódy. Azure tuto identitu spravuje za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte zadávat ani otáčet tajné klíče. Tento článek ukazuje, jak nastavit a používat spravovanou identitu přiřazenou systémem ve vaší aplikaci logiky.

Další informace najdete v těchto tématech:

* [Aktivační události a akce, které podporují spravované identity](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Služby Azure, které podporují ověřování Azure AD se spravovanými identitami](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [Podporované typy ověřování při odchozích voláních](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Omezení spravovaných identit pro Logic Apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure, nebo pokud předplatné nemáte, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). Spravovaná identita i cílový prostředek Azure, na kterém chcete mít přístup, musí používat stejné předplatné Azure.

* [Oprávnění správce Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) , která můžou přiřadit role ke spravovaným identitám ve stejném TENANTOVI Azure AD jako cílový prostředek. Pokud chcete spravované identitě udělit přístup k prostředku Azure, musíte do cílového prostředku přidat roli pro tuto identitu.

* Cílový prostředek Azure, ke kterému chcete získat přístup

* Aplikace logiky, která používá [triggery a akce, které podporují spravované identity](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>Povolit identitu přiřazenou systémem

Na rozdíl od uživatelsky přiřazených identit nemusíte ručně vytvářet identitu přiřazenou systémem. Chcete-li nastavit identitu přiřazenou systémem vaší aplikace logiky, můžete použít následující možnosti:

* [Azure Portal](#azure-portal-system-logic-app)
* [Šablony Azure Resource Manager](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Povolit identitu přiřazenou systémem v Azure Portal

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **Identita** > **systém přiřazeno**. V části **stav**vyberte **v** > **Uložit** > **Ano**.

   ![Povolit identitu přiřazenou systémem](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   Vaše aplikace logiky teď může používat identitu přiřazenou systémem, která je zaregistrovaná ve službě Azure Active Directory a je reprezentovaná ID objektu.

   ![ID objektu pro identitu přiřazenou systémem](./media/create-managed-service-identity/object-id.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **ID objektu** | <*identity-Resource-ID*> | Globálně jedinečný identifikátor (GUID), který představuje identitu přiřazenou systémem pro vaši aplikaci logiky ve vašem tenantovi Azure AD |
   ||||

1. Nyní postupujte podle [kroků, které přidávají identitě přístup k prostředku](#access-other-resources).

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Povolit identitu přiřazenou systémem v šabloně Azure Resource Manager

K automatizaci vytváření a nasazování prostředků Azure, jako jsou Logic Apps, můžete použít [šablony Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Pokud chcete pro vaši aplikaci logiky v šabloně povolit spravovanou identitu přiřazenou systémem, přidejte do definice prostředků aplikace logiky v šabloně objekt `identity` a vlastnost `type` podřízenou položku, například:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Když Azure vytvoří definici prostředků aplikace logiky, objekt `identity` získá tyto další vlastnosti:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property (JSON) | Hodnota | Popis |
|-----------------|-------|-------------|
| `principalId` | *ID objektu zabezpečení* <> | Globálně jedinečný identifikátor (GUID) instančního objektu služby pro spravovanou identitu, která představuje vaši aplikaci logiky v tenantovi Azure AD. Tento identifikátor GUID se někdy zobrazuje jako "ID objektu" nebo `objectID`. |
| `tenantId` | <*Azure-AD-tenant-ID*> | Globálně jedinečný identifikátor (GUID), který představuje tenanta Azure AD, ve kterém je teď aplikace logiky členem. V tenantovi služby Azure AD má instanční objekt stejný název jako instance aplikace logiky. |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Udělení přístupu k prostředkům v identitě

Po nastavení spravované identity pro vaši aplikaci logiky můžete [této identitě udělit přístup k jiným prostředkům Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Tuto identitu pak můžete použít k ověřování.

1. V [Azure Portal](https://portal.azure.com)přejděte do prostředku Azure, ke kterému chcete mít přístup ke spravované identitě.

1. V nabídce prostředku vyberte **řízení přístupu (IAM)**  > **přiřazení rolí** , kde můžete zkontrolovat aktuální přiřazení rolí pro daný prostředek. Na panelu nástrojů vyberte **přidat** > **Přidat přiřazení role**.

   ![Vyberte Přidat > přidat přiřazení role.](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Pokud je možnost **Přidat přiřazení role** zakázaná, pravděpodobně nemáte oprávnění. Další informace o oprávněních, která umožňují spravovat role pro prostředky, najdete v tématu [oprávnění role správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. V části **Přidat přiřazení role**vyberte **roli** , která poskytuje vaší identitě potřebný přístup k cílovému prostředku.

   V tomto tématu musí vaše identita mít [roli, která má přístup k objektu BLOB v kontejneru Azure Storage](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights):

   ![Vyberte roli Přispěvatel dat objektů BLOB úložiště.](./media/create-managed-service-identity/assign-role.png)

1. V poli **přiřadit přístup k** vyberte možnost **uživatel, skupina nebo instanční objekt služby Azure AD**.

   ![Vybrat přístup pro identitu přiřazenou systémem](./media/create-managed-service-identity/assign-access-system.png)

1. V poli **Vybrat** vyhledejte a vyberte svou aplikaci logiky.

   ![Vyberte aplikaci logiky pro systémově přiřazenou identitu.](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Jakmile budete mít hotovo, vyberte **Uložit**.

   V seznamu přiřazení role cílového prostředku se teď zobrazuje vybraná spravovaná identita a role.

   ![Do cílového prostředku se přidaly spravované identity a role.](./media/create-managed-service-identity/added-roles-for-identities.png)

1. Nyní postupujte podle [pokynů k ověření přístupu k identitě](#authenticate-access-with-identity) v aktivační události nebo akci, která podporuje spravované identity.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Ověření přístupu pomocí spravované identity

Po [Povolení spravované identity pro vaši aplikaci logiky](#azure-portal-system-logic-app) a [udělení této identity k cílovému prostředku](#access-other-resources)můžete tuto identitu použít v [aktivačních událostech a akcích, které podporují spravované identity](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Pokud máte funkci Azure, kde chcete používat identitu přiřazenou systémem, nejdřív [Povolte ověřování pro službu Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Tyto kroky ukazují, jak používat spravovanou identitu s triggerem nebo akcí prostřednictvím Azure Portal. Pokud chcete určit spravovanou identitu v aktivační události nebo v základní definici JSON akce, přečtěte si téma [ověřování spravované identity](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. Pokud jste to ještě neudělali, přidejte Trigger nebo akci [, která podporuje spravované identity](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Předpokládejme například, že chcete spustit [operaci Snapshot BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) u objektu BLOB v účtu Azure Storage, kde jste předtím nastavili přístup k vaší identitě, ale [konektor Azure Blob Storage](/connectors/azureblob/) tuto operaci momentálně nenabízí. Místo toho můžete použít [akci HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) ke spuštění operace nebo jakékoli jiné [operace REST API služby BLOB Service](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs). V případě ověřování může akce HTTP používat identitu přiřazenou systémem, kterou jste povolili pro vaši aplikaci logiky. Akce HTTP také pomocí těchto vlastností určí prostředek, ke kterému chcete získat přístup:

   * Vlastnost **URI** Určuje adresu URL koncového bodu pro přístup k cílovému prostředku Azure. Tato syntaxe identifikátoru URI obvykle zahrnuje [ID prostředku](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) pro prostředek nebo službu Azure.

   * Vlastnost **Headers** určuje všechny hodnoty hlaviček, které potřebujete nebo chcete zahrnout do žádosti, jako je například verze rozhraní API pro operaci, kterou chcete spustit na cílovém prostředku.

   * Vlastnost querys Určuje **Parametry dotazů,** které je třeba zahrnout do žádosti, jako je například parametr konkrétní operace nebo konkrétní verze rozhraní API v případě potřeby.

   Pokud tedy chcete spustit [operaci objektu BLOB snímku](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob), akce http určuje tyto vlastnosti:

   * **Metoda**: určuje operaci `PUT`.

   * **URI**: Určuje ID prostředku pro soubor Azure Blob Storage v globálním (veřejném) prostředí Azure a používá tuto syntaxi:

     `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}`

   * **Headers**: Určuje `x-ms-blob-type` jako `BlockBlob` a `x-ms-version` jako `2019-02-02` pro operaci objektu BLOB snímku. Další informace najdete v tématu [o hlavičkách žádostí – BLOB snímků](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) a [verzích pro Azure Storage Services](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services).

   * **Dotazy**: Určuje `comp` jako název parametru dotazu a `snapshot` jako hodnotu parametru.

   Tady je příklad akce HTTP, která zobrazuje všechny tyto hodnoty vlastností:

   ![Přidání akce HTTP pro přístup k prostředku Azure](./media/create-managed-service-identity/http-action-example.png)

   Další informace o všech dostupných operacích Azure REST API najdete v referenčních informacích k [azure REST API](https://docs.microsoft.com/rest/api/azure/).

1. V seznamu **ověřování** vyberte **spravovaná identita**. Pokud [je vlastnost **ověřování** podporovaná](logic-apps-securing-a-logic-app.md#add-authentication-outbound) , ale skrytá, otevřete seznam **Přidat nový parametr** a vyberte **ověřování**.

   > [!NOTE]
   > Ne všechny triggery a akce umožňují vybrat typ ověřování. Další informace najdete v tématu [Přidání ověřování do odchozích volání](logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![Ve vlastnosti ověřování vyberte spravovaná identita.](./media/create-managed-service-identity/select-managed-identity.png)

1. Po výběru možnosti **spravovaná identita**se pro některé triggery a akce zobrazí vlastnost **cílová skupina** . Pokud je vlastnost **cílová skupina** podporovaná, ale skrytá, otevřete seznam **Přidat nový parametr** a vyberte **cílovou skupinu**.

1. Ujistěte se, že jste u cílového prostředku nebo služby nastavili hodnotu **cílové skupiny** na ID prostředku. Jinak ve výchozím nastavení vlastnost **cílová skupina** používá id prostředku `https://management.azure.com/`, což je ID prostředku pro Azure Resource Manager.

   > [!IMPORTANT]
   > Ujistěte se, že ID cílového prostředku *přesně odpovídá* hodnotě, kterou Azure Active Directory (AD) očekává, včetně požadovaných koncových lomítek. ID prostředku pro všechny účty Azure Blob Storage například vyžaduje koncové lomítko. ID prostředku pro konkrétní účet úložiště ale nevyžaduje koncové lomítko. Ověřte [ID prostředků služeb Azure, které podporují Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Tento příklad nastaví vlastnost **cílové skupiny** na `https://storage.azure.com/` tak, aby přístupové tokeny používané pro ověřování byly platné pro všechny účty úložiště. Pro určitý účet úložiště ale taky můžete zadat adresu URL kořenové služby `https://fabrikamstorageaccount.blob.core.windows.net`.

   ![Zadejte ID cílového prostředku ve vlastnosti cílová skupina.](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Další informace o autorizaci přístupu ke službě Azure AD pro Azure Storage najdete v těchto tématech:

   * [Autorizace přístupu k Azure Blob a frontám pomocí Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorizovat přístup k Azure Storage pomocí Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>Odebrat identitu přiřazenou systémem

K zastavení používání identity přiřazené systémem pro vaši aplikaci logiky máte tyto možnosti:

* [Azure Portal](#azure-portal-disable)
* [Šablony Azure Resource Manager](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Azure CLI](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

Pokud odstraníte aplikaci logiky, Azure automaticky odebere spravovanou identitu ze služby Azure AD.

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>Odebrat identitu přiřazenou systémem v Azure Portal

V Azure Portal [z aplikace logiky](#disable-identity-logic-app) odeberte identitu přiřazenou systémem a přístup k této identitě [z cílového prostředku](#disable-identity-target-resource).

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>Odebrání identity přiřazené systémem z aplikace logiky

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **Identita** > **systém přiřazeno**. V části **stav**vyberte **vypnuto** > **Uložit** > **Ano**.

   ![Ukončení používání identity přiřazené systémem](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Odebrání přístupu k identitám z prostředků

1. V [Azure Portal](https://portal.azure.com)přejděte do cílového prostředku Azure, ve kterém chcete odebrat přístup pro spravovanou identitu.

1. V nabídce cílového prostředku vyberte **řízení přístupu (IAM)** . Na panelu nástrojů vyberte **přiřazení rolí**.

1. V seznamu role vyberte spravované identity, které chcete odebrat. Na panelu nástrojů vyberte **Odebrat**.

   > [!TIP]
   > Pokud je možnost **Odebrat** zakázaná, pravděpodobně nemáte oprávnění. Další informace o oprávněních, která umožňují spravovat role pro prostředky, najdete v tématu [oprávnění role správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Spravovaná identita je nyní odebrána a již nemá přístup k cílovému prostředku.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Zakázat spravovanou identitu v šabloně Azure Resource Manager

Pokud jste povolili identitu spravovanou systémem aplikace logiky pomocí Azure Resource Manager šablony, nastavte vlastnost `type` podřízenosti objektu `identity` na `None`. Tato akce také odstraní ID objektu zabezpečení pro identitu spravovanou systémem ze služby Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Další kroky

* [Zabezpečený přístup a data v Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
