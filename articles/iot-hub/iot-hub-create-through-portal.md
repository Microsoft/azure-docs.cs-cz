---
title: Vytvoření IoT Hub pomocí Azure Portal | Microsoft Docs
description: Jak vytvářet, spravovat a odstraňovat centra IoT Azure pomocí Azure Portal. Obsahuje informace o cenových úrovních, škálování, zabezpečení a konfiguraci zpráv.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 0a54d0c85902915d2ee62acd8a1d38b8db8b221c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536041"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Vytvoření centra IoT pomocí Azure Portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Tento článek popisuje, jak vytvořit a spravovat centra IoT pomocí [Azure Portal](https://portal.azure.com).

Pokud chcete použít kroky v tomto kurzu, budete potřebovat předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Změna nastavení centra IoT Hub

Nastavení stávajícího centra IoT Hub můžete změnit po jeho vytvoření z podokna IoT Hub.

![Snímek obrazovky s nastavením pro Centrum IoT](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Tady jsou některé z vlastností, které můžete nastavit pro Centrum IoT:

**Ceny a škálování** : tuto vlastnost můžete použít k migraci na jinou úroveň nebo nastavení počtu IoT Hubch jednotek. 

**Monitorování operací** : Zapněte nebo vypněte různé kategorie monitorování, například protokolování událostí souvisejících se zprávami typu zařízení-Cloud nebo zpráv typu cloud-zařízení.

**Filtr IP** : zadejte rozsah IP adres, které bude služba IoT Hub přijímat nebo odmítat.

**Vlastnosti** : poskytuje seznam vlastností, které můžete zkopírovat a použít jinde, například ID prostředku, skupinu prostředků, umístění atd.

### <a name="shared-access-policies"></a>Zásady sdíleného přístupu

Seznam zásad sdíleného přístupu můžete taky zobrazit nebo upravit kliknutím na **zásady sdíleného přístupu** v části **Nastavení** . Tyto zásady definují oprávnění pro zařízení a služby pro připojení k IoT Hub. 

Kliknutím na **Přidat** otevřete okno **Přidat zásadu sdíleného přístupu** .  Můžete zadat nový název zásady a oprávnění, která chcete k této zásadě přidružit, jak je znázorněno na následujícím obrázku:

![Snímek obrazovky znázorňující přidání zásady sdíleného přístupu](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* Zásady **čtení** a **zápisu** registru udělují oprávnění ke čtení a zápisu do registru identit. Tato oprávnění využívají back-endové cloudové služby ke správě identit zařízení. Výběr možnosti pro zápis automaticky zvolí možnost čtení.

* Zásady **služby Connect** udělují oprávnění pro přístup k koncovým bodům služby. Toto oprávnění využívají back-endové cloudové služby k posílání a přijímání zpráv ze zařízení a také k aktualizaci a čtení dvojitě vysílaných dat zařízení a modulu.

* Zásady **připojení zařízení** udělují oprávnění k posílání a přijímání zpráv pomocí koncových bodů na straně zařízení IoT Hub. Tato oprávnění používají zařízení k posílání a přijímání zpráv ze služby IoT Hub, k aktualizaci a čtení dvojitě využívaných dat zařízení a k provádění nahrávání souborů.

Kliknutím na **vytvořit** přidejte tuto nově vytvořenou zásadu do existujícího seznamu.

Podrobnější informace o přístupu uděleného konkrétními oprávněními najdete v tématu [IoT Hub oprávnění](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Směrování zpráv pro službu IoT Hub

Kliknutím na **směrování zpráv** v části **zasílání zpráv** zobrazíte podokno směrování zpráv, kde můžete definovat trasy a vlastní koncové body pro centrum. [Směrování zpráv](iot-hub-devguide-messages-d2c.md) umožňuje spravovat, jak se data z vašich zařízení odesílají do koncových bodů. Prvním krokem je přidání nové trasy. Pak můžete přidat existující koncový bod do trasy nebo vytvořit nový z podporovaných typů, jako je třeba úložiště objektů BLOB. 

![Podokno směrování zpráv](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Trasy

Trasy jsou první kartou v podokně směrování zpráv. Pokud chcete přidat novou trasu, klikněte na + **Přidat** . Zobrazí se následující obrazovka. 

![Snímek obrazovky znázorňující přidání nové trasy](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Pojmenujte trasu. Název trasy musí být jedinečný v rámci seznamu tras pro toto centrum. 

V případě **koncového bodu** můžete vybrat jeden z rozevíracího seznamu nebo přidat nový. V tomto příkladu je už dostupný účet úložiště a kontejner. Pokud je chcete přidat jako koncový bod, klikněte na + **Přidat** vedle rozevíracího seznamu koncových bodů a vyberte **BLOB Storage** . Na následující obrazovce vidíte, kde je zadaný účet úložiště a kontejner.

![Snímek obrazovky znázorňující přidání koncového bodu úložiště pro pravidlo směrování](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Klikněte na **Vybrat kontejner** a vyberte účet úložiště a kontejner. Když jste tato pole vybrali, vrátí se do podokna koncového bodu. Použijte výchozí hodnoty pro zbytek polí a **vytvořte** pro vytvoření koncového bodu pro účet úložiště a přidejte ho do pravidel směrování.

V případě **zdroje dat** vyberte zprávy telemetrie zařízení. 

Pak přidejte dotaz směrování. V tomto příkladu jsou zprávy, které mají vlastnost aplikace nazvané `level` s hodnotou rovnou, `critical` směrovány do účtu úložiště.

![Snímek obrazovky, který ukazuje uložení nového pravidla směrování](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Kliknutím na **Uložit** uložte pravidlo směrování. Vrátíte se do podokna směrování zpráv a zobrazí se nové pravidlo směrování.

### <a name="custom-endpoints"></a>Vlastní koncové body

Klikněte na kartu **vlastní koncové body** . Zobrazí se již vytvořené vlastní koncové body. Odsud můžete přidat nové koncové body nebo odstranit existující koncové body. 

> [!NOTE]
> Pokud odstraníte trasu, neodstraní se koncové body přiřazené k této trase. Pokud chcete odstranit koncový bod, klikněte na kartu vlastní koncové body, vyberte koncový bod, který chcete odstranit, a klikněte na Odstranit.
>

Další informace o vlastních koncových bodech si můžete přečíst v tématu [referenční koncové body centra IoT Hub](iot-hub-devguide-endpoints.md).

Pro Centrum IoT můžete definovat až 10 vlastních koncových bodů. 

Úplný příklad použití vlastních koncových bodů se směrováním najdete v tématu [směrování zpráv pomocí IoT Hub](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Vyhledání konkrétního centra IoT Hub

Tady jsou dva způsoby, jak v předplatném najít konkrétní centrum IoT:

1. Pokud znáte skupinu prostředků, do které Centrum IoT patří, klikněte na **skupiny prostředků** a pak v seznamu vyberte skupinu prostředků. Na obrazovce skupina prostředků se zobrazí všechny prostředky v této skupině, včetně centra IoT. Klikněte na centrum, pro které hledáte.

2. Klikněte na **Všechny prostředky** . V podokně **všechny prostředky** je rozevírací seznam, který je ve výchozím nastavení nastaven jako výchozí `All types` . Klikněte na rozevírací seznam a zrušte jeho kontrolu `Select all` . Vyhledejte `IoT Hub` ho a podívejte se na něj. Kliknutím na rozevírací seznam ho zavřete a položky se vyfiltrují, aby se zobrazily jenom vaše centra IoT.

## <a name="delete-the-iot-hub"></a>Odstranit centrum IoT

Pokud chcete odstranit centrum IoT, najděte centrum IoT, které chcete odstranit, a potom klikněte na tlačítko **Odstranit** pod názvem služby IoT Hub.

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o správě IoT Hub Azure, postupujte podle těchto odkazů:

* [Směrování zpráv pomocí IoT Hub](tutorial-routing.md)
* [Monitorování služby IoT Hub](monitor-iot-hub.md)
