---
title: Vytvoření aplikace pro průběžné monitorování pacientů s Azure IoT Central | Dokumenty společnosti Microsoft
description: Naučte se vytvářet aplikaci pro průběžné monitorování pacientů pomocí šablon aplikací Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35ac39109bfcb4dc63b738c947d2ad8caf8ac0a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021283"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Kurz: Nasazení a návod na šablonu aplikace pro průběžné monitorování pacientů



Tento kurz vám ukáže, jak jako tvůrce řešení začít nasazením šablony aplikace průběžného monitorování pacientů ioT Central. Dozvíte se, jak nasadit šablonu, co je součástí po vybalení z krabice a co můžete dělat dál.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření šablony aplikace
> * Procházení šablony aplikace

## <a name="create-an-application-template"></a>Vytvoření šablony aplikace

Přejděte na [web správce aplikací Azure IoT Central](https://apps.azureiotcentral.com/). Na levém navigačním panelu vyberte **Build** a klikněte na kartu **Zdravotní stav.** 

>[!div class="mx-imgBorder"] 
>![Manažer aplikací Zdravotnictví](media/app-manager-health.png)

Kliknutím na tlačítko **Vytvořit aplikaci** začněte vytvářet aplikaci a pak se přihlaste pomocí osobního, pracovního nebo školního účtu Microsoftu. Přejdete na stránku **Nová aplikace.**

![Vytvořit aplikaci Healthcare](media/app-manager-health-create.png)

![Vytvoření aplikace Informace o fakturaci zdravotní péče](media/app-manager-health-create-billinginfo.png)

Vytvoření aplikace:

1. Azure IoT Central automaticky navrhne název aplikace na základě vybrané šablony. Můžete přijmout tento název nebo zadat vlastní popisný název aplikace, například **Průběžné monitorování pacienta**. Azure IoT Central také generuje jedinečnou předponu URL pro vás na základě názvu aplikace. Pokud chcete, můžete tuto předponu URL změnit na něco nezapomenutelnějšího.

2. Můžete si vybrat, zda chcete vytvořit aplikaci pomocí *bezplatného* cenového plánu nebo jednoho ze *standardních* cenových plánů. Aplikace, které vytvoříte pomocí bezplatného plánu, jsou zdarma po dobu sedmi dnů před vypršením jejich platnosti a umožňují až pět bezplatných zařízení. Aplikaci můžete přesunout z bezplatného plánu do standardního cenového plánu kdykoli před vypršením jeho platnosti. Pokud zvolíte bezplatný plán, musíte zadat kontaktní údaje a zvolit, zda chcete od společnosti Microsoft dostávat informace a tipy. Aplikace, které vytvoříte pomocí standardního plánu, podporují až dvě bezplatná zařízení a vyžadují zadání informací o předplatném Azure pro fakturaci.

3. Vyberte **Vytvořit** v dolní části stránky pro nasazení aplikace.

## <a name="walk-through-the-application-template"></a>Procházení šablony aplikace

### <a name="dashboards"></a>Řídicí panely

Po nasazení šablony aplikace se nejprve dostanete na **řídicí panel monitorování pro hospitalitivně Lamna**. Lamna Zdravotnictví je fiktivní nemocniční systém, který obsahuje dvě nemocnice: Woodgrove nemocnice a Burkville nemocnice. Na tomto řídicím panelu operátora pro nemocnici Woodgrove se zobrazí informace a telemetrie o zařízeních v této šabloně spolu se sadou příkazů, úloh a akcí, které můžete provést. Z palubní desky můžete:

* Podívejte se na telemetrii zařízení a vlastnosti, jako je **úroveň baterie** vašeho zařízení nebo jeho stav **připojení.**

* Prohlédněte si **půdorys** a umístění zařízení Smart Vitals Patch.

* **Znovu zřídit** Smart Vitals Patch pro nového pacienta.

* Podívejte se na příklad **řídicího panelu poskytovatele,** který může tým nemocniční péče sledovat své pacienty.

* Změňte **stav pacienta** zařízení a označte, zda je zařízení používáno pro trpělivý nebo vzdálený scénář.

>[!div class="mx-imgBorder"] 
>![Lamna hospitalizován](media/lamna-in-patient.png)

Můžete také kliknout na **Přejít na vzdálenou řídicí panel pacienta** a podívat se na druhý palubní panel operátora používaný pro nemocnici Burkville. Tento řídicí panel obsahuje podobnou sadu akcí, telemetrie a informace. Kromě toho můžete vidět více používaných zařízení a mají možnost **aktualizovat firmware** na každém.

>[!div class="mx-imgBorder"] 
>![Lamna dálkový ovladač](media/lamna-remote.png)

Na obou řídicích panelech můžete vždy odkazovat zpět na tuto dokumentaci.

### <a name="device-templates"></a>Šablony zařízení

Pokud kliknete na kartu **Šablony zařízení,** uvidíte, že existují dva různé typy zařízení, které jsou součástí šablony:

* **Smart Vitals Patch**: Toto zařízení představuje patch, který měří různé typy vitálních funkcí. Může být použit pro sledování pacientů v nemocnici i mimo ni. Pokud kliknete na šablonu, uvidíte, že kromě odesílání dat zařízení, jako je úroveň baterie a teplota zařízení, patch také odesílá údaje o zdravotním stavu pacienta, jako je respirační frekvence a krevní tlak.

* **Smart Knee Brace**: Toto zařízení představuje kolenní ortézu, kterou mohou pacienti použít při zotavování z operace náhrady kolenního kloubu. Pokud kliknete na tuto šablonu, uvidíte kromě dat zařízení také možnosti, jako je rozsah pohybu a zrychlení.

>[!div class="mx-imgBorder"] 
>![Šablona zařízení Smart Vitals Patch](media/smart-vitals-device-template.png)

Pokud kliknete na kartu **Skupiny zařízení,** uvidíte také, že tyto šablony zařízení pro ně automaticky vytvářejí skupiny zařízení.

### <a name="rules"></a>Pravidla

Při přechodu na kartu pravidla se v šabloně aplikace zobrazí tři pravidla:

* **Vysoká teplota ortézy**: Toto pravidlo se aktivuje,&deg;když je teplota zařízení smart knee brace větší než 95 F během 5minutového okna. Toto pravidlo můžete použít k upozornění pacienta a pečovatelského týmu a ochlazení zařízení na dálku.

* **Zjištěn pád**: Toto pravidlo se spustí, pokud je zjištěn pokles pacienta. Toto pravidlo můžete použít ke konfiguraci akce k nasazení operačního týmu, který pomůže pacientovi, který spadl.

* **Vybitá baterie**: Toto pravidlo se spustí, když úroveň nabití baterie na zařízení klesne pod 10 %. Pomocí tohoto pravidla můžete spustit oznámení pacientovi o nabití zařízení.

>[!div class="mx-imgBorder"] 
>![Vysoké pravidlo pro vysokou teplotu ortézy](media/brace-temp-rule.png)

### <a name="devices"></a>Zařízení

Klikněte na kartu **Zařízení** a vyberte instanci **inteligentní kolenní ortézy**. Uvidíte, že existují tři zobrazení, která mají prozkoumat informace o konkrétním zařízení, které jste vybrali. Tato zobrazení se vytvářejí a publikují při vytváření šablony zařízení pro vaše zařízení, což znamená, že budou konzistentní na všech zařízeních, která připojíte nebo simulujete.

Zobrazení **řídicího panelu** poskytuje přehled telemetrie a vlastnosti, které pocházejí ze zařízení, které jsou orientované na operátora.

Karta **Vlastnosti** vám umožní upravovat vlastnosti cloudu a vlastnosti zařízení pro čtení a zápis.

Karta **Příkazy** vám umožní spouštět příkazy, které byly modelovány jako součást šablony zařízení.

>[!div class="mx-imgBorder"] 
>![Zobrazení kolenní ortézy](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v používání této aplikace, odstraňte aplikaci tak, že navštívíte **nastavení aplikace Správa >** a klepněte na tlačítko **Odstranit**.

>[!div class="mx-imgBorder"] 
>![Odstranit aplikaci](media/admin-delete.png)

## <a name="next-steps"></a>Další kroky

Přejdete k dalšímu článku a zjistěte, jak vytvořit řídicí panel zprostředkovatele, který se připojí k vaší aplikaci IoT Central.

> [!div class="nextstepaction"]
> [Vytvoření řídicího panelu poskytovatele](howto-health-data-triage.md)