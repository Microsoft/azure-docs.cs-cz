---
title: zahrnout soubor
description: zahrnout soubor
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 05/30/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 55920b6c147626f68f51b9e0479949330c71a748
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47102976"
---
> [!div class="op_single_selector"]
> * [C ve Windows](../articles/iot-suite/iot-suite-v1-connecting-devices.md)
> * [C v Linuxu](../articles/iot-suite/iot-suite-v1-connecting-devices-linux.md)
> * [Node.js](../articles/iot-suite/iot-suite-v1-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>Přehled scénáře
V tomto scénáři vytvoříte zařízení, které odesílá následující telemetrii do [předkonfigurovaného řešení][lnk-what-are-preconfig-solutions] vzdáleného monitorování:

* Venkovní teplota
* Vnitřní teplota
* Vlhkost

Kód v zařízení pro zjednodušení generuje ukázkové hodnoty, ale doporučujeme vám ukázku rozšířit připojením skutečných senzorů k zařízení a odesíláním skutečné telemetrie.

Zařízení je také schopné odpovídat na metody vyvolané z řídicího panelu řešení a na požadované hodnoty vlastností nastavené na řídicím panelu řešení.

K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure][lnk-free-trial].

## <a name="before-you-start"></a>Než začnete
Než začnete psát kód pro zařízení, je nutné zřídit předkonfigurované řešení vzdáleného monitorování a v něm zřídit nové vlastní zařízení.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Zřízení předkonfigurovaného řešení vzdáleného monitorování
Zařízení, které v tomto kurzu vytvoříte, odesílá data do instance předkonfigurovaného řešení [vzdáleného monitorování][lnk-remote-monitoring]. Pokud jste ve svém účtu Azure ještě nezřídili předkonfigurované řešení vzdáleného monitorování, použijte následující postup:

1. Na <https://www.azureiotsolutions.com/> klikněte na **+** vytvořte řešení.
2. Kliknutím na **Vybrat** na panelu **Vzdálené monitorování** vytvořte řešení.
3. Na stránce **Vytvořit řešení vzdáleného monitorování** zadejte **Název řešení** podle vašeho výběru, vyberte **Oblast**, do které chcete řešení nasadit, a vyberte předplatné Azure, které chcete použít. Potom klikněte na **Vytvořit řešení**.
4. Počkejte, dokud proces zřizování neskončí.

> [!WARNING]
> Předkonfigurovaná řešení využívají fakturovatelné služby Azure. Abyste se vyhnuli zbytečným poplatkům, nezapomeňte předkonfigurované řešení odebrat ze svého předplatného, jakmile s ním budete hotovi. Předkonfigurované řešení můžete úplně odebrat ze svého předplatného najdete <https://www.azureiotsolutions.com/> stránky.
> 
> 

Po skončení procesu zřizování řešení vzdáleného monitorování klikněte na **Spustit**. Ve vašem prohlížeči se otevře řídicí panel řešení.

![Řídicí panel řešení][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Zřízení zařízení v řešení vzdáleného monitorování
> [!NOTE]
> Pokud jste už ve svém řešení zařízení zřídili, můžete tento krok přeskočit. Při vytváření klientské aplikace potřebujete znát přihlašovací údaje zařízení.
> 
> 

Aby se zařízení mohlo připojit k předkonfigurovanému řešení, musí se identifikovat ve službě IoT Hub pomocí platných přihlašovacích údajů. Přihlašovací údaje zařízení můžete zjistit z řídicího panelu řešení. Přihlašovací údaje zařízení vložíte do klientské aplikace později v tomto kurzu.

Pokud chcete přidat zařízení do řešení vzdáleného monitorování, proveďte na řídicím panelu řešení následující kroky:

1. V levém dolním rohu řídicího panelu klikněte na **Přidat zařízení**.
   
   ![Přidání zařízení][1]
2. Na panelu **Vlastní zařízení** klikněte na **Přidat nové**.
   
   ![Přidání vlastního zařízení][2]
3. Vyberte možnost **Definovat vlastní ID zařízení**. Zadejte ID zařízení, třeba **mydevice**, klikněte na **Zkontrolovat ID** pro ověření, že se tento název ještě nepoužívá, a potom zřiďte zařízení kliknutím na **Vytvořit**.
   
   ![Přidání ID zařízení][3]
4. Poznamenejte si přihlašovací údaje zařízení (ID zařízení, název hostitele služby IoT Hub a Klíč zařízení). Klientská aplikace potřebuje tyto hodnoty pro připojení k řešení vzdáleného monitorování. Potom klikněte na **Done** (Hotovo).
   
    ![Zobrazení přihlašovacích údajů zařízení][4]
5. V seznamu zařízení na řídicím panelu řešení vyberte své zařízení. Pak na panelu **Podrobnosti o zařízení** klikněte na **Povolit zařízení**. Stav vašeho zařízení je teď **Spuštěno**. Řešení vzdáleného monitorování teď může z vašeho zařízení přijímat telemetrii a vyvolávat v něm metody.

[img-dashboard]: ./media/iot-suite-v1-selector-connecting/dashboard.png
[1]: ./media/iot-suite-v1-selector-connecting/suite0.png
[2]: ./media/iot-suite-v1-selector-connecting/suite1.png
[3]: ./media/iot-suite-v1-selector-connecting/suite2.png
[4]: ./media/iot-suite-v1-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/