---
title: Rychlý Start – vytvoření Stream Analytics úlohy pomocí Azure Portal
description: V tomto rychlém startu se dozvíte, jak začít vytvořením úlohy Stream Analytics, konfigurací vstupů a výstupů a definicí dotazu.
author: enkrumah
ms.author: ebnkruma
ms.date: 06/21/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: c585cedce677599528e036525dead13a85c8b102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012372"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-portal"></a>Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal

V tomto rychlém startu se naučíte, jak začít vytvořením úlohy Stream Analytics. V tomto rychlém startu definujete Stream Analytics úlohu, která čte data streamování v reálném čase a filtruje zprávy s teplotou větší než 27. Vaše úloha Stream Analytics načte data z IoT Hub, transformují data a zapíše data zpátky do kontejneru v úložišti objektů BLOB. Vstupní data použitá v tomto rychlém startu se generují simulátoru malinu PI v online režimu. 

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Před definováním Stream Analytics úlohy byste měli připravit vstupní data. Data snímače v reálném čase se ingestují na IoT Hub, která se později nakonfigurovala jako vstup úlohy. Chcete-li připravit vstupní data požadovaná úlohou, proveďte následující kroky:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. Vyberte **vytvořit prostředek**  >  **Internet věcí**  >  **IoT Hub**.

3. V podokně **IoT Hub** zadejte následující informace:
   
   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Předplatné  | \<Your subscription\> |  Vyberte předplatné Azure, které chcete použít. |
   |Skupina prostředků   |   asaquickstart-resourcegroup  |   Vyberte **Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. |
   |Oblast  |  \<Select the region that is closest to your users\> | Vyberte zeměpisnou polohu, kde můžete IoT Hub hostovat. Použijte umístění, které je nejblíže vašim uživatelům. |
   |Název IoT Hub  | MyASAIoTHub  |   Vyberte název IoT Hub.   |

   ![Vytvořit IoT Hub](./media/stream-analytics-quick-create-portal/create-iot-hub.png)

4. Vyberte **Další: nastavit velikost a měřítko**.

5. Zvolte si **Úroveň ceny a škálování**. Pro tento rychlý Start vyberte úroveň **F1** , pokud je stále k dispozici ve vašem předplatném. Další informace najdete v tématu [IoT Hub ceny](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Velikost a škálování IoT Hub](./media/stream-analytics-quick-create-portal/iot-hub-size-and-scale.png)

6. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte informace o IoT Hub a klikněte na **vytvořit**. Vytvoření IoT Hub může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení**.

7. V navigační nabídce IoT Hub klikněte na **Přidat** v části **zařízení IoT**. Přidejte **ID zařízení** a klikněte na **Uložit**.

   ![Přidání zařízení do IoT Hub](./media/stream-analytics-quick-create-portal/add-device-iot-hub.png)

8. Po vytvoření zařízení otevřete zařízení ze seznamu **zařízení IoT** . Zkopírujte **připojovací řetězec – primární klíč** a uložte ho do poznámkového bloku a použijte ho později.

   ![Kopírovat připojovací řetězec IoT Hub zařízení](./media/stream-analytics-quick-create-portal/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Vytvoření úložiště objektů BLOB

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** úložiště  >    >  **účet** úložiště.

2. V podokně **vytvořit účet úložiště** zadejte název účtu úložiště, umístění a skupinu prostředků. Vyberte stejné umístění a skupinu prostředků jako IoT Hub, který jste vytvořili. Pak klikněte na tlačítko **zkontrolovat + vytvořit** a vytvořte účet.

   ![Vytvoření účtu úložiště](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Po vytvoření účtu úložiště vyberte dlaždici **objektů BLOB** v podokně **Přehled** .

   ![Přehled účtu úložiště](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Na stránce **BLOB Service** vyberte **kontejner** a zadejte název svého kontejneru, například *container1*. Ponechte **úroveň veřejného přístupu** jako **soukromou (bez anonymního přístupu)** a vyberte **OK**.

   ![Vytvoření kontejneru objektů blob](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. Přihlaste se k webu Azure Portal.

2. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.  

3.   >  V seznamu výsledků vyberte **úlohu Stream Analytics** analýzy.  

4. Na stránce Úloha Stream Analytics zadejte následující informace:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Název úlohy   |  MyASAJob   |   Zadejte název pro identifikaci úlohy Stream Analytics. Název úlohy Stream Analytics může obsahovat jen alfanumerické znaky, spojovníky a podtržítka a musí být dlouhý 3 až 63 znaků. |
   |Předplatné  | \<Your subscription\> |  Vyberte předplatné Azure, které chcete použít pro vaši úlohu. |
   |Skupina prostředků   |   asaquickstart-resourcegroup  |   Vyberte stejnou skupinu prostředků jako IoT Hub. |
   |Umístění  |  \<Select the region that is closest to your users\> | Vyberte zeměpisnou polohu, kde je možné hostovat úlohu Stream Analytics. V zájmu vyššího výkonu a nižších nákladů za přenos dat zvolte umístění co nejbližší vašim uživatelům. |
   |Jednotky streamování  | 1  |   Jednotky streamování představují výpočetní prostředky nutné k provedení úlohy. Ve výchozím nastavení je tato hodnota nastavená na 1. Podrobnosti o škálování jednotek streamování najdete v článku věnovaném [principům a úpravám jednotek streamování](stream-analytics-streaming-unit-consumption.md).   |
   |Hostitelské prostředí  |  Cloud  |   Úlohy Stream Analytics můžete nasadit do cloudu nebo do hraničního zařízení. Cloud umožňuje nasazení do cloudu Azure a Edge umožňuje nasazení do zařízení IoT Edge. |

   ![Vytvoření úlohy](./media/stream-analytics-quick-create-portal/create-asa-job.png)

5. Umístěte úlohu do řídicího panelu zaškrtnutím políčka **Připnutí na řídicí panel** a klikněte na **Vytvořit**.  

6. V pravém horním rohu okna prohlížeče byste měli vidět, že *probíhá nasazení...* oznámení se zobrazuje. 

## <a name="configure-job-input"></a>Konfigurace vstupu úlohy

V této části nakonfigurujete vstup IoT Hub zařízení pro Stream Analytics úlohu. Použijte IoT Hub, který jste vytvořili v předchozí části tohoto rychlého startu.

1. Přejděte na úlohu Stream Analytics.  

2. Vyberte **vstupy**  >  **přidat vstupní datový proud**  >  **IoT Hub**.  

3. Vyplňte stránku **IoT Hub** s následujícími hodnotami:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias vstupu  |  IoTHubInput   |  Zadejte název pro identifikaci vstupu úlohy.   |
   |Předplatné   |  \<Your subscription\> |  Zadejte předplatné Azure vytvořeného účtu úložiště. Účet úložiště můžete využívat v rámci stejného, ale i jiného předplatného. V tomto příkladu se předpokládá, že jste účet vytvořili v rámci stejného předplatného. |
   |IoT Hub  |  MyASAIoTHub |  Zadejte název IoT Hub, který jste vytvořili v předchozí části. |

4. U ostatních možností ponechejte výchozí hodnoty a výběrem možnosti **Uložit** uložte nastavení.  

   ![Konfigurace vstupních dat](./media/stream-analytics-quick-create-portal/configure-asa-input.png)
 
## <a name="configure-job-output"></a>Konfigurace výstupu úlohy

1. Přejděte na vytvořenou úlohu Stream Analytics.  

2. Vyberte **výstupy**  >  **Přidat**  >  **úložiště objektů BLOB**.  

3. Na stránce **Úložiště objektů blob** vyplňte tyto hodnoty:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias pro výstup |   BlobOutput   |   Zadejte název pro identifikaci výstupu úlohy. |
   |Předplatné  |  \<Your subscription\>  |  Zadejte předplatné Azure vytvořeného účtu úložiště. Účet úložiště můžete využívat v rámci stejného, ale i jiného předplatného. V tomto příkladu se předpokládá, že jste účet vytvořili v rámci stejného předplatného. |
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

3. V tomto příkladu dotaz čte data z IoT Hub a kopíruje je do nového souboru v objektu BLOB. Vyberte **Uložit**.  

   ![Konfigurace transformace úlohy](./media/stream-analytics-quick-create-portal/add-asa-query.png)

## <a name="run-the-iot-simulator"></a>Spuštění simulátoru IoT

1. Otevřete [simulátor Azure IoT online pro maliny](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Zástupný text na řádku 15 nahraďte připojovacím řetězcem zařízení Azure IoT Hub, který jste uložili v předchozí části.

3. Klikněte na **Spustit**. Výstup by měl ukazovat data a zprávy snímače, které se odesílají do vašeho IoT Hub.

   ![Online simulátor Raspberry Pi Azure IoT](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

1. Vraťte se na stránku přehledu úlohy a vyberte **Spustit**.

2. V části **počáteční úloha** vyberte **nyní** u pole **čas zahájení výstupu úlohy** . Pak vyberte **Spustit** a spusťte svoji úlohu.

3. Po několika minutách najděte na portálu účet úložiště a kontejner, které jste nakonfigurovali jako výstup úlohy. Soubor výstupu teď uvidíte v kontejneru. První spuštění úlohy trvá několik minut. Potom bude úloha dál běžet s tím, jak budou data přicházet.  

   ![Transformovaný výstup](./media/stream-analytics-quick-create-portal/check-asa-results.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, úlohu Stream Analytics a všechny související prostředky. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal vyberte **Skupiny prostředků** a potom zvolte název vytvořeného prostředku.  

2. Na stránce skupiny prostředků zvolte **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a potom vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí webu Azure Portal nasadili jednoduchou úlohu Stream Analytics. Stream Analytics úlohy můžete nasadit také pomocí [prostředí PowerShell](stream-analytics-quick-create-powershell.md), sady [Visual Studio](stream-analytics-quick-create-vs.md)a [Visual Studio Code](quick-create-visual-studio-code.md).

Pokud se chcete dozvědět o konfiguraci dalších vstupních zdrojů a provádění detekce v reálném čase, pokračujte na další článek:

> [!div class="nextstepaction"]
> [Zjišťování možných podvodů v reálném čase pomocí Stream Analytics](stream-analytics-real-time-fraud-detection.md)
