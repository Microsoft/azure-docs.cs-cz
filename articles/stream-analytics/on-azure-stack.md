---
title: Spustit Azure Stream Analytics v Azure Stack
description: Vytvořte úlohu Azure Stream Analytics Edge a nasaďte ji do Azure Stack centra prostřednictvím modulu runtime IoT Edge.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 315b2efa042721a19fa779794c4921f9ced1fc83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589799"
---
# <a name="run-azure-stream-analytics-on-azure-stack"></a>Spustit Azure Stream Analytics v Azure Stack 

Azure Stream Analytics můžete spustit v centru Azure Stack jako modul IoT Edge. Do modulu IoT Edge byly přidány konfigurace umožňující interakci s úložištěm objektů blob, Event Hubs a centrem IoT běžícími v předplatném centra Azure Stack. tím se povolí vlastní adresy URL, které se nacházejí v každé komunikaci centra Azure Stack.

Díky Stream Analytics v Azure Stack můžete vytvářet skutečně hybridní architektury pro zpracování datových proudů ve vlastním privátním, autonomním cloudu, který se dá připojit nebo odpojit pomocí cloudových nativních aplikací s využitím konzistentních služeb Azure v místním prostředí. 

V tomto článku se dozvíte, jak streamovat data z IoT Hub nebo centra událostí do jiného centra událostí nebo Blob Storage v předplatném centra Azure Stack a zpracovat je pomocí Stream Analytics.

## <a name="set-up-environments"></a>Nastavení prostředí

Azure Stream Analytics je hybridní služba v centru Azure Stack. Je to modul IoT Edge, který je nakonfigurovaný v Azure, ale dá se spouštět v centru Azure Stack.  

Pokud s Azure Stack hub nebo IoT Edge začínáte, vytvořte prostředí podle pokynů níže.

### <a name="prepare-the-azure-stack-hub-environment"></a>Příprava prostředí Azure Stack hub

Vytvořte předplatné centra Azure Stack. Další informace najdete v [kurzu vytváření předplatného centra Azure Stack.](/azure-stack/user/azure-stack-subscribe-services/)

Pokud chcete vyhodnotit Azure Stack centrum na vlastním serveru, můžete použít Azure Stack Development Kit (ASDK).  Další informace o ASDK najdete v tématu [Přehled ASDK](/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>Instalace modulu runtime IoT Edge

Aby bylo možné spouštět Azure Stream Analytics v centru Azure Stack, musí mít vaše zařízení modul runtime IoT Edge a musí mít síťové připojení ke službě Azure Stack hub nebo virtuální počítač spuštěný v centru pro Azure Stack. Modul runtime IoT Edge umožňuje integraci úloh Stream Analytics Edge do Azure Storage a Event Hubs Azure, které jsou spuštěny na vašem Azure Stackm centru. Další informace najdete v tématu [Azure Stream Analytics na IoT Edge](stream-analytics-edge.md) 

Kromě přístupu k síti k prostředkům Azure Stack hub potřebuje IoT Edge zařízení (nebo VM) přístup k Azure IoT Hub ve veřejném cloudu Azure a nakonfigurovat tak modul Stream Analytics. 

Následující příručky ukazují, jak nastavit modul runtime IoT Edge na svém zařízení nebo na VIRTUÁLNÍm počítači:

* [Instalace modulu runtime Azure IoT Edge ve Windows](../iot-edge/how-to-install-iot-edge.md)
* [Instalace modulu runtime Azure IoT Edge v systémech Linux založených na distribuci Debian](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Vytvořit úlohu Azure Stream Analytics Edge

Hraniční úlohy ASA se spouštějí v kontejnerech nasazených do zařízení Azure IoT Edge. Skládají se ze dvou částí:
* Cloudová součást, která zodpovídá za definici úlohy: uživatelé definují vstupy, výstupy, dotazy a další nastavení (události mimo pořadí atd.) v cloudu.
* Modul spuštěný ve vašich zařízeních IoT. Obsahuje modul ASA a přijímá definici úlohy z cloudu.

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Když vytvoříte úlohu Azure Stream Analytics, která se má spouštět na zařízení IoT Edge, je potřeba ji uložit tak, aby ji bylo možné volat ze zařízení. Můžete použít existující účet úložiště Azure nebo vytvořit nový.
1. V Azure Portal klikněte na **vytvořit prostředek > úložiště > účet úložiště – objekt blob, soubor, tabulka, fronta**.
2. Zadejte následující hodnoty pro vytvoření účtu úložiště:

   | Pole | Hodnota |
   | --- | --- |
   | Název | Zadejte jedinečný název účtu úložiště. |
   | Umístění | Zvolte umístění, které je blízko vás.|
   | Předplatné | Zvolte stejné předplatné jako pro vaše centrum IoT.|
   | Skupina prostředků | Doporučujeme použít stejnou skupinu prostředků pro všechny prostředky testu, které vytvoříte během [IoT Edge rychlý Start](../iot-edge/quickstart.md) a kurzy. Například **IoTEdgeResources**. |

3. U ostatních polí ponechte výchozí hodnoty a vyberte **Vytvořit**.


### <a name="create-a-new-job"></a>Vytvoření nové úlohy

1. V Azure Portal klikněte na **vytvořit prostředek > Internet věcí úlohu > Stream Analytics**.
2. Zadejte následující hodnoty pro vytvoření účtu úložiště:

   | Pole | Hodnota |
   | --- | --- |
   | Název úlohy | Zadejte název úlohy. Například **IoTEdgeJob** |
   | Předplatné | Zvolte stejné předplatné jako pro vaše centrum IoT.|
   | Skupina prostředků | Doporučujeme použít stejnou skupinu prostředků pro všechny prostředky testu, které vytvoříte během [IoT Edge rychlý Start](../iot-edge/quickstart.md) a kurzy. Například **IoTEdgeResources**. |
   | Umístění | Zvolte umístění, které je blízko vás. |
   | Hostitelské prostředí | Vyberte **Edge**. |

3. Vyberte **Vytvořit**.

### <a name="configure-your-job"></a>Konfigurace úlohy

Po vytvoření úlohy Stream Analytics na webu Azure Portal pro ni můžete nakonfigurovat vstup, výstup a dotaz, který má spustit pro data, která jí procházejí. Můžete ručně zadat vstupy z IoT Hub nebo centra událostí v předplatném centra Azure Stack.

1. Přejděte k vaší úloze Stream Analytics na webu Azure Portal.
2. V části **Konfigurovat** vyberte **Nastavení účtu úložiště** a zvolte účet úložiště, který jste vytvořili v předchozím kroku.
   > [!div class="mx-imgBorder"]
   > [![Nastavení ](media/on-azure-stack/storage-account-settings.png) účtu úložiště úlohy](media/on-azure-stack/storage-account-settings.png#lightbox)
3. V části **topologie úlohy** vyberte **vstupy** a pak **přidejte vstup datového proudu.**
4. V rozevíracím seznamu vyberte možnost **IoT Hub**, **centrum událostí** nebo **centrum Edge** . 
5. Pokud se jedná o centrum událostí nebo IoT Hub v rámci předplatného centra Azure Stack, zadejte informace ručně, jak je znázorněno níže.

   #### <a name="event-hub"></a>Centrum událostí

   | Pole | Hodnota |
   | --- | --- |
   | Alias vstupu | Popisný název, který použijete v dotazu úlohy pro odkazování na tento vstup. |
   | Obor názvů Service Bus | Obor názvů je kontejner pro sadu entit zasílání zpráv. Když vytváříte nové centrum událostí, vytvoříte také obor názvů. (Příklad: *Sb:// <Event Hub Name> . eventhub.Shanghai.azurestack.corp.Microsoft.com*) |
   | Název centra událostí | Název centra událostí, které se má použít jako vstup |
   | Název zásad centra událostí | Zásady sdíleného přístupu, které poskytují přístup k centru událostí. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. Tato možnost se vyplní automaticky, pokud nevyberete možnost zadat nastavení centra událostí ručně. |
   | Klíč zásad centra událostí | Sdílený přístupový klíč, který slouží k autorizaci přístupu do centra událostí. Tato možnost se vyplní automaticky, pokud nevyberete možnost zadat nastavení centra událostí ručně. Můžete ji najít v nastavení centra událostí. |
   | Skupina uživatelů centra událostí (volitelné) | Důrazně doporučujeme použít pro každou úlohu Stream Analytics odlišnou skupinu uživatelů. Tento řetězec identifikuje skupinu uživatelů, která se má použít k ingestování dat z centra událostí. Pokud není zadána žádná skupina příjemců, úloha Stream Analytics používá skupinu uživatelů $Default. |
   | Počet oddílů | Počet oddílů je počet oddílů v centru událostí. |

   > [!div class="mx-imgBorder"]
   > [![Vstup ](media/on-azure-stack/event-hub-input.png) centra událostí](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT Hub

   | Pole | Hodnota |
   | --- | --- |
   | Alias vstupu | Popisný název, který použijete v dotazu úlohy pro odkazování na tento vstup. |
   | IoT Hub | Název IoT Hub, který se má použít jako vstup (Příklad:*<IoT Hub Name> . Shanghai.azurestack.corp.Microsoft.com*) |
   | Název zásady sdíleného přístupu | Zásada sdíleného přístupu, která poskytuje přístup k IoT Hub. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. |
   | Klíč zásad sdíleného přístupu | Sdílený přístupový klíč, který slouží k autorizaci přístupu k IoT Hub. Tato možnost se vyplní automaticky, pokud nevyberete možnost zadat nastavení centra IoT Hub ručně. |
   | Skupina uživatelů (volitelné) | Důrazně doporučujeme pro každou úlohu Stream Analytics použít jinou skupinu uživatelů. Skupina příjemců slouží k ingestování dat z IoT Hub. Stream Analytics používá skupinu příjemců $Default, pokud neurčíte jinak. |
   | Počet oddílů | Počet oddílů je počet oddílů v centru událostí. |

   > [!div class="mx-imgBorder"]
   > [![Vstup ](media/on-azure-stack/iot-hub-input.png) IoT Hub](media/on-azure-stack/iot-hub-input.png#lightbox)

6. V ostatních polích nechte výchozí hodnoty a vyberte Uložit.
7. V části Topologie úlohy otevřete Výstupy a pak vyberte Přidat.
8. V rozevíracím seznamu vyberte možnost Blob Storage, centrum událostí nebo centrum Edge.
9. Pokud je výstupem centrum událostí nebo Blob Storage v rámci předplatného centra Azure Stack, zadejte informace ručně, jak je znázorněno níže.

   #### <a name="event-hub"></a>Centrum událostí

   | Pole | Hodnota |
   | --- | --- |
   | Alias pro výstup | Popisný název, který se používá v dotazech k nasměrování výstupu dotazu do tohoto centra událostí. |
   | Obor názvů Service Bus | Kontejner pro sadu entit zasílání zpráv. Při vytváření nového centra událostí jste taky vytvořili obor názvů služby Service Bus. (Příklad: *Sb:// <Event Hub Name> . eventhub.Shanghai.azurestack.corp.Microsoft.com*) |
   | Název centra událostí | Název výstupu centra událostí. |
   | Název zásad centra událostí | Zásady sdíleného přístupu, které můžete vytvořit na kartě Konfigurace centra událostí. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. |
   | Klíč zásad centra událostí | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů centra událostí. |

   > [!div class="mx-imgBorder"]
   > [![Výstup ](media/on-azure-stack/event-hub-output.png) centra událostí](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob Storage 

   | Pole | Hodnota |
   | --- | --- |
   | Alias pro výstup | Popisný název, který se používá v dotazech k směrování výstupu dotazu do tohoto úložiště objektů BLOB. |
   | Účet úložiště | Název účtu úložiště, do kterého posíláte výstup. (Příklad: *<Storage Account Name> . blob.Shanghai.azurestack.corp.Microsoft.com*) |
   | Klíč účtu úložiště | Tajný klíč přidružený k účtu úložiště Tato možnost se vyplní automaticky, pokud nevyberete možnost zadat nastavení úložiště objektů BLOB ručně. |

> [!NOTE]
> Formát Parquet není podporován pro úlohy Edge na rozbočovači Azure Stack. Pro minimální řádky a maximální dobu zadejte hodnotu 0 nebo nechte pole prázdné.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Nasazení Stream Analytics do virtuálního počítače nebo zařízení připojeného k Azure Stack

1. V Azure Portal otevřete IoT Hub. Přejděte na **IoT Edge** a klikněte na zařízení (VM), pro které chcete toto nasazení cílit.
2. Vyberte **Set modules** (Nastavit moduly). Pak vyberte **+ Přidat** a zvolte **Azure Stream Analytics modul**. 
3. Vyberte předplatné a hraniční úlohu Stream Analytics, kterou jste vytvořili. Klikněte na **Uložit** a vyberte **Další: trasy**.

   > [!div class="mx-imgBorder"]
   > [![Přidat moduly ](media/on-azure-stack/edge-modules.png)](media/on-azure-stack/edge-modules.png#lightbox)

4. Klikněte na tlačítko **zkontrolovat + vytvořit >**.
5. V kroku **Revize + vytvořit** vyberte **vytvořit**. 
   > [!div class="mx-imgBorder"]
   > [![Manifest ](media/on-azure-stack/module-content.png)](media/on-azure-stack/module-content.png#lightbox)
6. Potvrďte, že se modul přidal do seznamu.
   > [!div class="mx-imgBorder"]
   > [![Stránka ](media/on-azure-stack/edge-deployment.png) nasazení](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>Další kroky
- [Azure Stream Analytics na hraničních zařízeních IoT](./stream-analytics-edge.md)
- [Vývoj úloh Stream Analytics Edge](/stream-analytics-query/stream-analytics-query-language-reference)
