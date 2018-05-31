---
title: Rozhraní API a nástroje služby Azure Batch pro vývojáře | Microsoft Docs
description: Přečtěte si o dostupných rozhraních API a nástrojích pro vývoj řešení pomocí služby Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: get-started-article
ms.date: 05/15/2018
ms.author: danlep
ms.openlocfilehash: 4a47c7355e8dfea97f7dee6787da87444a7642c3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359318"
---
# <a name="overview-of-batch-apis-and-tools"></a>Přehled rozhraní API a nástrojů služby Batch

Zpracování paralelních úloh službou Azure Batch se obvykle provádí programově pomocí jednoho z [rozhraní API služby Batch](#batch-development-apis). Vaše klientská aplikace nebo služba může používat rozhraní API služby Batch ke komunikaci se službou Batch. Pomocí rozhraní API služby Batch můžete vytvořit a spravovat fondy výpočetních uzlů – virtuální počítače nebo cloudové služby. Pak můžete plánovat úlohy a úkoly, které se mají v těchto uzlech spouštět. 

Umožní vám to efektivně zpracovávat rozsáhlé úlohy pro vaši organizaci nebo poskytovat front-end služby zákazníkům, aby mohli spouštět úlohy a úkoly – na vyžádání nebo naplánované – na jednom, stovkách nebo tisících uzlů. Službu Azure Batch můžete také používat jako součást rozsáhlejšího pracovního postupu spravovaného nástroji, například [Azure Data Factory](../data-factory/v1/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Až budete připraveni rozhraní API služby Batch prozkoumat podrobněji, abyste hlouběji porozuměli jeho poskytovaným funkcím, přečtěte si [Přehled funkcí služby Batch pro vývojáře](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-for-batch-development"></a>Účty Azure pro vývoj pro službu Batch
Při vývoji řešení ve službě Batch používáte následující účty předplatného Azure:

* **Účet Batch** – prostředky služby Azure Batch, včetně fondů, výpočetních uzlů, úloh a úkolů, jsou v Azure přidružené k [účtu Batch](batch-api-basics.md#account). Pokud vaše aplikace odešle požadavek na službu Batch, ověří se tato žádost pomocí názvu účtu Azure Batch, adresy URL účtu a přístupové klávesy, nebo tokenu Azure Active Directory. [Účet Batch můžete vytvořit](batch-account-create-portal.md) na webu Azure Portal nebo prostřednictvím kódu programu.
* **Účet Storage** – Služba Batch zahrnuje integrovanou podporu pro práci se soubory ve službě [Azure Storage][azure_storage]. Téměř každý scénář služby Batch používá úložiště objektů blob v Azure – pro přípravu programů, které budou vaše úkoly spouštět, a dat, která budou zpracovávat, a také pro ukládání generovaných výstupních dat. Možnosti účtu úložiště ve službě Batch najdete v tématu [Přehled funkcí Batch](batch-api-basics.md#azure-storage-account).

## <a name="batch-service-apis"></a>Rozhraní API služby Batch

Vaše aplikace a služby mohou provádět přímá volání rozhraní REST API nebo používat jednu nebo více následujících klientských knihoven ke spuštění a správě úloh služby Azure Batch.

| Rozhraní API | API – referenční informace | Ke stažení | Kurz | Ukázky kódů | Další informace |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[docs.microsoft.com][batch_rest] |neuvedeno |- |- | [Podporované verze](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Kurz](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [Poznámky k verzi](http://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[Kurz](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [Soubor Readme](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[Kurz](batch-nodejs-get-started.md) |- | [Soubor Readme](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[Soubor Readme][api_sample_java] | [Soubor Readme](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Rozhraní API služby Batch Management

Rozhraní API Azure Resource Manageru pro službu Batch poskytují programový přístup k účtům Batch. Pomocí těchto rozhraní API můžete prostřednictvím kódu programu spravovat účty Batch, kvóty, balíčky aplikací a další prostředky přes zprostředkovatele Microsoft.Batch.  

| Rozhraní API | API – referenční informace | Ke stažení | Kurz | Ukázky kódů |
| --- | --- | --- | --- | --- |
| **Správa služby Batch pomocí REST** |[docs.microsoft.com][api_rest_mgmt] |neuvedeno |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Knihovna Batch Management .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Kurz](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Správa služby Batch pomocí Pythonu** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Správa služby Batch pomocí Node.js** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **Správa služby Batch pomocí Javy** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>Nástroje příkazového řádku služby Batch

Tyto nástroje příkazového řádku poskytují stejné funkce jako rozhraní API služby Batch a služby Batch Management: 

* [Rutiny prostředí PowerShell služby Batch][batch_ps]: Rutiny služby Azure Batch vám v modulu [Azure PowerShell](/powershell/azure/overview) umožňují spravovat prostředky Batch v prostředí PowerShell.
* [Azure CLI 2.0:](/cli/azure) Rozhraní příkazového řádku Azure (Azure CLI) je sada nástrojů pro různé platformy, která poskytuje příkazy prostředí pro komunikaci s řadou služeb Azure, včetně služby Batch a služby Batch Management. Další informace o použití Azure CLI se službou Batch najdete v tématu [Správa prostředků služby Batch pomocí Azure CLI](batch-cli-get-started.md).

## <a name="other-tools-for-application-development"></a>Další nástroje pro vývoj aplikací

Tady je několik dalších nástrojů, které můžou být užitečné při sestavování a ladění aplikací a služeb Batch:

* [Azure Portal:][portal] Na webu Azure Portal můžete vytvářet, monitorovat a odstraňovat fondy, úlohy a úkoly služby Batch. Zatímco spouštíte úlohy, můžete zobrazit informace o stavu těchto a dalších prostředků, a dokonce i stahovat soubory z výpočetních uzlů ve fondech. Například při řešení potíží si můžete stáhnout soubor `stderr.txt` neúspěšné úlohy. Můžete si také stáhnout soubory vzdálené plochy (RDP), které lze použít k přihlášení do výpočetních uzlů.
* [Azure BatchLabs:][batch_labs] BatchLabs je bezplatný a samostatný klientský nástroj s bohatými funkcemi, který pomáhá vytvářet, ladit a monitorovat aplikace Azure Batch. Můžete si stáhnout [instalační balíček](https://azure.github.io/BatchLabs/) pro Mac, Linux nebo Windows.
* [Microsoft Azure Storage Explorer][storage_explorer]: Ačkoliv se nejedná vyloženě o nástroj služby Azure Batch, Storage Explorer je dalším užitečným nástrojem při vývoji a ladění řešení Batch.

## <a name="additional-resources"></a>Další zdroje

- Informace o protokolování událostí z aplikace Batch najdete v tématu [Protokolování událostí pro diagnostické hodnocení a monitorování řešení Batch](batch-diagnostics.md). Referenční informace k událostem vyvolaným službou Batch najdete v tématu [Analýza služby Batch](batch-analytics.md).
- Informace o proměnných prostředí pro výpočetní uzly najdete v tématu [Proměnné prostředí výpočetních uzlů služby Azure Batch](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [Přehled funkcí Batch pro vývojáře](batch-api-basics.md), kde jsou základní informace pro každého, kdo se připravuje použít Batch. Článek obsahuje podrobné informace o prostředcích služby Batch, jako jsou fondy, uzly a úlohy, a mnoha funkcích rozhraní API, které můžete použít při vytváření aplikace Batch.
* V kapitole [Začínáme s knihovnou Azure Batch pro .NET](tutorial-parallel-dotnet.md) zjistíte, jak použít C# a knihovnu Batch .NET ke spuštění jednoduché úlohy s použitím běžného pracovního postupu služby Batch. K dispozici jsou také verze tohoto kurzu pro [Python](tutorial-parallel-python.md) a [Node.js](batch-nodejs-get-started.md).
* Stáhněte si [ukázky kódu na GitHubu][github_samples] a podívejte se, jak můžou jazyk C# i Python komunikovat přes rozhraní se službou Batch při plánování a zpracování ukázkových úloh.


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch/clientlibrary:
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-mgmt-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/azurerm.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchLabs/
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com
