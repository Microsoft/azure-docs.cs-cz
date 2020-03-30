---
title: Úvodní příručka – vytvoření úlohy Stream Analytics pomocí portálu Azure
description: V tomto rychlém startu se dozvíte, jak začít vytvořením úlohy Stream Analytics, konfigurací vstupů a výstupů a definicí dotazu.
author: mamccrea
ms.author: mamccrea
ms.date: 06/21/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 4abf5078a005f9d928397d9666e7f2bc55d65f19
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75431551"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-portal"></a>Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal

V tomto rychlém startu se naučíte, jak začít vytvořením úlohy Stream Analytics. V tomto rychlém startu definujete úlohu Stream Analytics, která čte streamovaná data v reálném čase a filtruje zprávy s teplotou vyšší než 27. Úloha Stream Analytics bude číst data z IoT Hubu, transformovat data a zapsat data zpět do kontejneru v úložišti objektů blob. Vstupní data použitá v tomto rychlém startu jsou generována online simulátorem Raspberry Pi. 

## <a name="before-you-begin"></a>Než začnete

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Před definováním úlohy Stream Analytics byste měli připravit vstupní data. Data senzorů v reálném čase se ingestují do ioT hubu, který se později nakonfiguroval jako vstup úlohy. Chcete-li připravit vstupní data vyžadovaná úlohou, proveďte následující kroky:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.

3. V podokně **IoT Hub** zadejte následující informace:
   
   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Předplatné  | \<Vaše předplatné\> |  Vyberte předplatné Azure, které chcete použít. |
   |Skupina prostředků   |   asaquickstart-resourcegroup  |   Vyberte **Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. |
   |Region (Oblast)  |  \<Vyberte oblast nejbližší vašim uživatelům.\> | Vyberte geografickou polohu, kde můžete hostovat svůj IoT Hub. Použijte umístění, které je vašim uživatelům nejblíže. |
   |Název centra IoT  | MyASAIoTHub  |   Vyberte název vašeho IoT Hubu.   |

   ![Vytvořit IoT Hub](./media/stream-analytics-quick-create-portal/create-iot-hub.png)

4. Vyberte **další: Nastavit velikost a měřítko**.

5. Zvolte si **Úroveň ceny a škálování**. Pro tento rychlý start vyberte úroveň **F1 – Free,** pokud je stále dostupná ve vašem předplatném. Další informace najdete v tématu [Ceny ioT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Velikost a škálování ioT hubu](./media/stream-analytics-quick-create-portal/iot-hub-size-and-scale.png)

6. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte informace o centru IoT Hub a klepněte na **tlačítko Vytvořit**. Vytvoření ioT hubu může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení**.

7. V navigační nabídce služby IoT Hub klikněte v části **Zařízení IoT**na **Přidat** . Přidejte **ID zařízení** a klepněte na tlačítko **Uložit**.

   ![Přidání zařízení do centra IoT Hub](./media/stream-analytics-quick-create-portal/add-device-iot-hub.png)

8. Po vytvoření zařízení otevřete zařízení ze seznamu **zařízení IoT.** Zkopírujte **připojovací řetězec – primární klíč** a uložte jej do poznámkového bloku, který chcete použít později.

   ![Kopírování připojovacího řetězce zařízení centra IoT Hub](./media/stream-analytics-quick-create-portal/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Vytvoření úložiště objektů blob

1. V levém horním rohu portálu Azure vyberte **Vytvořit** > **účet úložiště****prostředků** > .

2. V podokně **Vytvořit účet úložiště** zadejte název účtu úložiště, umístění a skupinu prostředků. Zvolte stejné umístění a skupinu prostředků jako službu IoT Hub, kterou jste vytvořili. Pak klikněte na **Zkontrolovat + vytvořit** a vytvořte účet.

   ![Vytvoření účtu úložiště](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Po vytvoření účtu úložiště vyberte dlaždici **Objektů blob** v podokně **Přehled.**

   ![Přehled účtu úložiště](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Na stránce **Služba objektu Blob** vyberte **Kontejner** a zadejte název kontejneru, například *kontejner1*. Ponechte **úroveň veřejného přístupu** jako **soukromou (bez anonymního přístupu)** a vyberte **OK**.

   ![Vytvoření kontejneru objektů blob](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. Přihlaste se k portálu Azure.

2. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.  

3. V seznamu výsledků vyberte Úloha **Analytics** > **Stream Analytics.**  

4. Na stránce Úloha Stream Analytics zadejte následující informace:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Název úlohy   |  MyASAJob   |   Zadejte název pro identifikaci úlohy Stream Analytics. Název úlohy Stream Analytics může obsahovat jen alfanumerické znaky, spojovníky a podtržítka a musí být dlouhý 3 až 63 znaků. |
   |Předplatné  | \<Vaše předplatné\> |  Vyberte předplatné Azure, které chcete použít pro vaši úlohu. |
   |Skupina prostředků   |   asaquickstart-resourcegroup  |   Vyberte stejnou skupinu prostředků jako váš IoT Hub. |
   |Umístění  |  \<Vyberte oblast nejbližší vašim uživatelům.\> | Vyberte zeměpisnou polohu, kde je možné hostovat úlohu Stream Analytics. V zájmu vyššího výkonu a nižších nákladů za přenos dat zvolte umístění co nejbližší vašim uživatelům. |
   |Jednotky streamování  | 1  |   Jednotky streamování představují výpočetní prostředky nutné k provedení úlohy. Ve výchozím nastavení je tato hodnota nastavená na 1. Podrobnosti o škálování jednotek streamování najdete v článku věnovaném [principům a úpravám jednotek streamování](stream-analytics-streaming-unit-consumption.md).   |
   |Hostitelské prostředí  |  Cloud  |   Úlohy Stream Analytics můžete nasadit do cloudu nebo do hraničního zařízení. Cloud umožňuje nasazení do Azure Cloudu a Edge umožňuje nasazení na zařízení IoT Edge. |

   ![Vytvoření úlohy](./media/stream-analytics-quick-create-portal/create-asa-job.png)

5. Umístěte úlohu do řídicího panelu zaškrtnutím políčka **Připnutí na řídicí panel** a klikněte na **Vytvořit**.  

6. Měli byste vidět *nasazení v průběhu ...* oznámení zobrazené v pravém horním rohu okna prohlížeče. 

## <a name="configure-job-input"></a>Konfigurace vstupu úlohy

V této části nakonfigurujete vstup zařízení služby IoT Hub pro úlohu Stream Analytics. Použijte ioT hub, který jste vytvořili v předchozí části rychlého startu.

1. Přejděte na úlohu Stream Analytics.  

2. Vyberte **Vstupy** > **Přidat datový proud vstup** > **IoT Hub**.  

3. Vyplňte stránku **Centra IoT s** následujícími hodnotami:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias vstupu  |  IoTHubInput   |  Zadejte název pro identifikaci vstupu úlohy.   |
   |Předplatné   |  \<Vaše předplatné\> |  Zadejte předplatné Azure vytvořeného účtu úložiště. Účet úložiště můžete využívat v rámci stejného, ale i jiného předplatného. V tomto příkladu se předpokládá, že jste účet vytvořili v rámci stejného předplatného. |
   |IoT Hub  |  MyASAIoTHub |  Zadejte název centra IoT Hub, který jste vytvořili v předchozí části. |

4. U ostatních možností ponechejte výchozí hodnoty a výběrem možnosti **Uložit** uložte nastavení.  

   ![Konfigurace vstupních dat](./media/stream-analytics-quick-create-portal/configure-asa-input.png)
 
## <a name="configure-job-output"></a>Konfigurace výstupu úlohy

1. Přejděte na vytvořenou úlohu Stream Analytics.  

2. Vyberte **Výstupy** > **Přidat** > **úložiště objektů blob**.  

3. Na stránce **Úložiště objektů blob** vyplňte tyto hodnoty:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias pro výstup |   BlobOutput   |   Zadejte název pro identifikaci výstupu úlohy. |
   |Předplatné  |  \<Vaše předplatné\>  |  Zadejte předplatné Azure vytvořeného účtu úložiště. Účet úložiště můžete využívat v rámci stejného, ale i jiného předplatného. V tomto příkladu se předpokládá, že jste účet vytvořili v rámci stejného předplatného. |
   |Účet úložiště |  asaquickstartstorage |   Vyberte nebo zadejte název účtu úložiště. Pokud jsou názvy vytvořeny v rámci stejného předplatného, zjišťují se automaticky.       |
   |Kontejner |   container1  |  Vyberte existující kontejner, který jste vytvořili v účtu úložiště.   |

4. U ostatních možností ponechejte výchozí hodnoty a výběrem možnosti **Uložit** uložte nastavení.  

   ![Konfigurace výstupu](./media/stream-analytics-quick-create-portal/configure-asa-output.png)
 
## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

1. Přejděte na vytvořenou úlohu Stream Analytics.  

2. Vyberte možnost **Dotaz** a aktualizujte dotaz tímto způsobem:  

   ```sql
   SELECT *
   INTO BlobOutput
   FROM IoTHubInput
   HAVING Temperature > 27
   ```

3. V tomto příkladu dotaz přečte data z ioT hubu a zkopíruje je do nového souboru v objektu blob. Vyberte **Uložit**.  

   ![Konfigurace transformace úlohy](./media/stream-analytics-quick-create-portal/add-asa-query.png)

## <a name="run-the-iot-simulator"></a>Spuštění simulátoru IoT

1. Otevřete [Online simulátor Raspberry Pi Azure IoT](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Nahraďte zástupný symbol na lince 15 připojovacím řetězcem zařízení Azure IoT Hub, který jste uložili v předchozí části.

3. Klepněte na tlačítko **Spustit**. Výstup by měl zobrazovat data senzorů a zprávy, které jsou odesílány do vašeho ioT hubu.

   ![Online simulátor Raspberry Pi Azure IoT](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

1. Vraťte se na stránku přehledu úlohy a vyberte **Spustit**.

2. V části **Zahájit úlohu**vyberte **nyní**pro pole **Čas zahájení výstupu úlohy.** Potom vyberte **Spustit** a začněte svou úlohu.

3. Po několika minutách najděte na portálu účet úložiště a kontejner, které jste nakonfigurovali jako výstup úlohy. Soubor výstupu teď uvidíte v kontejneru. První spuštění úlohy trvá několik minut. Potom bude úloha dál běžet s tím, jak budou data přicházet.  

   ![Transformovaný výstup](./media/stream-analytics-quick-create-portal/check-asa-results.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete, odstraňte skupinu prostředků, úlohu Stream Analytics a všechny související prostředky. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal vyberte **Skupiny prostředků** a potom zvolte název vytvořeného prostředku.  

2. Na stránce skupiny prostředků zvolte **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a potom vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí webu Azure Portal nasadili jednoduchou úlohu Stream Analytics. Úlohy Stream Analytics můžete také nasadit pomocí [PowerShellu](stream-analytics-quick-create-powershell.md), [Visual Studia](stream-analytics-quick-create-vs.md)a kódu Sady [Visual Studio](quick-create-vs-code.md).

Pokud se chcete dozvědět o konfiguraci dalších vstupních zdrojů a provádění detekce v reálném čase, pokračujte na další článek:

> [!div class="nextstepaction"]
> [Zjišťování možných podvodů v reálném čase pomocí Stream Analytics](stream-analytics-real-time-fraud-detection.md)
