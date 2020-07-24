---
title: Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob
titleSuffix: Azure Storage
description: Naučte se, jak povolit nebo zakázat anonymní přístup k datům objektů BLOB pro účet úložiště. Nastavte nastavení kontejneru pro veřejný přístup tak, aby kontejnery a objekty blob byly dostupné pro anonymní přístup.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: a153a3735bbc46dbbce7e58374e1015ac1ec0bfb
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133176"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob

Azure Storage podporuje volitelný anonymní veřejný přístup pro čtení pro kontejnery a objekty blob. Ve výchozím nastavení se anonymní přístup k vašim datům nikdy nepovoluje. Pokud explicitně nepovolíte anonymní přístup, musí být všechny požadavky na kontejner a jeho objekty blob autorizované pomocí Azure Active Directory (Azure AD) nebo autorizace pomocí sdíleného klíče. Když nakonfigurujete nastavení úrovně veřejného přístupu kontejneru tak, aby povoloval anonymní přístup, můžou klienti číst data v tomto kontejneru bez autorizace této žádosti.

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

Ve výchozím nastavení je veřejný přístup povolený pro jednotlivé kontejnery v účtu úložiště. Pokud je veřejný přístup povolený, může uživatel s příslušnými oprávněními upravit nastavení veřejného přístupu kontejneru a povolit tak anonymní veřejný přístup k datům v tomto kontejneru.

Mějte na paměti, že veřejný přístup ke kontejneru je ve výchozím nastavení vždycky vypnutý a musí být explicitně nakonfigurovaný tak, aby povoloval anonymní požadavky. Bez ohledu na nastavení účtu úložiště nebudou vaše data nikdy k dispozici pro veřejný přístup, pokud uživatel s příslušnými oprávněními neudělá tento další krok, aby umožnil veřejný přístup ke kontejneru.

Nepovolení veřejného přístupu pro účet úložiště zabraňuje anonymnímu přístupu ke všem kontejnerům a objektům blob v tomto účtu. Pokud pro účet není povolený veřejný přístup, není možné nakonfigurovat nastavení veřejného přístupu pro kontejner, aby se povolil anonymní přístup. Pro lepší zabezpečení doporučuje společnost Microsoft zakázat veřejný přístup k účtům úložiště, pokud váš scénář nevyžaduje, aby uživatelé měli přístup k prostředkům objektů BLOB anonymně.

> [!IMPORTANT]
> Nepovolení veřejného přístupu pro účet úložiště přepíše nastavení veřejného přístupu pro všechny kontejnery v tomto účtu úložiště. Pokud pro účet úložiště není povolený veřejný přístup, všechny budoucí anonymní požadavky na daný účet selžou. Před změnou tohoto nastavení nezapomeňte pochopit dopad na klientské aplikace, které můžou přistupovat k datům ve vašem účtu úložiště anonymně. Další informace najdete v tématu [zabránění anonymnímu veřejnému přístupu pro čtení kontejnerů a objektů BLOB](anonymous-read-access-prevent.md).

Pokud chcete povolit nebo zakázat veřejný přístup k účtu úložiště, nakonfigurujte vlastnost **blobPublicAccess** účtu pomocí Azure Portal nebo Azure CLI. Tato vlastnost je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Další informace najdete v tématu [Přehled účtu úložiště](../common/storage-account-overview.md).

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete povolit nebo zakázat veřejný přístup k účtu úložiště v Azure Portal, postupujte podle následujících kroků:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části **Nastavení**vyhledejte nastavení **Konfigurace** .
1. Nastavte **veřejný přístup k objektu BLOB** na **povoleno** nebo **zakázáno**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Snímek obrazovky ukazující, jak povolit nebo zakázat veřejný přístup objektů BLOB pro účet":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete povolit nebo zakázat veřejný přístup k účtu úložiště pomocí Azure CLI, nejdřív Získejte ID prostředku pro svůj účet úložiště voláním příkazu [AZ Resource show](/cli/azure/resource#az-resource-show) . Potom zavolejte příkaz [AZ Resource Update](/cli/azure/resource#az-resource-update) a nastavte vlastnost **allowBlobPublicAccess** pro účet úložiště. Pokud chcete povolit veřejný přístup, nastavte vlastnost **allowBlobPublicAccess** na hodnotu true. Pokud ho chcete zakázat, nastavte ho na **false**.

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

---

> [!NOTE]
> Nepovolení veřejného přístupu pro účet úložiště nemá vliv na žádné statické weby hostované v tomto účtu úložiště. Kontejner **$Web** je vždy veřejně přístupný.

## <a name="check-whether-public-access-is-allowed-for-a-storage-account"></a>Ověřte, jestli je pro účet úložiště povolený veřejný přístup.

Pokud chcete zjistit, jestli je pro účet úložiště povolený veřejný přístup, Získejte hodnotu vlastnosti **allowBlobPublicAccess** . Pokud chcete tuto vlastnost pro velké číslo účtů úložiště ověřit najednou, použijte Průzkumníka Azure Resource graphu.

> [!IMPORTANT]
> Vlastnost **allowBlobPublicAccess** není nastavena ve výchozím nastavení a nevrací hodnotu, dokud ji explicitně nenastavíte. Účet úložiště umožňuje veřejný přístup, pokud hodnota vlastnosti má hodnotu **null** nebo je-li nastavena hodnota **true**.

### <a name="check-whether-public-access-is-allowed-for-a-single-storage-account"></a>Ověřte, jestli je pro jeden účet úložiště povolený veřejný přístup.

Pokud chcete ověřit, jestli je veřejný přístup pro jeden účet úložiště pomocí Azure CLI povolený, zavolejte příkaz [AZ Resource show](/cli/azure/resource#az-resource-show) a dotaz na vlastnost **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-whether-public-access-is-allowed-for-a-set-of-storage-accounts"></a>Ověřte, jestli je pro sadu účtů úložiště povolený veřejný přístup.

Pokud chcete zjistit, jestli je v rámci sady účtů úložiště s optimálním výkonem povolený veřejný přístup, můžete v Azure Portal použít Průzkumníka prostředků Azure. Další informace o používání Průzkumníka grafů prostředků najdete v tématu [rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](/azure/governance/resource-graph/first-query-portal).

Když spustíte následující dotaz, v Průzkumníku grafu prostředků se vrátí seznam účtů úložiště a zobrazí se hodnota vlastnosti **allowBlobPublicAccess** pro každý účet:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>Nastavení úrovně veřejného přístupu pro kontejner

Pokud chcete anonymním uživatelům udělit přístup pro čtení ke kontejneru a jeho objektům blob, nejdřív povolte veřejný přístup k účtu úložiště a pak nastavte úroveň veřejného přístupu kontejneru. Pokud je pro účet úložiště odepřený veřejný přístup, nebudete moct pro kontejner nakonfigurovat veřejný přístup.

Pokud je pro účet úložiště povolen veřejný přístup, můžete nakonfigurovat kontejner s následujícími oprávněními:

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

Pokud je pro účet úložiště zakázaný veřejný přístup, nedá se nastavit úroveň veřejného přístupu kontejneru. Pokud se pokusíte nastavit úroveň veřejného přístupu kontejneru, uvidíte, že nastavení je zakázané, protože pro tento účet není povolený veřejný přístup.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Snímek obrazovky zobrazující, že nastavení úrovně veřejného přístupu kontejneru je blokované, když je veřejný přístup nepovolený":::

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
