---
title: Vytvoření a použití souborů prostředků – Azure Batch | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit soubor prostředků služby Azure Batch z různých zdrojů.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 113faffb0ebac50a67c96ce21e0ee2c1564bb4fc
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405638"
---
# <a name="creating-and-using-resource-files"></a>Vytvoření a použití souborů prostředků

Úlohu služby Azure Batch často vyžaduje určitou formu data ke zpracování. Soubory prostředků jsou prostředky k poskytování těchto dat do služby Batch virtuálního počítače (VM) prostřednictvím úkolu. Soubory prostředků podporují všechny typy úloh: úlohy, spuštění úloh, úkolů přípravy úloh, uvolnění úloh atd. Tento článek popisuje několik běžných metod o tom, jak vytvořit soubor prostředků a umístit je na virtuálním počítači.  

Soubory prostředků slouží jako mechanismus pro dávat data do virtuálního počítače ve službě Batch, ale typ dat a způsobu jejich použití je flexibilní. Existují však některé běžné příklady využití:

1. Zřízení společné soubory na každém virtuálním počítači použití souborů prostředků na spouštěcí úkol
1. Zřízení vstupní data pro zpracování úloh

Společné soubory může být například soubory na spouštěcím úkolem použitý k instalaci aplikace, které vaše úkoly spouštět. Vstupní data mohou být nezpracovaná obrázek nebo video data nebo žádné informace pro dávkové zpracování.

## <a name="types-of-resource-files"></a>Typy zdrojových souborů

Existuje několik různých možností se vygenerovat soubory prostředku. Vytvoření procesu pro soubory prostředků se liší v závislosti na tom, kde původní data uložená.

Možnosti pro vytvoření souboru prostředků:

- [Adresa URL kontejneru úložiště](#storage-container-url): Generuje soubor prostředků z jakékoli kontejneru úložiště v Azure
- [Název kontejneru úložiště](#storage-container-name): Generuje soubor prostředků z názvu kontejneru v účtu služby Azure storage, propojené služby batch
- [Webový koncový bod](#web-endpoint): Generuje soubor prostředků z jakákoliv platná adresa URL protokolu HTTP

### <a name="storage-container-url"></a>Adresa URL kontejneru úložiště

Pomocí adresy URL kontejneru úložiště znamená, že může přístup k souborům v kontejneru úložiště v Azure se správnými oprávněními.

V tomto C# například souborů už se nahrály do kontejneru služby Azure storage jako úložiště objektů blob. Pro přístup k data potřebná k vytvoření souboru prostředků, musíme nejprve získat přístup ke kontejneru úložiště.

Vytvořte sdílený přístupový podpis (SAS) identifikátor URI se správnými oprávněními pro přístup ke kontejneru úložiště. Nastavte čas vypršení platnosti a oprávnění pro SAS. V takovém případě není zadán žádný čas spuštění, takže SAS začne platit okamžitě a jeho platnost vyprší dvě hodiny po jeho vygenerování.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Pro přístup k kontejneru, musí mít obě `Read` a `List` oprávnění, že se přístup k objektu blob, potřebujete jenom `Read` oprávnění.

Po nakonfigurování oprávnění vytvořit SAS token a formát adresy URL SAS pro přístup ke kontejneru úložiště. Pomocí formátované adresy URL SAS pro kontejner úložiště, vygenerujte soubor prostředků s [ `FromStorageContainerUrl` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Alternativu ke generování adresy URL SAS, je povolení anonymní, veřejné oprávnění ke čtení pro kontejner a jeho objekty BLOB v úložišti objektů Blob v Azure. Díky tomu můžete udělit přístup jen pro čtení k těmto prostředkům bez sdílení klíč účtu a bez nutnosti SAS. Veřejné oprávnění ke čtení se obvykle používá pro scénáře, ve kterém chcete určité přes bloby až po mít vždycky k dispozici pro anonymní přístup pro čtení. Pokud tento scénář vyhovuje řešení, najdete v článku [anonymní přístup k objektům blob](../storage/blobs/storage-manage-access-to-resources.md) článku se dozvíte informace o správě přístupu k datům objektu blob.

### <a name="storage-container-name"></a>Název kontejneru úložiště

Namísto konfigurace a vytvoření adresy URL SAS, můžete použít název kontejneru úložiště Azure pro přístup k datům objektu blob. Kontejner úložiště, který používá musí v účtu úložiště Azure, který je propojený s vaším účtem Batch, označované jako autostorage účtu. Pomocí názvu kontejneru úložiště účtu autostorage lze jednorázově přeskočit přihlašování, konfigurace a vytvoření adresy URL SAS pro přístup ke kontejneru úložiště.

V tomto příkladu předpokládáme, že data, která má být použit pro vytvoření souboru prostředků je již v účtu služby Azure Storage, který je propojený s vaším účtem Batch. Pokud nemáte účet autostorage, podívejte se na postup v [vytvoření účtu Batch](batch-account-create-portal.md) podrobnosti o tom, jak vytvořit a propojit účet.

S použitím propojený účet úložiště, není nutné vytvořit a nakonfigurovat adresy URL SAS pro kontejner úložiště. Místo toho zadejte název kontejneru úložiště ve vašem připojeném účtu úložiště.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Webový koncový bod

Data, která se nahraje do služby Azure Storage je stále možné k vytvoření souborů prostředků. Můžete zadat libovolnou platnou adresu URL HTTP obsahující vstupní data. Zadaná adresa URL k rozhraní API služby Batch, a pak data slouží k vytvoření souboru prostředků.

V následujícím C# příkladu se vstupní data hostována na koncovém bodu fiktivní Githubu. Rozhraní API načte soubor z platný webový koncový bod a vygeneruje soubor prostředků pro vaše úlohy. V tomto scénáři nejsou potřeba žádné přihlašovací údaje.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Tipy a návrhy

Každého úkolu služby Azure Batch používá soubory odlišně, což je důvod, proč Batch má dostupné možnosti pro správu souborů na úkolech. Následující scénáře nejsou určeny k být vyčerpávající, ale místo toho zahrnují několik běžné situace a poskytnout doporučení.

### <a name="many-resource-files"></a>Mnoho souborů prostředků

Dávkové úlohy může obsahovat několik úloh, že všechny používat stejné, běžné soubory. Pokud běžné úlohy soubory jsou sdíleny mezi mnoho úloh, pomocí balíčku aplikace obsahoval soubory, namísto použití souborů prostředků může být vhodnější. Balíčky aplikací poskytují možnost optimalizace pro rychlost stahování. Navíc se data v balíčky aplikací uložené v mezipaměti mezi úkoly, takže pokud se soubory úkolů nemění často, balíčky aplikací může být vhodné pro vaše řešení. Pomocí balíčků aplikací není nutné ručně spravovat několik souborů prostředků nebo generování adresy URL SAS pro přístup k souborům ve službě Azure Storage. Služba batch pracuje s Azure Storage k ukládání a nasadit balíčky aplikací do výpočetních uzlů na pozadí.

Pokud každý úkol má mnoho souborů, které jsou jedinečné pro tuto úlohu, soubory prostředků jsou většinou pravděpodobně nejlepší možností. Úlohy, které používají soubory jedinečné často potřeba aktualizovat nebo nahradit, což není snadné s balíčky obsahu aplikace. Soubory prostředků poskytují vyšší flexibilitu pro aktualizaci, přidání nebo úpravu jednotlivé soubory.

### <a name="number-of-resource-files-per-task"></a>Počet souborů prostředků na úkolu

Pokud existuje několik stovek prostředků soubory zadané na úlohu, Batch by mohla zamítat úkol, je příliš velký. Doporučujeme zachovat vaše úkoly malé minimalizací počet souborů prostředků na vlastní úloha.

Pokud neexistuje žádný způsob, chcete-li minimalizovat počet souborů, které vaše úloha potřebuje, úlohu můžete optimalizovat tak, že vytvoříte soubor jeden prostředek, který odkazuje na kontejner úložiště souborů prostředků. Umístěte svoje soubory prostředků do kontejneru služby Azure Storage a použít různé režimy "Kontejneru" soubory prostředků. Pomocí možností předponu objektu blob k zadání kolekce souborů ke stažení pro své úkoly.

## <a name="next-steps"></a>Další postup

- Další informace o [balíčky aplikací](batch-application-packages.md) jako alternativa k soubory prostředků.
- Další informace o používání kontejnerů pro zdrojové soubory, naleznete v tématu [úlohy kontejneru](batch-docker-container-workloads.md).
- Zjistěte, jak shromažďovat a ukládat výstupní data z vašich úkolů, najdete v článku [trvalý výstup úloh a úkolů](batch-task-output.md).
- Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
