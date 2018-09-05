---
title: Spouštění Azure Functions z balíčku | Dokumentace Microsoftu
description: Máte modul runtime Azure Functions spouštět funkce tak, že připojí soubor balíčku nasazení, který obsahuje soubory projektu aplikace funkce.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: conceptual
ms.workload: na
ms.date: 08/22/2018
ms.author: glenga
ms.openlocfilehash: 2b9cc3618bf21eac268e3c25f08b80124d52e6af
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669287"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Spouštění Azure Functions ze souboru balíčku

> [!NOTE]
> Funkce popsané v tomto článku je aktuálně ve verzi preview; není k dispozici pro službu Functions v Linuxu.

V Azure můžete spustit přímo ze souboru balíčku nasazení vašich funkcí ve své aplikaci function app. Další možností je k nasazení souborů v projektu funkce `d:\home\site\wwwroot` adresáře aplikace function App.

Tento článek popisuje výhody provozu vaší funkce z balíčku. Také ukazuje, jak tuto funkci povolit ve své aplikaci function app.

## <a name="benefits-of-running-from-a-package-file"></a>Výhody spuštění ze souboru balíčku
  
Existuje více výhod ke spuštění ze souboru balíčku:

+ Snižuje riziko problémy se zamykáním kopírování souborů.
+ Je možné nasadit do produkčního prostředí aplikace (s restartem).
+ Může být některé soubory, které běží ve vaší aplikaci.
+ Zlepšuje výkon [nasazení Azure Resource Manageru](functions-infrastructure-as-code.md).
+ Mohou snížit počáteční čas funkce jazyka JavaScript.

Další informace najdete v tématu [toto oznámení](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Povolení funkce ke spuštění z balíčku

Chcete-li aplikace function app na spuštění z balíčku, stačí přidat `WEBSITE_RUN_FROM_ZIP` nastavení na vaše nastavení aplikace function app. `WEBSITE_RUN_FROM_ZIP` Nastavení může mít jednu z následujících hodnot:

| Hodnota  | Popis  |
|---------|---------|
|**`<url>`**  | Umístění souboru konkrétního balíčku, kterou chcete spustit. Při použití úložiště objektů Blob, byste měli používat privátní kontejneru s [sdíleného přístupového podpisu (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) povolit modul runtime Functions pro přístup k balíčku. Můžete použít [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) k nahrání balíčku souborů do účtu Blob storage.         |
| **`1`**  | Spustit ze souboru balíčku v `d:\home\data\SitePackages` složky vaší aplikace function App. Tato možnost vyžaduje složku, kterou chcete mít také soubor s názvem `packagename.txt`. Tento soubor obsahuje pouze název souboru balíčku ve složce, bez jakékoli prázdné znaky. |

Následující obrázek znázorňuje aplikaci function app nakonfigurovaný ke spouštění ze souboru ZIP hostované ve službě Azure Blob storage:

![Nastavení aplikace, které WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> V současné době jsou podporovány pouze soubory balíčku .zip.

## <a name="integration-with-zip-deployment"></a>Integrace s nasazení zip

[Zazipovat nasazení] [ Zip deployment for Azure Functions] je funkce služby Azure App Service, který vám umožní nasadit váš projekt aplikace funkcí na `wwwroot` adresáře. Projekt je zabalena jako soubor ZIP nasazení. Stejná rozhraní API je možné nasadit balíček sady `d:\home\data\SitePackages` složky. S `WEBSITE_RUN_FROM_ZIP` hodnotu nastavení aplikace `1`, nasazení zip rozhraní API, zkopírujte balíček sady `d:\home\data\SitePackages` složky namísto extrahování souborů do `d:\home\site\wwwroot`. Také vytvoří `packagename.txt` souboru. Aplikace function app je spusťte z balíčku po restartování počítače a `wwwroot` jen pro čtení. Další informace o nasazení zip najdete v tématu [Zip nasazení pro službu Azure Functions](deployment-zip-push.md).

## <a name="adding-the-websiterunfromzip-setting"></a>Přidání nastavení WEBSITE_RUN_FROM_ZIP

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
