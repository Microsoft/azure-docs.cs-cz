---
title: Nasazení modelu ML na Edge Azure SQL pomocí ONNX
description: V třetí části tohoto kurzu Azure SQL Edge pro předpověď nečistot železa spustíte modely strojového učení ONNX pro SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422191"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Nasazení modelu ML na Edge Azure SQL pomocí ONNX 

V třetí části tohoto kurzu pro předpověď nečistot železa ve službě Azure SQL Edge:

1. Pomocí Azure Data Studio se připojte k SQL Database v instanci Azure SQL Edge.
2. Předpovídat nečistoty železa pomocí ONNX ve službě Azure SQL Edge.

## <a name="key-components"></a>Klíčové součásti

1. Řešení používá výchozí 500 milisekund mezi každou zprávou odeslanou do centra Edge. To se dá změnit v souboru **program. cs.** 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. Řešení vygenerovalo zprávu s následujícími atributy. Přidání nebo odebrání atributů podle požadavků. 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>Připojte se k SQL Database v instanci Azure SQL Edge, abyste mohli naučit, nasazovat a testovat model ML.

1. Otevřete Azure Data Studio.

2. Na kartě **Vítejte** spusťte nové připojení s následujícími podrobnostmi:

   |_Pole_|_Hodnota_|
   |-------|-------|
   |Typ připojení| Microsoft SQL Server|
   |Server|Veřejná IP adresa zmíněná na virtuálním počítači, který se vytvořil pro tuto ukázku|
   |Uživatelské jméno|sa|
   |Heslo|Silné heslo, které se použilo při vytváření instance Azure SQL Edge|
   |databáze|Výchozí|
   |Skupina serverů|Výchozí|
   |Název (nepovinný)|Zadání volitelného názvu|

3. Klikněte na **připojit** .

4. V části **soubor** otevřete **/deploymentscripts/MiningProcess_ONNX. jpynb** ze složky, do které jste naklonovat soubory projektu na vašem počítači.

5. Nastavte jádro na Python 3.


## <a name="next-steps"></a>Další kroky

Další informace o používání modelů ONNX ve službě Azure SQL Edge najdete v tématu [Machine Learning a AI s ONNX v SQL Edge](onnx-overview.md).