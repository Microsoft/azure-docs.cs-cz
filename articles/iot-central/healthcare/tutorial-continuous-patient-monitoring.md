---
title: Vytvoření aplikace pro monitorování nepřetržitého pacienta pomocí Azure IoT Central | Microsoft Docs
description: Naučte se vytvářet aplikace pro monitorování nepřetržitého pacienta pomocí šablon aplikací Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35ac39109bfcb4dc63b738c947d2ad8caf8ac0a6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77021283"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Kurz: nasazení a návod pro šablonu aplikace monitorování průběžného pacienta



V tomto kurzu se dozvíte, jak můžete začít tím, že nasadíte šablonu aplikace monitorování s průběžnými pacienty IoT Central. Naučíte se, jak nasadit šablonu, co je součástí okna a co můžete udělat dál.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření šablony aplikace
> * Procházení šablonou aplikace

## <a name="create-an-application-template"></a>Vytvoření šablony aplikace

Přejděte na [web Azure IoT Central Správce aplikací](https://apps.azureiotcentral.com/). V levém navigačním panelu vyberte **sestavovat** a pak klikněte na kartu **zdravotní péče** . 

>[!div class="mx-imgBorder"] 
>![](media/app-manager-health.png) péče Správce aplikací

Klikněte na tlačítko **vytvořit aplikaci** a začněte vytvářet aplikaci a pak se přihlaste pomocí osobního, pracovního nebo školního účtu Microsoft. Přejdete na stránku **Nová aplikace** .

![Vytvoření zdravotnictví pro aplikace](media/app-manager-health-create.png)

![Vytvořit informace o fakturaci zdravotní péče pro aplikace](media/app-manager-health-create-billinginfo.png)

Vytvoření aplikace:

1. Azure IoT Central automaticky navrhuje název aplikace na základě vámi vybrané šablony. Můžete přijmout tento název nebo zadat vlastní popisný název aplikace, například **monitorování nepřetržitého pacienta**. Azure IoT Central také na základě názvu aplikace vygeneruje jedinečnou předponu adresy URL. Tuto předponu adresy URL můžete změnit na něco, co byste si chtěli.

2. Můžete vybrat, jestli chcete vytvořit aplikaci pomocí cenového plánu *zdarma* nebo jednoho ze *standardních* cenových plánů. Aplikace, které vytvoříte pomocí bezplatného plánu, jsou po dobu sedmi dnů zdarma a dovolují až pět bezplatných zařízení. Aplikaci můžete kdykoli přesunout z bezplatného plánu do cenového plánu Standard. Pokud zvolíte bezplatný plán, je nutné zadat kontaktní informace a vybrat, zda chcete dostávat informace a tipy od společnosti Microsoft. Aplikace, které vytvoříte pomocí plánu Standard, podporují až dvě volná zařízení a vyžadují, abyste zadali informace o vašem předplatném Azure pro účely fakturace.

3. V dolní části stránky vyberte **vytvořit** a nasaďte svoji aplikaci.

## <a name="walk-through-the-application-template"></a>Procházení šablonou aplikace

### <a name="dashboards"></a>Řídicí panely

Po nasazení šablony aplikace se na **řídicím panelu monitorování Lamna na pacientech**stanete první půdou. Lamna zdravotnictví je fiktivní ústavní systém, který obsahuje dvě nemocnice: Woodgrove nemocnice a Burkville nemocnice. Na tomto řídicím panelu pro Woodgrove nemocnice uvidíte informace a telemetrii o zařízeních v této šabloně spolu se sadou příkazů, úloh a akcí, které můžete provést. Z řídicího panelu můžete:

* Podívejte se na telemetrii a vlastnosti zařízení, jako je například **úroveň baterie** vašeho zařízení nebo stav **připojení** .

* Prohlédněte si plán a umístění **dílenského** zařízení s opravou inteligentních zásad.

* Znovu **zajistěte** opravu pro nový pacient, která je v případě inteligentních zásad nezbytná.

* Podívejte se na příklad **řídicího panelu poskytovatele** , který může tým ústavní péče sledovat své pacienty.

* Změňte **stav pacienta** zařízení tak, aby označovalo, jestli se zařízení používá pro pacienta nebo vzdálený scénář.

>[!div class="mx-imgBorder"] 
>![Lamna v pacientech](media/lamna-in-patient.png)

Můžete také kliknout na **řídicí panel vzdáleného pacienta** a zobrazit tak druhý řídicí panel, který se používá pro Burkville nemocnice. Tento řídicí panel obsahuje podobnou sadu akcí, telemetrie a informací. Kromě toho můžete zobrazit více používaných zařízení a mít možnost **aktualizovat firmware** v každém z nich.

>[!div class="mx-imgBorder"] 
>![vzdálené](media/lamna-remote.png) Lamna

Na obou řídicích panelech můžete vždy propojit zpátky na tuto dokumentaci.

### <a name="device-templates"></a>Šablony zařízení

Pokud kliknete na kartu **šablony zařízení** , uvidíte, že existují dva různé typy zařízení, které jsou součástí šablony:

* **Oprava inteligentního zabezpečení**: Toto zařízení představuje opravu, která měří různé typy životně důležitých značek. Dá se použít k monitorování pacientů v nemocnicích i mimo něj. Pokud kliknete na šablonu, uvidíte, že kromě odesílání dat zařízení, jako je například úroveň baterie a teplota zařízení, tato oprava také odesílá data o zdravotním stavu, jako je třeba míra dýchacího a krevního tlaku.

* **Chytrá závorka kolena**: Toto zařízení představuje složenou závorku, kterou můžou pacienti použít při obnovování z karty s výměnou kolena. Pokud kliknete na tuto šablonu, uvidíte kromě dat zařízení i možnosti, jako je například rozsah pohybu a akcelerace.

>[!div class="mx-imgBorder"] 
>![oprava důležité aktualizace šablony zařízení](media/smart-vitals-device-template.png)

Pokud kliknete na kartu **skupiny zařízení** , zjistíte také, že tyto šablony zařízení mají pro ně automaticky vytvořené skupiny zařízení.

### <a name="rules"></a>Pravidla

Při přechodu na kartu pravidla se zobrazí tři pravidla, která existují v šabloně aplikace:

* **Teplota složené závorky vysoká**: Toto pravidlo se aktivuje, když je teplota zařízení v typografické závorce větší než 95&deg;F v intervalu 5 minut. Toto pravidlo můžete použít k upozornění týmu pacient a péče a k tomu, aby se zařízení vzdáleně vypnulo.

* **Zjištěná klesnout**: Toto pravidlo se aktivuje, pokud je zjištěno, že pacient je. Toto pravidlo můžete použít ke konfiguraci akce pro nasazení provozního týmu, která pomůže pacientovi, který se zařadí.

* **Oprava baterie nízká**: Toto pravidlo se aktivuje, když úroveň baterie na zařízení překročí 10%. Toto pravidlo můžete použít k aktivaci oznámení, že má pacientovi účtovat zařízení.

>[!div class="mx-imgBorder"] 
>![vysoké pravidlo teploty složených závorek](media/brace-temp-rule.png)

### <a name="devices"></a>Zařízení

Klikněte na kartu **zařízení** a pak vyberte instanci **čipové složené závorky**. Uvidíte, že existují tři zobrazení, která vám pomohou prozkoumat informace o konkrétním zařízení, které jste vybrali. Tato zobrazení jsou vytvářena a publikována při sestavování šablony zařízení pro vaše zařízení, což znamená, že budou konzistentní napříč všemi zařízeními, která jsou připojena nebo Simulovaná.

Zobrazení **řídicího panelu** poskytuje přehled telemetrie a vlastností, které pocházejí ze zařízení, které je orientované na obsluhu.

Karta **vlastnosti** vám umožní upravit vlastnosti cloudu a vlastnosti zařízení pro čtení a zápis.

Karta **příkazy** vám umožní spustit příkazy, které byly namodelované jako součást šablony zařízení.

>[!div class="mx-imgBorder"] 
>![zobrazení kolena ve složených závorkách](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte aplikaci tím, že navštívíte **správu > nastavení aplikace** a kliknete na **Odstranit**.

>[!div class="mx-imgBorder"] 
>![odstranit](media/admin-delete.png) aplikace

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak vytvořit řídicí panel poskytovatele, který se připojuje k vaší IoT Central aplikaci.

> [!div class="nextstepaction"]
> [Vytvoření řídicího panelu poskytovatele](howto-health-data-triage.md)