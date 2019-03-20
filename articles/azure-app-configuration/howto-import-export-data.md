---
title: Import a export dat s konfigurací aplikace Azure | Dokumentace Microsoftu
description: Zjistěte, jak importovat nebo exportovat data do nebo z konfigurace aplikace pro Azure
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
ms.openlocfilehash: 846472e00bc048de906ee8e14f6de38e366f3571
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225225"
---
# <a name="import-or-export-configuration-data"></a>Import nebo export konfiguračních dat

Azure data konfigurace aplikací podporuje import a export operace. Pomocí těchto operací pro práci s konfiguračními daty v datech hromadně a serveru exchange mezi úložiště konfigurace aplikace a kód projektu. Můžete například nastavit konfiguraci jeden obchod s aplikacemi pro testování a druhý k produkci. Potom můžete kopírovat nastavení aplikace mezi nimi prostřednictvím souboru tak, aby je nemuseli zadávat data dvakrát.

Tento článek poskytuje návod pro import a export dat pomocí konfigurace aplikace.

## <a name="import-data"></a>Import dat

Import přenese konfigurace úložiště dat do konfigurace aplikace z existujícího zdroje, nemusí ručně zadávat. Můžete migrovat data do obchodu s aplikacemi konfigurace nebo agregovaná data z různých zdrojů pomocí funkce importu. Konfigurace aplikací podporuje import ze souboru JSON, YAML nebo vlastnosti.

Import dat pomocí [webu Azure portal](https://aka.ms/azconfig/portal) nebo [rozhraní příkazového řádku Azure](./scripts/cli-import.md). Na webu Azure Portal postupujte podle těchto kroků:

1. Přejděte do úložiště konfigurace vaší aplikace a vyberte **Import/Export**.

2. Na **Import** kartu, vyberte možnost **zdroje služby** > **konfigurační soubor**.

3. Vyberte **pro jazyk** > **typ souboru**.

4. Vyberte **složky** ikonu a přejděte na soubor k importu.

    ![Import souboru](./media/import-file.png)

5. Vyberte **oddělovač**a volitelně zadejte **předpony** pro importované názvy klíčů.

6. Volitelně můžete vybrat **popisek**.

7. Vyberte **použít** na dokončení importu.

    ![Importovat soubor dokončeno](./media/import-file-complete.png)

## <a name="export-data"></a>Exportovat data

Export zapíše konfigurační data uložená v konfiguraci aplikace na jiné místo. Pomocí funkce exportu, třeba k uložení dat v app storu konfigurační soubor, který je vložený kódem aplikace během nasazení.

Exportovat data jedním [webu Azure portal](https://aka.ms/azconfig/portal) nebo [rozhraní příkazového řádku Azure](./scripts/cli-export.md). Na webu Azure Portal postupujte podle těchto kroků:

1. Přejděte do úložiště konfigurace vaší aplikace a vyberte **Import/Export**.

2. Na **exportovat** kartu, vyberte možnost **Cílová služba** > **konfigurační soubor**.

3. Volitelně můžete zadat **předpony** a vyberte **popisek** a v daném okamžiku pro klíče nelze exportovat.

4. Vyberte **typ souboru** > **oddělovač**.

5. Vyberte **použít** na dokončení exportu.

    ![Exportujte soubor dokončeno](./media/export-file-complete.png)

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření webové aplikace ASP.NET](./quickstart-aspnet-core-app.md)  
