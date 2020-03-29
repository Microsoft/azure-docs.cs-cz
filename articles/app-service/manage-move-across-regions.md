---
title: Přesunutí aplikace do jiné oblasti
description: Přečtěte si, jak přesunout prostředky služby App Service z jedné oblasti do druhé.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925706"
---
# <a name="move-an-app-service-app-to-another-region"></a>Přesunutí aplikace služby App Service do jiné oblasti

Tento článek popisuje, jak přesunout prostředky služby App Service do jiné oblasti Azure. Prostředky můžete přesunout do jiné oblasti z mnoha důvodů. Chcete-li například využít výhod nové oblasti Azure, nasadit funkce nebo služby dostupné pouze v určitých oblastech, splnit požadavky na interní zásady a zásady správného řízení nebo v reakci na požadavky na plánování kapacity.

Prostředky služby App Service jsou specifické pro oblast a nelze je přesouvat napříč oblastmi. Musíte vytvořit kopii stávajících prostředků služby App Service v cílové oblasti, přesunout obsah do nové aplikace. Pokud vaše zdrojová aplikace používá vlastní doménu, můžete ji po dokončení [migrovat do nové aplikace v cílové oblasti.](manage-custom-dns-migrate-domain.md)

Chcete-li usnadnit kopírování aplikace, můžete [naklonovat jednotlivé aplikace služby App Service](app-service-web-app-cloning.md) do plánu [služby](app-service-web-app-cloning.md#current-restrictions)App Service v jiné oblasti, ale má omezení , zejména to, že nepodporuje linuxové aplikace.

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že aplikace App Service je v oblasti Azure, ze kterého chcete přesunout.
- Ujistěte se, že cílová oblast podporuje službu App Service a všechny související služby, jejichž prostředky, které chcete přesunout.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Příprava

Identifikujte všechny prostředky služby App Service, které právě používáte. Například:

- Aplikace služby App Service
- [Plány služby App Service](overview-hosting-plans.md)
- [Nasazovací sloty](deploy-staging-slots.md)
- [Vlastní domény zakoupené v Azure](manage-custom-dns-buy-domain.md)
- [Certifikáty SSL](configure-ssl-certificate.md)
- [Integrace virtuální sítě Azure](web-sites-integrate-with-vnet.md)
- [Hybridní připojení](app-service-hybrid-connections.md).
- [Spravované identity](overview-managed-identity.md)
- [Nastavení zálohování](manage-backup.md)

Některé prostředky, jako jsou importované certifikáty nebo hybridní připojení, obsahují integraci s jinými službami Azure. Informace o tom, jak přesunout tyto prostředky napříč oblastmi, naleznete v dokumentaci k příslušným službám.

## <a name="move"></a>Přesunout

1. [Vytvořte zálohování zdrojové aplikace](manage-backup.md).
1. [Vytvořte aplikaci v novém plánu služby App Service v cílové oblasti](app-service-plan-manage.md#create-an-app-service-plan).
2. [Obnovení zálohy v cílové aplikaci](web-sites-restore.md)
2. Pokud používáte vlastní doménu, [spojte ji preventivně s cílovou aplikací](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) `awverify.` a [povolte doménu v cílové aplikaci](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Nakonfigurujte vše ostatní v cílové aplikaci tak, aby bylo stejné jako zdrojová aplikace a ověřte konfiguraci.
4. Až budete připraveni na to, aby vlastní doména ukazovala na cílovou aplikaci, [přemapujte název domény](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

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

Odstraňte zdrojovou aplikaci a plán služby App Service. [Plán služby App Service v nesvobodné úrovni nese poplatek, i když v něm není spuštěna žádná aplikace.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Další kroky

[Klonování aplikací služby Azure pomocí PowerShellu](app-service-web-app-cloning.md)