---
title: Import nebo export dat pomocí konfigurace aplikace Azure
description: Přečtěte si, jak importovat nebo exportovat data z konfigurace aplikace Azure nebo z ní.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 2c074cbd99620a482b18cbe2dfcce8f987d78bd5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372103"
---
# <a name="import-or-export-configuration-data"></a>Import nebo export konfiguračních dat

Konfigurace aplikace Azure podporuje operace importu a exportu dat. Tyto operace použijte pro práci s konfiguračními daty v hromadných a výměnných datech mezi úložištěm konfigurace aplikace a projektem kódu. Můžete například nastavit jedno úložiště konfigurace aplikace pro testování a další pro produkční prostředí. Můžete zkopírovat nastavení aplikace mezi nimi, abyste nemuseli zadávat data dvakrát.

Tento článek poskytuje návod pro import a export dat pomocí konfigurace aplikace.

## <a name="import-data"></a>Import dat

Import přináší konfigurační data do úložiště konfigurace aplikace z existujícího zdroje. Pomocí funkce Import můžete migrovat data do úložiště konfigurace aplikace nebo agregovat data z více zdrojů. Konfigurace aplikace podporuje import ze souboru JSON, YAML nebo Properties.

Importujte data buď pomocí [Azure Portal](https://portal.azure.com) , nebo pomocí [Azure CLI](./scripts/cli-import.md). V Azure Portal postupujte podle následujících kroků:

1. Přejděte do úložiště konfigurace aplikace a v nabídce **operace** vyberte **Import/export** .

1. Na kartě **Import** vyberte **zdrojové služby** > **konfigurační soubor**.

1. Vyberte možnost **jazyk** a vyberte požadovaný typ vstupu.

1. Vyberte ikonu **složky** a vyhledejte soubor, který chcete naimportovat.

    ![Importovat soubor](./media/import-file.png)

1. Vyberte **oddělovač**a volitelně zadejte **předponu** , která se má použít pro importované názvy klíčů.

1. Volitelně můžete vybrat **popisek**.

1. Pro dokončení importu vyberte **použít** .

    ![Import souboru byl dokončen.](./media/import-file-complete.png)

## <a name="export-data"></a>Export dat

Export vypíše konfigurační data uložená v konfiguraci aplikace do jiného cílového umístění. Použijte funkci exportu, například k uložení dat v úložišti konfigurace aplikace do souboru, který je vložený s vaším kódem aplikace během nasazení.

Exportujte data buď pomocí [Azure Portal](https://portal.azure.com) , nebo pomocí [Azure CLI](./scripts/cli-export.md). V Azure Portal postupujte podle následujících kroků:

1. Přejděte do úložiště konfigurace aplikace a vyberte **Import/export**.

1. Na kartě **exportovat** vyberte **cílovou službu** > **konfigurační soubor**.

1. Volitelně můžete zadat **předponu** a vybrat **popisek** a časový okamžik pro export klíčů.

1. Vyberte **typ souboru** > **oddělovač**.

1. Pro dokončení exportu vyberte **použít** .

    ![Export souboru byl dokončen.](./media/export-file-complete.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace v ASP.NET Core](./quickstart-aspnet-core-app.md)  
