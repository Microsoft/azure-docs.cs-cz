---
title: Vytvoření účtu úložiště
titleSuffix: Azure Storage
description: Naučte se vytvořit účet úložiště pro ukládání objektů blob, souborů, front a tabulek. Účet úložiště Azure poskytuje jedinečný obor názvů v Microsoft Azure pro čtení a zápis dat.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: cb5caeb7f75834a317b222392c6e827185cfac00
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714342"
---
# <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Účet úložiště Azure obsahuje všechny vaše Azure Storage datové objekty: objekty blob, soubory, fronty a tabulky. Účet úložiště poskytuje jedinečný obor názvů pro data Azure Storage, která jsou přístupná odkudkoli na světě přes protokol HTTP nebo HTTPS. Další informace o účtech Azure Storage najdete v tématu [Přehled účtu úložiště](storage-account-overview.md).

V tomto článku se dozvíte, jak vytvořit účet úložiště pomocí [Azure Portal](https://portal.azure.com/), [Azure POWERSHELL](/powershell/azure/), [Azure CLI](/cli/azure)nebo [šablony pro Azure Resource Manager](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Žádné

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete vytvořit účet Azure Storage pomocí PowerShellu, ujistěte se, že máte nainstalovanou [modul AZ PowerShell](https://www.powershellgallery.com/packages/Az), verze 0,7 nebo novější. Další informace najdete v tématu [představení modulu Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

Aktuální verzi zjistíte spuštěním následujícího příkazu:

```powershell
Get-InstalledModule -Name "Az"
```

Pokud chcete nainstalovat nebo upgradovat Azure PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Můžete se přihlásit k Azure a spustit příkazy rozhraní příkazového řádku Azure CLI jedním ze dvou způsobů:

- Příkazy rozhraní příkazového řádku můžete spustit z Azure Portal v Azure Cloud Shell.
- Můžete nainstalovat rozhraní příkazového řádku a spustit příkazy rozhraní příkazového řádku místně.

### <a name="use-azure-cloud-shell"></a>Použití služby Azure Cloud Shell

Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Rozhraní příkazového řádku Azure je předem nainstalované a nakonfigurované pro použití s vaším účtem. V nabídce v pravé horní části Azure Portal klikněte na tlačítko **Cloud Shell** :

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Tlačítko spustí interaktivní prostředí, které můžete použít ke spuštění kroků popsaných v tomto článku s návody:

[![Snímek obrazovky zobrazující okno Cloud Shell na portálu](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

Rozhraní příkazového řádku Azure můžete také nainstalovat a používat místně. Příklady v tomto článku vyžadují Azure CLI verze 2.0.4 nebo novější. Pokud `az --version` chcete najít nainstalovanou verzi, spusťte příkaz. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

# <a name="template"></a>[Šablona](#tab/template)

Žádné

---

Dále se přihlaste k Azure.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Přihlaste se na [Azure Portal](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkazu a podle pokynů na obrazovce proveďte ověření.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete spustit Azure Cloud Shell, přihlaste se k [Azure Portal](https://portal.azure.com).

K místní instalaci rozhraní příkazového řádku se přihlaste spuštěním příkazu [AZ Login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

# <a name="template"></a>[Šablona](#tab/template)

–

---

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Účet úložiště je Azure Resource Manager prostředek. Správce prostředků je služba pro nasazení a správu pro Azure. Další informace najdete v článku [Přehled Azure Resource Manageru](../../azure-resource-manager/management/overview.md).

Každý Správce prostředků prostředek, včetně účtu Azure Storage, musí patřit do skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků. V tomto postupu se dozvíte, jak vytvořit novou skupinu prostředků.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete vytvořit účet Azure Storage pomocí Azure Portal, postupujte takto:

1. V nabídce levého portálu vyberte **účty úložiště** , abyste zobrazili seznam účtů úložiště.
1. Na stránce **účty úložiště** vyberte **Nový**.

Možnosti pro nový účet úložiště se uspořádají do karet na stránce **vytvořit účet úložiště** . Následující části popisují každou kartu a jejich možnosti.

### <a name="basics-tab"></a>Karta základy

Na kartě **základy** zadejte základní informace o vašem účtu úložiště. Po dokončení karty **základy** můžete zvolit další přizpůsobení nového účtu úložiště nastavením možností na dalších kartách, nebo můžete vybrat možnost **zkontrolovat + vytvořit** a přijmout výchozí možnosti a pokračovat na ověřit a vytvořit účet.

V následující tabulce jsou popsána pole na kartě **základy** .

| Sekce | Pole | Požadované nebo volitelné | Description |
|--|--|--|--|
| Podrobnosti o projektu | Předplatné | Vyžadováno | Vyberte předplatné pro nový účet úložiště. |
| Podrobnosti o projektu | Skupina prostředků | Vyžadováno | Vytvořte novou skupinu prostředků pro tento účet úložiště, nebo vyberte existující. Další informace najdete v tématu [skupiny prostředků](../../azure-resource-manager/management/overview.md#resource-groups). |
| Podrobnosti o instancích | Název účtu úložiště | Vyžadováno | Vyberte jedinečný název účtu úložiště. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. |
| Podrobnosti o instancích | Oblast | Vyžadováno | Vyberte příslušnou oblast pro váš účet úložiště. Další informace najdete v tématu [oblasti a zóny dostupnosti v Azure](../../availability-zones/az-overview.md).<br /><br />Ne všechny oblasti jsou podporované pro všechny typy účtů úložiště nebo konfigurace redundance. Další informace najdete v článku [Možnosti redundance Azure Storage](storage-redundancy.md).<br /><br />Volba oblasti může mít dopad na fakturaci. Další informace najdete v tématu [fakturace účtu úložiště](storage-account-overview.md#storage-account-billing). |
| Podrobnosti o instancích | Výkon | Vyžadováno | Vyberte **standardní** výkon pro účty úložiště pro obecné účely v2 (výchozí). Tento typ účtu doporučuje společnost Microsoft pro většinu scénářů. Další informace najdete v tématu [typy účtů úložiště](storage-account-overview.md#types-of-storage-accounts).<br /><br />Vyberte **Premium** pro scénáře, které vyžadují nízkou latenci. Po výběru úrovně **Premium** vyberte typ účtu Premium Storage, který se má vytvořit. K dispozici jsou tyto typy účtů Premium Storage: <ul><li>[Objekty blob bloku](../blobs/storage-blob-performance-tiers.md)</li><li>[Sdílené složky](../files/storage-files-planning.md#management-concepts)</li><li>[Objekty blob stránky](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| Podrobnosti o instancích | Redundance | Vyžadováno | Vyberte požadovanou konfiguraci redundance. Ne všechny možnosti redundance jsou k dispozici pro všechny typy účtů úložiště ve všech oblastech. Další informace o konfiguracích redundance najdete v tématu [Azure Storage redundance](storage-redundancy.md).<br /><br />Pokud vyberete geograficky redundantní konfiguraci (GRS nebo GZRS), vaše data se replikují do datového centra v jiné oblasti. Pro přístup pro čtení dat v sekundární oblasti vyberte možnost **zpřístupnit přístup pro čtení k datům dostupným v případě nedostupnosti**. |

Následující obrázek ukazuje standardní konfiguraci nového účtu úložiště.

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="Snímek obrazovky se standardní konfigurací pro novou kartu základy účtu úložiště":::

### <a name="advanced-tab"></a>Karta Upřesnit

Na kartě **Upřesnit** můžete nakonfigurovat další možnosti a upravit výchozí nastavení nového účtu úložiště. Některé z těchto možností se dají nakonfigurovat i po vytvoření účtu úložiště, zatímco jiné musí být nakonfigurované v době vytváření.

Následující tabulka popisuje pole na kartě **Upřesnit** .

| Sekce | Pole | Požadované nebo volitelné | Description |
|--|--|--|--|
| Zabezpečení | Povolit zabezpečený přenos | Volitelné | Povolit zabezpečený přenos – vyžaduje, aby příchozí požadavky na tento účet úložiště byly provedeny pouze prostřednictvím protokolu HTTPS (výchozí). Doporučuje se pro optimální zabezpečení. Další informace najdete v tématu [vyžadování zabezpečeného přenosu pro zajištění zabezpečených připojení](storage-require-secure-transfer.md). |
| Zabezpečení | Povolit šifrování infrastruktury | Volitelné | Ve výchozím nastavení není šifrování infrastruktury zapnuté. Povolte šifrování infrastruktury pro šifrování dat na úrovni služby i na úrovni infrastruktury. Další informace najdete v tématu [Vytvoření účtu úložiště s povoleným šifrováním infrastruktury pro dvojitou šifrování dat](infrastructure-encryption-enable.md). |
| Zabezpečení | Povolit veřejný přístup k objektům blob | Volitelné | Když je toto nastavení povolené, umožní uživateli s příslušnými oprávněními povolit anonymní veřejný přístup ke kontejneru v účtu úložiště (výchozí). Když toto nastavení zakážete, zabráníte všem anonymnímu veřejnému přístupu k účtu úložiště. Další informace najdete v tématu [zabránění anonymnímu veřejnému přístupu pro čtení kontejnerů a objektů BLOB](../blobs/anonymous-read-access-prevent.md).<br> <br> Povolením přístupu do objektů BLOB se nezpřístupňuje data objektů BLOB pro veřejný přístup, pokud uživatel neprovede další krok, který explicitně nakonfiguruje nastavení veřejného přístupu kontejneru. |
| Zabezpečení | Povolit přístup k klíči účtu úložiště (Preview) | Volitelné | Když se toto nastavení povolí, umožníte klientům autorizaci žádostí na účet úložiště pomocí klíčů pro přístup k účtu nebo účtu Azure Active Directory (Azure AD) (výchozí). Zakázáním tohoto nastavení zabráníte autorizaci pomocí přístupových klíčů k účtu. Další informace najdete v tématu [zabránění autorizaci sdíleného klíče pro účet Azure Storage (Preview)](shared-key-authorization-prevent.md). |
| Zabezpečení | Minimální verze protokolu TLS | Vyžadováno | Vyberte minimální verzi protokolu TLS (Transport Layer Security) pro příchozí požadavky na účet úložiště. Výchozí hodnota je TLS verze 1,2. Pokud nastavíte výchozí hodnotu, odmítnou se příchozí požadavky vytvořené pomocí protokolu TLS 1,0 nebo TLS 1,1. Další informace najdete v tématu [vymáhání minimální požadované verze protokolu TLS (Transport Layer Security) pro požadavky na účet úložiště](transport-layer-security-configure-minimum-version.md). |
| Data Lake Storage Gen2 | Povolit hierarchický obor názvů | Volitelné | Pokud chcete tento účet úložiště použít pro úlohy Azure Data Lake Storage Gen2, nakonfigurujte hierarchický obor názvů. Další informace najdete v tématu [Úvod do Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md). |
| Blob Storage | Povolit síťové sdílení souborů (NFS) V3 (Preview) | Volitelné | Systém souborů NFS v3 poskytuje kompatibilitu systému souborů Linux v rozsahu úložiště objektů umožňuje klientům Linux připojit kontejner v úložišti objektů BLOB z virtuálního počítače Azure nebo místního počítače. Další informace najdete v tématu [Podpora protokolu NFS (Network File System) 3,0 ve službě Azure Blob Storage (Preview)](../blobs/network-file-system-protocol-support.md). |
| Blob Storage | Úroveň přístupu | Vyžadováno | Úroveň přístupu objektů BLOB umožňuje ukládat data objektů BLOB nejefektivnějším způsobem, a to na základě využití. Vyberte úroveň Hot (výchozí) pro často používaná data. Vyberte studenou vrstvu pro zřídka používaná data. Další informace najdete v tématu [úrovně přístupu pro Azure Blob Storage – horká, studená a archivní](../blobs/storage-blob-storage-tiers.md). |
| Azure Files | Povolení velkých sdílených složek | Volitelné | K dispozici pouze pro účty úložiště úrovně Premium pro sdílené složky souborů. Další informace najdete v tématu [Povolení standardního sdílení souborů pro rozsah až 100 TIB](../files/storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib). |
| Tabulky a fronty | Povolit podporu pro klíče spravované zákazníkem | Volitelné | Pokud chcete povolit podporu klíčů spravovaných zákazníkem v tabulkách a frontách, musíte toto nastavení vybrat v době, kdy vytváříte účet úložiště. Další informace najdete v tématu [Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky a fronty](account-encryption-key-create.md). |

### <a name="networking-tab"></a>Karta sítě

Na kartě **sítě** můžete nakonfigurovat nastavení předvolby sítě a možnosti směrování pro svůj nový účet úložiště. Tyto možnosti se dají nakonfigurovat i po vytvoření účtu úložiště.

Následující tabulka popisuje pole na kartě **síť** .

| Sekce | Pole | Požadované nebo volitelné | Description |
|--|--|--|--|
| Připojení k síti | Metoda připojení | Vyžadováno | Ve výchozím nastavení je příchozí síťový provoz směrován do veřejného koncového bodu pro váš účet úložiště. Můžete určit, že provoz musí být směrován do veřejného koncového bodu prostřednictvím služby Azure Virtual Network. Pro svůj účet úložiště můžete také nakonfigurovat privátní koncové body. Další informace najdete v tématu [použití privátních koncových bodů pro Azure Storage](storage-private-endpoints.md). |
| Síťové směrování | Předvolba směrování | Vyžadováno | Priorita síťového směrování určuje, jak se síťový provoz směruje do veřejného koncového bodu účtu úložiště od klientů přes Internet. Ve výchozím nastavení používá nový účet úložiště směrování sítě Microsoftu. Můžete se také rozhodnout směrovat síťový provoz přes bod POP nejbližší k účtu úložiště, což může snížit náklady na síť. Další informace najdete v tématu [Předvolby síťového směrování pro Azure Storage](network-routing-preference.md). |

### <a name="data-protection-tab"></a>Karta ochrana dat

Na kartě **Ochrana dat** můžete nakonfigurovat možnosti ochrany dat pro data objektů BLOB v novém účtu úložiště.  Tyto možnosti se dají nakonfigurovat i po vytvoření účtu úložiště. Přehled možností ochrany dat v Azure Storage najdete v tématu [Přehled ochrany](../blobs/data-protection-overview.md)dat.

V následující tabulce jsou popsána pole na kartě **Ochrana dat** .

| Sekce | Pole | Požadované nebo volitelné | Description |
|--|--|--|--|
| Obnovovací | Povolit obnovení k bodu v čase pro kontejnery | Volitelné | Obnovení k bodu v čase poskytuje ochranu proti náhodnému odstranění nebo poškození tím, že umožňuje obnovit data objektů blob bloku do dřívějšího stavu. Další informace najdete v tématu [obnovení k bodu v čase pro objekty blob bloku](../blobs/point-in-time-restore-overview.md).<br /><br />Povolení obnovení k časovému okamžiku také umožňuje vytvářet verze objektů blob, podmíněného odstranění objektů BLOB a kanálu změn objektů BLOB. Tyto požadované součásti můžou mít dopad na náklady. Další informace najdete v tématu [ceny a fakturace](../blobs/point-in-time-restore-overview.md#pricing-and-billing) pro obnovení k bodu v čase. |
| Obnovovací | Povolení obnovitelného odstranění pro objekty blob | Volitelné | Částečný odstranění objektu BLOB chrání jednotlivé objekty blob, snímky nebo verze před náhodným odstraněním nebo přepsáním tím, že v systému uchovává Odstraněná data pro zadanou dobu uchování. Během doby uchování můžete objekt s příslušným odstraněním obnovit do jeho stavu v době, kdy byl odstraněn. Další informace najdete v tématu [obnovitelné odstranění pro objekty blob](../blobs/soft-delete-blob-overview.md).<br /><br />Microsoft doporučuje povolit pro účty úložiště obnovitelné odstranění objektů BLOB a nastavit minimální dobu uchovávání sedmi dnů. |
| Obnovovací | Povolit obnovitelné odstranění pro kontejnery (Preview) | Volitelné | Obnovitelné odstranění kontejneru chrání kontejner a jeho obsah před náhodným odstraněním tím, že v systému uchovává Odstraněná data pro zadanou dobu uchování. Během doby uchování můžete obnovit místně odstraněný kontejner do jeho stavu v době, kdy byl odstraněn. Další informace najdete v tématu [obnovitelné odstranění pro kontejnery (Preview)](../blobs/soft-delete-container-overview.md).<br /><br />Microsoft doporučuje povolit pro účty úložiště obnovitelné odstranění kontejnerů a nastavit minimální dobu uchovávání sedmi dnů. |
| Obnovovací | Povolit obnovitelné odstranění pro sdílené složky | Volitelné | Obnovitelné odstranění sdílených složek chrání sdílenou složku a její obsah před náhodným odstraněním tím, že v systému uchovává Odstraněná data pro zadanou dobu uchování. Během doby uchování můžete obnovit odstraněnou sdílenou složku do stavu v době, kdy byla odstraněna. Další informace najdete v tématu [prevence náhodného odstranění sdílených složek Azure](../files/storage-files-prevent-file-share-deletion.md).<br /><br />Microsoft doporučuje povolit obnovitelné odstranění sdílených složek pro úlohy souborů Azure a nastavit minimální dobu uchovávání sedmi dnů. |
| Sledování | Povolení správy verzí pro objekty blob | Volitelné | Správa verzí objektů BLOB při přepisování objektu BLOB automaticky uloží stav objektu BLOB v předchozí verzi. Další informace najdete v tématu [Správa verzí objektů BLOB](../blobs/versioning-overview.md).<br /><br />Microsoft doporučuje povolit správu verzí objektů BLOB pro optimální ochranu dat pro účet úložiště. |
| Sledování | Povolit kanál změn objektu BLOB | Volitelné | Kanál změn objektu BLOB poskytuje transakční protokoly všech změn všech objektů BLOB v účtu úložiště i jejich metadata. Další informace najdete v tématu [Změna podpory kanálu v Azure Blob Storage](../blobs/storage-blob-change-feed.md). |

### <a name="tags-tab"></a>Karta značky

Na kartě **značky** můžete zadat správce prostředků značky, které vám pomůžou organizovat prostředky Azure. Další informace najdete v tématu [označení prostředků, skupin prostředků a předplatných pro logickou organizaci](../../azure-resource-manager/management/tag-resources.md).

### <a name="review--create-tab"></a>Revize + vytvořit kartu

Když přejdete na kartu **Revize + vytvořit** , Azure spustí ověřování v nastavení účtu úložiště, které jste si zvolili. Pokud ověření proběhne úspěšně, můžete pokračovat a vytvořit účet úložiště.

Pokud se ověření nepovede, portál indikuje, která nastavení je potřeba upravit.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete vytvořit účet úložiště pro obecné účely v2 pomocí PowerShellu, nejdřív vytvořte novou skupinu prostředků tak, že zavoláte příkaz [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Pokud si nejste jistí, kterou oblast pro parametr určíte `-Location` , můžete získat seznam podporovaných oblastí pro vaše předplatné pomocí příkazu [Get-AzLocation](/powershell/module/az.resources/get-azlocation) :

```azurepowershell-interactive
Get-AzLocation | select Location
```

Dále vytvořte účet úložiště standard pro obecné účely v2 s geograficky redundantním úložištěm s přístupem pro čtení (RA-GRS) pomocí příkazu [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . Mějte na paměti, že název účtu úložiště musí být v rámci Azure jedinečný, proto nahraďte hodnotu zástupný symbol v závorkách vlastní jedinečnou hodnotou:

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

Pokud chcete povolit hierarchický obor názvů pro účet úložiště, který se má použít [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), zahrňte `-EnableHierarchicalNamespace $True` parametr volání příkazu **New-AzStorageAccount** .

Následující tabulka uvádí, které hodnoty se mají použít pro `-SkuName` `-Kind` parametry a k vytvoření konkrétního typu účtu úložiště s požadovanou konfigurací redundance.

| Typ účtu úložiště | Podporované konfigurace redundance | Hodnota pro parametr-Kind | Možné hodnoty pro parametr-SkuName | Podporuje hierarchický obor názvů |
|--|--|--|--|--|
| Standardní pro obecné účely v2 | LRS/GRS/RA-GRS/ZRS/GZRS/RA-GZRS | StorageV2 | Standard_LRS/Standard_GRS/Standard_RAGRS/Standard_ZRS/Standard_GZRS/Standard_RAGZRS | Yes |
| Objekty blob bloku úrovně Premium | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | Yes |
| Soubory ke sdílení souborů úrovně Premium | LRS/ZRS | Úložiště | Premium_LRS/Premium_ZRS | No |
| Objekty blob stránky úrovně Premium | LRS | StorageV2 | Premium_LRS | No |
| Starší verze Standard pro obecné účely v1 | LRS/GRS/RA-GRS | Storage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |
| Starší verze úložiště objektů BLOB | LRS/GRS/RA-GRS | Blob Storage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete vytvořit účet úložiště pro obecné účely v2 pomocí Azure CLI, nejdřív vytvořte novou skupinu prostředků voláním příkazu [AZ Group Create](/cli/azure/group#az_group_create) .

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

Pokud si nejste jisti, jakou oblast máte zadat v parametru `--location`, můžete pomocí příkazu [az account list-locations](/cli/azure/account#az_account_list) načíst seznam podporovaných oblastí pro vaše předplatné.

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

V dalším kroku vytvořte účet úložiště standard pro obecné účely v2 s geograficky redundantním úložištěm s přístupem pro čtení pomocí příkazu [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) . Mějte na paměti, že název účtu úložiště musí být v rámci Azure jedinečný, proto nahraďte hodnotu zástupný symbol v závorkách vlastní jedinečnou hodnotou:

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

Pokud chcete povolit hierarchický obor názvů pro účet úložiště, který se má použít [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), zahrňte `--enable-hierarchical-namespace true` parametr volání příkazu **AZ Storage Account Create** . Vytvoření hierarchického oboru názvů vyžaduje Azure CLI verze 2.0.79 nebo novější.

Následující tabulka uvádí, které hodnoty se mají použít pro `-sku` `-kind` parametry a k vytvoření konkrétního typu účtu úložiště s požadovanou konfigurací redundance.

| Typ účtu úložiště | Podporované konfigurace redundance | Hodnota pro parametr-Kind | Možné hodnoty pro parametr-SKU | Podporuje hierarchický obor názvů |
|--|--|--|--|--|
| Standardní pro obecné účely v2 | LRS/GRS/RA-GRS/ZRS/GZRS/RA-GZRS | StorageV2 | Standard_LRS/Standard_GRS/Standard_RAGRS/Standard_ZRS/Standard_GZRS/Standard_RAGZRS | Yes |
| Objekty blob bloku úrovně Premium | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | Yes |
| Soubory ke sdílení souborů úrovně Premium | LRS/ZRS | Úložiště | Premium_LRS/Premium_ZRS | No |
| Objekty blob stránky úrovně Premium | LRS | StorageV2 | Premium_LRS | No |
| Starší verze Standard pro obecné účely v1 | LRS/GRS/RA-GRS | Storage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |
| Starší verze úložiště objektů BLOB | LRS/GRS/RA-GRS | Blob Storage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |

# <a name="template"></a>[Šablona](#tab/template)

K nasazení Správce prostředků šablony pro vytvoření účtu úložiště můžete použít buď Azure PowerShell, nebo rozhraní příkazového řádku Azure. Šablona použitá v tomto článku s návody je od [Azure Resource Manager šablon pro rychlý Start](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Chcete-li spustit skripty, vyberte příkaz **zkusit** pro otevření Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Tato šablona slouží pouze jako příklad. K dispozici je mnoho nastavení účtu úložiště, která nejsou nakonfigurována jako součást této šablony. Například pokud chcete použít [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), měli byste tuto šablonu upravit nastavením `isHnsEnabledad` vlastnosti `StorageAccountPropertiesCreateParameters` objektu na `true` .

Informace o tom, jak upravit tuto šablonu nebo vytvořit nové, najdete v těchto tématech:

- [Azure Resource Manager dokumentaci](../../azure-resource-manager/index.yml).
- [Odkaz na šablonu účtu úložiště](/azure/templates/microsoft.storage/allversions)
- [Další ukázky šablon účtu úložiště](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

---

## <a name="delete-a-storage-account"></a>Odstranění účtu úložiště

Odstranění účtu úložiště odstraní celý účet včetně všech dat v účtu a nedá se vrátit zpátky.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)přejděte na účet úložiště.
1. Klikněte na **Odstranit**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete odstranit účet úložiště, použijte příkaz [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete odstranit účet úložiště, použijte příkaz [AZ Storage Account Delete](/cli/azure/storage/account#az-storage-account-delete) :

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Šablona](#tab/template)

Pokud chcete odstranit účet úložiště, použijte buď Azure PowerShell, nebo rozhraní příkazového řádku Azure.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Alternativně můžete odstranit skupinu prostředků, která odstraní účet úložiště a všechny další prostředky v dané skupině prostředků. Další informace o odstranění skupiny prostředků najdete v tématu [odstranění prostředků a skupiny prostředků](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Odstraněný účet úložiště není možné obnovit ani není možné načíst žádný obsah, který byl součástí účtu před jeho odstraněním. Nezapomeňte si před odstraněním účtu zazálohovat všechno, co chcete uložit. To platí také pro všechny prostředky v rámci účtu – po odstranění jsou objekt blob, tabulka, fronta nebo soubor odstraněny trvale.
>
> Pokud se pokusíte odstranit účet úložiště, který je přidružený virtuálnímu počítači Azure, můžete obdržet chybu, že se účet úložiště stále používá. Pomoc při řešení této chyby najdete v tématu [řešení chyb při odstraňování účtů úložiště](/troubleshoot/azure/virtual-machines/welcome-virtual-machines).

## <a name="next-steps"></a>Další kroky

- [Přehled účtu úložiště](storage-account-overview.md)
- [Upgrade na účet úložiště pro obecné účely v2](storage-account-upgrade.md)
- [Přesunutí účtu úložiště do jiné oblasti](storage-account-move.md)
- [Obnovení odstraněného účtu úložiště](storage-account-recover.md)