---
title: Neměnné úložiště pro objekty BLOB Azure Storage | Dokumentace Microsoftu
description: Azure Storage nabízí podporu ČERV (zápis, mnoho čtení) pro úložiště objektů Blob (objekt), který uživatelům umožňuje ukládat data v nepůjdou upravit stavu pro zadaný interval.
services: storage
author: xyh1
ms.service: storage
ms.topic: article
ms.date: 03/02/2019
ms.author: hux
ms.subservice: blobs
ms.openlocfilehash: 76d2794d45e9cd73ae8faf203ba29c4dbba64eae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004623"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Store důležitých podnikových dat ve službě Azure Blob storage

Neměnné úložiště pro úložiště objektů Blob v Azure umožňuje uživatelům ukládat objekty důležitých podnikových dat ve stavu ČERV (zápis, mnoho čtení). Tento stav vytvoří data nepůjdou a neupravitelnými intervalu zadané uživatelem. Objekty BLOB nelze vytvořit a číst, ale upravit nebo odstranit po dobu trvání interval uchovávání informací. Neměnné úložiště je povolený pro obecné účely v2 a účty úložiště objektů Blob ve všech oblastech Azure.

## <a name="overview"></a>Přehled

Neměnné úložiště pomáhá zdravotnické organizace, finanční instituce a souvisejících oborech – zejména zprostředkovatele – aby mohla organizace – se zabezpečeně ukládat data. To můžete také využít v žádném scénáři a ochranou důležitých dat. pro úpravy nebo odstranění. 

Mezi typické případy použití patří:

- **Dodržování legislativních předpisů**: Neměnné úložiště pro úložiště objektů Blob v Azure pomáhá organizacím adresu Sekundu 17a-4(f), CFTC 1.31(d), FINRA a nařízení. Technický dokument White Paper o Cohasset přidruží podrobně popisuje, jak neměnné adresy úložiště je ke stažení přes tyto zákonné požadavky [Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). [Centrum zabezpečení Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) obsahuje podrobné informace o našich certifikátech dodržování předpisů.

- **Zabezpečení dokumentu uchování**: Neměnné úložiště pro úložiště objektů Blob v Azure zajistí, že data nelze změnit ani odstranit, každý uživatel, včetně uživatelů s oprávněním správce účtu.

- **Blokování z právních důvodů**: Neměnné úložiště pro úložiště objektů Blob v Azure umožňuje ukládání citlivých informací, které je zásadní pro sporu nebo obchodní použití ve stavu odolného proti požadovanou dobu, dokud se neodstraní blokování. Tato funkce není omezena pouze na případy použití právní, ale můžete také považovat za založené na událostech blokování nebo zámek pro organizace, ve kterém jsou vyžadována potřeba chránit data na základě aktivační události nebo podnikových zásad.

Neměnné úložiště podporuje následující funkce:

- **[Podpora zásad uchovávání informací podle času](#time-based-retention)**: Uživatelé můžou nastavit zásady pro ukládání dat pro zadaný interval. Když se zásada uchovávání informací podle času nastavit, objekty BLOB nelze vytvořit a číst, ale upravit nebo odstranit. Po vypršení doby uchování objektů BLOB můžete odstranit, ale není přepsán.

- **[Podpora zásad blokování z právních důvodů](#legal-holds)**: Pokud není znám interval uchovávání informací, uživatelé můžou nastavit právních immutably ukládat data, dokud se vymazat blokování z právních důvodů.  Pokud zásady skupiny s povinností uchování nastavíte, objekty BLOB nelze vytvořit a číst, ale upravit nebo odstranit. Každé skupiny s povinností uchování souvisí s uživatelem definované alfanumerické značky (třeba ID případu, události atd.), který se používá jako identifikátor řetězce. 

- **Podpora pro všechny úrovně objektu blob**: ČERV zásady platí bez ohledu na úroveň úložiště objektů Blob v Azure a použít na všech úrovních: horká, studená a archivní úrovně. Uživatelé můžou přecházet data na úroveň optimalizovaných náklady pro své úlohy při zachování dat neměnnosti.

- **Konfigurace na úrovni kontejneru**: Uživatelé můžou konfigurovat zásady uchovávání informací podle času a blokování z právních důvodů značek na úrovni kontejneru. S použitím jednoduché nastavení na úrovni kontejneru, uživatel může vytvořit a zamknout zásady uchovávání informací podle času, rozšíření intervaly uchovávání informací, nastavení a vymazat právních a další. Tyto zásady platí pro všechny objekty BLOB v kontejneru, stávající i nové.

- **Podpora protokolování auditu**: Každý kontejner obsahuje protokolu auditu. Zobrazuje až o pěti uchovávání informací podle času příkazy pro zásady uzamčené uchovávání informací podle času s délkou maximálně tři protokoly pro rozšíření interval uchovávání informací. Protokol pro uchovávání informací podle času, obsahuje ID uživatele, typ příkazu, časová razítka a interval uchovávání informací. Pro právních důvodů protokol obsahuje ID uživatele, typ příkazu, časová razítka a značky blokování z právních důvodů. Tento protokol se uchovávají po dobu jeho existence kontejneru, v souladu s pokyny k dodržování legislativních 17a-4(f) Sekundu. [Protokolu aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md) ukazuje komplexnější protokolu všechny aktivity rovina řízení; při povolování [diagnostické protokoly Azure](../../azure-monitor/platform/diagnostic-logs-overview.md) uchovává a zobrazuje operace roviny dat. Zodpovídá za uživatele k ukládání těchto protokolů trvale, jak může být nezbytný pro zákonné nebo jiné účely.

## <a name="how-it-works"></a>Jak to funguje

Neměnné úložiště pro úložiště objektů Blob v Azure podporuje dva typy ČERV nebo neměnné zásady: uchovávání informací podle času a právních důvodů. Při použití zásady uchovávání informací podle času nebo blokování z právních důvodů na kontejner, přesuňte všechny existující objekty BLOB do neměnného stavu ČERV za méně než 30 sekund. Všechny nové objekty BLOB, které se nahrají do tohoto kontejneru se přesunou také do neměnného stavu. Jakmile se všechny objekty BLOB, bylo přesunuto do neměnného stavu, je potvrzen neměnné zásady a všechny přepsat nebo odstranit operace pro stávající i nové objekty v kontejneru neměnné nejsou povoleny.

### <a name="time-based-retention"></a>Uchovávání informací podle času

> [!IMPORTANT]
> Zásady uchovávání informací podle času musí být *uzamčen* pro objekt blob ve neměnné (zápisu a odstranění chráněné) stavu Sekundu 17a-4(f) a dalších dodržování legislativních předpisů. Doporučujeme, abyste zamknout zásady v rozumném čase, obvykle během 24 hodin. Nedoporučujeme ale používat *odemknout* stavu jiným účelům, než krátkodobé zkušební verze funkce.

Při použití zásad uchovávání informací podle času na kontejner všechny objekty BLOB v kontejneru zůstanou v neměnného stavu po dobu trvání *efektivní* dobu uchování. Doba uchování efektivní existujících objektů blob je roven rozdílu mezi časem úpravy objektů blob a interval uchovávání informací zadané uživatelem.

Pro nové objekty blob se efektivní doba uchovávání informací rovná uživatelem zadanému intervalu uchovávání informací. Protože uživatelé můžou rozšířit interval uchovávání informací, neměnné úložiště používá k výpočtu doby uchování efektivní nejnovější hodnotu Interval uchovávání informací zadané uživatelem.

> [!TIP]
> **Příklad:** Uživatel vytvoří zásadu uchovávání informací podle času s intervalem uchovávání po dobu pěti let.
>
> Existující objekt blob v tomto kontejneru _testblob1_, byl vytvořen před rokem. Doba uchování efektivní _testblob1_ je 4 roky.
>
> Nový objekt blob _testblob2_, je nyní nahraný do kontejneru. Doby uchování efektivní pro tento nový objekt blob je pět let.

### <a name="legal-holds"></a>Blokování z právních důvodů

Při nastavení blokování z právních důvodů, zůstávají všechny stávající i nové objekty BLOB v neměnného stavu, dokud vymazat blokování z právních důvodů. Další informace o tom, jak sada a vymazat právních důvodů, najdete v článku [Začínáme](#getting-started) oddílu.

Kontejner můžete mít skupiny s povinností uchování a zásadu uchovávání informací podle času ve stejnou dobu. Všechny objekty BLOB v tomto kontejneru zůstat v neměnného stavu, dokud všechny právních důvodů jsou vymazány, i v případě, že vypršela platnost jejich efektivní uchovávají. Naopak objekt blob zůstanou v neměnného stavu do vypršení platnosti doby uchování efektivní, i v případě, že byly odstraněny všechny právních důvodů.

V následující tabulce jsou uvedeny typy operace objektů blob, které jsou zakázané pro různé scénáře neměnné. Další informace najdete v tématu [rozhraní API služby Azure Blob Service](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) dokumentaci.

|Scénář  |Stav objektu BLOB  |Není povolena operace objektů BLOB  |
|---------|---------|---------|
|Efektivní interval uchovávání informací pro objekt blob ještě nevypršel a/nebo je nastavené blokování z právních důvodů     |Neměnné: chráněné proti odstranění i zápisu         | Vložení objektu Blob<sup>1</sup>, umístěte blok<sup>1</sup>, vložte seznam blokovaných položek<sup>1</sup>, Metadata objektu Blob sadu kontejnerů, objektů Blob odstranit, odstranění, stránku, nastavit vlastnosti objektu Blob, vytvoření snímku objektu Blob, objekt Blob přírůstkového kopírování Přidat blok         |
|Vypršel efektivní interval uchovávání informací pro objekt blob     |Chráněné pouze proti zápisu (operace odstranění jsou povolené)         |Vložení objektu Blob<sup>1</sup>, umístěte blok<sup>1</sup>, vložte seznam blokovaných položek<sup>1</sup>, nastavte Metadata objektu Blob, stránku, nastavte Blob vlastnosti, vytvoření snímku objektu Blob, přírůstkové kopírování objektů Blob, připojte bloku         |
|Obsahuje všechny právní nezaškrtnuté a nejsou nastavené žádné zásady uchovávání informací podle času v kontejneru     |Měnitelné         |Žádný         |
|Žádné zásady ČERV vytvořena (uchovávání informací podle času nebo skupiny s povinností uchování)     |Měnitelné         |Žádný         |

<sup>1</sup> aplikace umožňuje tyto operace jednou vytvořit nový objekt blob. Všechny následné přepsat operace na existující cestu objektů blob v kontejneru prvku neměnné nejsou povoleny.

## <a name="pricing"></a>Ceny

Neexistuje žádné další poplatky za použití této funkce. Stejným způsobem jako regulární proměnlivých dat vychází z neměnnými daty. Podrobnosti o cenách Azure Blob Storage, najdete v článku [stránce s cenami služby Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Začínáme

Nejnovější verze [webu Azure portal](https://portal.azure.com), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), a [prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases) podporují neměnné úložiště pro úložiště objektů Blob v Azure. [Podpora knihovny klienta](#client-libraries) je také k dispozici.

> [!NOTE]
>
> Neměnné storage je dostupné jenom pro obecné účely v2 a účty Blob Storage. Tyto účet se musí spravovat přes [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Informace o upgradu existující obecné účely v1 účtu služby storage najdete v tématu [upgradovat účet úložiště](../common/storage-account-upgrade.md).

### <a name="azure-portal"></a>portál Azure

1. Vytvořte nový kontejner nebo vyberte stávající kontejner pro uložení objektů blob, které je potřeba zachovat v neměnném stavu.
 Kontejner musí být v účtu GPv2 nebo blob storage.
2. Vyberte **zásada přístupu** v nastavení kontejneru. Potom vyberte **přidat zásadu** pod **neměnná služba blob storage**.

    ![Nastavení kontejneru na portálu](media/storage-blob-immutable-storage/portal-image-1.png)

3. Chcete-li povolit uchovávání informací podle času, vyberte **uchovávání informací podle času** z rozevírací nabídky.

    !["Doba uchovávání informací podle" vybraný v části "Zásady typu"](media/storage-blob-immutable-storage/portal-image-2.png)

4. Zadejte interval uchovávání informací ve dnech (přijatelné hodnoty 1 na 146000 dnů).

    ![Pole "Aktualizace dobu uchování na"](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Počáteční stav zásad je odemknuté díky tomu můžete otestovat funkci a provést změny zásad předtím, než je zamknout. Zásady uzamčení je zásadní pro dodržování předpisů jako Sekundu 17a – 4.

5. Zamkněte zásady. Klikněte pravým tlačítkem na tři tečky (**...** ), a zobrazí se následující nabídka s další akce:

    !["Zamknout zásady" v nabídce](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Vyberte **zamknout zásady**. Zásada je nyní uzamčen a nelze ji odstranit, bude povoleno pouze rozšíření interval uchovávání informací.

6. Chcete-li povolit právních důvodů, vyberte **přidat zásadu**. Vyberte **blokování z právních důvodů** z rozevírací nabídky.

    !["Blokování z právních důvodů" v nabídce v části "Zásady typu"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Vytvoření skupiny s povinností uchování pomocí jedné nebo více značek.

    ![Pole "Název značky" v části Typ zásady](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

8. Vymazat blokování z právních důvodů, jednoduše odeberte identifikátor značky použité skupiny s povinností uchování.

### <a name="azure-cli"></a>Azure CLI

Tato funkce je součástí skupiny následující příkaz: `az storage container immutability-policy` a `az storage container legal-hold`. Spustit `-h` na ně podívat na příkazy.

### <a name="powershell"></a>PowerShell

Modul Az.Storage ve verzi preview podporuje neměnné úložiště.  Chcete-li tuto funkci povolit, postupujte takto:

1. Ujistěte se, že máte nejnovější verzi modulu PowerShellGet nainstalovaný: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Odebrání jakékoli předchozí instalace Azure Powershellu.
3. Instalace Azure Powershellu: `Install-Module Az –Repository PSGallery –AllowClobber`.
4. Instalace modulu Azure Powershellu pro úložiště ve verzi preview: `Install-Module Az.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

[Powershellu ukázkový kód](#sample-powershell-code) části dále v tomto článku znázorňuje používání funkcí.

## <a name="client-libraries"></a>Klientské knihovny

Tyto klientské knihovny podporují neměnné úložiště pro úložiště objektů Blob v Azure:

- [Klientská knihovna pro .NET verze 7.2.0-preview a novější](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Klientské knihovny pro Node.js verze 4.0.0 a novější](https://www.npmjs.com/package/azure-arm-storage)
- [Klientská knihovna Python verze 2.0.0 verze Release Candidate, 2 a novější](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Klientské knihovny pro Javu](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="supported-values"></a>Podporované hodnoty

- Minimální interval je za jeden den. Maximální počet je 146,000 dnů (400 let).
- Maximální počet kontejnerů uzamčené neměnné zásadami pro účet úložiště je 1 000.
- Maximální počet kontejnerů pomocí nastavení skupiny s povinností uchování pro účet úložiště je 1 000.
- Pro kontejner maximálního počtu značek skupiny s povinností uchování je 10.
- Maximální délka značku blokování z právních důvodů je 23 alfanumerických znaků. Minimální délka je tři znaky.
- Pro kontejner je maximální počet povolených uchování interval rozšíření pro uzamčené neměnné zásady tři.
- Pro kontejner s uzamčené neměnné zásady uchování maximálně pět protokoly zásad uchovávání informací podle času a maximálně 10 právní zásady, které protokoly se uchovávají po dobu trvání kontejneru.

## <a name="faq"></a>Nejčastější dotazy

**Můžete uvést dokumentaci ČERV dodržování předpisů?**

Ano. K dodržování předpisů dokumentu společnost Microsoft uchovávat přední nezávislé hodnocení podnik, který se specializuje na záznamy řízení a informace zásady správného řízení, Cohasset přidruží k vyhodnocení neměnné úložiště objektů Blob v Azure a dodržování požadavků na konkrétní z oboru finančních služeb v odvětví představuje špičku. Cohasset ověřit, že Azure neměnné úložiště objektů Blob, pokud se použije k zachování podle času objekty BLOB ve stavu ČERV, splňuje požadavky na příslušné úložiště 1.31(c)-(d) CFTC pravidlo, 4511 FINRA pravidla a pravidla Sekundu 17a – 4. Microsoft je určená tuto sadu pravidel, protože představují nejčastěji doporučený globálně pro uchovávání záznamů finančních institucí. Je k dispozici v sestavě Cohasset [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage).

**Platí jenom objekty BLOB bloku, nebo pro stránky a doplňovací objekty BLOB také funkci?**

Neměnné úložiště lze použít s žádným typem objektů blob, ale doporučujeme používat hlavně pro objekty BLOB bloku. Na rozdíl od objekty BLOB bloku objekty BLOB stránky a doplňovací objekty BLOB se musí vytvořit mimo kontejner ČERV a poté zkopírován v. Po zkopírování těchto objektů BLOB do kontejneru ČERV, ne další *připojí* připojit jsou povoleny změny pro objekt blob stránky nebo objekt blob.

**Je pro použití této funkce potřeba vždy vytvářet nový účet úložiště?**

Můžete použít neměnné úložiště pomocí jakékoli existující nebo nově vytvořený obecné účely v2 nebo účty Blob Storage. Tato funkce je určená pro použití s objekty BLOB bloku v účtech GPv2 a Blob Storage.

**Můžete použít skupiny s povinností uchování i zásady uchovávání informací podle času?**

Kontejner můžete mít skupiny s povinností uchování a zásadu uchovávání informací podle času ve stejnou dobu. Všechny objekty BLOB v tomto kontejneru zůstat v neměnného stavu, dokud všechny právních důvodů jsou vymazány, i v případě, že vypršela platnost jejich efektivní uchovávají. Naopak objekt blob zůstanou v neměnného stavu do vypršení platnosti doby uchování efektivní, i v případě, že byly odstraněny všechny právních důvodů.

**Jsou určeny pouze pro soudního řízení zásad blokování z právních důvodů nebo existují jiné scénáře použití?**

Ne, právní uchování je právě obecný termín, který se pro zásady uchovávání informací mimo časovou. Není nutné použít pouze pro sporů souvisejících řízení. Právní zásady uchování jsou užitečné pro zakázání přepsat a odstraní k ochraně důležitých podnikových dat ČERV, kde se doba uchovávání dat neznámý. Může ho použít jako podnikové zásady a chránit vaše nejdůležitější ČERV úlohy nebo ho použít jako pracovní zásadu před vlastní aktivační vyžaduje použití zásady uchovávání informací podle času. 

**Co se stane, když se pokusím odstranit kontejner s *uzamknutou* zásadou uchovávání informací podle času nebo blokováním z právních důvodů?**

Operace odstranění kontejneru se nezdaří, pokud existuje alespoň jeden objekt blob se zásadami uzamčené uchovávání informací podle času nebo skupiny s povinností uchování. Operace odstranění kontejneru bude úspěšné pouze v případě, že neexistuje žádný objekt blob s intervalem uchování aktivní a nejsou žádné právních důvodů. Než budete moct odstranit kontejner je nutné odstranit objekty BLOB.

**Co se stane, když se pokusím odstranit účet úložiště obsahující kontejner WORM s *uzamknutou* zásadou uchovávání informací podle času nebo blokováním z právních důvodů?**

Odstranění účtu úložiště selže, pokud obsahuje alespoň jeden kontejner WORM s blokováním z právních důvodů nebo objektem blob s aktivním intervalem uchovávání informací.  Než budete moct odstranit účet úložiště, je nutné odstranit všechny kontejnery ČERV. Informace o odstranění kontejneru najdete v předchozí otázce.

**Můžu přesouvat data mezi různými úrovněmi objektu blob (horká, studená, archivní), když je objekt blob v neměnném stavu?**

Ano, můžete pomocí příkazu Set Blob Tier přesouvat data v rámci úrovně objektů blob při zachování dat v kompatibilní neměnného stavu. Je podporováno neměnné úložiště na horké, studené a archivní úroveň objektu blob.

**Co se stane, když zapomenu zaplatit a můj interval uchovávání informací ještě nevypršel?**

V případě neuhrazení zásady uchovávání informací normální data použije stanovené v podmínky a ujednání smlouvy s Microsoftem.

**Nabízíte zkušební období nebo období odkladu pouze na vyzkoušení této funkce?**

Ano. Při prvním vytvoření zásady uchovávání informací podle času je *odemknout* stavu. V tomto stavu můžete provádět požadované změny pro interval uchovávání informací, jako je například zvýšení nebo snížení a dokonce odstranit zásady. Jakmile se zásady uzamknou, zůstane uzamčena než interval uchovávání informací vyprší. Tato uzamčená zásada zabraňuje odstranění a změny interval uchovávání informací. Důrazně doporučujeme používat *odemknout* pouze pro zkušební účely a zamknout zásady v období 24 hodin. Tyto postupy umožňují v souladu s Sekundu 17a-4(f) a nařízení.

**Můžete použít obnovitelné odstranění vedle zásady neměnná služba blob?**

Ano. [Obnovitelné odstranění pro úložiště objektů Blob v Azure](storage-blob-soft-delete.md) platí pro všechny kontejnery v rámci účtu úložiště bez ohledu na skupiny s povinností uchování a zásady uchovávání informací podle času. Doporučujeme povolit obnovitelného odstranění pro zvýšení ochrany před veškeré neměnná ČERV zásady se použijí a potvrzení. 

**Je tato funkce dostupná v národních cloudech a cloudech pro státní správu?**

Neměnné storage je dostupné v oblastech Azure veřejné, Číny a vládního. Pokud neměnné úložiště není ve vaší oblasti k dispozici, obraťte se prosím na podporu a e-mailu azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Ukázkový kód Powershellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Následující ukázkový skript Powershellu se pro referenci. Tento skript vytvoří nový účet úložiště a kontejner. To potom se dozvíte, jak nastavit a zrušte právních důvodů, vytvořit a zamknout zásady uchovávání informací podle času (označované také jako zásady neměnnosti) a rozšiřte interval uchovávání informací.

Nastavení a testování účet služby Azure Storage:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

Nastavit a zrušit právních důvodů:

```powershell
# Set a legal hold
Add-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Vytvořit nebo aktualizovat zásady neměnnosti:
```powershell
# with an account name or container name
Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Získat zásady neměnnosti:
```powershell
# Get an immutability policy
Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzStorageContainerImmutabilityPolicy -Container $containerObject
```

Zamknout zásady neměnnosti (Přidat - Force zavřete výzvu):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Rozšiřte zásady neměnnosti:
```powershell

# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Odstranit zásady neměnnosti (Přidat - Force zavřete výzvu):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
