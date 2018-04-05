---
title: Vytvoření služby IoT Hub pomocí portálu Azure | Microsoft Docs
description: Postup vytvoření, správě a odstranění služby Azure IoT hubs prostřednictvím portálu Azure. Obsahuje informace o cenových úrovní, škálování, zabezpečení a zasílání zpráv konfigurace.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 6ffde076caff6217bf6255c9294eca63d3e39b85
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Vytvoření služby IoT hub pomocí portálu Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Tento článek popisuje:

* Jak najít službu IoT Hub v portálu Azure.
* Jak vytvořit a spravovat centra IoT.

## <a name="where-to-find-the-iot-hub-service"></a>Kde najít službu IoT Hub

Služba IoT Hub můžete najít v následujících umístěních na portálu:

* Zvolte **+ nový**, zvolte **Internet věcí**.
* Na webu Marketplace, zvolte **Internet věcí**.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Můžete vytvořit služby IoT hub pomocí následujících metod:

* **+ Nový** možnost otevře okno znázorněno na následujícím snímku obrazovky. Kroky pro vytvoření služby IoT hub prostřednictvím této metody a přes marketplace jsou identické.
* Na webu Marketplace, zvolte **vytvořit** otevřete okno znázorněno na následujícím snímku obrazovky.

Následující části popisují několik kroků pro vytvoření služby IoT hub:

### <a name="choose-the-name-of-the-iot-hub"></a>Vyberte název centra IoT

Vytvoření služby IoT hub, název služby IoT hub. Tento název musí být jedinečný mezi všechny služby IoT hubs.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>Vyberte cenovou úroveň

Můžete vybrat z několika vrstev v závislosti na tom, kolik funkce chcete a kolik zprávy odesílat prostřednictvím řešení za den. Úroveň free je určený pro testování a vyhodnocení. To umožňuje 500 zařízení k připojení ke službě IoT hub a maximálně 8 000 jednotek zpráv za den. Každé předplatné Azure můžete vytvořit jeden IoT Hub v úrovni free. 

Podrobnosti o další úroveň možnosti najdete v tématu [Volba správné úrovně služby IoT Hub](iot-hub-scaling.md).

### <a name="iot-hub-units"></a>Jednotky centra IoT hub

Počet zpráv povolených na jednotku za den závisí na vaše Centrum cenovou úroveň. Například pokud chcete službu IoT hub pro podporu příchozího 700 000 zpráv, zvolte dvě jednotky vrstvy S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Zařízení do cloudu oddíly a skupiny prostředků

Můžete změnit počet oddílů pro Centrum IoT. Výchozí počet oddílů je 4, můžete v rozevíracím seznamu jiné číslo.

Není nutné explicitně vytvořit skupinu prostředků prázdný. Když vytvoříte prostředek, můžete zvolit buď vytvořit novou nebo použít existující skupinu prostředků.

![][5]

### <a name="choose-subscription"></a>Zvolte předplatné

Azure IoT Hub automaticky uvádí uživatelský účet je spojena se předplatná Azure. Můžete přidružit IoT hub, která předplatné Azure.

### <a name="choose-the-location"></a>Vyberte umístění

Možnosti umístění obsahuje seznam oblastí, kde je k dispozici služby IoT Hub.

### <a name="create-the-iot-hub"></a>Vytvoření centra IoT

Po dokončení všech předchozích kroků můžete vytvořit službu IoT hub. Klikněte na tlačítko **vytvořit** spuštění procesu back-end pro vytvoření a nasazení služby IoT hub s možnostmi, které jste zvolili.

To může trvat několik minut pro vytvoření služby IoT hub jako trvá dobu pro spuštění na serverech příslušné umístění nasazení back-end.

## <a name="change-the-settings-of-the-iot-hub"></a>Změňte nastavení centra IoT

Nastavení stávající služby IoT hub můžete změnit po vytvoření v okně služby IoT Hub.

![][8]

**Sdílené zásady přístupu**: tyto zásady určují oprávnění pro zařízení a služeb pro připojení ke službě IoT Hub. Tyto zásady můžete přístupu kliknutím **zásady sdíleného přístupu** pod **Obecné**. V tomto okně můžete upravit existující zásady nebo přidání nové zásady.

### <a name="create-a-policy"></a>Vytvořit zásadu

* Klikněte na tlačítko **přidat** a otevřete okno. Zde můžete zadat nový název zásady a oprávnění, které chcete přidružit k této zásadě, jak je znázorněno na následujícím obrázku:

    Jsou k dispozici několik oprávnění, které může být spojeno s tyto sdílené zásady. **Registru číst** a **registru zápisu** zásady udělit oprávnění ke čtení a zápis do registru identit. Výběrem možnosti zápisu automaticky zvolí možnost pro čtení.

    **Služba připojit** zásad uděluje oprávnění k přístupu koncové body služby, jako například **přijímat zařízení cloud**. **Zařízení připojit** zásad uděluje oprávnění pro odesílání a přijímání zpráv pomocí koncových bodů na straně zařízení IoT Hub.

* Klikněte na tlačítko **vytvořit** to nově vytvořený zásad do existujícího seznamu přidat.

![][10]

## <a name="endpoints"></a>Koncové body

Klikněte na tlačítko **koncové body** zobrazíte seznam koncových bodů pro službu IoT hub, který chcete upravit. Existují dva typy koncových bodů: koncové body, které jsou součástí služby IoT hub a koncové body, které přidáte do služby IoT hub po jeho vytvoření.

![][11]

### <a name="built-in-endpoints"></a>Integrované koncové body

Existují dva předdefinované koncové body: **cloudu na zařízení připomínky** a **události**.

* **Cloudu na zařízení připomínky** nastavení: Toto nastavení má dva subsettings: **cloudu do zařízení TTL** (time-to-live) a **dobu uchování** (v hodinách) pro zprávy. Při první vytvoření služby IoT hub, obě tato nastavení mají výchozí hodnotu 1 hodina. Chcete-li tato nastavení upravit, pomocí posuvníků nebo zadejte hodnoty.
* **Události** nastavení: Toto nastavení má několik subsettings, z nichž některé jsou jen pro čtení. Následující seznam popisuje tato nastavení:

  * **Oddíly**: výchozí hodnota je nastavena při vytvoření služby IoT hub. Počet oddílů pomocí tohoto nastavení můžete změnit.

  * **Název kompatibilní s centrem událostí a koncového bodu**: centra událostí je vytvořili interně, že potřebujete přístup k za určitých okolností, vzniká, když IoT hub. Nelze upravit hodnoty název a koncový bod kompatibilní s centrem událostí, ale můžete je zkopírovat kliknutím **kopie**.

  * **Doba uchování**: ve výchozím nastavení má jeden den, ale můžete změnit pomocí rozevíracího seznamu. Tato hodnota je ve dnech pro nastavení zařízení cloud.

  * **Skupiny příjemců**: skupiny příjemců povolit více uživatelům nezávisle číst zprávy z centra IoT. Každé centrum IoT je vytvořen s výchozí skupina příjemců. Můžete ale přidat nebo odstranit skupiny uživatelů do vašeho centra IoT použití tohoto nastavení.

  > [!NOTE]
  > Výchozí skupina příjemců nelze upravit ani odstranit.

### <a name="custom-endpoints"></a>Vlastní koncové body

Ve službě IoT hub pomocí portálu můžete přidat vlastní koncové body. Z **koncové body** okně klikněte na tlačítko **přidat** v horní části otevřete **přidání koncového bodu** okno. Zadejte požadované informace a pak klikněte na **OK**. Svůj vlastní koncový bod je nyní obsažena v hlavní **koncové body** okno.

![][13]

Další informace o vlastní koncové body v [odkaz – koncové body centra IoT][lnk-devguide-endpoints].

## <a name="routes"></a>Trasy

Klikněte na tlačítko **trasy** ke správě, jak IoT Hub odešle zprávu vaše zprávy typu zařízení cloud.

![][14]

Kliknutím můžete přidat trasy do služby IoT hub **přidat** v horní části **trasy*** okno, zadáte požadované informace a kliknutím na **OK**. Vaše trasy je pak uveden v hlavní **trasy** okno. Trasa můžete upravit kliknutím na seznam tras. Chcete-li povolit trasu, klikněte na něj v seznamu tras a nastavte **povoleno** přepnutím **vypnout**. Chcete-li uložit změny, klikněte na tlačítko **OK** v dolní části okna.

![][15]

## <a name="delete-the-iot-hub"></a>Odstranit službu IoT hub

Můžete procházet ke službě IoT hub, který chcete odstranit kliknutím **Procházet**a pak vyberete příslušné rozbočovače k odstranění. Pokud chcete odstranit centrum IoT, klikněte na tlačítko **odstranit** tlačítko pod název centra IoT.

## <a name="next-steps"></a>Další postup

Další informace o správě Azure IoT Hub na následujících odkazech:

* [Hromadné spravovat zařízení IoT][lnk-bulk]
* [Metriky služby IoT Hub][lnk-metrics]
* [Monitorování operací][lnk-monitor]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Příručka vývojáře pro službu IoT Hub][lnk-devguide]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]
* [Zabezpečení řešení IoT od základů nahoru][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
