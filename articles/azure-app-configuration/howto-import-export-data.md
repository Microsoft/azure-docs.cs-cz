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
ms.openlocfilehash: 33ba01440f599e2f019db46c304b0658632d9342
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885281"
---
# <a name="import-or-export-configuration-data"></a>Import a export konfiguračních dat

Azure data konfigurace aplikací podporuje import a export operace. Ty umožňují pracovat s konfiguračními daty hromadné také jako výměna dat mezi konfiguraci aplikací úložiště a projekt kódu. Například můžete nastavit jednu konfiguraci obchodu s aplikacemi pro testování a druhý pro produkční a zkopírujte nastavení aplikace mezi nimi prostřednictvím souboru tak, aby nebude muset zadávat data dvakrát.

Tento článek poskytuje návod pro import a export dat pomocí konfigurace aplikace.

## <a name="import-data"></a>Import dat

Import přenese konfigurace úložiště dat do konfigurace aplikace z existujícího zdroje, nemusí je ručně zadávat. Funkce importu můžete použít pro migraci dat do úložiště konfigurace aplikace nebo agregovaná data z různých zdrojů. Konfigurace aplikace podporovat, import ze souboru JSON, YAML nebo vlastnosti.

Můžete importovat data pomocí [webu Azure portal](https://aka.ms/azconfig/portal) nebo [rozhraní příkazového řádku Azure](./scripts/cli-import.md). Na webu Azure Portal postupujte podle těchto kroků:

1. Přejděte do úložiště konfigurace vaší aplikace a klikněte na tlačítko **Import/Export**.

2. V **Import** kartě **zdroje služby** a **konfigurační soubor**.

3. Zvolte **pro jazyk** a **typ souboru**.

4. Klikněte na **složky** ikonu a přejděte na soubor k importu.

    ![Import souboru](./media/import-file.png)

5. Zvolte **oddělovač** a volitelně zadejte **předpony** pro importované názvy klíčů.

6. Volitelně můžete **popisek**.

7. Klikněte na tlačítko **použít** k dokončení importu.

    ![Import souboru bylo dokončeno](./media/import-file-complete.png)

## <a name="export-data"></a>Exportovat data

Export zapíše konfigurační data uložená v konfiguraci aplikace na jiné místo. Funkce exportu můžete například použít k uložení dat v app storu konfigurační soubor, který bude vložen kódem aplikace během nasazení.

Můžete exportovat data pomocí [webu Azure portal](https://aka.ms/azconfig/portal) nebo [rozhraní příkazového řádku Azure](./scripts/cli-export.md). Na webu Azure Portal postupujte podle těchto kroků:

1. Přejděte do úložiště konfigurace vaší aplikace a klikněte na tlačítko **Import/Export**.

2. V **exportovat** kartě **Cílová služba** a **konfigurační soubor**.

3. Volitelně můžete zadat **předpony** a zvolte **popisek** a v daném okamžiku pro klíče nelze exportovat.

4. Zvolte **typ souboru** a **oddělovač**.

5. Klikněte na tlačítko **použít** dokončit export.

    ![Export souboru bylo dokončeno](./media/export-file-complete.png)

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření webové aplikace ASP.NET](quickstart-aspnet-core-app.md)  
