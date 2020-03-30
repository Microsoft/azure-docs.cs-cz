---
title: Výuka digitálního distribučního centra IoT | Dokumenty společnosti Microsoft
description: Výukový program šablony aplikace digitálního distribučního centra pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 93a77d73b5cc249c39609f98f055a7b1927dd6ed
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77025482"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Kurz: Nasazení a procházení šablony aplikace digitálního distribučního centra



V tomto kurzu se můžete začít nasazovat šablonu aplikace **digitálního distribučního centra** IoT Central. Dozvíte se, jak nasadit šablonu, co je součástí po vybalení z krabice a co budete chtít udělat dál.

V tomto tutoriálu se naučíte, jak, 
* Vytvoření aplikace digitálního distribučního centra 
* Projděte si aplikaci 

## <a name="prerequisites"></a>Požadavky
* K nasazení této aplikace nejsou vyžadovány žádné konkrétní požadavky.
* Doporučujeme mít předplatné Azure, ale můžete to dokonce zkusit i bez něj

## <a name="create-digital-distribution-center-application-template"></a>Vytvoření šablony aplikace digitálního distribučního centra

Aplikaci můžete vytvořit pomocí následujících kroků

1. Přejděte na web správce aplikací Azure IoT Central. Na levém navigačním panelu vyberte **Build** a klikněte na kartu **Maloobchodní.**

    > [!div class="mx-imgBorder"]
    > ![Centrum digitální distribuce](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Vyberte karta **Maloobchodní** a v části **Aplikace digitálního distribučního centra** vyberte Vytvořit **aplikaci.**

3. **Vytvořit aplikaci** otevře Nový formulář žádosti a vyplnit požadované údaje, jak je uvedeno níže.
   **Název aplikace**: můžete použít výchozí navržený název nebo zadat popisný název aplikace.
   **URL**: Můžete použít navrhovanou výchozí adresu URL nebo zadat svou přátelskou jedinečnou zapamatovatenou adresu URL. Dále se doporučuje výchozí nastavení, pokud už máte předplatné Azure. Můžete začít s 7-denní zkušební zkušební cenový plán a zvolit převést na standardní cenový plán kdykoli před vypršením platnosti bezplatné stezky.
   **Fakturační údaje**: K zajištění prostředků jsou nutné podrobnosti o adresáři, předplatném Azure a oblasti.
   **Vytvořit**: Vyberte vytvořit v dolní části stránky k nasazení aplikace.

    > [!div class="mx-imgBorder"]
    > ![Centrum digitální distribuce](./media/tutorial-iot-central-ddc/ddc-create.png)

    > [!div class="mx-imgBorder"]
    > ![Fakturační údaje o digitální distribuci](./media/tutorial-iot-central-ddc/ddc-create-billinginfo.png)

## <a name="walk-through-the-application-dashboard"></a>Procházení řídicího panelu aplikace 

Po úspěšném nasazení šablony aplikace je výchozím řídicím panelem portál zaměřený na operátora distribučního centra. Northwind Trader je fiktivní poskytovatel řešení distribučních center, který řídí dopravníkové systémy. 

Na tomto řídicím panelu uvidíte jednu bránu a jednu kameru, která funguje jako zařízení IoT. Brána poskytuje telemetrická data o balíčcích, jako jsou platné, neplatné, neidentifikované a velikost spolu s přidruženými vlastnostmi dvojčete zařízení. Všechny příkazy po proudu jsou prováděny na zařízeních IoT, jako je například kamera. Tento řídicí panel je předem nakonfigurovaný tak, aby předváděl aktivitu operací zařízení kritického distribučního centra.

Řídicí panel je logicky uspořádaný tak, aby zobrazoval možnosti správy zařízení brány Azure IoT a zařízení IoT.  
   * Můžete provádět příkazy brány & řídicí úlohy
   * Spravujte všechny kamery, které jsou součástí řešení. 

> [!div class="mx-imgBorder"]
> ![Centrum digitální distribuce](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Šablona zařízení

Klikněte na kartu Šablony zařízení a uvidíte model schopností brány. Model schopností je strukturován kolem dvou různých rozhraní **Fotoaparát** a **Digitální distribuční brána**

> [!div class="mx-imgBorder"]
> ![Centrum digitální distribuce](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Kamera** - Toto rozhraní uspořádá všechny funkce příkazu specifické pro kameru 

> [!div class="mx-imgBorder"]
> ![Centrum digitální distribuce](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Brána digitální distribuce** – toto rozhraní představuje veškerou telemetrii pocházející z kamery, vlastností dvojčete zařízení definovaných v cloudu a informací o bráně.

> [!div class="mx-imgBorder"]
> ![Centrum digitální distribuce](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Příkazy brány
Toto rozhraní uspořádá všechny možnosti příkazu brány.

> [!div class="mx-imgBorder"]
> ![Centrum digitální distribuce](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Pravidla
Výběrem karty pravidla zobrazíte dvě různá pravidla, která existují v této šabloně aplikace. Tato pravidla jsou konfigurována pro e-mailová oznámení operátorům pro další šetření.

 **Výstraha příliš mnoho neplatných balíčků** – toto pravidlo se aktivuje, když kamera zjistí vysoký počet neplatných balíků protékajících dopravníkovým systémem.
 
**Velký balíček** - Toto pravidlo se spustí, pokud fotoaparát detekuje obrovský balíček, který nelze zkontrolovat na kvalitu. 

> [!div class="mx-imgBorder"]
> ![Centrum digitální distribuce](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Úlohy
Vyberte kartu Úlohy, chcete-li zobrazit pět různých úloh, které existují jako součást této šablony aplikace: Funkci úloh můžete využít k provádění operací pro celé řešení. Zde digitální distribuční centrum úlohy používají příkazy zařízení & twin schopnost provádět úkoly, jako je,
   * kalibraci kamery před zahájením detekce balíku 
   * pravidelná aktualizace firmwaru fotoaparátu
   * úprava intervalu telemetrie pro správu nahrávání dat

> [!div class="mx-imgBorder"]
> ![Centrum digitální distribuce](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud nebudete pokračovat v používání této aplikace, odstraňte šablonu aplikace tak, že navštívíte**nastavení aplikace** **pro správu** > a klepněte na tlačítko **Odstranit**.

> [!div class="mx-imgBorder"]
> ![Centrum digitální distribuce](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Další kroky
* Další informace o konceptu digitálního distribučního centra architektury řešení [digitálních distribučních center](./architecture-digital-distribution-center-pnp.md)
* Další informace o dalších [maloobchodních šablonách IoT Central](./overview-iot-central-retail-pnp.md)
* Další informace o IoT Central najdete v [přehledu IoT Central](../core/overview-iot-central.md)
