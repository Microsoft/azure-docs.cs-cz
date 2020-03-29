---
title: Spuštění aplikace z balíčku ZIP
description: Nasazení balíčku ZIP aplikace s nedělitelností. Zlepšete předvídatelnost a spolehlivost chování vaší aplikace během procesu nasazení ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920718"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Spuštění aplikace ve službě Azure App Service přímo z balíčku ZIP

Ve [službě Azure App Service](overview.md)můžete spouštět aplikace přímo ze souboru balíčku ZIP nasazení. Tento článek ukazuje, jak povolit tuto funkci ve vaší aplikaci.

Všechny ostatní metody nasazení ve službě App Service mají něco společného: vaše soubory se nasazují do *Aplikace D:\home\site\wwwroot* (nebo */home/site/wwwroot* pro aplikace pro Linux). Vzhledem k tomu, že stejný adresář používá vaše aplikace za běhu, je možné, že nasazení nezdaří z důvodu konfliktů zámků souborů a aplikace se chová nepředvídatelně, protože některé soubory ještě nejsou aktualizovány.

Naopak při spuštění přímo z balíčku, soubory v balíčku nejsou zkopírovány do adresáře *wwwroot.* Místo toho balíček ZIP sám získá připojen přímo jako *adresář wwwroot* jen pro čtení. Spuštění přímo z balíčku přináší několik výhod:

- Eliminuje konflikty zámků souborů mezi nasazením a runtime.
- Zajišťuje, že jsou kdykoli spuštěny pouze plně nasazené aplikace.
- Dá se nasadit do produkční aplikace (s restartováním).
- Zlepšuje výkon nasazení Azure Resource Manageru.
- Může zkrátit dobu studeného startu, zejména pro funkce JavaScriptu s velkými stromy balíčků npm.

> [!NOTE]
> V současné době jsou podporovány pouze soubory balíčku ZIP.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Povolit spuštění z balíčku

Nastavení `WEBSITE_RUN_FROM_PACKAGE` aplikace umožňuje spuštění z balíčku. Chcete-li jej nastavit, spusťte následující příkaz pomocí azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`umožňuje spouštět aplikaci z místního balíčku do aplikace. Můžete také [spustit ze vzdáleného balíčku](#run-from-external-url-instead).

## <a name="run-the-package"></a>Spuštění balíčku

Nejjednodušší způsob, jak spustit balíček ve službě App Service je pomocí azure CLI [az az webapp nasazení zdroj config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) příkaz. Například:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Vzhledem `WEBSITE_RUN_FROM_PACKAGE` k tomu, že je nastaveno nastavení aplikace, tento příkaz neextrahuje obsah balíčku do adresáře *D:\home\site\wwwroot* vaší aplikace. Místo toho nahraje soubor ZIP jako-je do *D:\home\data\SitePackages*a vytvoří *soubor packagename.txt* ve stejném adresáři, který obsahuje název balíčku ZIP, který se má načíst za běhu. Pokud balíček ZIP nahrajete jiným způsobem (například [FTP](deploy-ftp.md)), musíte ručně vytvořit adresář *D:\home\data\SitePackages* a soubor *packagename.txt.*

Příkaz také restartuje aplikaci. Protože `WEBSITE_RUN_FROM_PACKAGE` je nastavena, App Service připojí nahraný balíček jako *adresář wwwroot* jen pro čtení a spustí aplikaci přímo z tohoto připojeného adresáře.

## <a name="run-from-external-url-instead"></a>Místo toho spustit z externí adresy URL

Balíček můžete spustit také z externí adresy URL, jako je azure blob storage. Azure Storage [Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) můžete použít k nahrání souborů balíčků do účtu úložiště objektů Blob. Měli byste použít kontejner soukromého úložiště se [sdíleným přístupovým podpisem (SAS),](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) abyste povolili runtime služby App Service pro bezpečný přístup k balíčku. 

Jakmile soubor nahrajete do úložiště objektů Blob a budete `WEBSITE_RUN_FROM_PACKAGE` mít adresu URL SAS pro soubor, nastavte nastavení aplikace na adresu URL. Následující příklad to provádí pomocí Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Pokud publikujete aktualizovaný balíček se stejným názvem do úložiště objektů Blob, budete muset restartovat aplikaci tak, aby aktualizovaný balíček se načte do služby App Service.

## <a name="troubleshooting"></a>Řešení potíží

- Spuštění přímo z `wwwroot` balíčku umožňuje jen pro čtení. Aplikace se zobrazí chyba, pokud se pokusí zapsat soubory do tohoto adresáře.
- Formáty TAR a GZIP nejsou podporovány.
- Tato funkce není kompatibilní s [místní mezipamětí](overview-local-cache.md).
- Pro lepší výkon studeného startu použijte`WEBSITE_RUN_FROM_PACKAGE`místní možnost Zip ( =1).

## <a name="more-resources"></a>Další zdroje informací

- [Průběžné nasazení pro službu Azure App Service](deploy-continuous-deployment.md)
- [Nasazení kódu se souborem ZIP nebo WAR](deploy-zip.md)
