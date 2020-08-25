---
title: Nastavení modulů IoT Edge v Azure SQL Edge
description: V třetí části tohoto kurzu Azure SQL Edge pro předpověď nečistot železa nastavíte IoT Edge moduly a připojení.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a4087ef56712e098443009bd0457029394ea7b51
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "84235022"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Nastavení IoT Edgech modulů a připojení

V druhé části tohoto kurzu pro předpověď nečistot železa ve službě Azure SQL Edge nastavíte následující IoT Edge moduly:

- Azure SQL Edge
- Modul IoT Edge generátoru dat

## <a name="create-azure-stream-analytics-module"></a>Vytvořit modul Azure Stream Analytics

Vytvořte modul Azure Stream Analytics, který se použije v tomto kurzu. Další informace o používání úloh streamování pomocí SQL Edge najdete v tématu [používání úloh streamování s SQL Edge](stream-analytics.md).

Po vytvoření úlohy Azure Stream Analytics s hostitelským prostředím nastaveným jako Edge nastavte vstupy a výstupy pro tento kurz.

1. Pokud chcete vytvořit **vstup**, klikněte na **+ Přidat vstup streamu**. Vyplňte část Podrobnosti pomocí následujících informací:

   Pole|Hodnota
   -----|-----
   Formát serializace události|JSON
   Encoding|UTF-8
   Typ komprese události|Žádné

2. **Výstup**vytvoříte tak, že kliknete na **+ přidat** a zvolíte SQL Database. Vyplňte část Podrobnosti pomocí následujících informací.

   > [!NOTE]
   > Heslo zadané v této části je potřeba zadat pro heslo SQL SA při nasazení modulu SQL Edge v části **nasazení modulu Azure SQL Edge**.

   Pole|Hodnota
   -----|-----
   Databáze|IronOreSilicaPrediction
   Název serveru|TCP:., 1433
   Uživatelské jméno|sa
   Heslo|Zadat silné heslo
   Tabulka|IronOreMeasurements1

3. Přejděte do části **dotaz** a nastavte dotaz následujícím způsobem:

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. V části **Konfigurovat**vyberte **publikovat**a pak vyberte tlačítko **publikovat** . Uložte identifikátor URI SAS pro použití s modulem SQL Database Edge.

## <a name="specify-container-registry-credentials"></a>Zadat přihlašovací údaje registru kontejneru

Je potřeba zadat přihlašovací údaje k imagím modulu container Registry Host. Ty najdete v registru kontejnerů, které se vytvořily ve vaší skupině prostředků. Přejděte do části **přístupové klíče** . Poznamenejte si následující pole:

- Název registru
- Přihlašovací server
- Uživatelské jméno
- Heslo

Nyní zadejte pověření kontejneru v modulu IoT Edge.

1. Přejděte do služby IoT Hub, která byla vytvořena ve vaší skupině prostředků.

2. V části **IoT Edge** v části **Automatická správa zařízení**klikněte na **ID zařízení**. V tomto kurzu je ID `IronOrePredictionDevice` .

3. Vyberte část **set modules** .

4. V části **Container Registry přihlašovací údaje**zadejte následující hodnoty:

   _Pole_|_Hodnota_
   -------|-------
   Name|Název registru
   Adresa|Přihlašovací server
   Uživatelské jméno|Uživatelské jméno
   Heslo|Heslo
  
## <a name="deploy-the-data-generator-module"></a>Nasazení modulu generátoru dat

1. V části **IoT Edge moduly** klikněte na **+ přidat** a vyberte **IoT Edge modul**.

2. Zadejte název modulu IoT Edge a identifikátor URI image.
   Identifikátor URI image najdete v registru kontejnerů ve skupině prostředků. V části **služby**vyberte část **úložiště** . Pro tento kurz vyberte úložiště s názvem `silicaprediction` . Vyberte příslušnou značku. Identifikátor URI image bude ve formátu:

   *přihlašovací server containerregistry* / *název úložiště*:*název značky*

   Příklad:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. Klikněte na tlačítko **Přidat**.

## <a name="deploy-the-azure-sql-edge-module"></a>Nasazení modulu Edge Azure SQL

1. Nasaďte modul Azure SQL Edge podle kroků uvedených v části [nasazení Azure SQL Edge (Preview)](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal).

2. Na stránce **Zadejte cestu** k **nastaveným modulům** zadejte trasy pro modul, aby IoT Edge komunikaci centra, jak je znázorněno níže. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   Příklad:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. V modulu nastavení s **dvojitou** dostupností se ujistěte, že jsou SQLPackage a ASAJonInfo aktualizované o příslušné adresy URL SAS, které jste předtím uložili v tomto kurzu.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>Další kroky

- [Nasazení modelu ML na Edge Azure SQL pomocí ONNX](tutorial-run-ml-model-on-sql-edge.md)
