---
title: Spuštění aplikace z balíčku ZIP
description: Nasaďte balíček ZIP vaší aplikace s nedělitelnost. Zlepšení předvídatelnosti a spolehlivosti chování vaší aplikace během procesu nasazování ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 3440653455626af4e3705d89349a66d6bf2fbfc0
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008125"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Spuštění aplikace v Azure App Service přímo z balíčku ZIP

V [Azure App Service](overview.md)můžete své aplikace spouštět přímo ze souboru balíčku zip nasazení. Tento článek ukazuje, jak povolit tuto funkci ve vaší aplikaci.

Všechny ostatní metody nasazení v App Service mají něco společného: vaše soubory se nasazují do *D:\home\site\wwwroot* ve vaší aplikaci (nebo */Home/site/wwwroot* pro aplikace pro Linux). Vzhledem k tomu, že aplikace používá stejný adresář za běhu, je možné, že nasazení selže kvůli konfliktům zámků souborů a že se aplikace bude chovat nepředvídatelné, protože některé soubory ještě nejsou aktualizované.

Naproti tomu, když spouštíte přímo z balíčku, soubory v balíčku nejsou zkopírovány do adresáře *wwwroot* . Místo toho se samotný balíček ZIP připojí přímo jako adresář *wwwroot* jen pro čtení. K dispozici je několik výhod pro spuštění přímo z balíčku:

- Eliminuje konflikty zámků souborů mezi nasazením a modulem runtime.
- Zajistí, že budou spuštěné jenom plně nasazené aplikace.
- Dá se nasadit do produkční aplikace (s restartováním).
- Zlepšuje výkon Azure Resource Manager nasazení.
- Může snížit dobu studených časů, zejména pro funkce JavaScriptu s velkými stromy balíčků npm.

> [!NOTE]
> V současné době jsou podporovány pouze soubory balíčku ZIP.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Povolit spuštění z balíčku

`WEBSITE_RUN_FROM_PACKAGE`Nastavení aplikace umožňuje spuštění z balíčku. Pokud ho chcete nastavit, spusťte následující příkaz pomocí Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` umožňuje spustit aplikaci z místního balíčku aplikace. Můžete také [Spustit ze vzdáleného balíčku](#run-from-external-url-instead).

## <a name="run-the-package"></a>Spustit balíček

Nejjednodušší způsob, jak balíček v App Service spustit, je pomocí příkazu Azure CLI [AZ WebApp Deployment source config-zip](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) . Příklad:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Vzhledem k tomu `WEBSITE_RUN_FROM_PACKAGE` , že nastavení aplikace je nastavené, tento příkaz neextrahuje obsah balíčku do adresáře *D:\home\site\wwwroot* vaší aplikace. Místo toho odešle soubor ZIP jako *D:\home\data\SitePackages* a vytvoří *packagename.txt* ve stejném adresáři, který obsahuje název balíčku zip, který se má načíst za běhu. Pokud váš balíček ZIP nahrajete jiným způsobem (například [FTP](deploy-ftp.md)), budete muset vytvořit adresář *D:\home\data\SitePackages* a soubor *packagename.txt* ručně.

Příkaz také restartuje aplikaci. Protože `WEBSITE_RUN_FROM_PACKAGE` je nastavena, App Service připojí nahraný balíček jako adresář *wwwroot* jen pro čtení a spustí aplikaci přímo z tohoto připojeného adresáře.

## <a name="run-from-external-url-instead"></a>Spustit z externí adresy URL

Balíček můžete také spustit z externí adresy URL, jako je například Azure Blob Storage. Pomocí [Průzkumník služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) můžete odeslat soubory balíčku do svého účtu BLOB Storage. Pomocí privátního kontejneru úložiště se [sdíleným přístupovým podpisem (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) můžete App Service modulu runtime povolit zabezpečený přístup k balíčku. 

Po nahrání souboru do úložiště objektů BLOB a zadání adresy URL SAS pro tento soubor nastavte `WEBSITE_RUN_FROM_PACKAGE` nastavení aplikace na adresu URL. V následujícím příkladu se používá Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Pokud publikujete aktualizovaný balíček se stejným názvem pro úložiště objektů blob, musíte restartovat aplikaci, aby se aktualizovaný balíček načetl do App Service.

## <a name="troubleshooting"></a>Řešení potíží

- Spuštění přímo z balíčku zpřístupňuje jen `wwwroot` pro čtení. Pokud se aplikace pokusí zapisovat soubory do tohoto adresáře, dojde k chybě.
- Formáty TAR a GZIP se nepodporují.
- Tato funkce není kompatibilní s [místní mezipamětí](overview-local-cache.md).
- Pro zlepšení výkonu pro studený start použijte místní možnost zip ( `WEBSITE_RUN_FROM_PACKAGE` = 1).

## <a name="more-resources"></a>Další zdroje informací

- [Průběžné nasazování pro Azure App Service](deploy-continuous-deployment.md)
- [Nasazení kódu pomocí souboru ZIP nebo WAR](deploy-zip.md)
