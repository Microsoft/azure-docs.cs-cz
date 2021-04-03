---
title: Vytváření a používání souborů prostředků
description: Naučte se vytvářet soubory prostředků služby Batch z různých vstupních zdrojů. Tento článek obsahuje několik běžných metod, jak je vytvořit a umístit na virtuální počítač.
ms.date: 03/18/2020
ms.topic: how-to
ms.openlocfilehash: 84a5e9780b4fa0abfec5b736e04d385f14716873
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109285"
---
# <a name="creating-and-using-resource-files"></a>Vytváření a používání souborů prostředků

Úkol Azure Batch často vyžaduje určitou formu zpracování dat. Soubory prostředků představují způsob, jak poskytnout tato data vašemu virtuálnímu počítači Batch prostřednictvím úlohy. Všechny typy úloh podporují soubory prostředků: úkoly, spouštěcí úkoly, úkoly přípravy úlohy, úkoly uvolnění úloh atd. Tento článek obsahuje několik běžných metod, jak vytvořit soubory prostředků a umístit je na virtuální počítač.  

Soubory prostředků umísťují data do virtuálního počítače v dávce, ale typ dat a způsob, jakým se používá, je flexibilní. Existují však některé běžné případy použití:

1. Zřizování běžných souborů na každém virtuálním počítači pomocí souborů prostředků u spouštěcího úkolu
1. Zřizování vstupních dat pro zpracování úlohami

Mezi běžné soubory může být například soubor v spouštěcím úkolu, který slouží k instalaci aplikací spouštěných vašimi úkoly. Vstupní data mohou být nezpracovaná data obrázku nebo videa nebo jakékoli informace, které mají být zpracovány službou Batch.

## <a name="types-of-resource-files"></a>Typy souborů prostředků

K dispozici je několik různých možností pro generování souborů prostředků. Proces vytváření souborů prostředků se liší v závislosti na tom, kde jsou uložena původní data.

Možnosti pro vytvoření souboru prostředků:

- [Adresa URL kontejneru úložiště](#storage-container-url): vygeneruje soubor prostředků z jakéhokoli kontejneru úložiště v Azure.
- [Název kontejneru úložiště](#storage-container-name): vygeneruje soubor prostředků z názvu kontejneru v účtu Azure Storage připojeném ke službě Batch.
- [Webový koncový bod](#web-endpoint): vygeneruje soubor prostředků z jakékoli platné adresy URL protokolu HTTP.

### <a name="storage-container-url"></a>Adresa URL kontejneru úložiště

Použití adresy URL kontejneru úložiště znamená, že se správnými oprávněními máte přístup k souborům v jakémkoli kontejneru úložiště v Azure. 

V tomto příkladu jazyka C# již byly soubory do kontejneru úložiště Azure odeslány jako úložiště objektů BLOB. Aby bylo možné získat přístup k datům potřebným k vytvoření souboru prostředků, musíme nejprve získat přístup k kontejneru úložiště.

Vytvořte identifikátor URI sdíleného přístupového podpisu (SAS) se správnými oprávněními pro přístup k kontejneru úložiště. Nastavte čas vypršení platnosti a oprávnění pro SAS. V takovém případě není zadána žádná počáteční doba, takže SAS bude platný okamžitě a po vygenerování skončí dvě hodiny.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Pro přístup k kontejneru musíte mít obojí `Read` i `List` oprávnění, zatímco s přístupem k objektu BLOB budete potřebovat jenom `Read` oprávnění.

Po nakonfigurování oprávnění vytvořte token SAS a naformátujte adresu URL SAS pro přístup k kontejneru úložiště. Pomocí formátované adresy URL SAS kontejneru úložiště vygenerujte soubor prostředků s [`FromStorageContainerUrl`](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl) .

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Alternativou k vygenerování adresy URL SAS je povolit anonymní veřejný přístup pro čtení ke kontejneru a jeho objektům blob v úložišti objektů BLOB v Azure. Díky tomu můžete k těmto prostředkům udělit přístup jen pro čtení bez sdílení klíče účtu a bez vyžadování SAS. Veřejný přístup pro čtení se obvykle používá u scénářů, u kterých chcete, aby byly některé objekty blob vždycky dostupné pro anonymní přístup pro čtení. Pokud tento scénář vyhovuje vašemu řešení, přečtěte si článek [anonymní přístup k](../storage/blobs/anonymous-read-access-configure.md) objektům blob, kde se dozvíte víc o správě přístupu k datům objektů BLOB.

### <a name="storage-container-name"></a>Název kontejneru úložiště

Místo konfigurace a vytvoření adresy URL SAS můžete použít název kontejneru úložiště Azure pro přístup k datům objektu BLOB. Kontejner úložiště, který použijete, musí být v účtu služby Azure Storage, který je propojený s vaším účtem Batch. Tento účet úložiště je známý jako účet úložiště. Použití kontejneru úložiště vám umožní obejít konfiguraci a vytvoření adresy URL SAS pro přístup k kontejneru úložiště.

V tomto příkladu předpokládáme, že data, která se mají použít pro vytvoření souboru prostředků, už jsou v účtu Azure Storage, který je propojený s vaším účtem Batch. Pokud nemáte účet úložiště, přečtěte si postup vytvoření [účtu Batch](batch-account-create-portal.md) , kde najdete podrobnosti o tom, jak vytvořit a propojit účet.

Pomocí propojeného účtu úložiště nemusíte vytvářet a konfigurovat adresu URL SAS kontejneru úložiště. Místo toho zadejte název kontejneru úložiště v propojeném účtu úložiště.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Webový koncový bod

Data, která nejsou nahraná do Azure Storage, se dají i nadále používat k vytváření souborů prostředků. Můžete zadat libovolnou platnou adresu URL protokolu HTTP, která obsahuje vstupní data. Adresa URL se poskytne rozhraní API pro dávkování a potom se data použijí k vytvoření souboru prostředků.

V následujícím příkladu jazyka C# jsou vstupní data hostována fiktivním koncovým bodem GitHubu. Rozhraní API načte soubor z platného webového koncového bodu a vygeneruje soubor prostředků, který má vaše úloha spotřebovat. Pro tento scénář nejsou potřeba žádné přihlašovací údaje.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Tipy a návrhy

Každý Azure Batch úlohy používá soubory odlišně, což je důvod, proč má Batch k dispozici možnosti pro správu souborů v úlohách. Následující scénáře nejsou určeny k komplexnímu, ale budou zahrnovat několik běžných situací a poskytnou doporučení.

### <a name="many-resource-files"></a>Mnoho souborů prostředků

Úloha služby Batch může obsahovat několik úloh, které všechny používají stejné běžné soubory. Pokud jsou společné soubory úloh sdíleny mezi mnoha úlohami, použití balíčku aplikace k zahrnutí souborů namísto použití souborů prostředků může být lepší volbou. Balíčky aplikací poskytují optimalizaci pro rychlost stahování. Data v balíčcích aplikací se také ukládají do mezipaměti mezi úkoly, takže pokud se soubory úloh často nemění, můžou být balíčky aplikací vhodné pro vaše řešení. Pomocí balíčků aplikací nemusíte ručně spravovat několik souborů prostředků nebo generovat adresy URL SAS pro přístup k souborům v Azure Storage. Batch funguje na pozadí s Azure Storage pro ukládání a nasazování balíčků aplikací do výpočetních uzlů.

Pokud každý úkol má pro daný úkol jedinečný počet souborů, považují se za nejlepší možnost soubory prostředků, protože úlohy, které používají jedinečné soubory, často musí být aktualizované nebo nahrazené, což není tak snadné dělat obsah balíčků aplikací. Soubory prostředků poskytují další flexibilitu pro aktualizaci, přidávání nebo úpravu jednotlivých souborů.

### <a name="number-of-resource-files-per-task"></a>Počet souborů prostředků na úlohu

Pokud je v úloze několik stovek souborů prostředků, může dávka úlohu odmítnout, protože je příliš velká. Je nejlepší zajistit, aby vaše úkoly byly malé tím, že minimalizují počet souborů prostředků na samotném úkolu.

Pokud neexistuje žádný způsob, jak minimalizovat počet souborů, které váš úkol potřebuje, můžete optimalizovat úlohu vytvořením jednoho souboru prostředků, který odkazuje na kontejner úložiště souborů prostředků. Provedete to tak, že zadáte soubory prostředků do kontejneru Azure Storage a použijete různé [metody](/dotnet/api/microsoft.azure.batch.resourcefile#methods) kontejneru pro soubory prostředků. Pomocí možností předpony objektů BLOB určete kolekce souborů, které se mají stáhnout pro vaše úkoly.

## <a name="next-steps"></a>Další kroky

- Seznamte se s [balíčky aplikací](batch-application-packages.md) jako s alternativou k souborům prostředků.
- Další informace o použití kontejnerů pro soubory prostředků najdete v tématu [úlohy kontejneru](batch-docker-container-workloads.md).
- Informace o tom, jak shromažďovat a ukládat výstupní data z úkolů, najdete v tématu [trvalé zpracování úloh a úkolů](batch-task-output.md).
- Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.