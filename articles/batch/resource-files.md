---
title: Vytváření a používání souborů prostředků – Azure Batch
description: Zjistěte, jak vytvořit dávkové soubory prostředků z různých vstupních zdrojů. Tento článek popisuje několik běžných metod, jak je vytvořit a umístit na virtuální počítač.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/18/2020
ms.author: labrenne
ms.openlocfilehash: 0fe859ac30e7b8050d1f4688d7cf106a465e7566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531137"
---
# <a name="creating-and-using-resource-files"></a>Vytváření a používání souborů prostředků

Úloha Azure Batch často vyžaduje určitou formu dat ke zpracování. Soubory prostředků jsou způsob, jak poskytnout tato data do vašeho virtuálního počítače (VM) dávky prostřednictvím úkolu. Všechny typy úkolů podporují soubory zdrojů: úkoly, počáteční úkoly, úkoly přípravy úloh, úkoly uvolnění úlohy atd. Tento článek popisuje několik běžných metod, jak vytvořit soubory prostředků a umístit je na virtuální počítač.  

Soubory prostředků umístit data na virtuální počítač v batch, ale typ dat a jak se používá, je flexibilní. Existují však některé běžné případy použití:

1. Zřízení běžných souborů na každém virtuálním počítači pomocí souborů prostředků na počáteční úloze
1. Zřizování vstupních dat, která mají být zpracována úkoly

Běžné soubory mohou být například soubory na počáteční úloze používané k instalaci aplikací, které vaše úlohy spouštějí. Vstupními daty mohou být nezpracovaná obrazová nebo obrazová data nebo jakékoli informace, které má Batch zpracovat.

## <a name="types-of-resource-files"></a>Typy souborů prostředků

Existuje několik různých možností, které jsou k dispozici pro generování souborů prostředků. Proces vytváření souborů prostředků se liší v závislosti na tom, kde jsou uložena původní data.

Možnosti pro vytvoření souboru prostředků:

- [Adresa URL kontejneru úložiště:](#storage-container-url)Generuje soubor prostředků z libovolného kontejneru úložiště v Azure
- [Název kontejneru úložiště:](#storage-container-name)Generuje soubor prostředků z názvu kontejneru v účtu úložiště Azure propojeném s Batch
- [Webový koncový bod](#web-endpoint): Generuje soubor prostředků z libovolné platné adresy URL HTTP.

### <a name="storage-container-url"></a>Adresa URL kontejneru úložiště

Použití adresy URL kontejneru úložiště znamená, že se správnými oprávněními máte přístup k souborům v libovolném kontejneru úložiště v Azure. 

V tomto příkladu Jazyka C# již byly soubory odeslány do kontejneru úložiště Azure jako úložiště objektů blob. Chcete-li získat přístup k datům potřebným k vytvoření souboru prostředků, musíme nejprve získat přístup ke kontejneru úložiště.

Vytvořte identifikátor URI sdíleného přístupového podpisu (SAS) se správnými oprávněními pro přístup ke kontejneru úložiště. Nastavte čas vypršení platnosti a oprávnění pro SAS. V tomto případě není zadán žádný čas zahájení, takže SAS se stane platným okamžitě a vyprší dvě hodiny po jeho vygenerování.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Pro přístup ke kontejneru `Read` `List` musíte mít jak a oprávnění, vzhledem k tomu, že přístup k objektům blob, stačí `Read` oprávnění.

Po konfiguraci oprávnění vytvořte token SAS a naformátujte adresu URL SAS pro přístup ke kontejneru úložiště. Pomocí formátované adresy URL SAS pro kontejner úložiště [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)vygenerujte soubor prostředků s .

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Alternativou k generování adresy URL SAS je povolit anonymní, veřejný přístup pro čtení do kontejneru a jeho objektů BLOB v úložišti objektů blob Azure. Tímto způsobem můžete udělit přístup jen pro čtení k těmto prostředkům bez sdílení klíče účtu a bez nutnosti SAS. Veřejný přístup pro čtení se obvykle používá pro scénáře, kde chcete, aby určité objekty BLOB byly vždy k dispozici pro anonymní přístup pro čtení. Pokud tento scénář vyhovuje vašemu řešení, najdete v článku [Anonymní přístup k objektům BLOB](../storage/blobs/storage-manage-access-to-resources.md) další informace o správě přístupu k datům objektu blob.

### <a name="storage-container-name"></a>Název kontejneru úložiště

Místo konfigurace a vytvoření adresy URL SAS můžete použít název kontejneru úložiště Azure pro přístup k datům objektu blob. Kontejner úložiště, který používáte, musí být v účtu úložiště Azure, který je propojený s vaším účtem Batch. Tento účet úložiště se označuje jako účet automatického úložiště. Pomocí kontejneru automatického úložiště můžete obejít konfiguraci a vytvoření adresy URL SAS pro přístup ke kontejneru úložiště.

V tomto příkladu předpokládáme, že data, která se mají použít pro vytváření souborů prostředků, už jsou v účtu Azure Storage propojeném s vaším dávkovým účtem. Pokud nemáte účet automatického úložiště, podívejte se na postup v [tématu Vytvoření dávkového účtu,](batch-account-create-portal.md) kde najdete podrobnosti o tom, jak vytvořit a propojit účet.

Pomocí propojeného účtu úložiště nemusíte vytvářet a konfigurovat adresu URL SAS pro kontejner úložiště. Místo toho zadejte název kontejneru úložiště v účtu propojeného úložiště.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Koncový bod webu

Data, která se nenahraná do Azure Storage pořád můžou použít k vytváření souborů prostředků. Můžete zadat libovolnou platnou adresu URL HTTP obsahující vstupní data. Adresa URL je k dispozici rozhraní API dávky a pak data slouží k vytvoření souboru prostředků.

V následujícím příkladu Jazyka C# jsou vstupní data hostována na fiktivním koncovém bodu GitHubu. Rozhraní API načte soubor z platného koncového bodu webu a vygeneruje soubor prostředků, který má být spotřebován vaší úlohou. Pro tento scénář nejsou potřeba žádná pověření.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Tipy a návrhy

Každá úloha Azure Batch používá soubory jinak, což je důvod, proč Batch má možnosti, které jsou k dispozici pro správu souborů na úkoly. Následující scénáře nejsou určeny k komplexní, ale místo toho pokrývají několik běžných situací a poskytují doporučení.

### <a name="many-resource-files"></a>Mnoho souborů prostředků

Dávková úloha může obsahovat několik úloh, které všechny používají stejné běžné soubory. Pokud jsou běžné soubory úloh sdíleny mezi mnoha úkoly, může být vhodnější použít balíček aplikace k obsahovat soubory namísto použití souborů prostředků. Balíčky aplikací poskytují optimalizaci pro rychlost stahování. Data v balíčcích aplikací jsou také ukládána do mezipaměti mezi úkoly, takže pokud se soubory úloh nemění často, mohou být balíčky aplikací vhodné pro vaše řešení. S balíčky aplikací nemusíte ručně spravovat několik souborů prostředků nebo generovat adresy URL SAS pro přístup k souborům ve službě Azure Storage. Batch funguje na pozadí s Azure Storage pro ukládání a nasazování balíčků aplikací pro výpočetní uzly.

Pokud má každý úkol mnoho souborů jedinečných pro tento úkol, jsou nejlepší volbou soubory prostředků, protože úkoly, které používají jedinečné soubory, je často nutné aktualizovat nebo nahradit, což není tak snadné s obsahem balíčků aplikací. Soubory prostředků poskytují další flexibilitu pro aktualizaci, přidání nebo úpravy jednotlivých souborů.

### <a name="number-of-resource-files-per-task"></a>Počet souborů prostředků na úlohu

Pokud je na úkolu zadáno několik set souborů prostředků, batch může odmítnout úkol jako příliš velký. Nejlepší je udržet úkoly malé minimalizací počtu souborů prostředků na samotném úkolu.

Pokud neexistuje žádný způsob, jak minimalizovat počet souborů, které vaše úloha potřebuje, můžete úlohu optimalizovat vytvořením jednoho souboru prostředků, který odkazuje na kontejner úložiště souborů prostředků. Chcete-li to provést, umístěte soubory prostředků do kontejneru úložiště Azure a použijte různé [metody](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods) "kontejneru" pro soubory prostředků. Pomocí možností předpony objektu blob určete kolekce souborů, které se mají stahovat pro vaše úkoly.

## <a name="next-steps"></a>Další kroky

- Další informace o [balíčcích aplikací](batch-application-packages.md) jako alternativu k souborům prostředků.
- Další informace o použití kontejnerů pro soubory prostředků naleznete v [tématu Úlohy kontejnerů](batch-docker-container-workloads.md).
- Informace o shromažďování a ukládání výstupních dat z úkolů naleznete v [tématu Persist job and task output](batch-task-output.md).
- Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
