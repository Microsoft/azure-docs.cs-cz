---
title: Kurz – vytvoření instance IoT Edge analýzy videí ve službě Azure IoT Central (virtuální počítač se systémem Linux)
description: V tomto kurzu se dozvíte, jak vytvořit instanci IoT Edge analýzy videí na virtuálním počítači se systémem Linux pro použití s šablonou aplikace pro video Analytics – objekt a pohyb.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 0b39ec9c8cb70adac1474b2647ac1c9591b9d5cd
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526387"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>Kurz: vytvoření instance IoT Edge pro video Analytics (virtuální počítač se systémem Linux)

Azure IoT Edge je plně spravovaná služba, která místně zajišťuje cloudovou logiku nasazením a spuštěním:

* Vlastní logika
* Služby Azure
* Umělá inteligence

V IoT Edge se tyto služby spouštějí přímo na zařízeních IoT pro různé platformy a umožňují bezpečné spuštění řešení IoT a škálování v cloudu nebo offline.

V tomto kurzu se dozvíte, jak připravit zařízení IoT Edge na virtuálním počítači Azure. Instance IoT Edge spouští analytické moduly pro video, které používá šablona aplikace pro vyhledávání videí Azure IoT Central video Analytics – objekt a pohyb.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření virtuálního počítače Azure s nainstalovaným modulem runtime Azure IoT Edge
> * Připraví IoT Edge instalaci, aby hostoval modul Live video Analytics a připojil se k IoT Central

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste v kurzu služby [Azure IoT Central vytvořit předchozí aplikaci video Analytics](./tutorial-video-analytics-create-app.md) .

Budete také potřebovat předplatné Azure. Pokud předplatné Azure nemáte, můžete si ho na [stránce pro registraci k Azure](https://aka.ms/createazuresubscription)vytvořit zdarma.

## <a name="deploy-azure-iot-edge"></a>Nasazení Azure IoT Edge

Pokud chcete vytvořit virtuální počítač Azure s nainstalovaným nejnovějšími moduly runtime IoT Edge a nástroji Live video Analytics, vyberte následující tlačítko:

[![Tlačítko Nasazení do Azure pro iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

K dokončení formuláře **vlastního nasazení** použijte informace v následující tabulce:

| Pole | Hodnota |
| ----- | ----- |
| Předplatné | Vyberte své předplatné Azure. |
| Skupina prostředků | *lva-RG* – skupina prostředků, kterou jste vytvořili v předchozím kurzu. |
| Region (Oblast)       | *East US* |
| Předpona popisku DNS | Vyberte jedinečnou předponu DNS pro virtuální počítač. |
| Uživatelské jméno správce | *AzureUser* |
| Heslo správce | Zadejte heslo. Poznamenejte si heslo v souboru *scratchpad.txt* , budete ho používat později. |
| ID oboru | **ID oboru** , které jste si poznamenali v souboru *scratchpad.txt* v předchozím kurzu, když jste přidali zařízení brány. |
| ID zařízení | *lva-Gateway – 001* – zařízení brány, které jste vytvořili v předchozím kurzu. |
| Klíč zařízení | Primární klíč zařízení, který jste si poznamenali v souboru *scratchpad.txt* v předchozím kurzu, když jste přidali zařízení brány. |
| Hostitel aplikace IoT Central | **Adresa URL aplikace** , kterou jste si poznamenali v souboru *scratchpad.txt* v předchozím kurzu. Například *Traders.azureiotcentral.com*. |
| Token API aplikace IoT Central | Token rozhraní API pro operátora, na který jste si poznamenali v předchozím kurzu. |
| Klíč pro zřizování zařízení IoT Central | Token sdíleného přístupového podpisu primární skupiny jste si poznamenali v souboru *scratchpad.txt* v předchozím kurzu. |
| Velikost virtuálního počítače | *Standard_DS1_v2* |
| Verze operačního systému Ubuntu | *18,04 – LTS* |
| Umístění | *[resourceName (). Location]* |

Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**. Dokončení nasazení obvykle trvá přibližně tři minuty. Po dokončení nasazení přejděte do skupiny prostředků **lva-RG** v Azure Portal.

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>Ujistěte se, že modul runtime IoT Edge načítá moduly.

V Azure Portal přejděte do skupiny prostředků **lva-RG** a vyberte virtuální počítač. Pak v části **Podpora a řešení potíží** vyberte **sériová konzola**.

Stisknutím klávesy **ENTER** zobrazíte `login:` výzvu. Jako uživatelské jméno a heslo, které jste si zvolili při vytváření virtuálního počítače, použijte *AzureUser* .

Spusťte následující příkaz a ověřte verzi modulu runtime IoT Edge. V době psaní je verze 1.0.9:

```bash
sudo iotedge --version
```

Výpis IoT Edge modulů pomocí příkazu:

```bash
sudo iotedge list
```

Nasazení nakonfigurovali následující pět modulů IoT Edge ke spuštění:

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

Nasazení vytvořilo vlastní IoT Edge prostředí s požadovanými moduly pro Live video Analytics. Nasazení aktualizovalo výchozí **config. yaml** , aby se zajistilo, že modul runtime IoT Edge používal službu IoT Device Provisioning pro připojení k IoT Central. Nasazení také vytvořilo soubor s názvem **state.js** ve složce **/Data/Storage** , aby poskytoval další konfigurační data modulům. Další informace najdete v kurzu [vytvoření instance IoT Edge pro video Analytics (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md) .

Řešení potíží se zařízením s IoT Edge najdete v tématu [řešení potíží s IoT Edge zařízením](https://docs.microsoft.com/azure/iot-edge/troubleshoot) .

## <a name="use-the-rtsp-simulator"></a>Použití simulátoru RTSP

Pokud nemáte zařízení se skutečnou kamerou pro připojení k vašemu IoT Edge zařízení, můžete použít dvě simulovaná zařízení kamery v šabloně aplikace video Analytics. V této části se dozvíte, jak používat simulovaný Stream videa na zařízení IoT Edge.

Tyto pokyny používají [Server Live555 Media](http://www.live555.com/mediaServer/) jako simulátor RTSP v kontejneru Docker.

> [!NOTE]
> Odkazy na software třetích stran v tomto úložišti mají jenom informativní a pohodlí. Společnost Microsoft neschvaluje ani neposkytuje práva pro software třetí strany. Další informace najdete v tématu [Live555 Media Server](http://www.live555.com/mediaServer/).

Pomocí následujícího příkazu spusťte nástroj **rtspvideo** v kontejneru Docker na vašem IoT EDGEovém virtuálním počítači. Kontejner Docker vytvoří datový proud RTSP na pozadí:

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

K výpisu kontejneru Docker použijte následující příkaz:

```bash
sudo docker ps
```

Seznam obsahuje kontejner nazvaný **LIVE555**.

## <a name="next-steps"></a>Další kroky

Nyní jste nasadili IoT Edge runtime, moduly LVA a datový proud simulace Live555 na virtuálním počítači Linux běžícím na Azure.

Pokud chcete spravovat kamery, postupujte podle dalšího kurzu.

> [!div class="nextstepaction"]
> [Monitorování a Správa aplikace video Analytics – objekt a detekce pohybu](./tutorial-video-analytics-manage.md)
