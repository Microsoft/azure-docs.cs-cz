---
title: Kurz – vytvoření aplikace pro monitorování nepřetržitého pacientu pomocí Azure IoT Central | Microsoft Docs
description: V tomto kurzu se naučíte vytvořit aplikaci pro monitorování nepřetržitých pacientů pomocí šablon aplikací Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 56ff43980aafc75d5936b86c6ba2cd400311c5fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719096"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Kurz: nasazení a návod pro šablonu aplikace monitorování průběžného pacienta

V tomto kurzu se dozvíte, jak můžete začít tím, že nasadíte šablonu aplikace monitorování s průběžnými pacienty IoT Central. Naučíte se, jak nasadit a používat šablonu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření šablony aplikace
> * Procházení šablonou aplikace

## <a name="prerequisites"></a>Předpoklady

Doporučuje se předplatné Azure. Alternativně můžete použít bezplatnou, 7. zkušební verzi. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).

## <a name="create-an-application-template"></a>Vytvoření šablony aplikace

Přejděte na [web Azure IoT Central Správce aplikací](https://apps.azureiotcentral.com/). V levém navigačním panelu vyberte **Build (sestavit** ) a pak vyberte kartu **zdravotní péče** .

:::image type="content" source="media/app-manager-health.png" alt-text="Šablona aplikace Healthcre":::

Vyberte tlačítko **vytvořit aplikaci** a začněte vytvářet aplikaci a pak se přihlaste pomocí osobního, pracovního nebo školního účtu Microsoft. Přejdete na stránku **Nová aplikace** .

![Vytvoření zdravotnictví pro aplikace](media/app-manager-health-create.png)

![Vytvořit informace o fakturaci zdravotní péče pro aplikace](media/app-manager-health-create-billinginfo.png)

Vytvoření aplikace:

1. Azure IoT Central automaticky navrhuje název aplikace na základě vámi vybrané šablony. Můžete přijmout tento název nebo zadat vlastní popisný název aplikace, například **monitorování nepřetržitého pacienta**. Azure IoT Central také na základě názvu aplikace vygeneruje jedinečnou předponu adresy URL. Tuto předponu adresy URL můžete změnit na něco, co byste si chtěli.

2. Můžete vybrat, jestli chcete vytvořit aplikaci pomocí cenového plánu *zdarma* nebo jednoho ze *standardních* cenových plánů. Aplikace, které vytvoříte pomocí bezplatného plánu, jsou po dobu sedmi dnů zdarma a dovolují až pět bezplatných zařízení. Aplikaci můžete kdykoli přesunout z bezplatného plánu do cenového plánu Standard. Pokud zvolíte bezplatný plán, je nutné zadat kontaktní informace a vybrat, zda chcete dostávat informace a tipy od společnosti Microsoft. Aplikace, které vytvoříte pomocí plánu Standard, podporují až dvě volná zařízení a vyžadují, abyste zadali informace o vašem předplatném Azure pro účely fakturace.

3. V dolní části stránky vyberte **vytvořit** a nasaďte svoji aplikaci.

## <a name="walk-through-the-application-template"></a>Procházení šablonou aplikace

### <a name="dashboards"></a>Řídicí panely

Po nasazení šablony aplikace se na **řídicím panelu monitorování Lamna na pacientech** stanete první půdou. Lamna zdravotnictví je fiktivní ústavní systém, který obsahuje dvě nemocnice: Woodgrove nemocnice a Burkville nemocnice. Na řídicím panelu operátoru ústavní Woodgrove můžete:

* Podívejte se na telemetrii a vlastnosti zařízení, jako je například **úroveň baterie** vašeho zařízení nebo stav **připojení** .

* Prohlédněte si plán a umístění **dílenského** zařízení s opravou inteligentních zásad.

* Znovu **zajistěte** opravu pro nový pacient, která je v případě inteligentních zásad nezbytná.

* Podívejte se na příklad **řídicího panelu poskytovatele** , který může tým ústavní péče sledovat své pacienty.

* Změňte **stav pacienta** zařízení tak, aby označovalo, jestli se zařízení používá pro pacienta nebo vzdálený scénář.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Stav ve stavu pacient":::

Můžete také vybrat **Přejít na řídicí panel Remote pacient** , kde se zobrazí řídicí panel pro Burkville ústavní zdravotní pracovník. Tento řídicí panel obsahuje podobnou sadu akcí, telemetrie a informací. Můžete také zobrazit více používaných zařízení a zvolit, aby **firmware aktualizoval** .

:::image type="content" source="media/lamna-remote.png" alt-text="Řídicí panel vzdáleného operátoru":::

### <a name="device-templates"></a>Šablony zařízení

Pokud vyberete možnost **šablony zařízení**, zobrazí se v šabloně tyto dva typy zařízení:

* **Oprava inteligentního nedůležitého**: Toto zařízení představuje opravu, která měří různé důležité příznaky. Používá se pro monitorování pacientů v nemocnicích i mimo něj. Pokud vyberete šablonu, uvidíte, že oprava odesílá data zařízení, jako je například teplota na úrovni baterie a zařízení, a data o zdravotním stavu, jako je třeba míra dýchacího a krevního tlaku.

* **Chytrá složená závorka**: Toto zařízení představuje složenou závorku, kterou pacienti používá při obnovování z karty s výměnou kolena. Pokud vyberete tuto šablonu, zobrazí se vám možnosti, jako jsou data zařízení, rozsah pohybu a akcelerace.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Šablona inteligentní opravy":::

### <a name="device-groups"></a>Skupiny zařízení

Pomocí skupin zařízení můžete logicky seskupovat sadu zařízení a pak na nich spouštět hromadné dotazy nebo operace.

Pokud vyberete kartu skupiny zařízení, zobrazí se pro každou šablonu zařízení v aplikaci výchozí skupina zařízení. K dispozici jsou také dvě další vzorové skupiny zařízení s názvem **zřízení zařízení** a **zařízení se zastaralým firmwarem**. Tyto ukázkové skupiny zařízení můžete použít jako vstupy ke spouštění některých [úloh](#jobs) v aplikaci.

### <a name="rules"></a>Pravidla

Pokud vyberete možnost **pravidla**, zobrazí se v šabloně tři pravidla:

* **Teplota složené závorky vysoká**: Toto pravidlo se aktivuje, když je teplota zařízení v typografické závorce větší než 95 &deg; F v intervalu 5 minut. Toto pravidlo použijte k upozornění týmu pacient a péče a ke vzdálenému chlazení zařízení.

* **Zjištěná klesnout**: Toto pravidlo se aktivuje, pokud je zjištěna přítomnost pacienta. Pomocí tohoto pravidla můžete nakonfigurovat akci nasazení provozního týmu, která bude pomáhat pacientovi, který si poklesl.

* **Oprava baterie nízká**: Toto pravidlo se aktivuje, když úroveň baterie na zařízení překročí 10%. Toto pravidlo použijte, když chcete, aby se do pacienta aktivovalo oznámení, aby se mohlo účtovat zařízení.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Pravidla":::

### <a name="jobs"></a>Úlohy

Úlohy umožňují spouštět hromadné operace se sadou zařízení, které jako vstup používají [skupiny zařízení](#device-groups) . Šablona aplikace má dvě ukázkové úlohy, které může operátor spustit:

* **Aktualizovat firmware složených závorek**: Tato úloha vyhledá zařízení v zařízení skupiny zařízení **s zastaralým firmwarem** a spustí příkaz, který tato zařízení aktualizuje na nejnovější verzi firmwaru. Tato ukázková úloha předpokládá, že zařízení mohou zpracovat příkaz **Update** a pak načíst soubory firmwaru z cloudu.  

* **Znovu zřídit zařízení**: máte sadu zařízení, která byla nedávno vrácena do nemocnice. Tato úloha vyhledá zařízení v zařízeních, která **zřídí** skupinu zařízení, a spustí příkaz, který je znovu zřídí pro další sadu pacientů.

### <a name="devices"></a>Zařízení

Vyberte kartu **zařízení** a pak vyberte instanci **čipové složené závorky**. Existují tři zobrazení k prozkoumání informací o konkrétním zařízení, které jste vybrali. Tato zobrazení jsou vytvářena a publikována při sestavování šablony zařízení pro vaše zařízení. Proto jsou tato zobrazení konzistentní napříč všemi zařízeními, která připojíte nebo simulujete.

Zobrazení **řídicího panelu** nabízí přehled telemetrie a vlastností, které jsou orientované na jednotlivé operátory, ze zařízení.

Karta **vlastnosti** umožňuje upravit vlastnosti cloudu a vlastnosti zařízení pro čtení a zápis.

Karta **příkazy** umožňuje spouštět na zařízení příkazy.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Řídicí panel pro kolenum ve složených závorkách":::

### <a name="data-export"></a>Export dat

Export dat umožňuje nepřetržitě exportovat data zařízení do jiných služeb Azure, včetně [rozhraní Azure API pro FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte aplikaci tím, že navštívíte **správu > nastavení aplikace** a kliknete na **Odstranit**.

:::image type="content" source="media/admin-delete.png" alt-text="Prostředky uklizený":::

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak vytvořit řídicí panel poskytovatele, který se připojuje k vaší IoT Central aplikaci.

> [!div class="nextstepaction"]
> [Vytvoření řídicího panelu poskytovatele](tutorial-health-data-triage.md)