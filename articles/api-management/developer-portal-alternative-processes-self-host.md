---
title: Alternativy k samoobslužnému hostování portálu pro vývojáře
titleSuffix: Azure API Management
description: Přečtěte si o alternativních přístupech, které můžete použít při samoobslužném hostování portálu pro vývojáře v Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: cb14ecd05c1590667ac9b5618b3f0de9da30ce96
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741624"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>Alternativní přístupy k samoobslužnému hostování portálu pro vývojáře

Existuje několik alternativních přístupů, které můžete prozkoumat při [samoobslužném hostování portálu pro vývojáře](developer-portal-self-host.md):

* Použijte výrobní sestavení návrháře a vydavatele.

* K publikování portálu použijte Function App Azure.

* Před soubory portálu pomocí Content Delivery Network (CDN) můžete zkrátit dobu načítání stránek.

Tento článek poskytuje informace o každém z těchto přístupů. 

Pokud jste to ještě neudělali, nastavte [místní prostředí](developer-portal-self-host.md#step-1-set-up-local-environment) pro nejnovější vydání portálu pro vývojáře.

## <a name="build-for-production"></a>Sestavení pro produkci

Pokud chcete pro účely spolupráce hostovat vývojové prostředí portálu online, použijte výrobní sestavení návrháře a vydavatele. Produkční sestavení zařadí soubory do sady, vyloučí zdrojové mapy atd.

Vytvořte v adresáři sadu prostředků `./dist/designer` spuštěním příkazu:

```sh
npm run build-designer
```

Výsledkem je jediná stránková aplikace, takže ji můžete nasadit na statického webového hostitele, jako je například Azure Blob Storage static Web.

Podobně umístěte zkompilované a optimalizované vydavatele do `./dist/publisher` složky:

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>Publikování portálu pomocí Function App

Spusťte krok publikování v cloudu jako alternativu k jejímu spuštění místně.

K implementaci publikování pomocí Function App Azure potřebujete následující požadavky:

- [Vytvoření funkce Azure Functions](../azure-functions/functions-create-first-azure-function.md) Funkce musí být funkcí jazyka JavaScript.
- Nainstalovat Azure Functions Core Tools:
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>Krok 1: Konfigurace výstupního úložiště

Nahrává se obsah přímo na hostující web ("$web kontejner výstupního úložiště) místo na místní složku. Nakonfigurujte tuto změnu v `./src/config.publish.json` souboru:

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>Krok 2: Sestavte a nasaďte Function App

Ve složce je ukázková funkce triggeru HTTP `./examples` . Pokud ho chcete sestavit a umístit do `./dist/function` , spusťte následující příkaz:

```sh
npm run build-function
```

Pak se přihlaste ke službě Azure CLI a nasaďte ji:

```sh
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

Po nasazení ji můžete vyvolat pomocí volání HTTP:

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>Hostování a CDN

Portál pro vývojáře, který jsme navrhli k hostování vašeho webu pomocí účtu služby Azure Storage, [je samoobslužně hostitelem portálu pro vývojáře](developer-portal-self-host.md) . Můžete ale publikovat soubory prostřednictvím libovolného řešení, včetně služeb poskytovatelů hostingu.

Můžete také soubory vytvořit pomocí Content Delivery Network (CDN) a zkrátit tak dobu načítání stránek. Doporučujeme použít [Azure CDN](https://azure.microsoft.com/services/cdn/).

## <a name="next-steps"></a>Další kroky

Další informace o portálu pro vývojáře:

- [Přehled vývojářského portálu pro službu Azure API Management](api-management-howto-developer-portal.md)
