---
title: Obnovitelné odstranění pro kontejnery (Preview)
titleSuffix: Azure Storage
description: Obnovitelné odstranění kontejnerů (ve verzi Preview) chrání vaše data, abyste mohli snadněji obnovit data v případě, že je omylem změnil nebo odstranila aplikace nebo jiný uživatel účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 3c6aa408ab3e89c367fca0701773b35fb9b41bb8
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460794"
---
# <a name="soft-delete-for-containers-preview"></a>Obnovitelné odstranění pro kontejnery (Preview)

Obnovitelné odstranění pro kontejnery (Preview) chrání vaše data před náhodným nebo omylem úpravou nebo odstraněním. Pokud je pro účet úložiště povolené obnovitelné odstranění kontejneru, všechny odstraněné kontejnery a jejich obsah se uchovávají v Azure Storage po dobu, kterou určíte. Během doby uchování můžete obnovit dříve odstraněné kontejnery a všechny objekty BLOB v nich obsažené.

V rámci koncové ochrany dat objektů BLOB doporučuje společnost Microsoft povolit následující funkce ochrany dat:

- Dočasná odstranění kontejneru pro ochranu proti náhodnému odstranění nebo přepsání kontejneru. Pokud chcete zjistit, jak povolit obnovitelné odstranění kontejneru, přečtěte si téma [povolení a Správa obnovitelného odstranění kontejnerů](soft-delete-container-enable.md).
- Pro zajištění ochrany před náhodným odstraněním nebo přepsáním jednotlivého objektu BLOB se jedná o obnovitelné odstranění objektu BLOB. Další informace o tom, jak povolit obnovitelné odstranění objektů blob, najdete v tématu [obnovitelné odstranění pro objekty blob](soft-delete-blob-overview.md).
- Správa verzí objektů BLOB pro automatické udržování předchozích verzí objektu BLOB. Pokud je povolená Správa verzí objektů blob, můžete obnovit předchozí verzi objektu blob, aby se data obnovila v případě, že se omylem změnila nebo odstranila. Informace o tom, jak povolit správu verzí objektů blob, najdete v tématu [povolení a správa verzí objektů BLOB](versioning-enable.md).

> [!WARNING]
> Odstranění účtu úložiště se nedá vrátit zpátky. Obnovitelné odstranění nechrání před odstraněním účtu úložiště. Pokud chcete zabránit nechtěnému odstranění účtu úložiště, nakonfigurujte na prostředku účtu úložiště zámek **CannotDelete** . Další informace o uzamykání prostředků Azure najdete v tématu [uzamčení prostředků, aby se zabránilo neočekávaným změnám](../../azure-resource-manager/management/lock-resources.md).

## <a name="how-container-soft-delete-works"></a>Jak funguje obnovitelné odstranění kontejneru

Pokud povolíte obnovitelné odstranění kontejneru, můžete zadat dobu uchování pro odstraněné kontejnery, které jsou v rozmezí od 1 do 365 dnů. Výchozí doba uchování je 7 dní. Během doby uchování můžete obnovit odstraněný kontejner voláním operace zrušit **odstranění kontejneru** .

Když obnovíte kontejner, můžete ho obnovit na jeho původní název, pokud se tento název znovu nepoužil. Pokud se použil původní název kontejneru, můžete kontejner obnovit s novým názvem.

Po vypršení doby uchování se kontejner trvale odstraní z Azure Storage a nedá se obnovit. Hodiny začínají dobou uchování v okamžiku odstranění kontejneru. Dobu uchovávání můžete kdykoli změnit, ale mějte na paměti, že aktualizovaná doba uchování se vztahuje pouze na nově odstraněné kontejnery. Dříve odstraněné kontejnery se trvale odstraní na základě doby uchování, která byla platná v době odstranění kontejneru.

Zakázání podmíněného odstranění kontejneru nevede k trvalému odstranění kontejnerů, které jste dříve odstranili. Všechny dočasně odstraněné kontejnery budou trvale odstraněny při vypršení doby uchování, která byla v platnosti v době odstranění kontejneru.

## <a name="about-the-preview"></a>O verzi Preview

Obnovitelné odstranění kontejneru je dostupné ve verzi Preview ve všech veřejných oblastech Azure.

> [!IMPORTANT]
> Ukázka podmíněného odstranění kontejneru je určená jenom pro neprodukční použití. Smlouvy o úrovni produkčních služeb (SLA) nejsou aktuálně k dispozici.

Verze 2019-12-12 a vyšší z Azure Storage REST API podporuje obnovitelné odstranění kontejneru.

### <a name="storage-account-support"></a>Podpora účtu úložiště

Obnovitelné odstranění kontejneru je k dispozici pro následující typy účtů úložiště:

- Účty úložiště pro obecné účely v2
- Zablokovat účty úložiště objektů BLOB
- Účty úložiště Blob

Pokud je váš účet úložiště účet pro obecné účely V1, použijte Azure Portal k upgradu na účet pro obecné účely v2. Další informace o účtech úložiště najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

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
