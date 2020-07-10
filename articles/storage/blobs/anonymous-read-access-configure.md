---
title: Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob
titleSuffix: Azure Storage
description: Naučte se, jak povolit nebo zakázat anonymní přístup k datům objektů BLOB pro účet úložiště. Nastavte nastavení kontejneru pro veřejný přístup tak, aby kontejnery a objekty blob byly dostupné pro anonymní přístup.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: af589874021baaf04a423b7bbaa0e36528eda93c
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209414"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob

Azure Storage podporuje anonymní veřejný přístup pro čtení kontejnerů a objektů BLOB. Ve výchozím nastavení musí být všechny požadavky na kontejner a jeho objekty blob autorizované pomocí Azure Active Directory (Azure AD) nebo autorizace pomocí sdíleného klíče. Když nakonfigurujete nastavení úrovně veřejného přístupu kontejneru tak, aby povoloval anonymní přístup, můžou klienti číst data v tomto kontejneru bez autorizace této žádosti.

> [!WARNING]
> Pokud je pro veřejný přístup nakonfigurován kontejner, může kterýkoli klient číst data v tomto kontejneru. Veřejný přístup představuje potenciální bezpečnostní riziko, takže pokud to váš scénář nevyžaduje, Microsoft doporučuje, abyste ho pro účet úložiště vypnuli. Další informace najdete v tématu [zabránění anonymnímu veřejnému přístupu pro čtení kontejnerů a objektů BLOB](anonymous-read-access-prevent.md).

Chcete-li nakonfigurovat veřejný přístup ke kontejneru, je nutné provést dva kroky:

1. Povolit veřejný přístup k účtu úložiště
1. Konfigurace nastavení veřejného přístupu kontejneru

Tento článek popisuje, jak nakonfigurovat anonymní veřejný přístup pro čtení pro kontejner a jeho objekty blob. Informace o tom, jak anonymně přistupovat k datům objektů BLOB z klientské aplikace, najdete v tématu [přístup k veřejným kontejnerům a objektům blob anonymně pomocí .NET](anonymous-read-access-client.md).

## <a name="enable-or-disable-public-read-access-for-a-storage-account"></a>Povolení nebo zakázání veřejného přístupu pro čtení pro účet úložiště

Ve výchozím nastavení je veřejný přístup povolen pro účet úložiště. Zakázáním veřejného přístupu předejdete všem anonymním přístupům k kontejnerům a objektům blob v tomto účtu. Pro zvýšení zabezpečení doporučuje společnost Microsoft zakázat veřejný přístup k účtům úložiště, pokud váš scénář nevyžaduje, aby uživatelé měli přístup k prostředkům objektů BLOB anonymně.

> [!WARNING]
> Zakázání veřejného přístupu pro účet úložiště přepíše nastavení veřejného přístupu pro všechny kontejnery v tomto účtu úložiště. Když je pro účet úložiště zakázaný veřejný přístup, všechny budoucí anonymní požadavky na daný účet selžou.

Pokud chcete povolit nebo zakázat veřejný přístup k účtu úložiště, nakonfigurujte vlastnost **blobPublicAccess** účtu pomocí Azure Portal nebo Azure CLI. Tato vlastnost je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Další informace najdete v tématu [Přehled účtu úložiště](../common/storage-account-overview.md).

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete povolit nebo zakázat veřejný přístup k účtu úložiště v Azure Portal, postupujte podle následujících kroků:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části **Nastavení**vyhledejte nastavení **Konfigurace** .
1. Nastavte **veřejný přístup k objektu BLOB** na **zakázáno** nebo **povoleno**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Snímek obrazovky ukazující, jak povolit nebo zakázat veřejný přístup objektů BLOB pro účet":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete povolit nebo zakázat veřejný přístup pro účet úložiště pomocí Azure CLI, nejdřív Získejte ID prostředku pro svůj účet úložiště voláním příkazu [AZ Resource show](/cli/azure/resource#az-resource-show) . Potom zavolejte příkaz [AZ Resource Update](/cli/azure/resource#az-resource-update) a nastavte vlastnost **allowBlobPublicAccess** pro účet úložiště. Pokud chcete povolit veřejný přístup, nastavte vlastnost **allowBlobPublicAccess** na hodnotu true. Pokud ho chcete zakázat, nastavte ho na **false**.

Následující příklad zakáže přístup k veřejnému objektu BLOB pro účet úložiště. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

Pokud chcete zjistit, jestli je v Azure CLI povolený veřejný přístup, zavolejte příkaz [AZ Resource show](/cli/azure/resource#az-resource-show) a získejte dotaz na vlastnost **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

---

> [!NOTE]
> Zakázání veřejného přístupu pro účet úložiště nemá vliv na žádné statické weby hostované v tomto účtu úložiště. Kontejner **$Web** je vždy veřejně přístupný.

## <a name="check-the-public-access-setting-for-a-storage-account"></a>Podívejte se na nastavení veřejného přístupu pro účet úložiště.

Pokud chcete zjistit nastavení veřejného přístupu pro účet úložiště, Získejte hodnotu vlastnosti **allowBlobPublicAccess** . Pokud chcete tuto vlastnost pro velké číslo účtů úložiště ověřit najednou, použijte Průzkumníka Azure Resource graphu.

Vlastnost **allowBlobPublicAccess** není nastavena ve výchozím nastavení a nevrací hodnotu, dokud ji explicitně nenastavíte. Účet úložiště se standardně povoluje pro přístup k veřejnému přístupu, pokud má vlastnost hodnotu null.

### <a name="check-the-public-access-setting-for-a-single-storage-account"></a>Podívejte se na nastavení veřejného přístupu pro jeden účet úložiště.

Pokud chcete zjistit nastavení veřejného přístupu pro jeden účet úložiště pomocí Azure CLI, zavolejte příkaz [AZ Resource show](/cli/azure/resource#az-resource-show) a dotaz na vlastnost **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-the-public-access-setting-for-a-set-of-storage-accounts"></a>Podívejte se na nastavení veřejného přístupu pro sadu účtů úložiště.

Pokud chcete zjistit nastavení veřejného přístupu v rámci sady účtů úložiště s optimálním výkonem, můžete použít Průzkumníka Azure Resource graphu v Azure Portal. Další informace o používání Průzkumníka grafů prostředků najdete v tématu [rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](/azure/governance/resource-graph/first-query-portal).

Když spustíte následující dotaz, v Průzkumníku grafu prostředků se vrátí seznam účtů úložiště a zobrazí se hodnota vlastnosti **allowBlobPublicAccess** pro každý účet:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>Nastavení úrovně veřejného přístupu pro kontejner

Pokud chcete anonymním uživatelům udělit přístup pro čtení ke kontejneru a jeho objektům blob, nejdřív povolte veřejný přístup k účtu úložiště a pak nastavte úroveň veřejného přístupu kontejneru. Pokud je pro účet úložiště zakázaný veřejný přístup, nebudete moct pro kontejner nakonfigurovat veřejný přístup.

Pokud je pro účet úložiště povolený veřejný přístup, můžete nakonfigurovat kontejner s následujícími oprávněními:

- **Žádný veřejný přístup pro čtení:** K kontejneru a jeho objektům blob lze přistupovat pouze s autorizovaným požadavkem. Tato možnost je výchozí pro všechny nové kontejnery.
- **Veřejný přístup pro čtení jenom pro objekty BLOB:** Objekty BLOB v kontejneru lze číst anonymním požadavkem, ale data kontejneru nejsou k dispozici anonymně. Anonymní klienti nemohou vytvořit výčet objektů BLOB v rámci kontejneru.
- **Veřejný přístup pro čtení kontejneru a jeho objektů BLOB:** Data kontejneru a objektu BLOB může číst anonymní požadavek, s výjimkou nastavení oprávnění kontejneru a metadat kontejneru. Klienti mohou vytvořit výčet objektů BLOB v kontejneru anonymním požadavkem, ale nemohou vytvořit výčet kontejnerů v rámci účtu úložiště.

Úroveň veřejného přístupu pro jednotlivý objekt BLOB se nedá změnit. Úroveň veřejného přístupu je nastavena pouze na úrovni kontejneru.

Pokud chcete nastavit úroveň veřejného přístupu kontejneru, použijte Azure Portal nebo Azure CLI. Můžete nastavit úroveň veřejného přístupu kontejneru při vytváření kontejneru nebo aktualizovat toto nastavení v existujícím kontejneru.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Chcete-li aktualizovat úroveň veřejného přístupu pro jeden nebo více existujících kontejnerů v Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal přejděte na přehled svého účtu úložiště.
1. V části **BLOB Service** v okně nabídky vyberte **kontejnery**.
1. Vyberte kontejnery, pro které chcete nastavit úroveň veřejného přístupu.
1. Pomocí tlačítka **změnit úroveň přístupu** můžete zobrazit nastavení veřejného přístupu.
1. Vyberte požadovanou úroveň veřejného přístupu v rozevíracím seznamu **úroveň veřejného přístupu** a kliknutím na tlačítko OK tuto změnu použijte u vybraných kontejnerů.

    ![Snímek obrazovky ukazující, jak nastavit úroveň veřejného přístupu na portálu](./media/anonymous-read-access-configure/configure-public-access-container.png)

Pokud je pro účet úložiště zakázaný veřejný přístup, nedá se nastavit úroveň veřejného přístupu kontejneru. Pokud se pokusíte nastavit úroveň veřejného přístupu kontejneru, uvidíte, že nastavení je zakázané, protože pro tento účet je zakázaný veřejný přístup.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Snímek obrazovky zobrazující, že nastavení úrovně veřejného přístupu kontejneru je blokované, když je zakázaný veřejný přístup":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li aktualizovat úroveň veřejného přístupu pro jeden nebo více kontejnerů pomocí rozhraní příkazového řádku Azure CLI, použijte příkaz [AZ Storage Container set Permission](/cli/azure/storage/container#az-storage-container-set-permission) . Autorizaci této operace provedete předáním klíče účtu, připojovacího řetězce nebo sdíleného přístupového podpisu (SAS). Operace [set ACL kontejneru](/rest/api/storageservices/set-container-acl) , která nastavuje úroveň veřejného přístupu kontejneru, nepodporuje autorizaci pomocí Azure AD. Další informace najdete v tématu [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Následující příklad nastaví nastavení veřejného přístupu pro kontejner, aby povoloval anonymní přístup ke kontejneru a jeho objektům blob. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

Pokud je pro účet úložiště zakázaný veřejný přístup, nedá se nastavit úroveň veřejného přístupu kontejneru. Pokud se pokusíte nastavit úroveň veřejného přístupu kontejneru, dojde k chybě, která značí, že v účtu úložiště není povolený veřejný přístup.

---

## <a name="check-the-container-public-access-setting"></a>Ověřte nastavení veřejného přístupu ke kontejneru

Pokud chcete zjistit nastavení veřejného přístupu pro jeden nebo více kontejnerů, můžete použít Azure Portal, PowerShell, rozhraní příkazového řádku Azure, jednu z klientských knihoven Azure Storage nebo poskytovatele prostředků Azure Storage. Následující části obsahují několik příkladů.  

### <a name="check-the-public-access-setting-for-a-single-container"></a>Ověřte nastavení veřejného přístupu pro jeden kontejner.

Pokud chcete pro jeden nebo více kontejnerů pomocí Azure CLI získat úroveň veřejného přístupu, zavolejte příkaz [AZ Storage Container show Permission](/cli/azure/storage/container#az-storage-container-show-permission) . Autorizaci této operace provedete předáním klíče účtu, připojovacího řetězce nebo sdíleného přístupového podpisu (SAS). Operace [získat seznam ACL kontejneru](/rest/api/storageservices/get-container-acl) , která vrací úroveň veřejného přístupu kontejneru, nepodporuje autorizaci se službou Azure AD. Další informace najdete v tématu [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Následující příklad přečte nastavení veřejného přístupu pro kontejner. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Podívejte se na nastavení veřejného přístupu pro sadu kontejnerů.

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