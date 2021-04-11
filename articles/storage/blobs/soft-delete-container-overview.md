---
title: Obnovitelné odstranění pro kontejnery (Preview)
titleSuffix: Azure Storage
description: Obnovitelné odstranění kontejnerů (ve verzi Preview) chrání vaše data, abyste mohli snadněji obnovit data v případě, že je omylem změnil nebo odstranila aplikace nebo jiný uživatel účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: af9d520bab3ff49b30672717414fbd651c915dd4
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552373"
---
# <a name="soft-delete-for-containers-preview"></a>Obnovitelné odstranění pro kontejnery (Preview)

Obnovitelné odstranění pro kontejnery (Preview) chrání vaše data proti náhodnému nebo škodlivému odstranění. Když je pro účet úložiště povolené obnovitelné odstranění kontejneru, odstraněný kontejner a jeho obsah se v Azure Storage zachovají v období, které zadáte. Během doby uchovávání můžete dříve odstraněné kontejnery obnovit. Obnovením kontejneru se obnoví všechny objekty blob, které kontejner obsahoval v době odstranění.

V rámci koncové ochrany dat objektů BLOB doporučuje společnost Microsoft povolit následující funkce ochrany dat:

- Dočasná odstranění kontejneru pro obnovení kontejneru, který byl odstraněn. Pokud chcete zjistit, jak povolit obnovitelné odstranění kontejneru, přečtěte si téma [povolení a Správa obnovitelného odstranění kontejnerů](soft-delete-container-enable.md).
- Správa verzí objektů BLOB pro automatické udržování předchozích verzí objektu BLOB. Pokud je povolená Správa verzí objektů blob, můžete obnovit předchozí verzi objektu blob, aby se data obnovila v případě, že se omylem změnila nebo odstranila. Informace o tom, jak povolit správu verzí objektů blob, najdete v tématu [povolení a správa verzí objektů BLOB](versioning-enable.md).
- Obnovitelné odstranění objektu BLOB pro obnovení objektu BLOB nebo verze, která byla odstraněna. Informace o tom, jak povolit obnovitelné odstranění objektů blob, najdete v tématu [povolení a Správa obnovitelného odstranění pro objekty blob](soft-delete-blob-enable.md).

> [!IMPORTANT]
> Obnovitelné odstranění kontejneru je momentálně ve **verzi Preview**. Přečtěte si další [podmínky použití Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview pro právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

## <a name="how-container-soft-delete-works"></a>Jak funguje obnovitelné odstranění kontejneru

Pokud povolíte obnovitelné odstranění kontejneru, můžete zadat dobu uchování pro odstraněné kontejnery, které jsou v rozmezí od 1 do 365 dnů. Výchozí doba uchování je 7 dní. Během doby uchování můžete odstraněné kontejnery obnovit voláním operace **obnovení kontejneru** .

Při obnovení kontejneru se obnoví také objekty blob kontejneru a všechny verze objektů BLOB. Pomocí podmíněného odstranění kontejneru ale můžete obnovit objekty blob jenom v případě, že jste kontejner sám odstranili. K obnovení odstraněného objektu BLOB v případě, že se jeho nadřazený kontejner neodstranil, je nutné použít měkké odstranění nebo správu verzí objektů BLOB.

> [!WARNING]
> Obnovitelné odstranění kontejneru může v době odstranění obnovit pouze celé kontejnery a jejich obsah. Odstraněné objekty blob nelze obnovit v rámci kontejneru pomocí obnovitelného odstranění kontejneru. Společnost Microsoft doporučuje taky povolit pro ochranu jednotlivých objektů BLOB v kontejneru zabezpečení pomocí objektů BLOB Soft DELETE a BLOB.

Následující diagram znázorňuje, jak se odstraněný kontejner dá obnovit, když je povolené podmíněné odstranění kontejneru:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagram znázorňující, jak může být obnovený kontejner pro dočasné odstranění":::

Když obnovíte kontejner, můžete ho obnovit na jeho původní název, pokud se tento název znovu nepoužil. Pokud se použil původní název kontejneru, můžete kontejner obnovit s novým názvem.

Po vypršení doby uchování se kontejner trvale odstraní z Azure Storage a nedá se obnovit. Hodiny začínají dobou uchování v okamžiku odstranění kontejneru. Dobu uchovávání můžete kdykoli změnit, ale mějte na paměti, že aktualizovaná doba uchování se vztahuje pouze na nově odstraněné kontejnery. Dříve odstraněné kontejnery se trvale odstraní na základě doby uchování, která byla platná v době odstranění kontejneru.

Zakázání podmíněného odstranění kontejneru nevede k trvalému odstranění kontejnerů, které jste dříve odstranili. Všechny dočasně odstraněné kontejnery budou trvale odstraněny při vypršení doby uchování, která byla v platnosti v době odstranění kontejneru.

> [!IMPORTANT]
> Obnovitelné odstranění kontejneru nechrání před odstraněním účtu úložiště. Chrání pouze proti odstranění kontejnerů v daném účtu. Pokud chcete chránit účet úložiště před odstraněním, nakonfigurujte zámek u prostředku účtu úložiště. Další informace o uzamykání účtu úložiště najdete v tématu [použití zámku Azure Resource Manager k účtu úložiště](../common/lock-account-resource.md).

## <a name="about-the-preview"></a>O verzi Preview

Obnovitelné odstranění kontejneru je dostupné ve verzi Preview ve všech oblastech Azure.

Verze 2019-12-12 nebo vyšší REST API Azure Storage podporuje obnovitelné odstranění kontejneru.

### <a name="storage-account-support"></a>Podpora účtu úložiště

Obnovitelné odstranění kontejneru je k dispozici pro následující typy účtů úložiště:

- Účty úložiště pro obecné účely v2 a v1
- Zablokovat účty úložiště objektů BLOB
- Účty úložiště Blob

Podporují se taky účty úložiště s hierarchickým oborem názvů povoleným pro použití s Azure Data Lake Storage Gen2.

### <a name="register-for-the-preview"></a>Zaregistrovat se pro verzi Preview

Pokud se chcete zaregistrovat do verze Preview pro obnovitelné odstranění kontejneru, pomocí PowerShellu nebo rozhraní příkazového řádku Azure zaslat žádost o registraci funkce v rámci vašeho předplatného. Po schválení žádosti můžete povolit obnovitelné odstranění kontejneru pomocí všech nových nebo existujících účtů úložiště blob bloku s obecným účelem v2, BLOB Storage nebo Premium.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud se chcete zaregistrovat v prostředí PowerShell, zavolejte příkaz [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud se chcete zaregistrovat v Azure CLI, zavolejte příkaz [AZ Feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Ověření stavu registrace

Pokud chcete zjistit stav registrace, použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete zjistit stav vaší registrace pomocí PowerShellu, zavolejte příkaz [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zjistit stav registrace pomocí Azure CLI, zavolejte příkaz [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Ceny a fakturace

Pro povolení obnovitelného odstranění kontejneru se neúčtují žádné další poplatky. Data v nejemných odstraněných kontejnerech se účtují stejnou sazbou jako aktivní data.

## <a name="next-steps"></a>Další kroky

- [Konfigurovat obnovitelné odstranění kontejneru](soft-delete-container-enable.md)
- [Obnovitelné odstranění pro objekty blob](soft-delete-blob-overview.md)
- [Správa verzí objektů BLOB](versioning-overview.md)
