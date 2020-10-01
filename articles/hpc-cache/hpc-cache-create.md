---
title: Vytvoření mezipaměti prostředí Azure HPC
description: Vytvoření instance mezipaměti prostředí Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: bed158fb99654bd48184073b1266ae630255558b
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613165"
---
# <a name="create-an-azure-hpc-cache"></a>Vytvoření mezipaměti prostředí Azure HPC

K vytvoření mezipaměti použijte Azure Portal nebo rozhraní příkazového řádku Azure.

![snímek obrazovky s přehledem mezipaměti v Azure Portal s tlačítkem vytvořit v dolní části](media/hpc-cache-home-page.png)

Kliknutím na obrázek níže si můžete přehrát [ukázku](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) pro vytvoření mezipaměti a Přidání cíle úložiště.

[![Miniatura videa: mezipaměť prostředí Azure HPC: Instalační program (kliknutím můžete navštívit stránku video)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>Definovat základní podrobnosti

![snímek stránky s podrobnostmi projektu v Azure Portal](media/hpc-cache-create-basics.png)

V části **Project Details (podrobnosti projektu**) vyberte předplatné a skupinu prostředků, které budou hostovat mezipaměť.

V části **Podrobnosti o službě**nastavte název mezipaměti a tyto ostatní atributy:

* Umístění – vyberte jednu z [podporovaných oblastí](hpc-cache-overview.md#region-availability).
* Virtuální síť – můžete vybrat existující virtuální síť nebo vytvořit novou.
* Podsíť – vyberte nebo vytvořte podsíť s aspoň 64 IP adresami (/24). Tato podsíť se musí použít jenom pro tuto instanci mezipaměti Azure HPC.

## <a name="set-cache-capacity"></a>Nastavit kapacitu mezipaměti
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stránce **mezipaměť** musíte nastavit kapacitu mezipaměti. Nastavené hodnoty určují, kolik dat vaše mezipaměť může uchovávat a jak rychle může obsluhovat požadavky klientů.

Kapacita má vliv také na náklady na mezipaměť.

Určete kapacitu nastavením těchto dvou hodnot:

* Maximální rychlost přenosu dat pro mezipaměť (propustnost), v GB/s
* Velikost úložiště přidělená pro data uložená v mezipaměti v TB

Vyberte jednu z dostupných hodnot propustnosti a velikosti úložiště mezipaměti.

Pamatujte, že skutečná rychlost přenosu dat závisí na zatížení, rychlosti sítě a typu cílů úložiště. Hodnoty, které zvolíte, nastaví maximální propustnost pro celý systém mezipaměti, ale některé z nich se použijí pro úlohy s režijními náklady. Pokud třeba klient požaduje soubor, který už není uložený v mezipaměti, nebo pokud je soubor označený jako zastaralý, mezipaměť použije určitou propustnost k načtení z back-endu úložiště.

Azure HPC cache spravuje, které soubory jsou uložené do mezipaměti a předem se načítají, aby se maximalizovala míra přístupů do mezipaměti. Obsah mezipaměti se průběžně vyhodnocuje a soubory se přesunou do dlouhodobého úložiště, když se k nim nepoužívá méně často. Vyberte velikost úložiště mezipaměti, která může pohodlně uchovávat aktivní sadu pracovních souborů a navíc další místo pro metadata a další režii.

![snímek stránky s nastavením velikosti mezipaměti](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Povolit šifrování Azure Key Vault (volitelné)

Pokud je vaše mezipaměť v oblasti, která podporuje šifrovací klíče spravované zákazníkem, zobrazí se stránka **klíče šifrování disku** mezi kartami **mezipaměti** a **značky** . Další informace o podpoře oblastí najdete v tématu věnovaném [místní dostupnosti](hpc-cache-overview.md#region-availability) .

Pokud chcete spravovat šifrovací klíče používané pro úložiště mezipaměti, zadejte informace o Azure Key Vault na stránce **klíče pro šifrování disku** . Trezor klíčů musí být ve stejné oblasti a ve stejném předplatném jako mezipaměť.

Pokud nepotřebujete klíče spravované zákazníky, můžete tuto část přeskočit. Azure ve výchozím nastavení šifruje data pomocí klíčů spravovaných Microsoftem. Další informace najdete v tématu [šifrování služby Azure Storage](../storage/common/storage-service-encryption.md) .

> [!NOTE]
>
> * Po vytvoření mezipaměti nemůžete měnit klíče spravované společností Microsoft ani klíče spravované zákazníkem.
> * Po vytvoření je mezipaměť nutné autorizovat pro přístup k trezoru klíčů. Kliknutím na tlačítko **Povolit šifrování** na stránce **Přehled** mezipaměti zapněte šifrování. Proveďte tento krok během 90 minut od vytvoření mezipaměti.
> * Po této autorizaci se vytvoří disky mezipaměti. To znamená, že počáteční čas vytvoření mezipaměti je krátký, ale mezipaměť nebude připravena k použití po dobu 10 minut nebo více po autorizaci přístupu.

Úplné vysvětlení procesu šifrování klíčů spravovaných zákazníkem najdete v článku [použití šifrovacích klíčů spravovaných zákazníkem pro mezipaměť HPC Azure](customer-keys.md).

![snímek stránky šifrovacích klíčů se zvoleným polem spravované zákazníky a poli trezoru klíčů](media/create-encryption.png)

Vyberte **zákazníka spravované** pro výběr šifrování klíče spravovaného zákazníkem. Zobrazí se pole Specifikace trezoru klíčů. Vyberte Azure Key Vault, který chcete použít, a pak vyberte klíč a verzi, které chcete použít pro tuto mezipaměť. Klíč musí být 2048 klíč RSA. Z této stránky můžete vytvořit nový trezor klíčů, klíč nebo verzi klíče.

Po vytvoření mezipaměti ji musíte autorizovat, aby používala službu trezoru klíčů. Podrobnosti najdete [v tématu autorizace Azure Key Vault šifrování z mezipaměti](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) .

## <a name="add-resource-tags-optional"></a>Přidat značky prostředků (volitelné)

Stránka **značky** umožňuje přidat [značky prostředků](https://go.microsoft.com/fwlink/?linkid=873112) do instance mezipaměti prostředí Azure HPC.

## <a name="finish-creating-the-cache"></a>Dokončení vytváření mezipaměti

Po konfiguraci nové mezipaměti klikněte na kartu **Revize + vytvořit** . Portál ověří vaše výběry a umožní vám zkontrolovat své volby. Pokud je vše správné, klikněte na **vytvořit**.

Vytvoření mezipaměti trvá přibližně 10 minut. Průběh můžete sledovat na panelu oznámení Azure Portal.

![snímek obrazovky se stránkou nasazení v mezipaměti a stránkami oznámení na portálu](media/hpc-cache-deploy-status.png)

Po dokončení vytváření se zobrazí oznámení s odkazem na novou instanci mezipaměti Azure HPC a mezipaměť se zobrazí v seznamu **prostředků** vašeho předplatného.

![snímek obrazovky instance mezipaměti HPC Azure v Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Pokud vaše mezipaměť používá šifrovací klíče spravované zákazníkem, může se mezipaměť zobrazit v seznamu prostředků předtím, než se stav nasazení změní na dokončeno. Jakmile je stav mezipaměti **čekat na klíč** , můžete [ho autorizovat](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) k použití trezoru klíčů.

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Vytvoření mezipaměti pomocí Azure CLI

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

> [!NOTE]
> Rozhraní příkazového řádku Azure v současné době nepodporuje vytváření mezipaměti pomocí šifrovacích klíčů spravovaných zákazníkem. Použijte Azure Portal.

Pomocí příkazu [AZ HPC-cache Create](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-create) vytvořte novou mezipaměť prostředí Azure HPC.

Zadejte tyto hodnoty:

* Název skupiny prostředků mezipaměti
* Název mezipaměti
* Oblast Azure
* Podsíť mezipaměti v tomto formátu:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  Podsíť mezipaměti potřebuje minimálně 64 IP adres (/24) a nemůže začínat žádné další prostředky.

* Kapacita mezipaměti. Dvě hodnoty nastaví maximální propustnost vaší mezipaměti HPC Azure:

  * Velikost mezipaměti (v GB)
  * SKU virtuálních počítačů použitých v infrastruktuře mezipaměti

  [AZ HPC-cache list](/cli/azure/ext/hpc-cache/hpc-cache/skus) zobrazí dostupné SKU a možnosti platné velikosti mezipaměti pro každé z nich. Možnosti velikosti mezipaměti jsou v rozsahu od 3 TB do 48 TB, ale podporují se jenom některé hodnoty.

  Tento graf znázorňuje, která velikost mezipaměti a kombinace SKU jsou platné v době přípravy tohoto dokumentu (červenec 2020).

  | Velikost mezipaměti | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | ano         | ne          | ne          |
  | 6144 GB    | ano         | ano         | ne          |
  | 12288 GB   | ano         | ano         | ano         |
  | 24576 GB   | ne          | ano         | ano         |
  | 49152 GB   | ne          | ne          | ano         |

  Informace o cenách, propustnosti a způsobu vhodné velikosti mezipaměti pro váš pracovní postup najdete v části **Nastavení kapacity mezipaměti** na kartě pokyny pro portál.

Příklad vytvoření mezipaměti:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

Vytvoření mezipaměti trvá několik minut. Po úspěšném provedení příkaz Create vrátí výstup podobný tomuto:

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

Zpráva obsahuje několik užitečných informací, včetně těchto položek:

* Adresy připojení klienta – tyto IP adresy použijte, až budete připraveni k propojení klientů s mezipamětí. Další informace najdete [v tématu připojení mezipaměti prostředí Azure HPC](hpc-cache-mount.md) .
* Stav upgradu – při vydání aktualizace softwaru se tato zpráva změní. [Software mezipaměti můžete ručně upgradovat](hpc-cache-manage.md#upgrade-cache-software) v pohodlném čase, nebo bude použit automaticky po několika dnech.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Modul PowerShell AZ. HPCCache je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb. Nedoporučuje se pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Požadavky

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell](/powershell/azure/install-az-ps). Pokud se rozhodnete použít Cloud Shell, přečtěte si téma [přehled Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) , kde najdete další informace.

> [!IMPORTANT]
> I když je modul PowerShell **AZ. HPCCache** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny. Až bude tento modul PowerShellu všeobecně dostupný, bude součástí budoucna k vydaným verzím modulu PowerShellu a k dispozici nativně z aplikace Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Vytvoření mezipaměti pomocí Azure PowerShell

> [!NOTE]
> Azure PowerShell v současné době nepodporuje vytváření mezipaměti pomocí šifrovacích klíčů spravovaných zákazníkem. Použijte Azure Portal.

Pomocí rutiny [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) vytvořte novou mezipaměť prostředí Azure HPC.

Zadejte tyto hodnoty:

* Název skupiny prostředků mezipaměti
* Název mezipaměti
* Oblast Azure
* Podsíť mezipaměti v tomto formátu:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  Podsíť mezipaměti potřebuje minimálně 64 IP adres (/24) a nemůže začínat žádné další prostředky.

* Kapacita mezipaměti. Dvě hodnoty nastaví maximální propustnost vaší mezipaměti HPC Azure:

  * Velikost mezipaměti (v GB)
  * SKU virtuálních počítačů použitých v infrastruktuře mezipaměti

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) zobrazí dostupné SKU a možnosti platné velikosti mezipaměti pro každé z nich. Možnosti velikosti mezipaměti jsou v rozsahu od 3 TB do 48 TB, ale podporují se jenom některé hodnoty.

  Tento graf znázorňuje, která velikost mezipaměti a kombinace SKU jsou platné v době přípravy tohoto dokumentu (červenec 2020).

  | Velikost mezipaměti | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | ano         | ne          | ne          |
  | 6144 GB    | ano         | ano         | ne          |
  | 12 288 GB   | ano         | ano         | ano         |
  | 24 576 GB   | ne          | ano         | ano         |
  | 49 152 GB   | ne          | ne          | ano         |

  Informace o cenách, propustnosti a způsobu vhodné velikosti mezipaměti pro váš pracovní postup najdete v části **Nastavení kapacity mezipaměti** na kartě pokyny pro portál.

Příklad vytvoření mezipaměti:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

Vytvoření mezipaměti trvá několik minut. Po úspěšném provedení příkaz Create vrátí následující výstup:

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

Zpráva obsahuje několik užitečných informací, včetně těchto položek:

* Adresy připojení klienta – tyto IP adresy použijte, až budete připraveni k propojení klientů s mezipamětí. Další informace najdete [v tématu připojení mezipaměti prostředí Azure HPC](hpc-cache-mount.md) .
* Stav upgradu – při vydání aktualizace softwaru se tato zpráva změní. [Software mezipaměti můžete ručně upgradovat](hpc-cache-manage.md#upgrade-cache-software) v pohodlný čas, nebo se použije automaticky po několik dní.

---

## <a name="next-steps"></a>Další kroky

Po zobrazení mezipaměti v seznamu **prostředky** můžete přejít k dalšímu kroku.

* [Definujte cíle úložiště](hpc-cache-add-storage.md) , které umožní vaší mezipaměti přístup ke zdrojům dat.
* Pokud používáte šifrovací klíče spravované zákazníkem, musíte [autorizovat Azure Key Vault šifrování](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) ze stránky přehled mezipaměti a dokončit tak nastavení mezipaměti. Než budete moct přidat úložiště, musíte provést tento krok. Podrobnosti najdete v tématu [použití šifrovacích klíčů spravovaných zákazníkem](customer-keys.md) .
