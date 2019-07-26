---
title: Spuštění Azure Functions z balíčku | Microsoft Docs
description: Modul runtime Azure Functions spouští vaše funkce připojením souboru balíčku pro nasazení, který obsahuje soubory projektu Function App.
services: functions
documentationcenter: na
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: glenga
ms.openlocfilehash: 96dbe9b15831a349afc0e68c15c39c1cb31b1032
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444068"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Spuštění Azure Functions ze souboru balíčku

V Azure můžete spouštět funkce přímo ze souboru balíčku pro nasazení ve vaší aplikaci Function App. Druhou možností je nasadit soubory do `d:\home\site\wwwroot` adresáře aplikace Function App.

Tento článek popisuje výhody spouštění funkcí z balíčku. Také ukazuje, jak povolit tuto funkci ve vaší aplikaci Function App.

> [!IMPORTANT]
> Při nasazení vašich funkcí do aplikace Functions pro Linux v [plánu Premium](functions-scale.md#premium-plan)byste měli vždy spustit ze souboru balíčku a [publikovat aplikaci pomocí Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Výhody spouštění ze souboru balíčku
  
Existuje několik výhod, které je potřeba spustit ze souboru balíčku:

+ Snižuje riziko problémů se zámkem při kopírování souborů.
+ Dá se nasadit do produkční aplikace (s restartováním).
+ Můžete si být jisti soubory, které jsou spuštěny ve vaší aplikaci.
+ Zlepšuje výkon [Azure Resource Manager nasazení](functions-infrastructure-as-code.md).
+ Může snížit dobu studených časů, zejména pro funkce JavaScriptu s velkými stromy balíčků npm.

Další informace najdete v [tomto oznámení](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Povolení spouštění funkcí z balíčku

Pokud chcete povolit spouštění aplikace Function App z balíčku, stačí přidat `WEBSITE_RUN_FROM_PACKAGE` nastavení do nastavení aplikace Function App. `WEBSITE_RUN_FROM_PACKAGE` Nastavení může mít jednu z následujících hodnot:

| Value  | Popis  |
|---------|---------|
| **`1`**  | Doporučuje se pro aplikace Function App běžící v systému Windows. Spusťte ze souboru balíčku ve `d:\home\data\SitePackages` složce aplikace Function App. Pokud nedojde k [nasazení pomocí nástroje zip Deploy](#integration-with-zip-deployment), tato možnost vyžaduje, aby složka měla také soubor `packagename.txt`s názvem. Tento soubor obsahuje pouze název souboru balíčku ve složce bez prázdných znaků. |
|**`<url>`**  | Umístění konkrétního souboru balíčku, který chcete spustit. Pokud používáte úložiště objektů blob, měli byste použít privátní kontejner se [sdíleným přístupovým podpisem (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) , aby modul runtime Functions mohl přistupovat k balíčku. Pomocí [Průzkumník služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) můžete odeslat soubory balíčku do svého účtu BLOB Storage.         |

> [!CAUTION]
> Při spuštění aplikace Function App ve Windows znamená možnost externí adresy URL horší výkon při studeném startu. Při nasazování aplikace Function App do Windows byste měli nastavit `WEBSITE_RUN_FROM_PACKAGE` `1` a publikovat s nasazením zip.

V následujícím příkladu je aplikace Function App nakonfigurovaná tak, aby běžela ze souboru. zip hostovaného ve službě Azure Blob Storage:

![Nastavení aplikace WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> V současné době jsou podporovány pouze soubory balíčku. zip.

## <a name="integration-with-zip-deployment"></a>Integrace s nasazením zip

[Nasazení zip][Zip deployment for Azure Functions] je funkce Azure App Service, která umožňuje nasadit projekt Function App do `wwwroot` adresáře. Projekt je zabalen jako soubor nasazení. zip. Stejná rozhraní API je možné použít k nasazení balíčku do `d:\home\data\SitePackages` složky. S hodnotou nastavení aplikace nakopírují rozhraní API pro nasazení `d:\home\site\wwwroot`zip balíček do složkymístoextrakcesouborůdo.`d:\home\data\SitePackages` `WEBSITE_RUN_FROM_PACKAGE` `1` Zároveň vytvoří `packagename.txt` soubor. Aplikace Function App se pak spustí z balíčku po restartování a `wwwroot` bude jen pro čtení. Další informace o nasazení zip najdete v tématu [nasazení zip pro Azure Functions](deployment-zip-push.md).

## <a name="adding-the-websiterunfrompackage-setting"></a>Přidání nastavení WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>Řešení potíží

- Příkaz spustit z balíčku `wwwroot` zpřístupňuje jen pro čtení, takže při zápisu souborů do tohoto adresáře se zobrazí chyba.
- Formáty tar a gzip se nepodporují.
- Tato funkce nevytváří místní mezipaměť.
- Pro zlepšení výkonu pro studený start použijte místní možnost zip (`WEBSITE_RUN_FROM_PACKAGE`= 1).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
