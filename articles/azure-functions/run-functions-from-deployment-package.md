---
title: Spuštění funkcí Azure z balíčku
description: Mít Azure Functions runtime spustit vaše funkce připojením souboru balíčku nasazení, který obsahuje soubory projektu aplikace funkce.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: d40896d6a4659945dbeda9ca965366f0b2ca4bd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365267"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Spuštění funkcí Azure ze souboru balíčku

V Azure můžete spouštět funkce přímo ze souboru balíčku nasazení ve vaší aplikaci funkce. Druhou možností je nasazení souborů `d:\home\site\wwwroot` v adresáři aplikace funkce.

Tento článek popisuje výhody spuštění funkcí z balíčku. Také ukazuje, jak povolit tuto funkci v aplikaci funkce.

> [!IMPORTANT]
> Při nasazování funkcí do aplikace pro funkce Linuxu v [plánu Premium](functions-scale.md#premium-plan)byste měli vždy spustit ze souboru balíčku a [publikovat aplikaci pomocí nástrojů Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Výhody spuštění ze souboru balíčku
  
Spuštění souboru balíčku přináší několik výhod:

+ Snižuje riziko problémů se zamykáním kopírování souborů.
+ Dá se nasadit do produkční aplikace (s restartováním).
+ Můžete si být jisti soubory, které jsou spuštěny ve vaší aplikaci.
+ Zlepšuje výkon [nasazení Azure Resource Manageru](functions-infrastructure-as-code.md).
+ Může zkrátit dobu studeného startu, zejména pro funkce JavaScriptu s velkými stromy balíčků npm.

Další informace naleznete v [tomto oznámení](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Povolení spuštění funkcí z balíčku

Chcete-li povolit spuštění aplikace funkce z `WEBSITE_RUN_FROM_PACKAGE` balíčku, stačí přidat nastavení do nastavení aplikace funkce. Nastavení `WEBSITE_RUN_FROM_PACKAGE` může mít jednu z následujících hodnot:

| Hodnota  | Popis  |
|---------|---------|
| **`1`**  | Doporučeno pro funkční aplikace spuštěné ve Windows. Spusťte ze souboru balíčku ve `d:\home\data\SitePackages` složce aplikace funkce. Pokud není [nasazení s zip nasazení](#integration-with-zip-deployment), tato možnost vyžaduje, aby složka má také soubor s názvem `packagename.txt`. Tento soubor obsahuje pouze název souboru balíčku ve složce bez jakýchkoli mezer. |
|**`<URL>`**  | Umístění konkrétního souboru balíčku, který chcete spustit. Při použití úložiště objektů blob byste měli použít soukromý kontejner se [sdíleným přístupovým podpisem (SAS),](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) abyste povolili runtime Funkce pro přístup k balíčku. Azure Storage [Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) můžete použít k nahrání souborů balíčků do účtu úložiště objektů Blob. Když zadáte adresu URL, musíte také [synchronizovat aktivační události](functions-deployment-technologies.md#trigger-syncing) po publikování aktualizovaného balíčku. |

> [!CAUTION]
> Při spuštění aplikace funkce ve Windows poskytuje možnost externí adresy URL horší výkon studeného startu. Při nasazování aplikace funkce do `WEBSITE_RUN_FROM_PACKAGE` Windows `1` byste měli nastavit a publikovat s nasazením zip.

Následující funkce aplikace nakonfigurované pro spuštění ze souboru ZIP hostovaného v úložišti objektů Blob Azure:

![WEBSITE_RUN_FROM_ZIP nastavení aplikace](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> V současné době jsou podporovány pouze soubory balíčků .zip.

## <a name="integration-with-zip-deployment"></a>Integrace s nasazením zipu

[Nasazení ZIP][Zip deployment for Azure Functions] je funkce služby Azure App Service, která `wwwroot` umožňuje nasadit projekt aplikace funkce do adresáře. Projekt je zabalen jako soubor nasazení ZIP. Stejná api lze použít k nasazení `d:\home\data\SitePackages` balíčku do složky. S `WEBSITE_RUN_FROM_PACKAGE` hodnotou nastavení `1`aplikace , zip nasazení API `d:\home\data\SitePackages` zkopírujte balíček do `d:\home\site\wwwroot`složky namísto extrahování souborů do . Vytvoří také `packagename.txt` soubor. Po restartování je balíček `wwwroot` připojen jako souborový systém jen pro čtení. Další informace o nasazení zip, najdete v [tématu zip nasazení pro funkce Azure](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Přidání nastavení WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Řešení potíží

- Spustit z `wwwroot` balíčku umožňuje jen pro čtení, takže se zobrazí chyba při zápisu souborů do tohoto adresáře.
- Formáty Tar a gzip nejsou podporovány.
- Tato funkce nevytváří s místní mezipaměti.
- Pro lepší výkon studeného startu použijte`WEBSITE_RUN_FROM_PACKAGE`místní možnost Zip ( =1).
- Spustit balíček není kompatibilní s`SCM_DO_BUILD_DURING_DEPLOYMENT=true`možností přizpůsobení nasazení ( ), krok sestavení bude během nasazení ignorován.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
