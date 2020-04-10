---
title: Výuka logistiky připojení IoT | Dokumenty společnosti Microsoft
description: Výukový program šablony aplikace Connected logistics pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: eac43ae68b10436b3e45452c6b1d03bec3ae4c9c
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000557"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Kurz: Nasazení a procházení šablony připojené logistické aplikace



Tento kurz vám ukáže, jak začít nasazením šablony **aplikace pro propojenou logistickou aplikaci** IoT Central. Dozvíte se, jak nasadit šablonu, co je součástí po vybalení z krabice a co budete chtít udělat dál.

V tomto tutoriálu se naučíte, jak,

* vytvořit propojenou logistickou aplikaci
* projít aplikací 

## <a name="prerequisites"></a>Požadavky

* K nasazení této aplikace nejsou vyžadovány žádné konkrétní požadavky.
* Doporučujeme mít předplatné Azure, ale můžete to dokonce zkusit i bez něj

## <a name="create-connected-logistics-application-template"></a>Vytvoření šablony aplikace propojené logistiky

Aplikaci můžete vytvořit pomocí následujících kroků

1. Přejděte na web správce aplikací Azure IoT Central. Na levém navigačním panelu vyberte **Build** a klikněte na kartu **Maloobchodní.**

    > [!div class="mx-imgBorder"]
    > ![Připojený logistický řídicí panel](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. V části **Aplikace propojená logistika** vyberte **Vytvořit aplikaci.**

3. **Vytvořit aplikaci** otevře Nový formulář žádosti a vyplnit požadované údaje, jak je uvedeno níže.
   * **Název aplikace**: můžete použít výchozí navržený název nebo zadat popisný název aplikace.
   * **URL**: Můžete použít navrhovanou výchozí adresu URL nebo zadat svou přátelskou jedinečnou zapamatovatenou adresu URL. Dále se doporučuje výchozí nastavení, pokud už máte předplatné Azure. Můžete začít s 7-denní zkušební zkušební cenový plán a zvolit převést na standardní cenový plán kdykoli před vypršením platnosti bezplatné stezky.
   * **Fakturační údaje**: K zajištění prostředků jsou nutné podrobnosti o adresáři, předplatném Azure a oblasti.
   * **Vytvořit**: Vyberte vytvořit v dolní části stránky k nasazení aplikace.

    > [!div class="mx-imgBorder"]
    > ![Připojený logistický řídicí panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![Fakturační údaje připojené logistiky](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Projděte si aplikaci 

## <a name="dashboard"></a>Řídicí panel

Po úspěšném nasazení šablony aplikace je výchozím řídicím panelem portál zaměřený na operátora propojené logistiky. Northwind Trader je fiktivní poskytovatel logistiky, který řídí flotilu nákladu v oceánu a na zemi. Na tomto řídicím panelu uvidíte dvě různé brány poskytující telemetrickou datovu dat o dodávkách spolu s přidruženými příkazy, úlohami a akcemi, které můžete provést. Tento řídicí panel je předem nakonfigurovaný tak, aby předváděl aktivitu operací kritických logistických zařízení.
Řídicí panel je logicky rozdělen mezi dvě různé operace správy zařízení brány, 
   * Logistická trasa pro přepravu nákladních vozidel a podrobnosti o poloze námořní zásilky jsou základním prvkem pro veškerou multimodální přepravu
   * Zobrazení stavu brány & relevantníinformace 

> [!div class="mx-imgBorder"]
> ![Připojený logistický řídicí panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * Můžete snadno sledovat celkový počet bran, aktivních a neznámých značek.
   * Můžete provádět operace správy zařízení, jako je aktualizace firmwaru, zakázat senzor, povolit senzor, aktualizovat prahovou hodnotu senzoru, aktualizovat intervaly telemetrie, & aktualizovat servisní smlouvy zařízení.
   * Zobrazit spotřebu baterie zařízení

> [!div class="mx-imgBorder"]
> ![Připojený logistický řídicí panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Šablona zařízení

Klikněte na kartu Šablony zařízení a uvidíte model schopností brány. Model schopností je strukturován kolem dvou různých rozhraní **gateway telemetrie & vlastnosta** a **gateway příkazy**

**Brána telemetrie & vlastnost** – Toto rozhraní představuje všechny telemetrie související se senzory, umístění a informace o zařízení, stejně jako schopnost vlastnosti dvojče zařízení, jako jsou prahové hodnoty senzorů & intervalech aktualizace.

> [!div class="mx-imgBorder"]
> ![Připojený logistický řídicí panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Příkazy brány** – toto rozhraní uspořádá všechny možnosti příkazů brány.

> [!div class="mx-imgBorder"]
> ![Připojený logistický řídicí panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Pravidla
Výběrem karty pravidla zobrazíte dvě různá pravidla, která existují v této šabloně aplikace. Tato pravidla jsou konfigurována pro e-mailová oznámení operátorům pro další šetření.
 
**Upozornění na krádež brány**: Toto pravidlo se aktivuje, když senzory během cesty neočekávaně detekují světlo. Operátoři musí být informováni co nejdříve vyšetřovat potenciální krádež.
 
**Nereagující brána**: Toto pravidlo se aktivuje, pokud se brána nehlásí do cloudu po delší dobu. Brána může nereagovat kvůli režimu nízkého stavu baterie, ztrátě připojení, stavu zařízení.

> [!div class="mx-imgBorder"]
> ![Připojený logistický řídicí panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Úlohy
Výběrem karty Úlohy zobrazíte pět různých úloh, které existují jako součást této šablony aplikace:

> [!div class="mx-imgBorder"]
> ![Připojený logistický řídicí panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

Funkci úloh můžete použít k operacím pro celé řešení. Zde úlohy používají příkazy zařízení a schopnost dvojčete k provádění úkolů, jako je zakázání určitých senzorů napříč celou bránou nebo úprava prahové hodnoty senzoru v závislosti na režimu zásilky a trase. 
   * Jedná se o standardní operaci, která vypíná šokové senzory během dodávky oceánu, aby se šetřila baterie nebo snížila teplotní práh během přepravy studeného řetězce. 
 
   * Úlohy umožňují provádět operace v rámci celého systému, jako je aktualizace firmwaru na branách nebo aktualizace servisní smlouvy, aby zůstaly aktuální v činnostech údržby.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud nebudete pokračovat v používání této aplikace, odstraňte šablonu aplikace tak, že navštívíte**nastavení aplikace** **pro správu** > a klepněte na tlačítko **Odstranit**.

> [!div class="mx-imgBorder"]
> ![Připojený logistický řídicí panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>Další kroky
* Další informace o [konceptu propojené logistiky](./architecture-connected-logistics.md)
* Další informace o dalších [maloobchodních šablonách IoT Central](./overview-iot-central-retail.md)
* Další informace o [přehledu IoT Central](../core/overview-iot-central.md)
