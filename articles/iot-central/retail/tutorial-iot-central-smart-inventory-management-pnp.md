---
title: Výuka řízení inventáře IoT Smart | Dokumenty společnosti Microsoft
description: Kurz šablony aplikace pro inteligentní správu zásob pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 5632d98d9d853d9a4b0882c28cad1836bb6f3cef
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77025465"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Kurz: Nasazení a procházení šablony aplikace pro inteligentní správu inventáře



V tomto kurzu se můžete začít nasazovat šablonu aplikace pro **inteligentní správu inventáře** služby IoT Central. Dozvíte se, jak nasadit šablonu, co je součástí po vybalení z krabice a co budete chtít udělat dál.

V tomto tutoriálu se naučíte, jak, 
* vytvoření aplikace pro inteligentní řízení zásob 
* projít aplikací 

## <a name="prerequisites"></a>Požadavky

* K nasazení této aplikace nejsou vyžadovány žádné konkrétní požadavky.
* Doporučujeme mít předplatné Azure, ale můžete to dokonce zkusit i bez něj

## <a name="create-smart-inventory-management-application-template"></a>Vytvoření šablony aplikace pro inteligentní řízení zásob

Aplikaci můžete vytvořit pomocí následujících kroků

1. Přejděte na web správce aplikací Azure IoT Central. Na levém navigačním panelu vyberte **Build** a klikněte na kartu **Maloobchodní.**

    > [!div class="mx-imgBorder"]
    > ![Řídicí panel inteligentní správy zásob](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. Vyberte karta **Maloobchodní** a vyberte **Vytvořit aplikaci** v části **inteligentní správa inventáře.**

3. **Vytvořit aplikaci** otevře Nový formulář žádosti a vyplnit požadované údaje, jak je uvedeno níže.
   **Název aplikace**: můžete použít výchozí navržený název nebo zadat popisný název aplikace.
   **URL**: Můžete použít navrhovanou výchozí adresu URL nebo zadat svou přátelskou jedinečnou zapamatovatenou adresu URL. Dále se doporučuje výchozí nastavení, pokud už máte předplatné Azure. Můžete začít s 7-denní zkušební zkušební cenový plán a zvolit převést na standardní cenový plán kdykoli před vypršením platnosti bezplatné stezky.
   **Fakturační údaje**: K zajištění prostředků jsou nutné podrobnosti o adresáři, předplatném Azure a oblasti.
   **Vytvořit**: Vyberte vytvořit v dolní části stránky k nasazení aplikace.

    > [!div class="mx-imgBorder"]
    > ![Řídicí panel inteligentní správy zásob](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

    > [!div class="mx-imgBorder"]
    > ![Fakturační údaje pro inteligentní správu inventáře](./media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Projděte si aplikaci 

### <a name="dashboard"></a>Řídicí panel 

Po úspěšném nasazení šablony aplikace je výchozím řídicím panelem portál zaměřený na inteligentní řízení inventáře. Northwind Trader je fiktivní poskytovatel inteligentního inventáře, který spravuje sklad s nízkoenergetickým (BLE) bluetooth (BLE) a maloobchodní prodejnu s radiofrekvenční identifikací (RFID). Na tomto řídicím panelu uvidíte dvě různé brány poskytující telemetrickou analýzu inventáře spolu s přidruženými příkazy, úlohami a akcemi, které můžete provádět. Tento řídicí panel je předem nakonfigurovaný tak, aby prezentoval důležitou aktivitu operací zařízení pro správu inteligentního inventáře.
Řídicí panel je logicky rozdělen mezi dvě různé operace správy zařízení brány, 
   * Sklad je nasazen s pevnou bránou BLE & visačkami BLE na paletách, aby bylo možné sledovat & trasování zásob ve větším zařízení.
   * Maloobchodní prodejna je implementována s pevnou RFID bránou & RFID tagy na úrovni jednotlivých položek pro sledování a sledování zásob v prodejně
   * Zobrazení umístění brány, stavu & souvisejících podrobností 

> [!div class="mx-imgBorder"]
> ![Řídicí panel inteligentní správy zásob](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Můžete snadno sledovat celkový počet bran, aktivních a neznámých značek.
   * Můžete provádět operace správy zařízení, jako je aktualizace firmwaru, zakázat senzor, povolit senzor, aktualizovat prahovou hodnotu senzoru, aktualizovat intervaly telemetrie & aktualizovat servisní smlouvy zařízení
   * Zařízení brány mohou provádět správu zásob na vyžádání pomocí úplného nebo přírůstkového prohledávání.

> [!div class="mx-imgBorder"]
> ![Řídicí panel inteligentní správy zásob](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Šablona zařízení
Klikněte na kartu Šablony zařízení a uvidíte model schopností brány. Model schopností je strukturován kolem dvou různých rozhraní **gateway telemetrie & vlastnosta** a **gateway příkazy**

**Brána telemetrie & vlastnost** – Toto rozhraní představuje všechny telemetrie související se senzory, umístění, informace o zařízení a vlastnosti dvojče zařízení, jako jsou prahové hodnoty brány a intervaly aktualizace.

> [!div class="mx-imgBorder"]
> ![Řídicí panel inteligentní správy zásob](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Příkazy brány** – toto rozhraní uspořádá všechny možnosti příkazů brány.

> [!div class="mx-imgBorder"]
> ![Řídicí panel inteligentní správy zásob](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Pravidla
Výběrem karty pravidla zobrazíte dvě různá pravidla, která existují v této šabloně aplikace. Tato pravidla jsou konfigurována pro e-mailová oznámení operátorům pro další šetření.

**Brána offline**: Toto pravidlo se aktivuje, pokud se brána nehlásí do cloudu po delší dobu. Brána může nereagovat kvůli režimu nízkého stavu baterie, ztrátě připojení, stavu zařízení.

**Neznámé značky**: Je důležité sledovat každý RFID & BLE tagy spojené s datovým zdrojem. Pokud brána detekuje příliš mnoho neznámých značek, je to indikace problémů synchronizace s aplikacemi pro získávání značek.

> [!div class="mx-imgBorder"]
> ![Řídicí panel inteligentní správy zásob](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Úlohy
Výběrem karty Úlohy zobrazíte pět různých úloh, které existují jako součást této šablony aplikace: Funkci úloh můžete použít k provádění operací pro celé řešení. Zde úlohy řízení zásob používají příkazy zařízení a schopnost dvojčete provádět úkoly, jako je
   * zakázání čtenářů ve všech branách
   * úpravou prahu telemetrie mezi 
   * provádět prohledávání zásob na vyžádání v celém řešení.

> [!div class="mx-imgBorder"]
> ![Řídicí panel inteligentní správy zásob](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v používání této aplikace, odstraňte šablonu aplikace tak, že navštívíte**nastavení aplikace** **pro správu** > a klepněte na tlačítko **Odstranit**.

> [!div class="mx-imgBorder"]
> ![Řídicí panel inteligentní správy zásob](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Další kroky
* Další informace o [inteligentním konceptu inteligentního řízení zásob pro inteligentní řízení zásob](./architecture-smart-inventory-management-pnp.md)
* Další informace o dalších [maloobchodních šablonách IoT Central](./overview-iot-central-retail-pnp.md)
* Další informace o IoT Central najdete v [přehledu IoT Central](../core/overview-iot-central.md)
