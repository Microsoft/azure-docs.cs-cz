---
title: Kurz spojený s logistikou IoT | Microsoft Docs
description: Kurz připojené šablony logistické aplikace pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 7c6c50b6fd8200726aa11f120d9748cc1b40aa22
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831616"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Kurz: nasazení a procházení prostřednictvím připojené šablony logistické aplikace

V tomto kurzu se dozvíte, jak začít s šablonou aplikace IoT Central *připojené k logistické* aplikaci. Naučíte se, jak nasadit a používat šablonu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte připojenou logistickou aplikaci.
> * Použijte klíčové funkce v aplikaci.
> * Pomocí řídicího panelu můžete zobrazit aktivitu kritických operací logistického zařízení.
> * Použít šablonu zařízení
> * Sledovat pravidla
> * Použití úloh

## <a name="prerequisites"></a>Požadavky

* K nasazení této aplikace nejsou nutné žádné konkrétní požadavky.
* Můžete použít bezplatný cenový plán nebo použít předplatné Azure.

## <a name="create-connected-logistics-application"></a>Vytvořit připojenou logistickou aplikaci

Vytvořte aplikaci pomocí následujících kroků:

1. Přejděte na web [Azure IoT Central Build](https://aka.ms/iotcentral) . Pak se přihlaste pomocí osobního, pracovního nebo školního účtu Microsoft. V levém navigačním panelu vyberte **Build (sestavit** ) a pak vyberte kartu **maloobchod** :

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png" alt-text="Šablona spojené logistiky":::

1. V části **připojená logistická aplikace** vyberte **vytvořit aplikaci** .

1. **Vytvoření aplikace** otevře formulář **nové aplikace** . Zadejte následující podrobnosti:


    * **Název aplikace**: můžete použít výchozí navrhovaný název nebo zadat popisný název aplikace.
    * **Adresa URL**: můžete použít navrhovanou výchozí adresu URL nebo zadat svou snadno jedinečnou adresu URL. V dalším kroku se doporučuje výchozí nastavení, pokud už předplatné Azure máte. Můžete začít s cenovým tarifem bezplatné zkušební verze na 7 dní a po dobu platnosti bezplatného záznamu můžete kdykoli převést na standardní cenový plán.
    * **Informace o fakturaci**: ke zřízení prostředků se vyžaduje adresář, předplatné Azure a podrobnosti o oblasti.
    * **Vytvořit**: v dolní části stránky vyberte vytvořit a nasaďte svoji aplikaci.

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png" alt-text="Šablona připojené logistické aplikace":::

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png" alt-text="Informace o fakturaci pro Spojené logistiky":::

## <a name="walk-through-the-application"></a>Procházení aplikací

Tady je snímek obrazovky, který ukazuje, jak vybrat šablonu připojené logistické aplikace.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky ukazující, jak vybrat šablonu připojené logistické aplikace](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

Následující části vás seznámí s klíčovými funkcemi aplikace.

### <a name="dashboard"></a>Řídicí panel

Po nasazení šablony aplikace je výchozím řídicím panelem připojený portál logistiky. Northwind obchodník je fiktivní logistický poskytovatel, který spravuje nákladní loďstvo na moři a na půdě. V tomto řídicím panelu uvidíte dvě různé brány, které poskytují telemetrii z dodávek, spolu s přidruženými příkazy, úlohami a akcemi.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky ukazující, jak vytvořit aplikaci ze šablony připojené logistické aplikace](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky znázorňující možnosti fakturace při vytváření aplikace](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

Tento řídicí panel je předem nakonfigurovaný tak, aby zobrazoval aktivitu kritických operací logistického zařízení.

Řídicí panel umožňuje dvě různé operace správy zařízení brány:

* Zobrazit logistické postupy pro dodávky nákladní přepravy a podrobnosti o poloze dodávek v oceánu.
* Zobrazení stavu brány a dalších relevantních informací.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png" alt-text="Řídicí panel připojené logistiky":::

* Můžete sledovat celkový počet bran, aktivních a neznámých značek.
* Můžete provádět operace správy zařízení, jako je například firmware aktualizace, zakázat a povolit senzory, aktualizovat prahovou hodnotu snímače, aktualizovat intervaly telemetrie a aktualizovat kontrakty služby zařízení.
* Zobrazit spotřebu baterie zařízení.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png" alt-text="Stav připojeného řídicího panelu logistiky":::

#### <a name="device-template"></a>Šablona zařízení

Vyberte **šablony zařízení** a podívejte se na model schopností brány. Model schopností je strukturovaný kolem **telemetrie brány & vlastností** a rozhraní **příkazů brány** .

**Vlastnost telemetrie brány &** – toto rozhraní definuje veškerou telemetrii související s senzory, umístěním a informacemi o zařízení. Rozhraní také definuje funkce pro dopředné vlastnosti zařízení, jako jsou mezní hodnoty senzoru a intervaly aktualizací.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png" alt-text="Rozhraní telemetrie a vlastnost":::

**Příkazy brány** – toto rozhraní uspořádá všechny možnosti příkazu brány:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png" alt-text="Rozhraní příkazů brány":::

### <a name="rules"></a>Pravidla

Vyberte kartu **pravidla** pro pravidla v této šabloně aplikace. Tato pravidla jsou nakonfigurovaná tak, aby se k nim pro další šetření nakonfigurovala e-mailová oznámení:

**Výstraha o krádeži brány**: Toto pravidlo se aktivuje v případě neočekávaného zjištění světla senzory během cesty. Operátory je nutné okamžitě upozornit na prošetření potenciální krádeže.

**Nereagující bránu**: Toto pravidlo se aktivuje, pokud brána nehlásí do cloudu po dlouhou dobu. Brána může přestat reagovat z důvodu nízkého nabití baterie, ztráty připojení nebo poškození zařízení.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png" alt-text="Definice pravidel":::

### <a name="jobs"></a>Úlohy

Kliknutím na kartu **úlohy** zobrazíte úlohy v této aplikaci:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png" alt-text="Úlohy, které se mají spustit":::

Úlohy můžete použít k provádění operací v nejrůznějších aplikacích. Úlohy v této aplikaci používají příkazy zařízení a možnosti, které umožňují provádět úlohy, jako je například zakázání specifických senzorů napříč všemi branami nebo změna prahové hodnoty snímače v závislosti na způsobu dodávek a trase:

* Je to standardní operace zakázání senzorů v průběhu expedice v oceánu za účelem úspory baterie nebo nižší prahové hodnoty teploty během přepravy studených řetězů.

* Úlohy umožňují provádět operace v rámci systému, jako je například aktualizace firmwaru v bránách nebo aktualizace kontraktu služby, aby zůstaly aktuální na aktivitách údržby.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte šablonu aplikace na stránce   >  **nastavení aplikace** pro správu a vyberte možnost **Odstranit**.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png" alt-text="Vyčištění šablony":::

## <a name="next-steps"></a>Další kroky

Další informace o:

> [!div class="nextstepaction"]
> [Koncepty spojené logistiky](./architecture-connected-logistics.md)
