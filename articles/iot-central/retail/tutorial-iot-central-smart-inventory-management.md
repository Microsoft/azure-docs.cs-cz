---
title: Kurz správy IoT Smart Inventory | Microsoft Docs
description: Kurz šablony aplikace pro správu inteligentního inventáře pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 3dbb6ca64451cb60d5a8ec67ecdc528865a4438c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719079"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Kurz: nasazení a procházení šablonou aplikace pro správu inteligentního inventáře

V tomto kurzu se dozvíte, jak začít tím, že nasadíte šablonu aplikace IoT Central **správy inteligentního inventáře** . Naučíte se, jak nasadit šablonu, co je součástí okna a co byste chtěli udělat dál.

V tomto kurzu se naučíte,

> [!div class="checklist"]
> * vytvořit aplikaci pro správu inteligentních zásob 
> * Procházení aplikací 

## <a name="prerequisites"></a>Požadavky

* K nasazení této aplikace nejsou nutné žádné konkrétní požadavky.
* Doporučuje se použít předplatné Azure, ale můžete to zkusit i bez něj.

## <a name="create-smart-inventory-management-application-template"></a>Vytvořit šablonu aplikace pro správu inteligentního inventáře

Aplikaci můžete vytvořit pomocí následujících kroků.

1. Přejděte na web Azure IoT Central Správce aplikací. V levém navigačním panelu vyberte **Build (sestavit** ) a pak klikněte na kartu **maloobchod** .

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png" alt-text="Snímek obrazovky ukazující, jak vybrat šablonu aplikace pro správu inteligentního inventáře":::

2. Vyberte kartu **maloobchod** a v části **Správa inteligentního inventáře** vyberte **vytvořit aplikaci** .

3. Při **vytváření aplikace** se otevře formulář nové aplikace a vyplní se požadované podrobnosti, jak je vidět níže.
    **Název aplikace**: můžete použít výchozí navrhovaný název nebo zadat popisný název aplikace.
    **Adresa URL**: můžete použít navrhovanou výchozí adresu URL nebo zadat svou snadno jedinečnou adresu URL. V dalším kroku se doporučuje výchozí nastavení, pokud už předplatné Azure máte. Můžete začít s cenovým tarifem bezplatné zkušební verze na 7 dní a po dobu platnosti bezplatného záznamu můžete kdykoli převést na standardní cenový plán.
    **Informace o fakturaci**: ke zřízení prostředků se vyžaduje adresář, předplatné Azure a podrobnosti o oblasti.
    **Vytvořit**: v dolní části stránky vyberte vytvořit a nasaďte svoji aplikaci.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png" alt-text="Snímek obrazovky ukazující, jak vytvořit aplikaci ze šablony aplikace pro správu inteligentního inventáře":::

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png" alt-text="Snímek obrazovky znázorňující možnosti fakturace při vytváření aplikace":::

## <a name="walk-through-the-application"></a>Procházení aplikací 

### <a name="dashboard"></a>Řídicí panel 

Po úspěšném nasazení šablony aplikace je výchozím řídicím panelem portál pro správu inteligentních inventářů. Northwind obchodník je fiktivní poskytovatel inteligentního inventáře, který spravuje sklad pomocí technologie Bluetooth Low energií (BULK) a prodejnového obchodu s identifikací rádiového kmitočtu (RFID). V tomto řídicím panelu uvidíte dvě různé brány, které poskytují telemetrii o inventáři spolu s přidruženými příkazy, úlohami a akcemi, které můžete provádět. Tento řídicí panel je předem nakonfigurovaný tak, aby předvedl kritickou aktivitu operací zařízení správy inteligentních inventářů.
Řídicí panel je logicky dělený mezi dvěma různými operacemi správy zařízení brány. 
   * Datový sklad je nasazený s pevnou bránou v aplikaci v poli & tagů na paletách, aby bylo možné sledovat & inventáře trasování na větším zařízení.
   * Maloobchodní obchod se implementuje pomocí pevné brány RFID & značky RFID na úrovni položek za účelem sledování a trasování zásob ve výstupu ze Storu.
   * Zobrazit umístění brány, podrobnosti o stavu & 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky znázorňující horní polovinu managementdashboard inteligentního inventáře](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Můžete snadno sledovat celkový počet bran, aktivních a neznámých značek.
   * Můžete provádět operace správy zařízení, jako je firmware aktualizace, zakázat senzor, zapnout senzor, práh aktualizace, aktualizovat intervaly telemetrie & aktualizovat kontrakty služby zařízení.
   * Zařízení brány můžou provádět správu inventáře na vyžádání s kompletní nebo přírůstkovou prohledáním.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující spodní polovinu managementdashboard inteligentního inventáře](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Šablona zařízení
Klikněte na kartu šablony zařízení a zobrazí se model schopností brány. Model schopností je strukturovaný kolem dvou různých rozhraní **telemetrie brány & vlastností** a **příkazů brány** .

**Vlastnost telemetrie brány &** – toto rozhraní představuje veškerou telemetrii týkající se senzorů, umístění, informací o zařízení a možností vlastností, které jsou v zařízení, jako jsou prahové hodnoty brány a intervaly aktualizací.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky se šablonou zařízení brány inventáře v aplikaci](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Příkazy brány** – toto rozhraní uspořádá všechny možnosti příkazů brány.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s rozhraním příkazů brány v šabloně zařízení brány inventáře](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Pravidla
Vyberte kartu pravidla a podívejte se na dvě různá pravidla, která existují v této šabloně aplikace. Tato pravidla jsou nakonfigurovaná tak, aby se pro další šetření použila e-mailová oznámení pro operátory.

**Brána offline**: Toto pravidlo se aktivuje, pokud brána nehlásí do cloudu po dlouhou dobu. Brána může přestat reagovat z důvodu nízkého stavu baterie, ztráty připojení a stavu zařízení.

**Neznámé značky**: je velmi důležité sledovat všechny štítky & v rámci RFID přidružené k assetu. Pokud brána zjišťuje příliš mnoho neznámých značek, znamená to, že se jedná o výzvy k synchronizaci s aplikacemi pro zdrojové značky.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující seznam pravidel v managementapplication Smart Inventory](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Úlohy
Vyberte kartu úlohy pro zobrazení pěti různých úloh, které existují jako součást této šablony aplikace: můžete použít funkci Jobs k provádění operací v rámci řešení. Tady jsou uvedené úlohy správy inventáře, které používají příkazy zařízení a možnost provádět úlohy, jako je například,
   * zákaz čtenářů napříč všemi branami
   * Úprava prahové hodnoty telemetrie mezi 
   * proveďte kontrolu inventáře na vyžádání v rámci celého řešení.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující seznam úloh v managementapplication Smart Inventory](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte šablonu aplikace na stránce   >  **nastavení aplikace** pro správu a klikněte na **Odstranit**.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky ukazující, jak aplikaci odstranit, když s ní jste hotovi](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Další kroky

Další informace o správě inteligentního inventáře:

> [!div class="nextstepaction"]
> [Koncept správy inteligentních zásob](./architecture-smart-inventory-management.md)
