---
title: Spravujte jazyková rozšíření v clusteru Azure Data Explorer.
description: Pomocí rozšíření jazyka můžete integrovat další jazyky v rámci dotazů KQL aplikace Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 88c3047afebf3d31b50145c6df47776fdc1ddcd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522469"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Správa jazykových rozšíření v clusteru Azure Data Explorer (preview)

Funkce rozšíření jazyka umožňuje používat pluginy rozšíření jazyka k integraci dalších jazyků do dotazů KQL průzkumníka dat Azure. Když spustíte uživatelem definovanou funkci (UDF) pomocí příslušného skriptu, skript získá tabulková data jako svůj vstup a očekává se, že vytvoří tabulkový výstup. Modul runtime pluginu je hostován v [izolovaném](/azure/kusto/concepts/sandboxes)a zabezpečeném prostředí, které běží na uzlech clusteru. V tomto článku spravujete plugin pro rozšíření jazyka v clusteru Azure Data Explorer v rámci portálu Azure.

> [!NOTE]
> Rozšíření jazyka Azure Data Explorer, které jsou aktuálně podporované, jsou Python a R.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvořte cluster a databázi Průzkumníka dat Azure](create-cluster-database-portal.md).

## <a name="enable-language-extensions-on-your-cluster"></a>Povolení jazykových rozšíření v clusteru

> [!WARNING]
> Před povolením rozšíření jazyka si zkontrolujte [omezení.](#limitations)

Chcete-li v clusteru povolit rozšíření o jazyk, postupujte takto:

1. Na webu Azure Portal přejděte do clusteru Azure Data Explorer. 
1. V **části Nastavení**vyberte Možnost **Konfigurace**. 
1. V podokně **Konfigurace** vyberte **Zapnuto,** chcete-li povolit rozšíření jazyka.
1. Vyberte **Uložit**.
 
    ![rozšíření jazyka na](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> Povolení procesu rozšíření jazyka může trvat několik minut. Během této doby bude cluster pozastaven.
 
## <a name="run-language-extension-integrated-queries"></a>Spuštění dotazů integrovaných s rozšířením jazyka

* Přečtěte si, jak [spustit integrované kql dotazy v Pythonu](/azure/kusto/query/pythonplugin).
* Přečtěte si, jak [spustit r integrované kql dotazy](/azure/kusto/query/rplugin). 

## <a name="disable-language-extensions-on-your-cluster"></a>Zakázání rozšíření jazyka v clusteru

> [!NOTE]
> Zakázání rozšíření jazyka může trvat několik minut.

Chcete-li v clusteru zakázat rozšíření jazyka, postupujte takto:

1. Na webu Azure Portal přejděte do clusteru Azure Data Explorer. 
1. V **části Nastavení**vyberte Možnost **Konfigurace**. 
1. V podokně **Konfigurace** vyberte **Vypnout,** chcete-li rozšíření jazyka zakázat.
1. Vyberte **Uložit**.

    ![Rozšíření jazyka vypnuto](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>Omezení

* Funkce rozšíření jazyka nepodporuje [šifrování disku](manage-cluster-security.md). 
* Jazyk rozšíření runtime izolovaného prostoru přiděluje místo na disku i v případě, že žádný dotaz běží v oboru příslušného jazyka.

