---
title: Vytvoření IoT Hubu pomocí webu Azure portal | Dokumentace Microsoftu
description: Jak vytvářet, spravovat a odstraňovat Azure IoT hub na webu Azure portal. Obsahuje informace o cenových úrovních, škálování, zabezpečení a konfigurace pro zasílání zpráv.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: 8f08141f5c14a734f89ba91045767e2a36a44fd2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985601"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Vytvoření IoT hubu pomocí webu Azure portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Tento článek popisuje, jak vytvořit a spravovat pomocí centra IoT hub [webu Azure portal](https://portal.azure.com).

Pokud chcete použít kroky v tomto kurzu, potřebujete předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Zvolte +**vytvořit prostředek**, klikněte na tlačítko **Internet of Things**.

3. Klikněte na tlačítko **služby Iot Hub** ze seznamu na pravé straně. Zobrazí se první obrazovka pro vytvoření služby IoT hub.

   ![Snímek obrazovky znázorňující vytvoření Centrum na webu Azure Portal](./media/iot-hub-create-through-portal/iot-hub-create-screen-basics.png)

   Vyplňte jednotlivá pole.

   **Předplatné**: Vyberte předplatné, které chcete použít pro službu IoT hub.

   **Skupina prostředků**: můžete vytvořit novou skupinu prostředků nebo použijte již existující. Chcete-li vytvořit nový, klikněte na tlačítko **vytvořit nový** a vyplňte název, který chcete použít. Chcete-li použít existující skupinu prostředků, klikněte na tlačítko **použít existující** a z rozevíracího seznamu vyberte skupinu prostředků.

   **Oblast**: Vyberte oblast, ve kterém chcete centrem umístit z rozevíracího seznamu.

   **Název centra IoT**: vložte název služby IoT hub. Tento název musí být globálně jedinečný. 

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. Klikněte na tlačítko **Další: dimenzování a škálování** přejdete na další obrazovce.

   ![Snímek obrazovky zobrazující nastavení velikosti a měřítka novou službu IoT hub pomocí webu Azure portal](./media/iot-hub-create-through-portal/iot-hub-create-screen-size-scale.png)

   Na této obrazovce můžete přijmout výchozí hodnoty a stačí kliknout na **revize + vytvořit** v dolní části. Nebo můžete přejít k vyplnění polí podle potřeby.

   **Úroveň cen a škálování**: můžete si vybrat z několika vrstev v závislosti na tom, kolik funkce chcete a tom, kolik zpráv, můžete odeslat prostřednictvím řešení za den. Bezplatná úroveň slouží pro testování a vyhodnocení. Umožňuje 500 zařízení k připojení ke službě IoT hub a až 8 000 zpráv denně. Každé předplatné Azure můžete vytvořit v jednom centru IoT v bezplatné úrovni. 

   **Jednotky služby IoT Hub**: počet zpráv povolených na jednotku a den závisí na cenové úrovni vašeho centra. Například pokud chcete pro podporu příchozího přenosu dat 700 000 zpráv služby IoT hub, zvolit dvě jednotky úrovně S1.

   Podrobnosti o další úroveň možnosti najdete v tématu [výběru správné úrovně služby IoT Hub](iot-hub-scaling.md).

   **Pokročilé / typu zařízení cloud oddíly**: tuto vlastnost souvisí s počtem souběžných čtenářů zpráv zpráv typu zařízení cloud. Většina centra IoT hub stačí jenom čtyři oddíly. 

5. Klikněte na tlačítko **zkontrolujte + vytvořit** zkontrolujte zvolené volby. Vypadá podobně jako tato obrazovka.

   ![Snímek obrazovky zkontrolování informací pro vytvoření nového centra IoT](./media/iot-hub-create-through-portal/iot-hub-create-review.png)

5. Klikněte na tlačítko **vytvořit** vytvořit novou službu IoT hub. Vytváří se Centrum trvá několik minut.

## <a name="change-the-settings-of-the-iot-hub"></a>Změna nastavení služby IoT hub

Nastavení stávající služby IoT hub můžete změnit poté, co je vytvořen z podokna služby IoT Hub.

![Snímek obrazovky zobrazující nastavení pro službu IoT hub](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Tady jsou některé vlastnosti, které můžete nastavit pro službu IoT hub:

**Ceny a škálování**: Tato vlastnost slouží k migraci na jinou úroveň nebo nastavit počet služby IoT Hub jednotek. 

**Monitorování operací**: zapnout nebo vypnout, různé kategorie monitorování jako je například protokolování událostí souvisejících s zpráv typu zařízení cloud nebo zprávy typu cloud zařízení.

**Filtr IP**: určit rozsah IP adres, které budou přijímat nebo odmítat službou IoT hub.

**Vlastnosti**: poskytuje seznam vlastností, můžete zkopírovat a použít jinde, jako je například ID prostředku, skupiny prostředků, umístění a tak dále.

### <a name="shared-access-policies"></a>Zásady sdíleného přístupu

Můžete také zobrazit nebo upravit seznam zásady sdíleného přístupu kliknutím **zásady sdíleného přístupu** v **nastavení** oddílu. Tyto zásady definovat oprávnění pro zařízení a služby pro připojení ke službě IoT Hub. 

Klikněte na tlačítko **přidat** otevřít **přidat zásady sdíleného přístupu** okno.  Můžete zadat nový název zásady a oprávnění, které chcete přidružit k této zásadě, jak je znázorněno na následujícím obrázku:

![Snímek obrazovky Přidání zásady sdíleného přístupu](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* **Čtení registru** a **zápis do registru** zásady udělovat oprávnění ke čtení a zápis do registru identit. Pokud vyberete možnost zápisu automaticky zvolí možnost čtení.

* **Služba připojit** zásady udělí oprávnění pro přístup ke koncovým bodům služby, jako **příjem typu zařízení cloud**. 

* **Zařízení připojit** zásady udělí oprávnění pro odesílání a příjem zpráv pomocí koncových bodů na straně zařízení služby IoT Hub.

Klikněte na tlačítko **vytvořit** přidáte tuto nově vytvořenou zásadu do existujícího seznamu.

## <a name="message-routing-for-an-iot-hub"></a>Směrování zpráv pro službu IoT hub

Klikněte na tlačítko **směrování zpráv** pod **zasílání zpráv** zobrazíte podokně směrování zpráv, ve kterém můžete definovat trasy a vlastní koncové body pro rozbočovač. [Směrování zpráv](iot-hub-devguide-messages-d2c.md) umožňuje spravovat jak odeslání dat ze zařízení do koncových bodů. Prvním krokem je přidání novou trasu. Potom můžete přidat existující koncový bod na trase, nebo vytvořte novou typy podporované, jako je například úložiště objektů blob. 

![Podokno směrování zpráv](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Trasy

Trasy je na první kartě v podokně směrování zpráv. Chcete-li přidat novou trasu, klikněte na tlačítko +**přidat**. Zobrazí se následující obrazovka. 

![Snímek obrazovky ukazující, přidává se nová cesta.](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Pojmenujte svoje centrum. Název musí být jedinečný v rámci seznamu trasy pro daný rozbočovač. 

Pro **koncový bod**, můžete vybrat jednu z rozevíracího seznamu nebo přidat nový. V tomto příkladu účtu úložiště a kontejneru jsou už k dispozici. Chcete-li přidat jako koncový bod, klikněte na tlačítko +**přidat** vedle rozevíracího seznamu koncových bodů a vyberte **úložiště objektů Blob**. Následující obrazovka ukazuje, kde zadaný účet úložiště a kontejner.

![Snímek obrazovky přidání koncový bod úložiště pro pravidlo směrování](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Klikněte na tlačítko **vyberte kontejner** vybrat účet úložiště a kontejner. Pokud jste vybrali těchto polí, vrátí koncový bod. Použít výchozí hodnoty pro zbývající pole a **vytvořit** vytvořte koncový bod pro účet úložiště a přidat ho do pravidla směrování.

Pro **zdroj dat**, vyberte zařízení telemetrické zprávy. 

V dalším kroku přidáte směrování dotazu. V tomto příkladu, zprávy, které mají aplikaci vlastnost s názvem `level` s hodnotou rovná `critical` jsou směrovány do účtu úložiště.

![Snímek obrazovky zobrazující uložení nové pravidlo směrování](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Klikněte na tlačítko **Uložit** uložte pravidlo směrování. Vraťte do podokna směrování zpráv a zobrazí se nové pravidlo směrování.

### <a name="custom-endpoints"></a>Vlastní koncové body

Klikněte na tlačítko **vlastní koncové body** kartu. Zobrazí všechny vlastní koncové body už vytvořili. Odsud můžete přidat nové koncové body nebo odstranit stávající koncové body. 

> [!NOTE]
> Pokud odstraníte trasu, neodstraní koncových bodů přiřazené pro danou trasu. Pokud chcete odstranit koncový bod, klikněte na vlastní kartu koncové body, vyberte koncový bod, který chcete odstranit a klikněte na příkaz odstranit.
>

Další informace o vlastních koncových bodech v [Reference – koncové body IoT hubu](iot-hub-devguide-endpoints.md).

Můžete definovat až 10 vlastní koncové body pro službu IoT hub. 

Úplný příklad, jak používat vlastní koncové body se směrováním najdete v tématu [zpráva směrovací službou IoT Hub](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Najít konkrétní IoT hubu

Tady jsou dva způsoby, jak najít konkrétní služby IoT hub v rámci vašeho předplatného:

1. Pokud je, že prostředek skupiny, ke které patří službě IoT hub, klikněte na tlačítko **skupiny prostředků**, pak vyberte skupinu prostředků ze seznamu. Na obrazovce skupiny prostředků se zobrazí všechny prostředky v této skupině, včetně centra IoT hub. Klikněte na centrum, pro který potřebujete.

2. Klikněte na tlačítko **všechny prostředky**. Na **všechny prostředky** podokně je rozevírací seznam s výchozím `All types`. Klikněte na rozevírací seznam, zrušte zaškrtnutí políčka `Select all`. Najít `IoT Hub` a zkontrolujte ji. Klikněte na možnost v rozevíracím seznamu ho zavřete a položky se dá filtrovat, zobrazuje pouze vašeho centra IoT hub.

## <a name="delete-the-iot-hub"></a>Odstranit centrum IoT

Pokud chcete odstranit centrum Iot, najít IoT hub, kterou chcete odstranit a pak klikněte na tlačítko **odstranit** pod název centra IoT.

## <a name="next-steps"></a>Další postup

Další informace o správě služby Azure IoT Hub na následujících odkazech:

* [Směrování zpráv pomocí služby IoT Hub](tutorial-routing.md)
* [Metriky služby IoT Hub](iot-hub-metrics.md)
* [Monitorování operací](iot-hub-operations-monitoring.md)