---
title: Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob
titleSuffix: Azure Storage
description: Naučte se, jak povolit nebo zakázat anonymní přístup k datům objektů BLOB pro účet úložiště. Nastavte nastavení kontejneru pro veřejný přístup tak, aby kontejnery a objekty blob byly dostupné pro anonymní přístup.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: feac7b890c973b1541c5362f860432687082953f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96533872"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob

Azure Storage podporuje volitelný anonymní veřejný přístup pro čtení pro kontejnery a objekty blob. Ve výchozím nastavení se anonymní přístup k vašim datům nikdy nepovoluje. Pokud explicitně nepovolíte anonymní přístup, musí být všechny požadavky na kontejner a jeho objekty blob autorizovány. Když nakonfigurujete nastavení úrovně veřejného přístupu kontejneru tak, aby povoloval anonymní přístup, můžou klienti číst data v tomto kontejneru bez autorizace této žádosti.

> [!WARNING]
> Pokud je pro veřejný přístup nakonfigurován kontejner, může kterýkoli klient číst data v tomto kontejneru. Veřejný přístup představuje potenciální bezpečnostní riziko, takže pokud to váš scénář nevyžaduje, Microsoft doporučuje, abyste ho zakázali pro účet úložiště. Další informace najdete v tématu [zabránění anonymnímu veřejnému přístupu pro čtení kontejnerů a objektů BLOB](anonymous-read-access-prevent.md).

Tento článek popisuje, jak nakonfigurovat anonymní veřejný přístup pro čtení pro kontejner a jeho objekty blob. Informace o tom, jak anonymně přistupovat k datům objektů BLOB z klientské aplikace, najdete v tématu [přístup k veřejným kontejnerům a objektům blob anonymně pomocí .NET](anonymous-read-access-client.md).

## <a name="about-anonymous-public-read-access"></a>Informace o anonymním veřejném přístupu pro čtení

Veřejný přístup k vašim datům je vždycky ve výchozím nastavení zakázaný. Existují dvě samostatná nastavení, která mají vliv na veřejný přístup:

1. **Povolí veřejný přístup k účtu úložiště.** Ve výchozím nastavení účet úložiště umožňuje uživateli s příslušnými oprávněními povolit veřejný přístup ke kontejneru. Data objektu BLOB nejsou pro veřejný přístup k dispozici, pokud uživatel neprovede další krok, který explicitně nakonfiguruje nastavení veřejného přístupu kontejneru.
1. **Nakonfigurujte nastavení veřejného přístupu kontejneru.** Ve výchozím nastavení je nastavení veřejného přístupu kontejneru zakázané, což znamená, že pro každý požadavek na kontejner nebo jeho data se vyžaduje autorizace. Uživatel s příslušnými oprávněními může upravit nastavení veřejného přístupu kontejneru a povolit anonymní přístup pouze v případě, že je pro účet úložiště povolen anonymní přístup.

Následující tabulka shrnuje, jak obě nastavení dohromady ovlivňují veřejný přístup ke kontejneru.

| Nastavení veřejného přístupu | Veřejný přístup je pro kontejner zakázaný (výchozí nastavení). | Veřejný přístup ke kontejneru je nastavený na kontejner. | Veřejný přístup ke kontejneru je nastavený na BLOB. |
|--|--|--|--|
| Pro účet úložiště není povolený veřejný přístup. | Žádný veřejný přístup k žádnému kontejneru v účtu úložiště. | Žádný veřejný přístup k žádnému kontejneru v účtu úložiště. Nastavení účtu úložiště přepisuje nastavení kontejneru. | Žádný veřejný přístup k žádnému kontejneru v účtu úložiště. Nastavení účtu úložiště přepisuje nastavení kontejneru. |
| Pro účet úložiště je povolený veřejný přístup (výchozí nastavení). | Nemáte veřejný přístup k tomuto kontejneru (výchozí konfigurace). | K tomuto kontejneru a jeho objektům blob je povolen veřejný přístup. | Veřejný přístup je povolený pro objekty BLOB v tomto kontejneru, ale ne do kontejneru samotného. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Povolit nebo zakázat veřejný přístup pro čtení pro účet úložiště

Ve výchozím nastavení je účet úložiště nakonfigurovaný tak, aby umožňoval uživateli s příslušnými oprávněními povolit veřejný přístup ke kontejneru. Když je veřejný přístup povolený, může uživatel s příslušnými oprávněními upravit nastavení veřejného přístupu kontejneru a povolit tak anonymní veřejný přístup k datům v tomto kontejneru. Data objektů BLOB nejsou nikdy k dispozici pro veřejný přístup, pokud uživatel neprovede další krok, který explicitně nakonfiguruje nastavení veřejného přístupu kontejneru.

Mějte na paměti, že veřejný přístup ke kontejneru je ve výchozím nastavení vždycky vypnutý a musí být explicitně nakonfigurovaný tak, aby povoloval anonymní požadavky. Bez ohledu na nastavení účtu úložiště nebudou vaše data nikdy k dispozici pro veřejný přístup, pokud uživatel s příslušnými oprávněními neudělá tento další krok, aby umožnil veřejný přístup ke kontejneru.

Nepovolení veřejného přístupu pro účet úložiště zabraňuje anonymnímu přístupu ke všem kontejnerům a objektům blob v tomto účtu. Pokud pro účet není povolený veřejný přístup, není možné nakonfigurovat nastavení veřejného přístupu pro kontejner, aby se povolil anonymní přístup. Pro lepší zabezpečení doporučuje společnost Microsoft zakázat veřejný přístup k účtům úložiště, pokud váš scénář nevyžaduje, aby uživatelé měli přístup k prostředkům objektů BLOB anonymně.

> [!IMPORTANT]
> Nepovolení veřejného přístupu pro účet úložiště přepíše nastavení veřejného přístupu pro všechny kontejnery v tomto účtu úložiště. Pokud pro účet úložiště není povolený veřejný přístup, všechny budoucí anonymní požadavky na daný účet selžou. Před změnou tohoto nastavení nezapomeňte pochopit dopad na klientské aplikace, které můžou přistupovat k datům ve vašem účtu úložiště anonymně. Další informace najdete v tématu [zabránění anonymnímu veřejnému přístupu pro čtení kontejnerů a objektů BLOB](anonymous-read-access-prevent.md).

Pokud chcete povolit nebo zakázat veřejný přístup k účtu úložiště, nakonfigurujte vlastnost **AllowBlobPublicAccess** účtu. Tato vlastnost je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Další informace najdete v tématu [Přehled účtu úložiště](../common/storage-account-overview.md).

Vlastnost **AllowBlobPublicAccess** není ve výchozím nastavení nastavena pro účet úložiště a nevrací hodnotu, dokud ji explicitně nenastavíte. Účet úložiště povoluje veřejný přístup, pokud je hodnota vlastnosti buď **null** , nebo **true**.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete povolit nebo zakázat veřejný přístup k účtu úložiště v Azure Portal, postupujte podle následujících kroků:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části **Nastavení** vyhledejte nastavení **Konfigurace** .
1. Nastavte **veřejný přístup k objektu BLOB** na **povoleno** nebo **zakázáno**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Snímek obrazovky ukazující, jak povolit nebo zakázat veřejný přístup objektů BLOB pro účet":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete povolit nebo zakázat veřejný přístup k účtu úložiště pomocí PowerShellu, nainstalujte [Azure PowerShell verze 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) nebo novější. Dále nakonfigurujte vlastnost **AllowBlobPublicAccess** pro nový nebo existující účet úložiště.

Následující příklad vytvoří účet úložiště a explicitně nastaví vlastnost **AllowBlobPublicAccess** na **hodnotu true**. Pak aktualizuje účet úložiště, aby vlastnost **AllowBlobPublicAccess** byla nastavena na **hodnotu false**. Příklad také načte hodnotu vlastnosti v každém případě. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete povolit nebo zakázat veřejný přístup k účtu úložiště pomocí Azure CLI, nainstalujte Azure CLI verze 2.9.0 nebo novější. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Dále nakonfigurujte vlastnost **allowBlobPublicAccess** pro nový nebo existující účet úložiště.

Následující příklad vytvoří účet úložiště a explicitně nastaví vlastnost **allowBlobPublicAccess** na **hodnotu true**. Pak aktualizuje účet úložiště, aby vlastnost **allowBlobPublicAccess** byla nastavena na **hodnotu false**. Příklad také načte hodnotu vlastnosti v každém případě. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[Šablona](#tab/template)

Pokud chcete povolit nebo zakázat veřejný přístup k účtu úložiště se šablonou, vytvořte šablonu s vlastností **AllowBlobPublicAccess** nastavenou na **hodnotu true** nebo **false**. Následující postup popisuje, jak vytvořit šablonu v Azure Portal.

1. V Azure Portal klikněte na možnost **vytvořit prostředek**.
1. V **části Hledat na Marketplace** zadejte **šablonu Deployment** a potom stiskněte **ENTER**.
1. Zvolte **template Deployment (nasadit pomocí vlastních šablon) (Preview)**, zvolte **vytvořit** a potom **v editoru zvolte sestavit vlastní šablonu**.
1. V editoru šablon vložte následující JSON pro vytvoření nového účtu a nastavte vlastnost **AllowBlobPublicAccess** na **hodnotu true** nebo **false**. Nezapomeňte nahradit zástupné symboly v lomených závorkách vlastními hodnotami.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Uložte šablonu.
1. Zadejte parametr skupiny prostředků a potom kliknutím na tlačítko **Revize + vytvořit** nasaďte šablonu a vytvořte účet úložiště s nakonfigurovanou vlastností **allowBlobPublicAccess** .

---

> [!NOTE]
> Nepovolení veřejného přístupu pro účet úložiště nemá vliv na žádné statické weby hostované v tomto účtu úložiště. Kontejner **$Web** je vždy veřejně přístupný.
>
> Po aktualizaci nastavení veřejného přístupu pro účet úložiště může trvat až 30 sekund, než se změna úplně rozšíří.

Povolení nebo zákaz veřejného přístupu objektu BLOB vyžaduje verzi 2019-04-01 nebo novější poskytovatele prostředků Azure Storage. Další informace najdete v tématu [REST API poskytovatele prostředků Azure Storage](/rest/api/storagerp/).

Příklady v této části ukazují, jak si přečíst vlastnost **AllowBlobPublicAccess** pro účet úložiště, abyste zjistili, jestli je veřejný přístup aktuálně povolený nebo zakázaný. Další informace o tom, jak ověřit, že nastavení veřejného přístupu účtu je nakonfigurované tak, aby zabránilo anonymnímu přístupu, najdete v tématu věnovaném [napravení anonymního přístupu](anonymous-read-access-prevent.md#remediate-anonymous-public-access).

## <a name="set-the-public-access-level-for-a-container"></a>Nastavení úrovně veřejného přístupu pro kontejner

Pokud chcete anonymním uživatelům udělit přístup pro čtení ke kontejneru a jeho objektům blob, nejdřív povolte veřejný přístup k účtu úložiště a pak nastavte úroveň veřejného přístupu kontejneru. Pokud je pro účet úložiště odepřený veřejný přístup, nebudete moct pro kontejner nakonfigurovat veřejný přístup.

Pokud je pro účet úložiště povolen veřejný přístup, můžete nakonfigurovat kontejner s následujícími oprávněními:

- **Žádný veřejný přístup pro čtení:** K kontejneru a jeho objektům blob lze přistupovat pouze s autorizovaným požadavkem. Tato možnost je výchozí pro všechny nové kontejnery.
- **Veřejný přístup pro čtení jenom pro objekty BLOB:** Objekty BLOB v kontejneru lze číst anonymním požadavkem, ale data kontejneru nejsou k dispozici anonymně. Anonymní klienti nemohou vytvořit výčet objektů BLOB v rámci kontejneru.
- **Veřejný přístup pro čtení kontejneru a jeho objektů BLOB:** Data kontejneru a objektu BLOB může číst anonymní požadavek, s výjimkou nastavení oprávnění kontejneru a metadat kontejneru. Klienti mohou vytvořit výčet objektů BLOB v kontejneru anonymním požadavkem, ale nemohou vytvořit výčet kontejnerů v rámci účtu úložiště.

Úroveň veřejného přístupu pro jednotlivý objekt BLOB se nedá změnit. Úroveň veřejného přístupu je nastavena pouze na úrovni kontejneru. Úroveň veřejného přístupu kontejneru můžete nastavit při vytváření kontejneru, nebo můžete nastavení aktualizovat v existujícím kontejneru.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Chcete-li aktualizovat úroveň veřejného přístupu pro jeden nebo více existujících kontejnerů v Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal přejděte na přehled svého účtu úložiště.
1. V části **BLOB Service** v okně nabídky vyberte **kontejnery**.
1. Vyberte kontejnery, pro které chcete nastavit úroveň veřejného přístupu.
1. Pomocí tlačítka **změnit úroveň přístupu** můžete zobrazit nastavení veřejného přístupu.
1. Vyberte požadovanou úroveň veřejného přístupu v rozevíracím seznamu **úroveň veřejného přístupu** a kliknutím na tlačítko OK tuto změnu použijte u vybraných kontejnerů.

    ![Snímek obrazovky ukazující, jak nastavit úroveň veřejného přístupu na portálu](./media/anonymous-read-access-configure/configure-public-access-container.png)

Pokud je pro účet úložiště zakázaný veřejný přístup, nedá se nastavit úroveň veřejného přístupu kontejneru. Pokud se pokusíte nastavit úroveň veřejného přístupu kontejneru, uvidíte, že nastavení je zakázané, protože pro tento účet není povolený veřejný přístup.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Snímek obrazovky zobrazující, že nastavení úrovně veřejného přístupu kontejneru je blokované, když je veřejný přístup nepovolený":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li aktualizovat úroveň veřejného přístupu pro jeden nebo více kontejnerů pomocí prostředí PowerShell, zavolejte příkaz [set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) . Autorizaci této operace provedete předáním klíče účtu, připojovacího řetězce nebo sdíleného přístupového podpisu (SAS). Operace [set ACL kontejneru](/rest/api/storageservices/set-container-acl) , která nastavuje úroveň veřejného přístupu kontejneru, nepodporuje autorizaci pomocí Azure AD. Další informace najdete v tématu [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Následující příklad vytvoří kontejner s povoleným veřejným přístupem a pak aktualizuje nastavení veřejného přístupu kontejneru tak, aby povoloval anonymní přístup ke kontejneru a jeho objektům blob. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

Pokud je pro účet úložiště zakázaný veřejný přístup, nedá se nastavit úroveň veřejného přístupu kontejneru. Pokud se pokusíte nastavit úroveň veřejného přístupu kontejneru, Azure Storage vrátí chybu oznamující, že v účtu úložiště není povolený veřejný přístup.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li aktualizovat úroveň veřejného přístupu pro jeden nebo více kontejnerů pomocí rozhraní příkazového řádku Azure CLI, použijte příkaz [AZ Storage Container set Permission](/cli/azure/storage/container#az-storage-container-set-permission) . Autorizaci této operace provedete předáním klíče účtu, připojovacího řetězce nebo sdíleného přístupového podpisu (SAS). Operace [set ACL kontejneru](/rest/api/storageservices/set-container-acl) , která nastavuje úroveň veřejného přístupu kontejneru, nepodporuje autorizaci pomocí Azure AD. Další informace najdete v tématu [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Následující příklad vytvoří kontejner s povoleným veřejným přístupem a pak aktualizuje nastavení veřejného přístupu kontejneru tak, aby povoloval anonymní přístup ke kontejneru a jeho objektům blob. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

Pokud je pro účet úložiště zakázaný veřejný přístup, nedá se nastavit úroveň veřejného přístupu kontejneru. Pokud se pokusíte nastavit úroveň veřejného přístupu kontejneru, Azure Storage vrátí chybu oznamující, že v účtu úložiště není povolený veřejný přístup.

# <a name="template"></a>[Šablona](#tab/template)

Není k dispozici.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Podívejte se na nastavení veřejného přístupu pro sadu kontejnerů.

Je možné zkontrolovat, které kontejnery v jednom nebo více účtech úložiště jsou nakonfigurovány pro veřejný přístup, a to tak, že seznam kontejnerů a kontrola nastavení veřejného přístupu. Tento přístup je praktickou možností, když účet úložiště neobsahuje velký počet kontejnerů, nebo když kontrolujete nastavení v rámci malého počtu účtů úložiště. Výkon ale může být zhoršený, pokud se pokusíte vytvořit výčet velkého počtu kontejnerů.

Následující příklad používá PowerShell k získání nastavení veřejného přístupu pro všechny kontejnery v účtu úložiště. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Další kroky

- [Zabránit anonymnímu veřejnému přístupu pro čtení kontejnerů a objektů BLOB](anonymous-read-access-prevent.md)
- [Anonymní přístup k veřejným kontejnerům a objektům blob pomocí .NET](anonymous-read-access-client.md)
- [Autorizace přístupu k Azure Storage](../common/storage-auth.md)
