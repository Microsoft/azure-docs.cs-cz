---
title: Funkce Immutable Storage služby Azure Blob Storage (Preview) | Microsoft Docs
description: Azure Storage teď pro úložiště objektů blob nabízí podporu WORM umožňující uchovávat data v nesmazatelném a neupravitelném stavu po uživatelem zadanou dobu. Tato funkce umožňuje organizacím v řadě regulovaných odvětví, zejména organizacím zprostředkovatelů a obchodníků, uchovávat data způsobem odpovídajícím nařízení SEC 17a-4(f) a dalším nařízením.
services: storage
author: sangsinh
manager: twooley
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: sangsinh
ms.openlocfilehash: 195537b271c442b954d6d6e6fa8d1491c07822e8
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970240"
---
# <a name="immutable-storage-feature-of-azure-blob-storage-preview"></a>Funkce Immutable Storage služby Azure Blob Storage (Preview)

Funkce Immutable Storage pro objekty blob Azure umožňuje uživatelům uchovávat důležitá podniková data ve službě Azure Blob Storage ve stavu WORM (Write Once Read Many). V tomto stavu jsou data po uživatelem zadanou dobu nesmazatelná a neupravitelná. Po dobu trvání intervalu uchovávání informací je možné objekty blob vytvářet a číst, ale ne upravovat ani odstraňovat.

## <a name="overview"></a>Přehled

Funkce Immutable Storage umožňuje organizacím v řadě regulovaných odvětví, zejména organizacím zprostředkovatelů a obchodníků, uchovávat data způsobem odpovídajícím nařízení SEC 17a-4(f) a dalším nařízením.

Mezi typické případy použití patří:

- **Dodržování legislativních předpisů:** Funkce Immutable Storage pro objekty blob Azure je navržená tak, aby finančním institucím a souvisejícím odvětvím pomohla splnit nařízení SEC 17a-4(f), CFTC 1.31©-(d), FINRA atd.

- **Zabezpečené uchovávání dokumentů:** Uživatelé získají maximální ochranu dat, protože služba Blob Storage zajišťuje, aby data nemohl upravit ani odstranit jiný uživatel, a to ani uživatel s oprávněními správce účtu.

- **Blokování z právních důvodů:** Funkce Immutable Storage pro objekty blob Azure umožňuje uživatelům po požadovanou dobu uchovávat citlivé informace důležité pro soudní proces nebo vyšetřování trestné činnosti atd. ve stavu odolném proti neoprávněné manipulaci.

Funkce Immutable Storage umožňuje následující:

- **Podpora zásad uchovávání informací podle času:** Uživatelé můžou nastavit zásady pro uchovávání dat po zadanou dobu.

- **Podpora zásad blokování z právních důvodů:** Pokud je interval uchovávání informací neznámý, uživatelé můžou nastavit blokování z právních důvodů a uchovávat data v neměnném stavu, dokud se blokování z právních důvodů nezruší.  Když je nastavené blokování z právních důvodů, objekty blob je možné vytvářet a číst, ale ne upravovat nebo odstraňovat. Ke každému blokování z právních důvodů se přiřadí uživatelem definovaná alfanumerická značka, která slouží jako řetězec identifikátor (například ID případu).

- **Podpora všech úrovní objektů blob:** Zásady WORM jsou nezávislé na úrovni služby Azure Blob Storage a budou platit pro všechny úrovně – horkou, studenou i archivní. To umožňuje zákazníkům uchovávat data pro své úlohy na úrovni nejvíce optimalizované z hlediska nákladů při zachování neměnnosti dat.

- **Konfigurace na úrovni kontejneru:** Funkce Immutable Storage umožňuje uživatelům konfigurovat zásady uchovávání informací podle času a značky blokování z právních důvodů na úrovni kontejneru.  Uživatelé můžou vytvořit a uzamknout zásady uchovávání informací podle času, prodloužit intervaly uchovávání informací, nastavit a zrušit blokování z právních důvodů atd. prostřednictvím jednoduchých nastavení na úrovni kontejneru.  Tyto zásady budou platit pro všechny stávající i nové objekty blob v kontejneru.

- **Podpora protokolování auditu:** Každý kontejner obsahuje protokol auditu, který ukazuje až pět příkazů uchovávání informací podle času pro uzamknuté zásady uchovávání informací podle času a maximálně tři protokoly pro prodloužení intervalů uchovávání informací.  V případě uchovávání informací podle času obsahuje protokol ID uživatele, typ příkazu, časová razítka a interval uchovávání informací. V případě blokování z právních důvodů obsahuje protokol ID uživatele, typ příkazu, časová razítka a značky blokování z právních důvodů. Tento protokol se s ohledem na legislativní pokyny v nařízení SEC 17a-4(f) uchovává po celou dobu životnosti kontejneru. Podrobnější protokol všech aktivit roviny řízení najdete v [protokolu aktivit Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Uživatel je zodpovědný za trvalé uchování těchto protokolů, protože se můžou vyžadovat pro legislativní nebo jiné účely.

 Funkce je povolená ve všech veřejných oblastech Azure.

## <a name="how-it-works"></a>Jak to funguje

Funkce Immutable Storage pro objekty blob Azure podporuje dva typy zásad WORM neboli zásad neměnnosti: uchovávání informací podle času a blokování z právních důvodů. Podrobnosti o tom, jak tyto zásady neměnnosti vytvořit, najdete v části [Začínáme](#Getting-started).
Když se na kontejner použije zásada uchovávání informací podle času nebo blokování z právních důvodů, všechny stávající objekty blob se přesunou do neměnného stavu (chráněného proti zápisu a odstranění). Všechny nové objekty blob nahrané do kontejneru se také přesunou do neměnného stavu.

> [!IMPORTANT]
> Kvůli dodržování předpisů v nařízení SEC 17a-4(f) a dalších legislativních předpisů musí být zásada uchovávání informací podle času *uzamknutá*, aby byl objekt blob v neměnném stavu (chráněném před zápisem a odstraněním). Zásady se doporučuje uzamknout v přiměřené lhůtě, obvykle do 24 hodin. *Odemknutý* stav nedoporučujeme používat pro žádné jiné účely, než je krátkodobé vyzkoušení funkce.

 Když se na kontejner použije zásada uchovávání informací podle času, všechny objekty blob v kontejneru zůstanou v neměnném stavu po celou dobu trvání *efektivní* doby uchovávání informací. Efektivní doba uchovávání informací pro stávající objekty blob se rovná rozdílu mezi časem vytvoření objektu blob a uživatelem zadaným intervalem uchovávání informací. Pro nové objekty blob se efektivní doba uchovávání informací rovná uživatelem zadanému intervalu uchovávání informací. Vzhledem k tomu, že uživatelé můžou interval uchovávání informací změnit, se při výpočtu efektivní doby uchovávání informací použije poslední hodnota uživatelem zadaného intervalu uchovávání informací.

> [!TIP]
> Příklad: Uživatel vytvoří zásadu uchovávání informací podle času s pětiletým intervalem uchovávání informací.
> V kontejneru existuje objekt blob testblob1 vytvořený před rokem. Efektivní doba uchovávání informací pro testblob1 bude čtyři roky.
> Do kontejneru se nahraje nový objekt blob testblob2. Efektivní doba uchovávání informací pro tento nový objekt blob bude čtyři roky.

### <a name="legal-holds"></a>Blokování z právních důvodů

V případě blokování z právních důvodů zůstanou všechny stávající i nové objekty blob v neměnném stavu, dokud se blokování z právních důvodů nezruší.
Další informace o tom, jak nastavit a zrušit blokování z právních důvodů, najdete v podrobnostech v části [Začínáme](#Getting-started).

Pro kontejner může platit blokování z právních důvodů i zásada uchovávání informací podle času současně. Všechny objekty blob v kontejneru zůstanou v neměnném stavu, dokud se nezruší všechna blokování z právních důvodů, a to i poté, co uplyne jejich efektivní doba uchovávání informací. A naopak, objekt blob zůstane v neměnném stavu, dokud neuplyne efektivní doba uchovávání informací, a to i po zrušení všech blokování z právních důvodů.
Následující tabulka obsahuje typy operací s objekty blob, které se zakáží v různých scénářích neměnnosti.
Podrobnosti o rozhraní REST API pro objekty blob najdete v dokumentaci k [rozhraní API služby Azure Blob Service](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

|Scénář  |Stav objektů blob  |Nepovolené operace s objekty blob  |
|---------|---------|---------|
|Efektivní interval uchovávání informací pro objekt blob ještě nevypršel a/nebo je nastavené blokování z právních důvodů     |Neměnné: chráněné proti odstranění i zápisu         |Delete Container, Delete Blob, Put Blob1, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|Vypršel efektivní interval uchovávání informací pro objekt blob     |Chráněné pouze proti zápisu (operace odstranění jsou povolené)         |Put Blob, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|Všechna blokování z právních důvodů jsou zrušená a kontejner nemá nastavenou žádnou zásadu uchovávání informací podle času     |Měnitelné         |Žádný         |
|Nevytvořily se žádné zásady WORM (uchovávání informací podle času ani blokování z právních důvodů)     |Měnitelné         |Žádný         |

> [!NOTE]
> V prvních dvou scénářích v předchozí tabulce jsou povolené první operace Put Blob a operace Put Block List a Put Block nezbytné k vytvoření objektu blob, všechny následující operace jsou zakázané.
> Funkce Immutable Storage je dostupná pouze v účtech GPv2 nebo Blob Storage a musí se vytvořit prostřednictvím [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="pricing"></a>Ceny

Za používání této funkce se neúčtují žádné další poplatky a pro neměnná data platí stejné ceny jako pro normální měnitelná data. Související podrobnosti o cenách najdete na [stránce s cenami za Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="restrictions"></a>Omezení

Během období Public Preview platí následující omezení:

- **Neukládejte produkční ani důležitá podniková data.**
- Platí všechna omezení plynoucí z verze Preview a smlouvy o utajení.

## <a name="getting-started"></a>Začínáme

Funkce Azure Immutable Storage pro objekty blob Azure je podporovaná v nejnovějších verzích webu [Azure Portal](http://portal.azure.com), Azure [CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a Azure [PowerShellu](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May2018).

### <a name="azure-portal"></a>Azure Portal

1. Vytvořte nový kontejner nebo vyberte stávající kontejner pro uložení objektů blob, které je potřeba zachovat v neměnném stavu.
 Kontejner se musí nacházet v účtu úložiště GPv2.
2. V nastavení kontejneru klikněte na Zásady přístupu a pak v části zásady **Neměnná služba Blob Storage** klikněte na **+ Přidat zásadu**, jak je znázorněno níže.

    ![Portál](media/storage-blob-immutable-storage/portal-image-1.png)

3. Pokud chcete povolit uchovávání informací podle času, zvolte v rozevírací nabídce Uchovávání informací podle času.

    ![Uchovávání](media/storage-blob-immutable-storage/portal-image-2.png)

4. Zadejte požadovaný interval uchovávání informací ve dnech (minimum je jeden den).

    ![Interval uchovávání informací](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Jak je vidět výše, počáteční stav zásady je odemknutý. Díky tomu můžete funkci testovat s menším intervalem uchovávání informací a provádět změny zásady před tím, než ji odemknete. Uzamknutí je nezbytné pro dodržování legislativních předpisů v nařízení SEC 17a-4 a dalších.

5. Zásadu uzamknete tak, že kliknete pravým tlačítkem na ... a zobrazí se následující nabídka:

    ![Uzamknutí zásady](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Klikněte na Uzamknout zásadu a stav zásady teď bude Uzamčeno. Po uzamknutí už nebude možné zásadu odstranit a povolené bude pouze prodloužení intervalu uchovávání informací.

6. Pokud chcete povolit blokování z právních důvodů, klikněte na + Přidat zásadu a v rozevírací nabídce zvolte Blokování z právních důvodů.

    ![Blokování z právních důvodů](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Vytvořte blokování z právních důvodů s jednou nebo několika značkami.

    ![Nastavení značek blokování z právních důvodů](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

### <a name="cli-20"></a>CLI 2.0

Nainstalujte [rozšíření rozhraní příkazového řádku](http://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pomocí příkazu `az extension add -n storage-preview`.

Pokud již máte rozšíření nainstalované, pomocí následujícího příkazu povolte funkci Immutable Storage: `az extension update -n storage-preview`

Tato funkce je součástí následujících skupin příkazů (příkazy zobrazíte spuštěním příkazu s parametrem -h pro tyto skupiny): `az storage container immutability-policy` a `az storage container legal-hold`.

### <a name="powershell"></a>PowerShell

Funkce Immutable Storage je podporovaná v [PowerShellu verze 4.4.0-preview](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May20180).
Pokud chcete tuto funkci povolit, postupujte podle následujících kroků:

1. Pomocí příkazu `Install-Module PowerShellGet –Repository PSGallery –Force` zajistěte, abyste měli nainstalovanou nejnovější verzi modulu PowerShellGet.
2. Odeberte všechny předchozí instalace Azure PowerShellu.
3. Nainstalujte AzureRM (podobným způsobem je možné z tohoto úložiště nainstalovat i Azure): `Install-Module AzureRM –Repository PSGallery –AllowClobber`
4. Nainstalujte verzi Preview rutin roviny správy služby Storage: `Install-Module -Name AzureRM.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

Ukázkový kód PowerShellu ukazující použití této funkce najdete níže.

## <a name="client-libraries"></a>Klientské knihovny

Funkce Immutable Storage pro objekty blob Azure je podporovaná v následujících verzích klientských knihoven:

- [Klientská knihovna pro .NET (verze 7.2.0-preview nebo novější)](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Klientská knihovna pro Node.js (verze 4.0.0 nebo novější)](https://www.npmjs.com/package/azure-arm-storage)
- [Klientská knihovna pro Python (verze 2.0.0 Release Candidate 2 nebo novější)](https://pypi.org/project/azure-mgmt-storage/2.0.0rc1/)

## <a name="supported-values"></a>Podporované hodnoty

- Minimální interval uchovávání informací je jeden den a maximální interval je 400 let.
- Maximální počet kontejnerů s uzamknutými zásadami neměnnosti je pro každý účet úložiště 1 000.
- Maximální počet kontejnerů s nastaveným blokováním z právních důvodů je pro každý účet úložiště 1 000.
- Maximální počet značek blokování z právních důvodů je pro každý kontejner 10.
- Maximální délka značky blokování z právních důvodů je 23 alfanumerických znaků; minimální délka je tři znaky.
- Maximální počet povolených prodloužení intervalu uchovávání informací u uzamknutých zásad neměnnosti je pro každý kontejner 3.
- Pro každý kontejner s uzamknutou zásadou neměnnosti se po celou dobu trvání kontejneru uchovává maximálně 5 protokolů zásad uchovávání informací podle času a maximálně 10 protokolů zásad blokování z právních důvodů.

## <a name="faq"></a>Nejčastější dotazy

**Vztahuje se tato funkce pouze na objekty blob bloku, nebo i na objekty blob stránky a doplňovací objekty blob?**

Funkci Immutable Storage pro objekty blob je možné používat s jakýmkoli typem objektu blob.  Mějte však na paměti, že se doporučuje funkci používat hlavně pro objekty blob bloku. Na rozdíl od objektů blob bloku se musí objekty blob stránky a doplňovací objekty blob vytvořit mimo kontejner WORM a pak se do něj zkopírovat.  Po zkopírování do kontejneru WORM už nejsou povolená žádná *připojení* k doplňovacímu objektu blob ani změny objektu blob stránky.

**Je pro použití této funkce potřeba vždy vytvářet nový účet úložiště?**

Funkci Immutable Storage můžete používat s jakýmikoli stávajícími účty GPv2 nebo v nových účtech úložiště, pokud je typ účtu GPv2. Tato funkce je dostupná pouze pro službu Blob Storage.

**Co se stane, když se pokusím odstranit kontejner s *uzamknutou* zásadou uchovávání informací podle času nebo blokováním z právních důvodů?**

Operace odstranění kontejneru selže, pokud obsahuje alespoň jeden objekt blob s uzamknutou zásadou uchovávání informací pode času nebo blokováním z právních důvodů. Operace odstranění kontejneru bude úspěšná, pokud neobsahuje žádný objekt blob s aktivním intervalem uchovávání informací a pokud v něm nejsou žádná blokování z právních důvodů. Před odstraněním kontejneru je potřeba nejprve odstranit objekty blob.

**Co se stane, když se pokusím odstranit účet úložiště obsahující kontejner WORM s *uzamknutou* zásadou uchovávání informací podle času nebo blokováním z právních důvodů?**

Odstranění účtu úložiště selže, pokud obsahuje alespoň jeden kontejner WORM s blokováním z právních důvodů nebo objektem blob s aktivním intervalem uchovávání informací.  Před odstraněním účtu úložiště je potřeba odstranit všechny kontejnery WORM.  Informace o odstranění kontejneru najdete v odpovědi na otázku 2.

**Můžu přesouvat data mezi různými úrovněmi objektu blob (horká, studená, archivní), když je objekt blob v neměnném stavu?**

Ano, pomocí příkazu Set Blob Tier můžete přesouvat data mezi úrovněmi objektu blob, zatímco jsou data stále v neměnném stavu. Funkce Immutable Storage je podporovaná na horké, studené a archivní úrovni objektu blob.

**Co se stane, když zapomenu zaplatit a můj interval uchovávání informací ještě nevypršel?**

V případě nezaplacení budou platit normální zásady uchovávání dat, a to v délce smluvní lhůty uvedené v podmínkách a ujednáních vaší smlouvy s Microsoftem.

**Nabízíte zkušební období nebo období odkladu pouze na vyzkoušení této funkce?**

Ano, když se poprvé vytvoří zásada uchovávání informací podle času, bude v *odemknutém* stavu. V tomto stavu můžete provádět jakékoli požadované změny intervalu uchovávání informací, například ho zkrátit nebo prodloužit, a dokonce i odstranit zásadu. Po uzamknutí zůstane zásada navždy uzamknutá a odstranění nebude možné. Navíc není možné zkrátit interval uchovávání informací, když je zásada uzamknutá. Důrazně doporučujeme používat *odemknutý* stav pouze pro zkušební účely a během 24 hodin zásadu uzamknout, abyste neriskovali nedodržování předpisů v nařízení SEC 17a-4(f) a dalších.

**Je tato funkce dostupná v národních cloudech a cloudech pro státní správu?**

Funkce Immutable Storage je v současné době dostupná pouze ve veřejných oblastech Azure. Pokud vás zajímá konkrétní národní cloud, odešlete e-mail na adresu azurestoragefeedback@microsoft.com.

## <a name="sample-code"></a>Ukázka kódu

Níže je uvedený ukázkový skript PowerShellu pro referenční účely.
Tento skript vytvoří nový účet úložiště a kontejner. Následně ukazuje postup pro nastavení a zrušení blokování z právních důvodů, vytvoření a uzamknutí zásady uchovávání informací podle času (neboli ImmutabilityPolicy), prodloužení intervalu uchovávání informací atd.

```powershell
\$ResourceGroup = "\<Enter your resource group\>”

\$StorageAccount = "\<Enter your storage account name\>"

\$container = "\<Enter your container name\>"

\$container2 = "\<Enter another container name\>”

\$location = "\<Enter the storage account location\>"

\# Login to the Azure Resource Manager Account

Login-AzureRMAccount

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage"

\# Create your Azure Resource Group

New-AzureRmResourceGroup -Name \$ResourceGroup -Location \$location

\# Create your Azure storage account

New-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup -StorageAccountName
\$StorageAccount -SkuName Standard_LRS -Location \$location -Kind Storage

\# Create a new container

New-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Create Container 2 with Storage Account object

\$accountObject = Get-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

New-AzureRmStorageContainer -StorageAccount \$accountObject -Name \$container2

\# Get container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Get Container with Account object

\$containerObject = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container

\#list container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

\#remove container (Add -Force to dismiss prompt)

Remove-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container2

\#with Account object

Remove-AzureRmStorageContainer -StorageAccount \$accountObject -Name
\$container2

\#with Container object

\$containerObject2 = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container2

Remove-AzureRmStorageContainer -InputObject \$containerObject2

\#Set LegalHold

Add-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag1,tag2

\#with Account object

Add-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3

\#with Container object

Add-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4,tag5

\#Clear LegalHold

Remove-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag2

\#with Account object

Remove-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3,tag5

\#with Container object

Remove-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4

\# create/update ImmutabilityPolicy

\#\# with account/container name

Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 10

\#with Account object

Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -ImmutabilityPeriod 1 -Etag \$policy.Etag

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 7

\#\# with ImmutabilityPolicy object

Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy
-ImmutabilityPeriod 5

\#get ImmutabilityPolicy

Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container

\#with Account object

Get-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container

\#with Container object

Get-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject

\#Lock ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -force

\#\# with account/container name

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -Etag \$policy.Etag

\#with Container object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -Etag \$policy.Etag -force

\#Extend ImmutabilityPolicy

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -ImmutabilityPeriod 11 -ExtendPolicy

\#\# with account/container name

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 11 -Etag \$policy.Etag -ExtendPolicy

\#with Account object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -ImmutabilityPeriod 12 -Etag
\$policy.Etag -ExtendPolicy

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 13 -Etag \$policy.Etag -ExtendPolicy

\#Remove ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

Remove-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy

\#\# with account/container name

Remove-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

Remove-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -Etag \$policy.Etag

\#with Container object

Remove-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject
-Etag \$policy.Etag
```