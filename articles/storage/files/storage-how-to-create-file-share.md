---
title: Vytvoření sdílené složky Azure
titleSuffix: Azure Files
description: Jak vytvořit sdílenou složku Azure pomocí portálu Azure, PowerShellu nebo azure cli.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596872"
---
# <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Chcete-li vytvořit sdílenou složku Azure, musíte odpovědět na tři otázky o tom, jak ji budete používat:

- **Jaké jsou požadavky na výkon pro sdílenou složku Azure?**  
    Azure Files nabízí standardní sdílené složky, které jsou hostované na pevném disku (hdd-based) hardware a sdílené složky premium, které jsou hostované na ssd diskový (SSD založené) hardware.

- **Jakou velikost sdílené složky potřebujete?**  
    Standardní sdílené složky mohou zahrnovat až 100 TiB, ale tato funkce není ve výchozím nastavení povolena; Pokud potřebujete sdílenou složku, která je větší než 5 TiB, budete muset povolit funkci sdílení velkých souborů pro váš účet úložiště. Sdílené složky Premium mohou span až 100 TiB bez zvláštního nastavení, nicméně premium sdílení souborů jsou zřízeny, spíše než platit, jak jdete jako standardní sdílené složky. To znamená, že zřizování sdílené složky mnohem větší, než potřebujete, zvýší celkové náklady na úložiště.

- **Jaké jsou vaše požadavky na redundanci pro sdílenou složku Azure?**  
    Standardní sdílené složky nabízejí místně redundantní (LRS), zónové redundantní (ZRS), geograficky redundantní (GRS) nebo geograficky zónově redundantní (GZRS), ale funkce velké sdílení souborů je podporována pouze na místně redundantních a zónově redundantních sdílených složek. Sdílené složky Premium nepodporují žádnou formu geografické redundance.

    Sdílené složky Premium jsou k dispozici s místní redundance ve většině oblastí, které nabízejí účty úložiště a s redundance zóny v menší podmnožinu oblastí. Pokud chcete zjistit, jestli jsou akcie prémiových souborů momentálně dostupné ve vaší oblasti, podívejte se na stránku [produktů dostupných podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=storage) pro Azure. Informace o oblastech, které podporují ZRS, najdete [v tématu redundance úložiště Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Další informace o těchto třech možnostech najdete v [tématu Plánování nasazení souborů Azure](storage-files-planning.md).

## <a name="prerequisites"></a>Požadavky
- Tento článek předpokládá, že jste už vytvořili předplatné Azure. Pokud ještě nemáte předplatné, vytvořte si před zahájením [bezplatný účet.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Pokud máte v úmyslu používat Azure PowerShell, [nainstalujte nejnovější verzi](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Pokud máte v úmyslu používat azure cli, [nainstalujte nejnovější verzi](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Sdílené složky Azure se nasazují do *účtů úložiště*, což jsou objekty nejvyšší úrovně, které představují sdílený fond úložiště. Tento fond úložiště lze použít k nasazení více sdílených složek souborů. 

Azure podporuje více typů účtů úložiště pro různé scénáře úložiště, které zákazníci mohou mít, ale existují dva hlavní typy účtů úložiště pro soubory Azure. Typ účtu úložiště, který potřebujete vytvořit, závisí na tom, zda chcete vytvořit standardní sdílenou složku nebo sdílenou složku premium: 

- **Účty úložiště pro obecné účely verze 2 (GPv2):** Účty úložiště GPv2 umožňují nasazovat sdílené složky Azure na standardním hardwaru založeném na pevném disku (na pevném disku). Kromě ukládání sdílených složek Azure mohou účty úložiště GPv2 ukládat další prostředky úložiště, jako jsou kontejnery objektů blob, fronty nebo tabulky. 

- **Účty úložiště úložiště úložiště Úložiště**souborů : Účty úložiště Úložiště souborů umožňují nasazovat sdílené složky Azure na hardware založený na prémiovém nebo ssd disku (SSD). Účty FileStorage lze použít pouze k ukládání sdílených složek Azure; žádné jiné prostředky úložiště (kontejnery objektů blob, fronty, tabulky atd.) nelze nasadit v účtu FileStorage.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Pokud chcete vytvořit účet úložiště přes portál Azure, vyberte **+ Vytvořit prostředek** z řídicího panelu. Ve výsledném vyhledávacím okně Azure Marketplace vyhledejte **účet úložiště** a vyberte výsledný výsledek hledání. To povede k přehledu stránky pro účty úložiště; vyberte **Vytvořit,** chcete-li pokračovat v průvodci vytvořením účtu úložiště.

![Snímek obrazovky s možností rychlého vytvoření účtu úložiště v prohlížeči](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>Sekce Základy
První část, kterou chcete vyplnit, je označena jako **Základy**. Obsahuje všechna povinná pole pro vytvoření účtu úložiště. Chcete-li vytvořit účet úložiště GPv2, ujistěte se, že je přepínač **Výkon** nastaven ý na *standardní* a rozevírací seznam **Druhu účtu** je vybrán na *StorageV2 (pro obecné účely v2)*.

![Snímek obrazovky s přepínatým tlačítkem Výkon s vybranou možností Standard a typ účtu s vybranou možností StorageV2](media/storage-how-to-create-file-share/create-storage-account-1.png)

To create a FileStorage storage account, ensure the **Performance** radio button is set to *Premium* and the **Account kind** drop-down list is selected to *FileStorage*.

![Snímek obrazovky s přepínatým tlačítkem Výkon s vybranou možností Premium a typ účtu s vybranou možností FileStorage](media/storage-how-to-create-file-share/create-storage-account-2.png)

Ostatní základní pole jsou nezávislá na výběru účtu úložiště:
- **Předplatné**: Předplatné pro účet úložiště, které mají být nasazeny do. 
- **Skupina prostředků**: Skupina prostředků pro účet úložiště, do který se má nasadit. Můžete vytvořit novou skupinu prostředků nebo použít existující skupinu prostředků. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků.
- **Název účtu úložiště**: Název prostředku účtu úložiště, který má být vytvořen. Tento název musí být globálně jedinečný, ale jinak může libovolný název, který si přejete. Název účtu úložiště se použije jako název serveru při připojení sdílené složky Azure přes SMB.
- **Umístění**: Oblast pro účet úložiště, které mají být nasazeny do. Může se jedná o oblast přidruženou ke skupině prostředků nebo o jakoukoli jinou dostupnou oblast.
- **Replikace**: I když je toto replikace označeno, toto pole ve skutečnosti znamená **redundanci**; toto je požadovaná úroveň redundance: místně redundance (LRS), redundance zóny (ZRS), geografická redundance (GRS) a redundance geografické zóny. Tento rozevírací seznam také obsahuje geografickou redundanci přístuppro čtení (RA-GRS) a redundanci geografické zóny přístup pro čtení (RA-GZRS), které se nevztahují na sdílené složky Azure; všechny sdílené složky vytvořené v účtu úložiště s těmito vybranými bude ve skutečnosti geograficky redundantní nebo geograficky zónově redundantní. V závislosti na vaší oblasti nebo vybraném typu účtu úložiště nemusí být některé možnosti redundance povoleny.
- **Úroveň přístupu**: Toto pole se nevztahuje na soubory Azure, takže můžete zvolit jedno z přepínacích tlačítek.

#### <a name="the-networking-blade"></a>Síťový list
Část Síť umožňuje konfigurovat možnosti sítě. Tato nastavení jsou volitelná pro vytvoření účtu úložiště a v případě potřeby je lze později nakonfigurovat. Další informace o těchto možnostech najdete v [tématu Aspekty sítě Azure Files](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>Pokročilá čepel
Pokročilá část obsahuje několik důležitých nastavení pro sdílené složky Azure:

- **Je vyžadován zabezpečený přenos**: Toto pole označuje, zda účet úložiště vyžaduje šifrování při přenosu pro komunikaci s účtem úložiště. Doporučujeme, aby toto ponechat povoleno, ale pokud požadujete podporu SMB 2.1, musíte zakázat. Doporučujeme, pokud zakážete šifrování, které omezí přístup účtu úložiště k virtuální síti s koncovými body služby a nebo privátní koncové body.
- **Velké sdílené složky**: Toto pole umožňuje účet úložiště pro sdílené složky zahrnující až 100 TiB. Povolení této funkce omezí váš účet úložiště pouze místně redundantní a zónově redundantní možnosti úložiště. Jakmile je pro velké sdílené složky povolen účet úložiště GPv2, nelze zakázat funkci sdílení velkých souborů. Účty úložiště Úložiště souboru (účty úložiště pro sdílené složky premium) nemají tuto možnost, protože všechny sdílené složky premium mohou škálovat až na 100 TiB. 

![Snímek obrazovky s důležitými pokročilými nastaveními, která se vztahují k souborům Azure](media/storage-how-to-create-file-share/create-storage-account-3.png)

Ostatní nastavení, které jsou k dispozici na kartě rozšířené (blob soft delete, hierarchický obor názvů pro Azure Data Lake storage gen 2 a NFSv3 pro úložiště objektů blob) se nevztahují na soubory Azure.

#### <a name="tags"></a>Značky
Značky jsou dvojice název/hodnota, které umožňují kategorizovat prostředky a zobrazit konsolidovanou fakturaci použitím stejné značky pro více prostředků a skupin prostředků. Ty jsou volitelné a lze použít po vytvoření účtu úložiště.

#### <a name="review--create"></a>Recenze + vytvořit
Posledním krokem k vytvoření účtu úložiště je výběr tlačítka **Vytvořit** na kartě **Revize + vytvoření.** Toto tlačítko nebude k dispozici, pokud nejsou vyplněna všechna povinná pole pro účet úložiště.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Chcete-li vytvořit účet úložiště pomocí prostředí `New-AzStorageAccount` PowerShell, použijeme rutinu. Tato rutina má mnoho možností; jsou zobrazeny pouze požadované možnosti. Další informace o rozšířených možnostech naleznete v [ `New-AzStorageAccount` dokumentaci k rutině](/powershell/module/az.storage/new-azstorageaccount).

Pro zjednodušení vytvoření účtu úložiště a následné sdílení souborů uložíme několik parametrů do proměnných. Obsah proměnné můžete nahradit hodnotami libovolné, které chcete, ale všimněte si, že název účtu úložiště musí být globálně jedinečný.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Chcete-li vytvořit účet úložiště schopný ukládat standardní sdílené složky Azure, použijeme následující příkaz. Parametr `-SkuName` se vztahuje k typu požadované redundance; Pokud si přejete geograficky redundantní nebo geograficky redundantní `-EnableLargeFileShare` účet úložiště, musíte také odebrat parametr.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Chcete-li vytvořit účet úložiště schopný ukládat sdílené složky premium Azure, použijeme následující příkaz. Všimněte `-SkuName` si, že parametr `Premium` byl změněn tak, aby zahrnoval`LRS`a to jak požadovanou úroveň redundance místně redundantní ( ). Parametr `-Kind` je `FileStorage` místo `StorageV2` toho, protože sdílené složky premium musí být vytvořeny v účtu úložiště FileStorage namísto účtu úložiště GPv2.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Chcete-li vytvořit účet úložiště pomocí příkazového příkazu Azure CLI, použijeme příkaz vytvořit účet úložiště az. Tento příkaz má mnoho možností; jsou zobrazeny pouze požadované možnosti. Další informace o rozšířených možnostech naleznete v [ `az storage account create` dokumentaci k příkazům](/cli/azure/storage/account).

Pro zjednodušení vytvoření účtu úložiště a následné sdílení souborů uložíme několik parametrů do proměnných. Obsah proměnné můžete nahradit hodnotami libovolné, které chcete, ale všimněte si, že název účtu úložiště musí být globálně jedinečný.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Chcete-li vytvořit účet úložiště schopný ukládat standardní sdílené složky Azure, použijeme následující příkaz. Parametr `--sku` se vztahuje k typu požadované redundance; Pokud si přejete geograficky redundantní nebo geograficky redundantní `--enable-large-file-share` účet úložiště, musíte také odebrat parametr.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Chcete-li vytvořit účet úložiště schopný ukládat sdílené složky premium Azure, použijeme následující příkaz. Všimněte `--sku` si, že parametr `Premium` byl změněn tak, aby zahrnoval`LRS`a to jak požadovanou úroveň redundance místně redundantní ( ). Parametr `--kind` je `FileStorage` místo `StorageV2` toho, protože sdílené složky premium musí být vytvořeny v účtu úložiště FileStorage namísto účtu úložiště GPv2.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Vytvoření sdílené složky
Po vytvoření účtu úložiště zbývá už jen vytvořit sdílenou složku. Tento proces je většinou stejný bez ohledu na to, zda používáte sdílenou složku premium nebo standardní sdílenou složku. Hlavním rozdílem je **kvóta** a její význam.

Pro standardní sdílené složky je to horní hranice sdílené složky Azure, za kterou koncoví uživatelé nemohou přejít. Primárním účelem kvóty pro standardní sdílenou složku je rozpočet: "Nechci, aby tento podíl souboru rostl za tento bod". Pokud není zadána kvóta, standardní sdílená složka může span až 100 TiB (nebo 5 TiB, pokud velký soubor sdílené složky vlastnost není nastavena pro účet úložiště).

Pro sdílené složky premium je kvóta přetížena na **průměrnou zřízenou velikost**. Zřízená velikost je částka, která se vám bude účtovat, bez ohledu na skutečné využití. Při zřizování sdílené složky premium, chcete zvážit dva faktory: 1) budoucí růst sdílené složky z hlediska využití prostoru a 2) VOPS potřebné pro vaše úlohy. Každý zřízený GiB vás opravňuje k další rezervované a praskla VOPS. Další informace o tom, jak plánovat pro sdílenou složku premium, naleznete [v tématu zřizování akcií souborů premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

# <a name="portal"></a>[Portál](#tab/azure-portal)
Pokud jste právě vytvořili účet úložiště, můžete na něj přejít z obrazovky nasazení tak, že vyberete **Přejít na prostředek**. Pokud jste již dříve vytvořili účet úložiště, můžete přejít na něj prostřednictvím skupiny prostředků, která jej obsahuje. V účtu úložiště vyberte dlaždici s názvem **Sdílené složky** (můžete také přejít na **sdílené složky** souborů prostřednictvím obsahu účtu úložiště).

![Snímek obrazovky dlaždice Sdílení souborů](media/storage-how-to-create-file-share/create-file-share-1.png)

V seznamu sdílených složek byste měli vidět všechny sdílené složky, které jste dříve vytvořili v tomto účtu úložiště; prázdnou tabulku, pokud ještě nebyly vytvořeny žádné sdílené složky. Výběrem **+ sdílení souborů** vytvořte novou sdílenou složku.

Na obrazovce by se měl zobrazit nový okno sdílené složky. Chcete-li vytvořit sdílenou složku, vyplňte pole v novém okně sdílené složky:

- **Název**: název sdílené složky, která má být vytvořena.
- **Kvóta**: Kvóta sdílené složky pro standardní sdílené složky; zřízená velikost sdílené složky pro sdílené složky premium.

Vyberte **Vytvořit,** chcete-li dokončit vytvoření nové sdílené složky. Všimněte si, že pokud váš účet úložiště je ve virtuální síti, nebude možné úspěšně vytvořit sdílenou složku Azure, pokud váš klient je také ve virtuální síti. Můžete také obejít toto omezení bodu v čase `New-AzRmStorageShare` pomocí rutiny Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Můžete vytvořit sdílenou složku [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) Azure s rutinou. Následující příkazy Prostředí PowerShell předpokládají, `$resourceGroupName` že `$storageAccountName` jste nastavili proměnné a jak je definováno výše při vytváření účtu úložiště s Azure PowerShellem. 

> [!Important]  
> Pro sdílené složky `-QuotaGiB` premium parametr odkazuje na zřízenou velikost sdílené složky. Zřízená velikost sdílené složky je částka, za kterou se vám bude účtovat, bez ohledu na použití. Standardní sdílené složky se účtují na základě použití, nikoli zřízené velikosti.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> Název vaší sdílené složky musí obsahovat jen malá písmena. Úplné podrobnosti o pojmenování sdílených složek a souborů naleznete v [tématu Pojmenování a odkazování na sdílené složky, adresáře, soubory a metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Než budeme moct vytvořit sdílenou složku Azure s Azure CLI, musíte získat klíč účtu úložiště autorizovat operaci vytvoření sdílené složky s. To lze provést [`az storage account keys list`](/cli/azure/storage/account/keys) pomocí příkazu:

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

Jakmile budete mít klíč účtu úložiště, můžete vytvořit [`az storage share create`](/cli/azure/storage/share) sdílenou složku Azure pomocí příkazu. 

> [!Important]  
> Pro sdílené složky `--quota` premium parametr odkazuje na zřízenou velikost sdílené složky. Zřízená velikost sdílené složky je částka, za kterou se vám bude účtovat, bez ohledu na použití. Standardní sdílené složky se účtují na základě použití, nikoli zřízené velikosti.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Tento příkaz se nezdaří, pokud je účet úložiště obsažen ve virtuální síti a počítač, ze kterých se odvoláváte, není součástí virtuální sítě. Toto omezení v okamžiku můžete obejít pomocí `New-AzRmStorageShare` rutiny Azure PowerShell, jak je popsáno výše, nebo spuštěním příkazového příkazového příkazu Azure z počítače, který je součástí virtuální sítě, včetně připojení VPN.

---

> [!Note]  
> Název vaší sdílené složky musí obsahovat jen malá písmena. Úplné podrobnosti o pojmenování sdílených složek a souborů naleznete v [tématu Pojmenování a odkazování na sdílené složky, adresáře, soubory a metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="next-steps"></a>Další kroky
- [Naplánujte nasazení souborů Azure](storage-files-planning.md) nebo [plán pro nasazení Azure File Sync](storage-sync-files-planning.md). 
- [Přehled sítě](storage-files-networking-overview.md).
- Připojte a připojte sdílenou složku ve [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)a [Linuxu](storage-how-to-use-files-linux.md).