---
title: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak začít vytvořením úlohy Stream Analytics, konfigurací vstupů a výstupů a definicí dotazu.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 11/21/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 7762a48fd34973872fe4d0b00906a03a18d52867
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311921"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-portal"></a>Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal

V tomto rychlém startu se naučíte, jak začít vytvořením úlohy Stream Analytics. V tomto rychlém startu definujete úlohu Stream Analytics, která načte streamování dat v reálném čase a filtry zprávy s teplotu vyšší než 27. Vaší úlohy Stream Analytics bude číst data ze služby IoT Hub device transformaci dat a zapisovat data zpět do kontejneru v úložišti objektů blob. Vstupní data použitá v tomto rychlém startu je generován simulátor Raspberry Pi online. 

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Než začnete definovat úlohu Stream Analytics, měli byste data, která je novější nakonfigurovaný jako vstup úlohy. Chcete-li připravíte vstupní data vyžadovaná úlohou, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.

3. V **služby IoT Hub** podokně zadejte následující informace:
   
   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Předplatné  | \<Vaše předplatné\> |  Vyberte předplatné Azure, které chcete použít. |
   |Skupina prostředků   |   asaquickstart-resourcegroup  |   Vyberte**Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. |
   |Oblast  |  \<Vyberte oblast nejbližší vašim uživatelům.\> | Vyberte zeměpisné umístění, kde je možné hostovat služby IoT Hub. Použijte umístění co nejblíže vašim uživatelům. |
   |Název IoT Hubu  | MyASAIoTHub  |   Vyberte název centra IoT.   |

   ![Vytvořit IoT Hub](./media/stream-analytics-quick-create-portal/create-iot-hub.png)

4. Vyberte **Další: nastavení velikosti a měřítka**.

5. Zvolte si **Úroveň ceny a škálování**. Pro účely tohoto rychlého startu vyberte **F1 – Free** vrstvy, pokud je stále k dispozici v rámci předplatného. Další informace najdete v tématu [ceny služby IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Velikosti a škálování služby IoT Hub](./media/stream-analytics-quick-create-portal/iot-hub-size-and-scale.png)

6. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte informace o službě IoT Hub a klikněte na tlačítko **vytvořit**. Služby IoT Hub může trvat několik minut pro vytvoření. Průběh můžete sledovat v podokně **Oznámení**.

7. V navigační nabídce služby IoT Hub, klikněte na tlačítko **přidat** pod **zařízení IoT**. Přidat **ID zařízení** a klikněte na tlačítko **Uložit**.

   ![Přidání zařízení do služby IoT Hub](./media/stream-analytics-quick-create-portal/add-device-iot-hub.png)

8. Po vytvoření zařízení ho otevřete z **zařízení IoT** seznamu. Kopírovat **připojovací řetězec – primární klíč** a uložte ho do poznámkového bloku pro pozdější použití.

   ![Zkopírujte připojovací řetězec služby IoT Hub zařízení](./media/stream-analytics-quick-create-portal/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Vytvořit úložiště objektů blob

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Úložiště** > **Účet úložiště**.

2. V **vytvořit účet úložiště** podokně zadejte skupinu název, umístění a prostředek účtu úložiště. Zvolte stejné umístění a skupině prostředků jako centra IoT, které jste vytvořili. Pak klikněte na tlačítko **revize + vytvořit** k vytvoření účtu.

   ![Vytvoření účtu úložiště](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Po vytvoření účtu úložiště, vyberte **objekty BLOB** na dlaždici **přehled** podokně.

   ![Přehled účtu úložiště](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Z **služby Blob Service** stránce **kontejneru** a zadejte název kontejneru, jako třeba *container1*. Nechte **úroveň veřejného přístupu** jako **privátní (bez anonymního přístupu)** a vyberte **OK**.

   ![Vytvoření kontejneru objektů blob](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. Přihlaste se k portálu Azure.

2. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.  

3. V seznamu výsledků vyberte **Data + analýzy** > **Úloha Stream Analytics job**.  

4. Na stránce Úloha Stream Analytics zadejte následující informace:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Název úlohy   |  MyASAJob   |   Zadejte název pro identifikaci úlohy Stream Analytics. Název úlohy Stream Analytics může obsahovat jen alfanumerické znaky, spojovníky a podtržítka a musí být dlouhý 3 až 63 znaků. |
   |Předplatné  | \<Vaše předplatné\> |  Vyberte předplatné Azure, které chcete použít pro vaši úlohu. |
   |Skupina prostředků   |   asaquickstart-resourcegroup  |   Vyberte stejnou skupinu prostředků jako službu IoT Hub. |
   |Umístění  |  \<Vyberte oblast nejbližší vašim uživatelům.\> | Vyberte zeměpisnou polohu, kde je možné hostovat úlohu Stream Analytics. V zájmu vyššího výkonu a nižších nákladů za přenos dat zvolte umístění co nejbližší vašim uživatelům. |
   |Jednotky streamování  | 1  |   Jednotky streamování představují výpočetní prostředky nutné k provedení úlohy. Ve výchozím nastavení je tato hodnota nastavená na 1. Podrobnosti o škálování jednotek streamování najdete v článku věnovaném [principům a úpravám jednotek streamování](stream-analytics-streaming-unit-consumption.md).   |
   |Hostitelské prostředí  |  Cloud  |   Úlohy Stream Analytics můžete nasadit do cloudu nebo do hraničního zařízení. Možnost Cloud umožňuje nasazení do Azure Cloud, možnost Edge do zařízení IoT Edge. |

   ![Vytvoření úlohy](./media/stream-analytics-quick-create-portal/create-asa-job.png)

5. Umístěte úlohu do řídicího panelu zaškrtnutím políčka **Připnutí na řídicí panel** a klikněte na **Vytvořit**.  

6. Měli byste vidět *probíhá nasazení...*  zobrazené v horním pravém rohu okna prohlížeče. 

## <a name="configure-job-input"></a>Konfigurace vstupu úlohy

V této části můžete nakonfigurovat zařízení služby IoT Hub vstup do úlohy Stream Analytics. Použijte IoT Hub, který jste vytvořili v předchozí části tohoto rychlého startu.

1. Přejděte na úlohu Stream Analytics.  

2. Vyberte **vstupy** > **přidat vstup Stream** > **služby IoT Hub**.  

3. Vyplňte **služby IoT Hub** stránky s použitím následujících hodnot:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias vstupu  |  IoTHubInput   |  Zadejte název pro identifikaci vstupu úlohy.   |
   |Předplatné   |  \<Vaše předplatné\> |  Zadejte předplatné Azure vytvořeného účtu úložiště. Účet úložiště můžete využívat v rámci stejného, ale i jiného předplatného. V tomto příkladu se předpokládá, že jste účet vytvořili v rámci stejného předplatného. |
   |IoT Hub  |  MyASAIoTHub |  Zadejte název služby IoT Hub, který jste vytvořili v předchozí části. |

4. U ostatních možností ponechejte výchozí hodnoty a výběrem možnosti **Uložit** uložte nastavení.  

   ![Konfigurace vstupních dat](./media/stream-analytics-quick-create-portal/configure-asa-input.png)
 
## <a name="configure-job-output"></a>Konfigurace výstupu úlohy

1. Přejděte na vytvořenou úlohu Stream Analytics.  

2. Vyberte **výstupy** > **přidat** > **úložiště objektů Blob**.  

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

3. V tomto příkladu dotaz načítá data ze služby IoT Hub a zkopíruje je do nového souboru v objektu blob. Vyberte **Uložit**.  

   ![Konfigurace transformace úlohy](./media/stream-analytics-quick-create-portal/add-asa-query.png)

## <a name="run-the-iot-simulator"></a>Spustit simulátor IoT

1. Otevřít [Raspberry Pi Online simulátoru Azure IoT](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Nahraďte zástupný text v řádku 15 připojovací řetězec služby Azure IoT Hub zařízení, které jste si uložili v předchozí části.

3. Klikněte na **Run** (Spustit). Výstup by měl zobrazit data ze senzorů a zprávy odeslané do služby IoT Hub.

   ![Online simulátor Raspberry Pi Azure IoT](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

1. Vraťte se na stránku přehledu úlohy a vyberte **Spustit**.

2. V části **spuštění úlohy**vyberte **nyní**, pro **čas spuštění výstupu úlohy** pole. Vyberte **Start** ke spuštění vaší úlohy.

3. Po několika minutách najděte na portálu účet úložiště a kontejner, které jste nakonfigurovali jako výstup úlohy. Soubor výstupu teď uvidíte v kontejneru. První spuštění úlohy trvá několik minut. Potom bude úloha dál běžet s tím, jak budou data přicházet.  

   ![Transformovaný výstup](./media/stream-analytics-quick-create-portal/check-asa-results.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal vyberte **Skupiny prostředků** a potom zvolte název vytvořeného prostředku.  

2. Na stránce skupiny prostředků zvolte **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a potom vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste pomocí webu Azure Portal nasadili jednoduchou úlohu Stream Analytics. Úlohy Stream Analytics můžete nasadit také pomocí [PowerShellu](stream-analytics-quick-create-powershell.md) a sady [Visual Studio](stream-analytics-quick-create-vs.md).

Pokud se chcete dozvědět o konfiguraci dalších vstupních zdrojů a provádění detekce v reálném čase, pokračujte na další článek:

> [!div class="nextstepaction"]
> [Zjišťování možných podvodů v reálném čase pomocí Stream Analytics](stream-analytics-real-time-fraud-detection.md)

