---
title: Rozhraní API a nástroje pro vývojáře
description: Přečtěte si o dostupných rozhraních API a nástrojích pro vývoj řešení pomocí služby Azure Batch.
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: 502eb08631223215933b75dca882c12c02d17bd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89146398"
---
# <a name="overview-of-batch-apis-and-tools"></a>Přehled rozhraní API a nástrojů služby Batch

Zpracování paralelních úloh službou Azure Batch se obvykle provádí programově pomocí jednoho z rozhraní API služby Batch. Vaše klientská aplikace nebo služba může používat rozhraní API služby Batch ke komunikaci se službou Batch. Pomocí rozhraní API služby Batch můžete vytvořit a spravovat fondy výpočetních uzlů – virtuální počítače nebo cloudové služby. Pak můžete plánovat úlohy a úkoly, které se mají v těchto uzlech spouštět.

Umožní vám to efektivně zpracovávat rozsáhlé úlohy pro vaši organizaci nebo poskytovat front-end služby zákazníkům, aby mohli spouštět úlohy a úkoly – na vyžádání nebo naplánované – na jednom, stovkách nebo tisících uzlů. Službu Azure Batch můžete také používat jako součást rozsáhlejšího pracovního postupu spravovaného nástroji, například [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Další informace o funkcích a pracovním postupu používaném v Azure Batch najdete v tématu [pracovní postup služby Batch a prostředky](batch-service-workflow-features.md).

## <a name="azure-accounts-for-batch-development"></a>Účty Azure pro vývoj pro službu Batch

Při vývoji řešení ve službě Batch používáte následující účty předplatného Azure:

- **Účet Batch** – prostředky služby Azure Batch, včetně fondů, výpočetních uzlů, úloh a úkolů, jsou v Azure přidružené k [účtu Batch](accounts.md). Pokud vaše aplikace odešle požadavek na službu Batch, ověří se tato žádost pomocí názvu účtu Azure Batch, adresy URL účtu a přístupové klávesy, nebo tokenu Azure Active Directory. [Účet Batch můžete vytvořit](batch-account-create-portal.md) v Azure Portal nebo programově.
- **Účet Storage** – Služba Batch zahrnuje integrovanou podporu pro práci se soubory ve službě [Azure Storage](../storage/index.yml). Téměř každý scénář služby Batch používá úložiště objektů blob v Azure – pro přípravu programů, které budou vaše úkoly spouštět, a dat, která budou zpracovávat, a také pro ukládání generovaných výstupních dat. Každý účet Batch je obvykle přidružen k odpovídajícímu účtu úložiště.

## <a name="service-level-and-management-level-apis"></a>Rozhraní API na úrovni služby a správu

Azure Batch má dvě sady rozhraní API, jednu pro úroveň služby a jednu pro úroveň správy. Pojmenování je často podobné, ale vrací různé výsledky.

V protokolu aktivit jsou sledovány pouze akce z rozhraní API pro správu. Rozhraní API na úrovni služby obcházejí vrstvu správy prostředků Azure (management.azure.com) a nezaprotokolují se.

Například [rozhraní API služby Batch pro odstranění fondu](/rest/api/batchservice/pool/delete) je cílené přímo na účet Batch: `DELETE {batchUrl}/pools/{poolId}`

Vzhledem k tomu, že [rozhraní API pro správu dávek pro odstranění fondu](/rest/api/batchmanagement/pool/delete)  je cílené na Management.Azure.com vrstvu: `DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>Rozhraní API služby Batch

Vaše aplikace a služby mohou provádět přímá volání rozhraní REST API nebo používat jednu nebo více následujících klientských knihoven ke spuštění a správě úloh služby Azure Batch.

| Rozhraní API | referenční dokumentace k rozhraní API | Stáhnout | Kurz | Ukázky kódů | Další informace |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[Azure REST API – dokumentace](/rest/api/batchservice/) |Není k dispozici |- |- | [Podporované verze](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[Azure SDK pro .NET – dokumentace](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[Kurz](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [Zpráva k vydání verze](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[Azure SDK pro Python – dokumentace](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[Kurz](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [Soubor Readme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch Node.js** |[Azure SDK pro JavaScript – dokumentace](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[Kurz](batch-nodejs-get-started.md) |- | [Soubor Readme](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[Azure SDK pro Java – dokumentace](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [Soubor Readme](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Rozhraní API služby Batch Management

Rozhraní API Azure Resource Manageru pro službu Batch poskytují programový přístup k účtům Batch. Pomocí těchto rozhraní API můžete prostřednictvím kódu programu spravovat účty Batch, kvóty, balíčky aplikací a další prostředky přes zprostředkovatele Microsoft.Batch.  

| Rozhraní API | referenční dokumentace k rozhraní API | Stáhnout | Kurz | Ukázky kódů |
| --- | --- | --- | --- | --- |
| **Správa služby Batch pomocí REST** |[Azure REST API – dokumentace](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Knihovna Batch Management .NET** |[Azure SDK pro .NET – dokumentace](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [Kurz](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Správa služby Batch pomocí Pythonu** |[Azure SDK pro Python – dokumentace](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **Správa služby Batch pomocí Node.js** |[Azure SDK pro JavaScript – dokumentace](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Správa služby Batch pomocí Javy** |[Azure SDK pro Java – dokumentace](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>Nástroje příkazového řádku služby Batch

Tyto nástroje příkazového řádku poskytují stejné funkce jako rozhraní API služby Batch a služby Batch Management: 

- [Rutiny prostředí PowerShell služby Batch](/powershell/module/az.batch/): Rutiny služby Azure Batch vám v modulu [Azure PowerShell](/powershell/azure/) umožňují spravovat prostředky Batch v prostředí PowerShell.
- [Azure CLI:](/cli/azure) Azure CLI je sada nástrojů pro různé platformy, která poskytuje příkazy prostředí pro komunikaci s řadou služeb Azure, včetně služby Batch a služby Batch Management. Další informace o použití Azure CLI se službou Batch najdete v tématu [Správa prostředků služby Batch pomocí Azure CLI](batch-cli-get-started.md).

## <a name="other-tools-for-application-development"></a>Další nástroje pro vývoj aplikací

Tyto další nástroje mohou být užitečné při sestavování a ladění aplikací a služeb Batch.

- [Azure Portal:](https://portal.azure.com/) Na webu Azure Portal můžete vytvářet, monitorovat a odstraňovat fondy, úlohy a úkoly služby Batch. Můžete zobrazit informace o stavu těchto a dalších prostředků, zatímco spouštíte úlohy, a dokonce i stahovat soubory z výpočetních uzlů ve fondech. Například při řešení potíží si můžete stáhnout soubor `stderr.txt` neúspěšné úlohy. Můžete si také stáhnout soubory vzdálené plochy (RDP), které lze použít k přihlášení do výpočetních uzlů.
- [Azure Batch Explorer:](https://azure.github.io/BatchExplorer/) Batch Explorer (dříve BatchLabs) je bezplatný a samostatný klientský nástroj s bohatými funkcemi, který pomáhá vytvářet, ladit a monitorovat aplikace Azure Batch. Můžete si stáhnout [instalační balíček](https://azure.github.io/BatchExplorer/) pro Mac, Linux nebo Windows.
- [Azure Batch loděnice](https://github.com/Azure/batch-shipyard): dávková loděnice je nástroj, který umožňuje zřídit, spouštět a monitorovat dávkové zpracování a úlohy prostředí HPC založené na kontejnerech na Azure Batch.
- [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/): i když není výhradně Azure Batch nástroj, Průzkumník služby Storage je dalším užitečným nástrojem při vývoji a ladění řešení Batch.

## <a name="additional-resources"></a>Další zdroje

- Další informace o protokolování událostí z aplikace Batch najdete v tématu [metriky služby Batch, výstrahy a protokoly pro vyhodnocení a monitorování diagnostiky](batch-diagnostics.md).
- Referenční informace o událostech vyvolaných službou Batch najdete v tématu [Batch Analytics](batch-analytics.md).
- Informace o proměnných prostředí pro výpočetní uzly najdete v tématu [Azure Batch proměnných prostředí modulu runtime](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- V kapitole [Začínáme s knihovnou Azure Batch pro .NET](tutorial-parallel-dotnet.md) zjistíte, jak použít C# a knihovnu Batch .NET ke spuštění jednoduché úlohy s použitím běžného pracovního postupu služby Batch. K dispozici jsou také verze tohoto kurzu pro [Python](tutorial-parallel-python.md) a [Node.js](batch-nodejs-get-started.md).
- Stáhněte si [ukázky kódu na GitHubu](https://github.com/Azure-Samples/azure-batch-samples) a podívejte se, jak můžou jazyk C# i Python komunikovat přes rozhraní se službou Batch při plánování a zpracování ukázkových úloh.
