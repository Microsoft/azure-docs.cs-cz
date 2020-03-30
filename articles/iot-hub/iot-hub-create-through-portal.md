---
title: Vytvoření centra IoT Hub pomocí portálu Azure | Dokumenty společnosti Microsoft
description: Jak vytvářet, spravovat a odstraňovat centra Azure IoT prostřednictvím portálu Azure. Obsahuje informace o cenových úrovních, škálování, zabezpečení a konfiguraci zasílání zpráv.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: c43c142b22709d42416b2dd14dfc78812970916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284730"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Vytvoření centra IoT pomocí portálu Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Tento článek popisuje, jak vytvořit a spravovat centra IoT pomocí [portálu Azure](https://portal.azure.com).

Chcete-li použít kroky v tomto kurzu, budete potřebovat předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Změna nastavení centra IoT hub

Nastavení existujícího centra IoT hub můžete změnit po jeho vytvoření z podokna Služby IoT Hub.

![Snímek obrazovky s nastavením centra IoT hub](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Tady jsou některé vlastnosti, které můžete nastavit pro centrum IoT:

**Ceny a škálování**: Tuto vlastnost můžete použít k migraci na jinou úroveň nebo nastavit počet jednotek IoT Hub. 

**Monitorování operací**: Zapněte nebo vypněte různé kategorie monitorování, jako je například protokolování událostí souvisejících se zprávami mezi zařízeními nebo zprávami mezi cloudy.

**Filtr IP**: Zadejte rozsah ADRES IP, které budou centrem IoT přijaty nebo odmítnuty.

**Vlastnosti**: Poskytuje seznam vlastností, které můžete kopírovat a používat jinde, například ID prostředku, skupinu prostředků, umístění a tak dále.

### <a name="shared-access-policies"></a>Zásady sdíleného přístupu

Seznam zásad sdíleného přístupu můžete také zobrazit nebo upravit klepnutím na položku **Zásady sdíleného přístupu** v části **Nastavení.** Tyto zásady definují oprávnění pro zařízení a služby pro připojení k centru IoT Hub. 

Kliknutím na **Přidat** otevřete okno **Přidat sdílené zásady přístupu.**  Můžete zadat nový název zásady a oprávnění, která chcete přidružit k této zásadě, jak je znázorněno na následujícím obrázku:

![Snímek obrazovky s přidáním zásad sdíleného přístupu](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* **Zásady čtení** a **zápisu registru registru** udělují přístupová práva pro čtení a zápis do registru identit. Tato oprávnění se používají back-end cloudové služby ke správě identit zařízení. Výběrem možnosti zápisu se automaticky zvolí možnost čtení.

* Zásada **připojení služby** uděluje oprávnění k přístupu ke koncovým bodům služby. Toto oprávnění používají cloudové služby back-end k odesílání a přijímání zpráv ze zařízení a také k aktualizaci a čtení dat dvojčete a dvojčete modulu zařízení.

* Zásady **device connect** udělují oprávnění pro odesílání a přijímání zpráv pomocí koncových bodů na straně zařízení služby IoT Hub. Toto oprávnění používají zařízení k odesílání a přijímání zpráv z centra IoT hub, aktualizaci a čtení dat dvojčete a dvojčete modulu zařízení a k nahrávání souborů.

Kliknutím na **Vytvořit** přidáte tuto nově vytvořenou zásadu do existujícího seznamu.

Podrobnější informace o přístupu uděleném konkrétními oprávněními naleznete v [tématu Oprávnění centra IoT Hub](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Směrování zpráv pro centrum IoT

Kliknutím na **Směrování zpráv** v části **Zasílání zpráv** zobrazíte podokno Směrování zpráv, ve kterém definujete trasy a vlastní koncové body pro rozbočovač. [Směrování zpráv](iot-hub-devguide-messages-d2c.md) umožňuje spravovat způsob, jakým jsou data odesílána ze zařízení do koncových bodů. Prvním krokem je přidání nové trasy. Pak můžete přidat existující koncový bod do trasy nebo vytvořit nový z podporovaných typů, jako je například úložiště objektů blob. 

![Podokno směrování zpráv](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Trasy

Trasy jsou první záložkou v podokně Směrování zpráv. Chcete-li přidat novou trasu, klepněte na tlačítko +**Přidat**. Zobrazí se následující obrazovka. 

![Snímek obrazovky s přidáním nové trasy](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Pojmenujte svůj rozbočovač. Název musí být jedinečný v seznamu tras pro tento rozbočovač. 

V **části Koncový bod**můžete vybrat jeden z rozevíracího seznamu nebo přidat nový. V tomto příkladu je již k dispozici účet úložiště a kontejner. Pokud je chcete přidat jako koncový bod, klikněte na +**Přidat** vedle rozevíracího přehledu Koncový bod a vyberte **Úložiště objektů blob**. Na následující obrazovce se zobrazí, kde jsou určeny účet úložiště a kontejner.

![Snímek obrazovky s přidáním koncového bodu úložiště pro pravidlo směrování](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Kliknutím na **Vybrat kontejner** vyberte účet úložiště a kontejner. Po výběru těchto polí se vrátí do podokna Koncový bod. Výchozí hodnoty použijte pro zbytek polí a **Create** vytvořte koncový bod pro účet úložiště a přidejte ho do pravidel směrování.

V **části Zdroj dat**vyberte možnost Telemetrické zprávy zařízení. 

Dále přidejte směrovací dotaz. V tomto příkladu zprávy, které `level` mají vlastnost aplikace `critical` volána s hodnotou rovnou jsou směrovány do účtu úložiště.

![Snímek obrazovky s uložením nového pravidla směrování](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Chcete-li uložit pravidlo směrování, klepněte na tlačítko **Uložit.** Vrátíte se do podokna Směrování zpráv a zobrazí se nové pravidlo směrování.

### <a name="custom-endpoints"></a>Vlastní koncové body

Klikněte na kartu **Vlastní koncové body.** Zobrazí se všechny vlastní koncové body, které již byly vytvořeny. Tady můžete přidat nové koncové body nebo odstranit existující koncové body. 

> [!NOTE]
> Pokud odstraníte trasu, neodstraní koncové body přiřazené k této trase. Pokud chcete odstranit koncový bod, klikněte na kartu Vlastní koncové body, vyberte koncový bod, který chcete odstranit, a klikněte na Odstranit.
>

Další informace o vlastních koncových bodech najdete v [odkazech – koncových bodech centra IoT](iot-hub-devguide-endpoints.md).

Můžete definovat až 10 vlastních koncových bodů pro centrum IoT. 

Úplný příklad použití vlastních koncových bodů s směrováním najdete v [tématu Směrování zpráv pomocí služby IoT Hub](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Vyhledání konkrétního centra IoT hub

Tady jsou dva způsoby, jak najít konkrétní centrum IoT ve vašem předplatném:

1. Pokud znáte skupinu prostředků, do které centrum IoT patří, klikněte na **skupiny prostředků**a vyberte skupinu prostředků ze seznamu. Obrazovka skupiny prostředků zobrazuje všechny prostředky v této skupině, včetně center IoT. Klikněte na rozbočovač, pro který hledáte.

2. Klikněte na **Všechny prostředky**. V podokně **Všechny prostředky** je rozevírací seznam, který je výchozí pro `All types`. Klikněte na rozevírací `Select all`seznam, zrušte zaškrtnutí . Najděte `IoT Hub` a zkontrolujte to. Kliknutím na rozevírací seznam ho zavřete a položky se vyfiltrují a zobrazí se jenom vaše centra IoT.

## <a name="delete-the-iot-hub"></a>Odstranění centra IoT hub

Pokud chcete odstranit centrum Iot, najděte centrum IoT, které chcete odstranit, a klikněte na tlačítko **Odstranit** pod názvem centra IoT hub.

## <a name="next-steps"></a>Další kroky

Další informace o správě služby Azure IoT Hub najdete na těchto odkazech:

* [Směrování zpráv pomocí služby IoT Hub](tutorial-routing.md)
* [Metriky IoT Hubu](iot-hub-metrics.md)
* [Monitorování operací](iot-hub-operations-monitoring.md)