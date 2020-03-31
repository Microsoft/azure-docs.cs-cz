---
title: Import nebo export dat pomocí konfigurace aplikací Azure
description: Přečtěte si, jak importovat nebo exportovat data do nebo z Azure App Configuration
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5b3d6d0561d7d6d6b23cb4f579b0988850da9771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056869"
---
# <a name="import-or-export-configuration-data"></a>Import nebo export konfiguračních dat

Azure App Configuration podporuje operace importu a exportu dat. Tyto operace slouží k hromadné práci s konfiguračními daty a k výměně dat mezi úložištěm konfigurace aplikace a projektem kódu. Můžete například nastavit jeden obchod konfigurace aplikace pro testování a jiný pro produkční prostředí. Nastavení aplikace mezi nimi můžete kopírovat, abyste nemuseli zadávat data dvakrát.

Tento článek obsahuje průvodce pro import a export dat pomocí konfigurace aplikace. Pokud chcete nastavit průběžnou synchronizaci s úložištěm GitHub, podívejte se na naši [akci GitHub](https://aka.ms/azconfig-gha1).

## <a name="import-data"></a>Import dat

Import přináší konfigurační data do úložiště konfigurace aplikací z existujícího zdroje. Pomocí funkce importu můžete migrovat data do úložiště konfigurace aplikací nebo agregovat data z více zdrojů. Konfigurace aplikace podporuje import ze souboru JSON, YAML nebo vlastností.

Importujte data pomocí [portálu Azure nebo](https://portal.azure.com) pomocí [příkazového příkazového příkazu k Azure](./scripts/cli-import.md). Na webu Azure Portal postupujte takto:

1. Přejděte do obchodu Konfigurace aplikací a z nabídky **Operace** vyberte **Importovat nebo exportovat.**

1. Na kartě **Import** vyberte Položku**Konfigurační soubor zdrojové** **služby** > .

1. Vyberte **Pro jazyk** a vyberte požadovaný typ vstupu.

1. Vyberte ikonu **Složka** a přejděte k souboru, který chcete importovat.

    ![Importovat soubor](./media/import-file.png)

1. Vyberte **oddělovač**a volitelně zadejte **předponu,** která se má použít pro importované názvy klíčů.

1. Volitelně vyberte **popisek**.

1. Chcete-li import dokončit, vyberte **použít.**

    ![Importovat soubor dokončen](./media/import-file-complete.png)

## <a name="export-data"></a>Exportovat data

Exportovat konfigurační data zápisů uložená v konfiguraci aplikace do jiného cíle. Pomocí funkce exportu můžete například ukládat data v úložišti konfigurace aplikací do souboru, který je vložený s kódem aplikace během nasazení.

Exportujte data pomocí [portálu Azure nebo](https://portal.azure.com) pomocí [příkazového příkazového příkazu k Webu řízení o azure](./scripts/cli-export.md). Na webu Azure Portal postupujte takto:

1. Přejděte do obchodu Konfigurace aplikací a vyberte **Importovat nebo exportovat**.

1. Na kartě **Export** vyberte možnost Cílový**konfigurační soubor** **služby** > .

1. Volitelně zadejte **předponu** a vyberte **popisek** a bod v čase pro klíče, které mají být exportovány.

1. Vyberte > **oddělovač** **typu souboru**.

1. Chcete-li export dokončit, vyberte **použít.**

    ![Exportovat soubor byl dokončen.](./media/export-file-complete.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace ASP.NET Core](./quickstart-aspnet-core-app.md)  
