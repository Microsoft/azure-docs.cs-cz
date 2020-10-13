---
title: Kurz služby IoT digital Distribution Center | Microsoft Docs
description: Kurz šablony aplikace digitálního distribučního centra pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: a76df67d1ef9d203d5e5d54cb95bb8fc90f70180
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90980736"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Kurz: nasazení a procházení šablonou aplikace digitálního distribučního centra



V tomto kurzu se dozvíte, jak začít tím, že nasadíte šablonu aplikace IoT Central **digital Distribution Center** . Naučíte se, jak nasadit šablonu, co je součástí okna a co byste chtěli udělat dál.

V tomto kurzu se naučíte, 
* Vytvořit aplikaci digital Distribution Center 
* Procházení aplikací 

## <a name="prerequisites"></a>Požadavky
* K nasazení této aplikace nejsou nutné žádné konkrétní požadavky.
* Doporučuje se použít předplatné Azure, ale můžete to zkusit i bez něj.

## <a name="create-digital-distribution-center-application-template"></a>Vytvořit šablonu aplikace digitálního distribučního centra

Aplikaci můžete vytvořit pomocí následujících kroků.

1. Přejděte na web Azure IoT Central Správce aplikací. V levém navigačním panelu vyberte **Build (sestavit** ) a pak klikněte na kartu **maloobchod** .

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky ukazující, jak vybrat šablonu aplikace pro digitální distribuční centrum](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Vyberte kartu **maloobchod** a v části **aplikace Digital Distribution Center** vyberte **vytvořit aplikaci** .

3. Při **vytváření aplikace** se otevře formulář nové aplikace a vyplní se požadované podrobnosti, jak je vidět níže.
   **Název aplikace**: můžete použít výchozí navrhovaný název nebo zadat popisný název aplikace.
   **Adresa URL**: můžete použít navrhovanou výchozí adresu URL nebo zadat svou snadno jedinečnou adresu URL. V dalším kroku se doporučuje výchozí nastavení, pokud už předplatné Azure máte. Můžete začít s cenovým tarifem bezplatné zkušební verze na 7 dní a po dobu platnosti bezplatného záznamu můžete kdykoli převést na standardní cenový plán.
   **Informace o fakturaci**: ke zřízení prostředků se vyžaduje adresář, předplatné Azure a podrobnosti o oblasti.
   **Vytvořit**: v dolní části stránky vyberte vytvořit a nasaďte svoji aplikaci.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky ukazující, jak vytvořit aplikaci ze šablony aplikace digitálního distribučního centra](./media/tutorial-iot-central-ddc/ddc-create.png)

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky znázorňující možnosti fakturace při vytváření aplikace](./media/tutorial-iot-central-ddc/ddc-create-billinginfo.png)

## <a name="walk-through-the-application-dashboard"></a>Procházení řídicím panelem aplikace 

Po úspěšném nasazení šablony aplikace je výchozím řídicím panelem operátor distribučního centra, který se zaměřuje na portál. Northwind obchodník je fiktivním poskytovatelem řešení distribučního centra, které spravuje systémy dopravníků. 

V tomto řídicím panelu se zobrazí jedna brána a jedna kamera, která funguje jako zařízení IoT. Brána poskytuje telemetrii o balíčcích, jako je třeba platný, neplatný, neidentifikovaný a velikost spolu s přidruženými dvojitými vlastnostmi zařízení. Všechny příkazy pro příjem dat jsou spouštěny v zařízeních IoT, jako je třeba fotoaparát. Tento řídicí panel je předem nakonfigurovaný tak, aby předvedl kritické aktivity zařízení v distribučním centru.

Řídicí panel je logicky uspořádaný tak, aby zobrazoval možnosti správy zařízení v bráně Azure IoT Gateway a v zařízení IoT.  
   * Příkazy brány můžete provádět & úlohách řízení.
   * Spravujte všechny fotoaparáty, které jsou součástí řešení. 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s řídicím panelem digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Šablona zařízení

Klikněte na kartu šablony zařízení a zobrazí se model schopností brány. Model schopností je strukturovaný kolem dvou různých rozhraní **kamery** a **brány digitální distribuce** .

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s šablonou zařízení brány digitální distribuce v aplikaci](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Kamera** – toto rozhraní uspořádá všechny možnosti příkazu specifické pro fotoaparát. 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující rozhraní kamery v šabloně zařízení brány digitální distribuce](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Brána digitální distribuce** – toto rozhraní představuje veškerou telemetrii, která přichází z kamery, využívané vlastnosti cloudového zařízení a informace o bráně.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s rozhraním brány digitální distribuce brány v šabloně zařízení brány digitální distribuce](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Příkazy brány
Toto rozhraní uspořádá všechny možnosti příkazů brány.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s rozhraním příkazů brány v šabloně zařízení brány digitální distribuce](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Pravidla
Vyberte kartu pravidla a podívejte se na dvě různá pravidla, která existují v této šabloně aplikace. Tato pravidla jsou nakonfigurovaná tak, aby se pro další šetření použila e-mailová oznámení pro operátory.

 **Upozornění na příliš mnoho neplatných balíčků** – toto pravidlo se aktivuje, když fotoaparát detekuje vysoký počet neplatných balíčků v rámci systému dopravníkových toků.
 
**Velký balíček** – toto pravidlo se aktivuje, pokud fotoaparát detekuje velký balíček, u kterého se nedá zkontrolovat kvalita. 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující seznam pravidel v aplikaci digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Úlohy
Vyberte kartu úlohy pro zobrazení pěti různých úloh, které existují jako součást této šablony aplikace: funkce úloh můžete využít k provádění operací v rámci řešení. V rámci úlohy digitálního distribučního centra používají příkazy zařízení & zdvojené schopnosti provádět úlohy, jako například,
   * kalibrace kamery před inicializací detekce balíčku 
   * pravidelná aktualizace firmwaru fotoaparátu
   * Úprava intervalu telemetrie pro správu nahrávání dat

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující seznam úloh v aplikaci digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud nebudete tuto aplikaci nadále používat, odstraňte šablonu aplikace na stránce **Administration**  >  **nastavení aplikace** pro správu a klikněte na **Odstranit**.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky ukazující, jak aplikaci odstranit, když s ní jste hotovi](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Další kroky
* Další informace o architektuře řešení Digital Distribution Center 
> [!div class="nextstepaction"]
> [koncept centra digitálního distribuce](./architecture-digital-distribution-center.md)
* Další informace o jiných [šablonách IoT Central maloobchodních prodejů](./overview-iot-central-retail.md)
* Další informace o IoT Central najdete v tématu [IoT Central Overview](../core/overview-iot-central.md) .
