---
title: Import nebo export dat pomocí konfigurace aplikace Azure | Microsoft Docs
description: Přečtěte si, jak importovat nebo exportovat data z konfigurace aplikace Azure nebo z ní.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9780dc34aa6b146fe62b11586cbab46825e60535
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185157"
---
# <a name="import-or-export-configuration-data"></a>Import nebo export konfiguračních dat

Konfigurace aplikace Azure podporuje operace importu a exportu dat. Tyto operace použijte pro práci s konfiguračními daty v hromadných a výměnných datech mezi úložištěm konfigurace aplikace a projektem kódu. Můžete například nastavit jedno úložiště konfigurace aplikace pro testování a další pro produkční prostředí. Pak můžete zkopírovat nastavení aplikace mezi nimi prostřednictvím souboru, abyste nemuseli zadávat data dvakrát.

Tento článek poskytuje návod pro import a export dat pomocí konfigurace aplikace.

## <a name="import-data"></a>Import dat

Import přináší konfigurační data do úložiště konfigurace aplikace z existujícího zdroje namísto ručního zadání. Pomocí funkce Import můžete migrovat data do úložiště konfigurace aplikace nebo agregovat data z více zdrojů. Konfigurace aplikace podporuje import ze souboru JSON, YAML nebo Properties.

Importujte data buď pomocí [Azure Portal](https://portal.azure.com) , nebo pomocí [Azure CLI](./scripts/cli-import.md). V Azure Portal postupujte podle následujících kroků:

1. Přejděte do úložiště konfigurace aplikace a vyberte **Import/export**.

2. Na kartě **Import** vyberte **zdrojové služby** > **konfigurační soubor**.

3. Vyberte **pro jazyk** > **typ souboru**.

4. Vyberte ikonu **složky** a vyhledejte soubor, který chcete naimportovat.

    ![Importovat soubor](./media/import-file.png)

5. Vyberte **oddělovač**a volitelně zadejte **předponu** , která se má použít pro importované názvy klíčů.

6. Volitelně můžete vybrat **popisek**.

7. Pro dokončení importu vyberte **použít** .

    ![Import souboru byl dokončen.](./media/import-file-complete.png)

## <a name="export-data"></a>Exportovat data

Export vypíše konfigurační data uložená v konfiguraci aplikace do jiného cílového umístění. Použijte funkci exportu, například k uložení dat v úložišti konfigurace aplikace do souboru, který je vložený s vaším kódem aplikace během nasazení.

Exportujte data buď pomocí [Azure Portal](https://portal.azure.com) , nebo pomocí [Azure CLI](./scripts/cli-export.md). V Azure Portal postupujte podle následujících kroků:

1. Přejděte do úložiště konfigurace aplikace a vyberte **Import/export**.

2. Na kartě **exportovat** vyberte **cílovou službu** > **konfigurační soubor**.

3. Volitelně můžete zadat **předponu** a vybrat **popisek** a časový okamžik pro export klíčů.

4. Vyberte **typ souboru** > **oddělovač**.

5. Pro dokončení exportu vyberte **použít** .

    ![Export souboru byl dokončen.](./media/export-file-complete.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace v ASP.NET Core](./quickstart-aspnet-core-app.md)  
