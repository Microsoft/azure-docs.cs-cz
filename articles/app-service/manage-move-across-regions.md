---
title: Přesunutí aplikace do jiné oblasti
description: Přečtěte si, jak přesunout App Service prostředky z jedné oblasti do druhé.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 87a2da31802a8b6858e875c23ef1dbd2d6d006bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86524850"
---
# <a name="move-an-app-service-resource-to-another-region"></a>Přesunutí prostředku App Service do jiné oblasti

Tento článek popisuje, jak přesunout prostředky App Service do jiné oblasti Azure. Prostředky můžete přesunout do jiné oblasti z řady důvodů. Například pro využití nové oblasti Azure, k nasazení funkcí nebo služeb dostupných pouze v konkrétních oblastech, pro splnění požadavků na požadavky na interní zásady a zásady správného řízení nebo v reakci na požadavky na plánování kapacity.

Prostředky App Service jsou specifické pro oblast a nelze je přesouvat mezi oblastmi. Musíte vytvořit kopii stávajících prostředků App Service v cílové oblasti a pak přesunout obsah do nové aplikace. Pokud vaše zdrojová aplikace používá vlastní doménu, můžete ji po dokončení [migrovat do nové aplikace v cílové oblasti](manage-custom-dns-migrate-domain.md) .

Pro snazší kopírování vaší aplikace můžete [naklonovat jednotlivé App Service aplikace](app-service-web-app-cloning.md) do plánu App Service v jiné oblasti, ale mají [omezení](app-service-web-app-cloning.md#current-restrictions), zejména v případě, že nepodporují aplikace pro Linux.

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je aplikace App Service v oblasti Azure, ze které chcete přejít.
- Ujistěte se, že cílová oblast podporuje App Service a všechny související služby, jejichž prostředky chcete přesunout.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Příprava

Identifikujte všechny App Service prostředky, které aktuálně používáte. Například:

- Aplikace služby App Service
- [Plány služby App Service](overview-hosting-plans.md)
- [Nasazovací sloty](deploy-staging-slots.md)
- [Vlastní domény zakoupené v Azure](manage-custom-dns-buy-domain.md)
- [Certifikáty SSL](configure-ssl-certificate.md)
- [Integrace se službou Azure Virtual Network](web-sites-integrate-with-vnet.md)
- [Hybridní připojení](app-service-hybrid-connections.md).
- [Spravované identity](overview-managed-identity.md)
- [Nastavení zálohování](manage-backup.md)

Některé prostředky, jako jsou importované certifikáty nebo hybridní připojení, obsahují integraci s dalšími službami Azure. Informace o tom, jak tyto prostředky přesunout do různých oblastí, najdete v dokumentaci k příslušným službám.

## <a name="move"></a>Přesunout

1. [Vytvořte zálohu zdrojové aplikace](manage-backup.md).
1. [Vytvořte aplikaci v novém plánu App Service v cílové oblasti](app-service-plan-manage.md#create-an-app-service-plan).
2. [Obnovení zálohy v cílové aplikaci](web-sites-restore.md)
2. Pokud používáte vlastní doménu, [vytvořte ji bez přerušení v cílové aplikaci](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) `awverify.` a [Povolte doménu v cílové aplikaci](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Nakonfigurujte vše ostatní v cílové aplikaci tak, aby byla stejná jako zdrojová aplikace a ověřte svou konfiguraci.
4. Až budete připraveni, aby vlastní doména odkazovala na cílovou aplikaci, [přemapujte název domény](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Vyčištění zdrojových prostředků

Odstraňte zdrojovou aplikaci a plán App Service. [Plán App Service v nebezplatné úrovni účtuje poplatek, a to i v případě, že v něm není spuštěná žádná aplikace.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Další kroky

[Azure App Service klonování aplikace pomocí PowerShellu](app-service-web-app-cloning.md)