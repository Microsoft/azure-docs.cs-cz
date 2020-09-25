---
title: Vytvoření sdílené složky Azure
titleSuffix: Azure Files
description: Postup vytvoření sdílené složky Azure pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 15f9387aac909c0245d25b3a208ed24444b2b343
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329384"
---
# <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Pokud chcete vytvořit sdílenou složku Azure, musíte odpovědět na tři otázky týkající se toho, jak ji budete používat:

- **Jaké jsou požadavky na výkon pro sdílenou složku Azure?**  
    Soubory Azure nabízí standardní sdílené složky (včetně optimalizovaných, Hot a studených sdílených složek), které jsou hostované na hardwaru založeném na pevných discích (HDD), a na špičkových sdílených složkách, které se hostují na discích založených na disku SSD (Solid-State).

- **Jakou velikost sdílení souborů potřebujete?**  
    Standardní sdílené složky můžou zahrnovat až 100 TiB, ale tato funkce není ve výchozím nastavení povolená. Pokud potřebujete sdílenou složku, která je větší než 5 TiB, budete muset povolit funkci Velká sdílená složka pro váš účet úložiště. Soubory úrovně Premium můžou zahrnovat až 100 TiB bez jakýchkoli zvláštních nastavení, ale prémiové sdílené složky se zřídí místo toho, aby platily jako standardní sdílené složky. To znamená, že zřízení sdílené složky bude mnohem větší, než kolik potřebujete, a zvýšit tak celkové náklady na úložiště.

- **Jaké jsou vaše požadavky na redundanci sdílené složky Azure?**  
    Standardní sdílené složky nabízí místně redundantní úložiště (LRS), zónu redundantního úložiště (ZRS), geograficky redundantního úložiště (GRS) nebo geograficky redundantního úložiště (GZRS), ale funkce pro velkou sdílenou složku je podporovaná jenom u místně redundantních a redundantních sdílených složek v zóně. Soubory úrovně Premium nepodporují žádnou formu geografické redundance.

    Prémiové sdílené složky jsou k dispozici s místní redundancí ve většině oblastí, které nabízí účty úložiště a redundanci zóny v menší podmnožině oblastí. Pokud chcete zjistit, jestli jsou v současnosti ve vaší oblasti dostupné sdílené složky Premium, přečtěte si stránku [Dostupné produkty podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=storage) pro Azure. Informace o oblastech, které podporují ZRS, najdete v tématu [Azure Storage redundance](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Další informace o těchto třech volbách najdete v tématu [Plánování nasazení služby soubory Azure](storage-files-planning.md).

## <a name="prerequisites"></a>Požadavky
- V tomto článku se předpokládá, že jste už vytvořili předplatné Azure. Pokud ještě nemáte předplatné, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Pokud máte v úmyslu použít Azure PowerShell, [nainstalujte nejnovější verzi](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Pokud máte v úmyslu používat rozhraní příkazového řádku Azure, [nainstalujte nejnovější verzi](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Sdílené složky Azure se nasazují do *účtů úložiště*, což jsou objekty nejvyšší úrovně, které představují sdílený fond úložiště. Tento fond úložiště se dá použít k nasazení několika sdílených složek. 

Azure podporuje více typů účtů úložiště pro různé scénáře úložiště, které mohou mít zákazníci, ale existují dva hlavní typy účtů úložiště pro soubory Azure. Typ účtu úložiště, který chcete vytvořit, závisí na tom, jestli chcete vytvořit standardní sdílenou složku nebo sdílenou složku prémiových souborů: 

- **Účty úložiště pro obecné účely verze 2 (GPv2)**: účty úložiště GPv2 umožňují nasadit sdílené složky Azure na hardwaru založeném na standardu a na pevných discích (HDD). Kromě ukládání sdílených složek Azure můžou účty úložiště GPv2 ukládat i další prostředky úložiště, jako jsou kontejnery objektů blob, fronty nebo tabulky. Sdílené složky lze nasadit do optimalizované transakce (výchozí), horké nebo studené úrovně.

- **Účty úložiště**úložiště: účty úložiště úložiště umožňují nasadit sdílené složky Azure na hardware Premium/Solid-State (SSD) na disku (SSD). Účty úložiště souborů se dají použít jenom k ukládání sdílených složek Azure. v účtu úložiště úložiště se nedají nasadit žádné další prostředky úložiště (kontejnery objektů blob, fronty, tabulky atd.).

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Pokud chcete účet úložiště vytvořit přes Azure Portal, vyberte v řídicím panelu **+ vytvořit prostředek** . Ve výsledném okně hledání Azure Marketplace vyhledejte **účet úložiště** a vyberte výsledný výsledek hledání. To bude mít za následek stránku s přehledem pro účty úložiště. Vyberte **vytvořit** a pokračujte v Průvodci vytvořením účtu úložiště.

![Snímek obrazovky s možností rychlého vytvoření účtu úložiště v prohlížeči](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>Oddíl základy
První část, která se má dokončit pro vytvoření účtu úložiště, je označená **základy**. Obsahuje všechna povinná pole pro vytvoření účtu úložiště. Pokud chcete vytvořit účet úložiště GPv2, ujistěte se, že je přepínač **výkon** nastavený *na standardní* a rozevírací seznam **druh účtu** je vybraný jako *StorageV2 (pro obecné účely v2)*.

![Snímek obrazovky s přepínačem výkonu s vybraným standardním a druhem účtu s StorageV2](media/storage-how-to-create-file-share/create-storage-account-1.png)

Pokud chcete vytvořit účet úložiště úložiště, ujistěte se, že je přepínač **výkon** nastavený na hodnotu *Premium* a rozevírací seznam **druh účtu** je vybraný jako *úložiště*.

![Snímek obrazovky s přepínačem výkonu s vybraným a druhem účtu s vybraným úložištěm](media/storage-how-to-create-file-share/create-storage-account-2.png)

Ostatní pole základy jsou nezávislá na volbě účtu úložiště:
- **Předplatné**: předplatné pro účet úložiště, do kterého se má nasadit. 
- **Skupina prostředků**: Skupina prostředků pro účet úložiště, do kterého se má nasadit. Můžete buď vytvořit novou skupinu prostředků, nebo použít existující skupinu prostředků. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků.
- **Název účtu úložiště**: název prostředku účtu úložiště, který se má vytvořit. Tento název musí být globálně jedinečný, ale jinak může být libovolný název, který si přejete. Název účtu úložiště se použije jako název serveru při připojení sdílené složky Azure přes SMB.
- **Umístění**: oblast, do které se má účet úložiště nasadit. Může to být oblast přidružená ke skupině prostředků nebo libovolné jiné dostupné oblasti.
- **Replikace**: i když se jedná o replikaci s označením, toto pole ve skutečnosti znamená **redundanci**. Toto je požadovaná úroveň redundance: místně redundance (LRS), redundance zóny (ZRS), geografická redundance (GRS) a redundance geografické zóny. Tento rozevírací seznam taky obsahuje geografickou redundanci přístupu pro čtení (RA-GRS) a redundanci s přístupem pro čtení (RA-GZRS), které se nevztahují na sdílené složky Azure. jakákoli sdílená složka vytvořená v účtu úložiště s těmito vybranými možnostmi bude ve skutečnosti buď geograficky redundantní, nebo geografické zóny, v uvedeném pořadí. V závislosti na vaší oblasti nebo typu vybraného účtu úložiště nemusí být některé možnosti redundance povoleny.
- **Úroveň přístupu objektu BLOB**: Toto pole se nevztahuje na soubory Azure, takže můžete zvolit jeden z přepínačů. 

> [!Important]  
> Výběr úrovně přístupu objektu BLOB nemá vliv na úroveň sdílené složky.

#### <a name="the-networking-blade"></a>Okno síť
Část síť vám umožní nakonfigurovat možnosti sítě. Tato nastavení jsou volitelná pro vytvoření účtu úložiště a v případě potřeby je můžete nakonfigurovat později. Další informace o těchto možnostech najdete v tématu věnovaném [důležitým informacím o sítích Azure Files](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>Okno Upřesnit
Oddíl Upřesnit obsahuje několik důležitých nastavení pro sdílené složky Azure:

- **Vyžadován zabezpečený přenos**: Toto pole označuje, zda účet úložiště vyžaduje při přenosu přenos dat do účtu úložiště šifrování. Doporučujeme, abyste tuto možnost nechali zapnuti, ale pokud požadujete podporu protokolu SMB 2,1, je nutné ji zakázat. Doporučujeme zakázat šifrování, které omezíte přístup účtu úložiště k virtuální síti pomocí koncových bodů služby a/nebo privátních koncových bodů.
- **Velké sdílené složky**: Toto pole umožňuje, aby byl účet úložiště pro sdílené složky rozložený až do 100 TIB. Povolením této funkce omezíte účet úložiště jenom na místně redundantní a redundantní možnosti úložiště v zóně. Jakmile je pro velké sdílené složky povolený účet úložiště GPv2, nemůžete zakázat možnost sdílení velkých souborů. Účty úložiště úložiště (účty úložiště pro soubory úrovně Premium) nemají tuto možnost, protože všechny sdílené složky Premium se můžou škálovat až na 100 TiB. 

![Snímek obrazovky s důležitými pokročilými nastaveními, která se vztahují na soubory Azure](media/storage-how-to-create-file-share/create-storage-account-3.png)

Další nastavení, která jsou k dispozici na kartě Upřesnit (objekt BLOB Soft-DELETE, hierarchický obor názvů pro úložiště Azure Data Lake Gen 2 a NFSv3 pro úložiště objektů BLOB), se nevztahují na soubory Azure.

#### <a name="tags"></a>Značky
Značky jsou páry název-hodnota, které umožňují kategorizaci prostředků a zobrazení konsolidované fakturace, a to použitím stejné značky na více prostředků a skupin prostředků. Tyto jsou volitelné a dají se použít po vytvoření účtu úložiště.

#### <a name="review--create"></a>Kontrola a vytvoření
Posledním krokem při vytváření účtu úložiště je výběr tlačítka **vytvořit** na kartě **Revize + vytvořit** . Toto tlačítko nebude k dispozici, pokud nejsou vyplněna všechna povinná pole pro účet úložiště.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
K vytvoření účtu úložiště pomocí PowerShellu použijeme `New-AzStorageAccount` rutinu. Tato rutina má mnoho možností; zobrazí se pouze požadované možnosti. Další informace o rozšířených možnostech najdete v [ `New-AzStorageAccount` dokumentaci k rutině](/powershell/module/az.storage/new-azstorageaccount).

Abychom zjednodušili vytváření účtu úložiště a další sdílené složky, uložíme do proměnných několik parametrů. Obsah proměnné můžete nahradit libovolnými hodnotami, ale Všimněte si, že název účtu úložiště musí být globálně jedinečný.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

K vytvoření účtu úložiště, který podporuje ukládání standardních sdílených složek Azure, použijeme následující příkaz. `-SkuName`Parametr se vztahuje k typu redundance; Pokud si přejete, aby byl účet geograficky redundantního úložiště nebo geograficky redundantního úložiště, je nutné také odebrat tento `-EnableLargeFileShare` parametr.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

K vytvoření účtu úložiště, který podporuje ukládání prémiových sdílených složek Azure, použijeme následující příkaz. Všimněte si, že `-SkuName` parametr se změnil tak, aby zahrnoval i `Premium` požadovanou úroveň redundance místně redundantního ( `LRS` ). `-Kind`Parametr je místo toho, aby se `FileStorage` v účtu úložiště úložiště `StorageV2` místo účtu úložiště GPv2 musely vytvořit soubory Premium.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
K vytvoření účtu úložiště pomocí Azure CLI použijeme příkaz AZ Storage Account Create. Tento příkaz má mnoho možností; zobrazí se pouze požadované možnosti. Další informace o rozšířených možnostech najdete v [ `az storage account create` dokumentaci k příkazům](/cli/azure/storage/account).

Abychom zjednodušili vytváření účtu úložiště a další sdílené složky, uložíme do proměnných několik parametrů. Obsah proměnné můžete nahradit libovolnými hodnotami, ale Všimněte si, že název účtu úložiště musí být globálně jedinečný.

```bash
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

K vytvoření účtu úložiště, který podporuje ukládání standardních sdílených složek Azure, použijeme následující příkaz. `--sku`Parametr se vztahuje k typu redundance; Pokud si přejete, aby byl účet geograficky redundantního úložiště nebo geograficky redundantního úložiště, je nutné také odebrat tento `--enable-large-file-share` parametr.

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

K vytvoření účtu úložiště, který podporuje ukládání prémiových sdílených složek Azure, použijeme následující příkaz. Všimněte si, že `--sku` parametr se změnil tak, aby zahrnoval i `Premium` požadovanou úroveň redundance místně redundantního ( `LRS` ). `--kind`Parametr je místo toho, aby se `FileStorage` v účtu úložiště úložiště `StorageV2` místo účtu úložiště GPv2 musely vytvořit soubory Premium.

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Vytvoření sdílené složky
Po vytvoření účtu úložiště je vše, co zbývá, vytvořit sdílenou složku. Tento proces je většinou stejný bez ohledu na to, jestli používáte sdílenou složku Premium nebo standardní sdílení souborů. Měli byste zvážit následující rozdíly.

Standardní sdílené složky můžou být nasazené na jednu z úrovní Standard: transakce je optimalizovaná (výchozí), horká nebo studená. Jedná se o vrstvu sdílení souborů, která není ovlivněná **úrovní přístupu objektu BLOB** účtu úložiště (Tato vlastnost se vztahuje pouze na Azure Blob Storage – nesouvisí s Azure Files vůbec). Úroveň sdílené složky můžete kdykoli změnit poté, co byla nasazena. Soubory úrovně Premium není možné přímo převést na standardní sdílené složky na jakékoli úrovni Standard.

> [!Important]  
> Můžete přesouvat sdílené složky mezi vrstvami v rámci typů účtů úložiště GPv2 (transakce optimalizovaná, horká a studená). Sdílení se pohybuje mezi úrovněmi. transakce: přechod z Hotter úrovně na úroveň chladiče bude za každý soubor ve sdílené složce účtovat poplatek za transakci zápisu ve vrstvě chladicího objektu, zatímco přesun z úrovně chladicího počítače na Hotter vrstvu bude za každý soubor sdílené složky účtovat poplatek za transakce čtení ve studené vrstvě.

Vlastnost **Quota** znamená něco jiného, co se liší od úrovně Premium a standardní sdílení souborů:

- U standardních sdílených složek se jedná o horní hranici sdílené složky Azure, nad kterou koncoví uživatelé nemůžou přejít. Hlavním účelem pro kvótu pro standardní sdílenou složku je rozpočtový stav: "Nechci, aby tato sdílená složka překročila rámec tohoto bodu". Pokud není zadaná kvóta, může standardní sdílená složka zahrnovat až 100 TiB (nebo 5 TiB, pokud pro účet úložiště není nastavená vlastnost sdílené složky velkých souborů).

- U sdílených složek úrovně Premium je kvóta přetížená, což znamená **zřízené velikosti**. Zřízená velikost je množství, které se vám bude účtovat bez ohledu na skutečné využití. Když zřizujete sdílenou složku Premium, budete chtít vzít v úvahu dva faktory: 1) budoucí nárůst podílu z perspektivy využití prostoru a 2) IOPS požadované pro vaše zatížení. Každý zřízený GiB vám opravňuje k dodatečnému rezervovanému a roztržení IOPS. Další informace o tom, jak naplánovat pro sdílenou složku Premium, najdete v tématu [zřizování souborů ke sdílení prémiových souborů](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Pokud jste právě vytvořili účet úložiště, můžete na něj přejít z obrazovky nasazení výběrem možnosti **Přejít k prostředku**. Pokud jste už vytvořili účet úložiště, můžete na něj přejít přes skupinu prostředků, která ho obsahuje. V účtu úložiště vyberte dlaždici s názvem **sdílené složky** (můžete také přejít ke **sdíleným složkám souborů** prostřednictvím obsahu pro účet úložiště).

![Snímek obrazovky dlaždice sdílení souborů](media/storage-how-to-create-file-share/create-file-share-1.png)

V seznamu sdílení souborů byste měli vidět všechny sdílené složky, které jste předtím vytvořili v tomto účtu úložiště. prázdná tabulka, pokud dosud nebyly vytvořeny žádné sdílené složky. Pokud chcete vytvořit novou sdílenou složku, vyberte **+ Shared File** .

Na obrazovce by se mělo zobrazit okno nové sdílení souborů. V okně Nová sdílená složka vyplňte pole, aby se vytvořila sdílená složka:

- **Name (název**): název sdílené složky, která se má vytvořit.
- **Kvóta**: kvóta sdílení souborů pro standardní sdílené složky; zřízená velikost sdílené složky pro sdílené složky prémiových souborů.
- **Úrovně**: Vybraná vrstva pro sdílenou složku. Toto pole je dostupné jenom v **účtu úložiště pro obecné účely (GPv2)**. Můžete zvolit možnost transakce optimalizovaná, horká nebo studená. Úroveň sdílené složky se dá kdykoli změnit. Doporučujeme, abyste při migraci vypnuli nejvyšší možnou úroveň, abyste minimalizovali náklady na transakce a pak po dokončení migrace přepnuli na nižší úroveň.

Vyberte **vytvořit** a dokončí se vytváření nové sdílené složky. Všimněte si, že pokud je váš účet úložiště ve virtuální síti, nebudete moct úspěšně vytvořit sdílenou složku Azure, pokud je váš klient zároveň ve virtuální síti. Pomocí rutiny Azure PowerShell můžete také obejít toto omezení bodu v čase `New-AzRmStorageShare` .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pomocí rutiny můžete vytvořit sdílenou složku Azure [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) . Následující příkazy PowerShellu předpokládají, že jste nastavili proměnné `$resourceGroupName` a `$storageAccountName` jak je definováno výše v části Vytvoření účtu úložiště s Azure PowerShell. 

Následující příklad ukazuje vytvoření sdílené složky s explicitní vrstvou pomocí `-AccessTier` parametru. K tomu je potřeba použít Preview modul AZ. Storage, jak je uvedeno v příkladu. Pokud není nastavená úroveň, protože používáte modul GA AZ. Storage, nebo protože jste tento příkaz nezahrnuli, je výchozí úroveň pro standardní sdílené složky transakce optimalizovaná.

> [!Important]  
> Pro sdílené složky Premium `-QuotaGiB` parametr odkazuje na zřízenou velikost sdílené složky. Zřízená velikost sdílené složky je množství, které se vám bude účtovat bez ohledu na využití. Standardní sdílené složky se účtují na základě využití místo zřízené velikosti.

```powershell
# Update the Azure storage module to use the preview version. You may need to close and 
# reopen PowerShell before running this command. If you are running PowerShell 5.1, ensure 
# the following:
# - Run the below cmdlets as an administrator.
# - Have PowerShellGet 2.2.3 or later. Uncomment the following line to check.
# Get-Module -ListAvailable -Name PowerShellGet
Remove-Module -Name Az.Storage -ErrorAction SilentlyContinue
Uninstall-Module -Name Az.Storage
Install-Module -Name Az.Storage -RequiredVersion "2.1.1-preview" -AllowClobber -AllowPrerelease 

# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

> [!Note]  
> Možnost nastavit a změnit úrovně prostřednictvím PowerShellu je k dispozici ve verzi Preview AZ. Storage PowerShell Module. Tyto rutiny nebo jejich výstup se můžou před vydáním v všeobecně dostupném modulu AZ. Storage PowerShellu změnit, takže si můžete vytvořit skripty.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Sdílenou složku Azure můžete vytvořit pomocí [`az storage share-rm create`](https://docs.microsoft.com/cli/azure/storage/share-rm?view=azure-cli-latest&preserve-view=true#az_storage_share_rm_create) příkazu. Následující příkazy rozhraní příkazového řádku Azure předpokládají, že jste nastavili proměnné `$resourceGroupName` a `$storageAccountName` jak je definováno výše v části Vytvoření účtu úložiště pomocí Azure CLI.

Funkce pro vytvoření nebo přesunutí sdílené složky na určitou úroveň je k dispozici v nejnovější aktualizaci Azure CLI. Aktualizace rozhraní příkazového řádku Azure CLI je specifická pro distribuci operačního systému nebo Linux, kterou používáte. Pokyny, jak aktualizovat rozhraní příkazového řádku Azure CLI v systému, najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

> [!Important]  
> Pro sdílené složky Premium `--quota` parametr odkazuje na zřízenou velikost sdílené složky. Zřízená velikost sdílené složky je množství, které se vám bude účtovat bez ohledu na využití. Standardní sdílené složky se účtují na základě využití místo zřízené velikosti.

```bash
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

> [!Note]  
> Možnost nastavit úroveň s `--access-tier` parametrem se poskytuje ve verzi Preview v nejnovějším balíčku Azure CLI. Tento příkaz nebo jeho výstup se může změnit před označením jako všeobecně k dispozici, takže Vytvořte skripty s ohledem na tyto skutečnosti.

---

> [!Note]  
> Název vaší sdílené složky musí obsahovat jen malá písmena. Podrobné informace o pojmenovávání sdílených složek a souborů najdete v tématu [pojmenování a odkazování na sdílené složky, adresáře, soubory a metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

### <a name="changing-the-tier-of-an-azure-file-share"></a>Změna úrovně sdílené složky Azure
Sdílené složky nasazené v **účtu úložiště pro obecné účely v2 (GPv2)** můžou být v transakci optimalizované, horké nebo studené úrovně. Úroveň sdílené složky Azure můžete kdykoli změnit v závislosti na transakčních cenách, jak je popsáno výše.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Na stránce hlavní účet úložiště vyberte **sdílené složky**  , vyberte dlaždici označené jako **sdílené složky** (můžete také přejít ke **sdíleným složkám souborů** prostřednictvím obsahu pro účet úložiště).

![Snímek obrazovky dlaždice sdílení souborů](media/storage-how-to-create-file-share/create-file-share-1.png)

V seznamu sdílené složky v tabulce vyberte sdílenou složku, pro kterou chcete změnit vrstvu. Na stránce s přehledem sdílení souborů vyberte v nabídce možnost **změnit úroveň** .

![Snímek obrazovky se stránkou s přehledem sdílení souborů s zvýrazněným tlačítkem změnit úroveň](media/storage-how-to-create-file-share/change-tier-0.png)

V výsledném dialogu Vyberte požadovanou úroveň: transakce je optimalizovaná, horká nebo studená.

![Snímek obrazovky dialogového okna změnit úroveň](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Následující rutina prostředí PowerShell předpokládá, že jste nastavili `$resourceGroupName` `$storageAccountName` proměnné,, `$shareName` jak je popsáno v předchozích částech tohoto dokumentu.

```PowerShell
# This cmdlet requires Az.Storage version 2.1.1-preview, which is installed
# in the earlier example.
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Následující příkaz rozhraní příkazového řádku Azure CLI předpokládá, že jste nastavili `$resourceGroupName` `$storageAccountName` proměnné, a, jak je `$shareName` popsáno v předchozích částech tohoto dokumentu.

```bash
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Další kroky
- [Plánování nasazení služby soubory Azure](storage-files-planning.md) nebo [Plánování nasazení Azure File Sync](storage-sync-files-planning.md). 
- [Přehled sítí](storage-files-networking-overview.md).
- Připojení a připojení sdílené složky v [systémech Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)a [Linux](storage-how-to-use-files-linux.md).