---
title: Vytvoření IoT Hubu pomocí webu Azure portal | Dokumentace Microsoftu
description: Jak vytvářet, spravovat a odstraňovat Azure IoT hub na webu Azure portal. Obsahuje informace o cenových úrovních, škálování, zabezpečení a konfigurace pro zasílání zpráv.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 0b03ae434e93dbab45235fe67c499497e1257064
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058344"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Vytvoření IoT hubu pomocí webu Azure portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Tento článek popisuje:

* Jak najít službu IoT Hub na webu Azure Portal.
* Jak vytvořit a spravovat centra IoT hub.

## <a name="where-to-find-the-iot-hub-service"></a>Kde najít službu IoT Hub

Služby IoT Hub můžete najít na portálu pro následující lokality:

* Zvolte **+ nová**, klikněte na tlačítko **Internet of Things**.
* Na webu Marketplace, zvolte **Internet of Things**.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Můžete vytvořit službu IoT hub pomocí následujících metod:

* **+ Nová** volba otevře v okně je znázorněno na následujícím snímku obrazovky. Kroky pro vytvoření služby IoT hub pomocí této metody a prostřednictvím webu marketplace jsou identické.

* Na webu Marketplace, zvolte **vytvořit** a otevřete tak okno je znázorněno na následujícím snímku obrazovky.

Následující části popisují několik kroků k vytvoření služby IoT hub.

### <a name="choose-the-name-of-the-iot-hub"></a>Zvolte název centra IoT

Vytvoření služby IoT hub, název služby IoT hub. Tento název musí být jedinečný ve všech centrech IoT hubs.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>Zvolte cenovou úroveň

Můžete si vybrat z několika vrstev v závislosti na tom, kolik funkce chcete a tom, kolik zpráv, můžete odeslat prostřednictvím řešení za den. Bezplatná úroveň slouží pro testování a vyhodnocení. Umožňuje 500 zařízení k připojení ke službě IoT hub a až 8 000 zpráv denně. Každé předplatné Azure můžete vytvořit v jednom centru IoT v bezplatné úrovni. 

Podrobnosti o další úroveň možnosti najdete v tématu [výběru správné úrovně služby IoT Hub](iot-hub-scaling.md).

### <a name="iot-hub-units"></a>Jednotky centra IoT

Počet zpráv povolených na jednotku a den závisí na cenové úrovni vašeho centra. Například pokud chcete pro podporu příchozího přenosu dat 700 000 zpráv služby IoT hub, zvolit dvě jednotky úrovně S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Zařízení, aby oddíly cloudu a skupiny prostředků

Můžete změnit počet oddílů pro službu IoT hub. Výchozí počet oddílů je 4; můžete vybrat jiné číslo z rozevíracího seznamu.

Není nutné explicitně vytvořit prázdné skupiny prostředků. Při vytváření prostředku můžete zvolit buď vytvořit novou skupinu prostředků nebo použijte existující skupinu prostředků.

![Snímek obrazovky znázorňující vytvoření Centrum na webu Azure Portal](./media/iot-hub-create-through-portal/location1.png)

### <a name="choose-subscription"></a>Zvolte předplatné

Azure IoT Hub automaticky zobrazí seznam předplatných Azure, uživatelský účet je spojena se. Můžete přidružit služby IoT hub na předplatné Azure.

### <a name="choose-the-location"></a>Zvolte umístění

Možnost umístění obsahuje seznam oblastí, kde je k dispozici služby IoT Hub.

### <a name="create-the-iot-hub"></a>Vytvoření IoT hubu

Po dokončení všech předchozích kroků můžete vytvořit IoT hub. Klikněte na tlačítko **vytvořit** zahájíte proces back-endu k vytvoření a nasazení služby IoT hub s možnostmi, které jste zvolili.

Může trvat pár minut vytvořit službu IoT hub, kterou trvá čas pro nasazení back-endu k provozování na serverech příslušné místo.

## <a name="change-the-settings-of-the-iot-hub"></a>Změna nastavení služby IoT hub
<!--robinsh these screenshots are out of date -->

Nastavení stávající služby IoT hub můžete změnit po vytvoření v okně služby IoT Hub.

![Snímek obrazovky zobrazující nastavení pro službu IoT hub](./media/iot-hub-create-through-portal/portal-settings.png)

**Sdílené zásady přístupu**: tyto zásady definovat oprávnění pro zařízení a služby pro připojení ke službě IoT Hub. Tyto zásady se zpřístupní po kliknutí **zásady sdíleného přístupu** pod **Obecné**. V tomto okně můžete upravit existující zásady nebo přidat nové zásady.

### <a name="create-a-policy"></a>Vytvořit zásadu

* Klikněte na tlačítko **přidat** otevřete okno. Tady můžete zadat nový název zásady a oprávnění, které chcete přidružit k této zásadě, jak je znázorněno na následujícím obrázku:

    Jsou k dispozici několik oprávnění, které můžou být přidružené k zásadám tyto sdílené. **Čtení registru** a **zápis do registru** zásady udělovat oprávnění ke čtení a zápis do registru identit. Pokud vyberete možnost zápisu automaticky zvolí možnost čtení.

    **Služba připojit** zásady udělí oprávnění pro přístup ke koncovým bodům služby, jako **příjem typu zařízení cloud**. **Zařízení připojit** zásady udělí oprávnění pro odesílání a příjem zpráv pomocí koncových bodů na straně zařízení služby IoT Hub.

* Klikněte na tlačítko **vytvořit** přidáte tuto nově vytvořenou zásadu do existujícího seznamu.

   ![Snímek obrazovky Přidání zásady sdíleného přístupu](./media/iot-hub-create-through-portal/shared-access-policies.png)

## <a name="endpoints"></a>Koncové body

Klikněte na tlačítko **koncové body** zobrazíte seznam koncových bodů pro IoT hub, který chcete upravit. Existují dva typy koncových bodů: koncové body, které jsou integrované do služby IoT hub a koncové body, které přidáte do služby IoT hub po jeho vytvoření.

![Snímek obrazovky přidání koncového bodu](./media/iot-hub-create-through-portal/messaging-settings.png)

### <a name="built-in-endpoints"></a>Integrované koncové body

Existují dva integrované koncové body: **cloudu na zařízení zpětnou vazbu od** a **události**.

* **Cloudové zařízení zpětné vazby** nastavení: Toto nastavení má dvě subsettings: **cloudu do zařízení TTL** (time-to-live) a **doba uchovávání** (v hodinách) pro zprávy. Při první vytvoření služby IoT hub, obě tato nastavení mají výchozí hodnotu 1 hodina. Tato nastavení uvedli, pomocí posuvníků nebo zadejte hodnoty.

* **Události** nastavení: Toto nastavení má několik subsettings, z nichž některé jsou jen pro čtení. Následující seznam popisuje tato nastavení:

  * **Oddíly**: výchozí hodnota je nastavena při vytvoření služby IoT hub. Počet oddílů pomocí tohoto nastavení můžete změnit.

  * **Název kompatibilní s centrem událostí a koncový bod**: když IoT hub je vytvořen, Centrum událostí je vytvořili interně, že potřebujete přístup k za určitých okolností. Nelze upravit hodnoty název a koncový bod kompatibilní s centrem událostí, ale můžete je zkopírovat kliknutím **kopírování**.

  * **Doba uchovávání**: ve výchozím nastavení má jeden den, ale můžete změnit pomocí rozevíracího seznamu. Tato hodnota se ve dnech pro nastavení zařízení cloud.

  * **Skupiny příjemců**: skupiny uživatelů povolit více čtenářům nezávisle na sobě čtení zpráv ze služby IoT hub. Každá služba IoT hub se vytvoří s výchozí skupiny příjemců. Můžete však přidat nebo odstranit skupiny uživatelů do vašeho centra IoT hub pomocí tohoto nastavení.

  > [!NOTE]
  > Výchozí skupinu příjemců nedá upravovat ani odstranit.

### <a name="custom-endpoints"></a>Vlastní koncové body

Ve službě IoT hub pomocí portálu můžete přidat vlastní koncové body. Z **koncové body** okna, klikněte na tlačítko **přidat** v horní části stránky otevřete **přidat koncový bod** okno. Zadejte požadované informace a pak klikněte na **OK**. Vlastní koncový bod je nyní obsažena v hlavním **koncové body** okno.

![Snímek obrazovky znázorňující vytvoření vlastního koncového bodu](./media/iot-hub-create-through-portal/endpoint-creation.png)

Další informace o vlastních koncových bodech v [Reference – koncové body IoT hubu]( iot-hub-devguide-endpoints.md).

## <a name="routes"></a>Trasy

Klikněte na tlačítko **trasy** ke správě, jak služby IoT Hub odesílá zprávy typu zařízení cloud.

![Snímek obrazovky ukazující, přidává se nová cesta.](./media/iot-hub-create-through-portal/routes-list.png)

Kliknutím můžete přidat trasy do služby IoT hub **přidat** v horní části **trasy*** okně doplnit požadované informace a kliknutím na **OK**. Trasy je pak uvedené v hlavním **trasy** okno. Upravit trasu kliknutím v seznamu trasy. Pokud chcete povolit trasu, klikněte v seznamu tras a nastavte **povoleno** přepnutím **vypnout**. Chcete-li uložit změny, klikněte na tlačítko **OK** v dolní části okna.

![Snímek obrazovky zobrazující úpravy nové pravidlo směrování](./media/iot-hub-create-through-portal/route-edit.png)

## <a name="delete-the-iot-hub"></a>Odstranit centrum IoT

Můžete procházet ke službě IoT hub, kterou chcete odstranit klepnutím **Procházet**a následným výběrem příslušné IOT hub a odstranit. Pokud chcete odstranit centrum IoT, klikněte na tlačítko **odstranit** pod název centra IoT.

## <a name="next-steps"></a>Další postup

Další informace o správě služby Azure IoT Hub na následujících odkazech:

* [Hromadná správa zařízení IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriky služby IoT Hub](iot-hub-metrics.md)
* [Monitorování operací](iot-hub-operations-monitoring.md)

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Příručka vývojáře pro IoT Hub](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení pomocí služby Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Zabezpečení řešení IoT od základů nahoru](../iot-fundamentals/iot-security-ground-up.md)
