---
title: Přehled akcelerátoru řešení prediktivní údržby – Azure | Microsoft Docs
description: Popis akcelerátoru řešení prediktivní údržby Azure
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 064c94c420b1ca9e45be37024e9b2b73135e1242
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938345"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Přehled akcelerátoru řešení prediktivní údržby

[Akcelerátor řešení][lnk_preconfigured_solutions] *prediktivní údržby* je jedním z [akcelerátorů řešení Microsoft Azure IoT][lnk_iot_suite]. Toto řešení integruje sběr telemetrických dat ze zařízení v reálném čase s prediktivním modelem vytvořeným pomocí služby [Azure Machine Learning][lnk-machine-learning].

Pomocí akcelerátorů řešení Azure IoT se můžete rychle a snadno připojit k prostředkům, monitorovat je a v reálném čase analyzovat telemetrii na řídicích panelech a ve vizualizacích. Řídicí panely a vizualizace v akcelerátoru řešení prediktivní údržby poskytují nové informace, které vám můžou pomoct zvýšit efektivitu a výnosy.

## <a name="the-scenario"></a>Scénář

Fabrikam je regionální letecká společnost, která se zaměřuje na pohodlí zákazníků za konkurenční ceny. Jednou z příčin zpoždění letů jsou problémy s údržbou, protože údržba leteckých motorů je velmi náročná. Společnost Fabrikam musí za každou cenu zabránit poruchám motorů během letu, a proto své motory pravidelně kontroluje a plánuje údržbu v souladu s plánem. Letecké motory se ale neopotřebovávají všechny stejně. Některou údržbu motorů není vždy nutné provádět. Naproti tomu se můžou vyskytnout problémy, kvůli kterým musí letadlo zůstat na zemi, dokud není opravené. Pokud je letadlo v místě, kde nejsou k dispozici vhodní technici nebo náhradní díly, pak tyto problémy můžou být zvláště nákladné.

Motory letadel společnosti Fabrikam jsou vybaveny snímači, které monitorují stav motoru během letu. Společnost Fabrikam pomocí akcelerátoru řešení prediktivní údržby sbírá data ze snímačů shromážděná během letu. Z údajů o provozu a selháních motorů nashromážděných za mnoho let analytici společnosti Fabrikam vytvořili model, který předpovídá zbývající dobu životnosti (RUL) leteckého motoru. Tento model využívá korelaci mezi daty ze čtyř snímačů v motoru a opotřebením motoru, které vede k jeho případnému selhání. I když společnost Fabrikam stále provádí pravidelné kontroly k zajištění bezpečnosti, může pomocí modelu vypočítat zbývající životnost jednotlivých motorů po každém letu. Model využívá telemetrická data shromážděná z motorů během letu. Společnost Fabrikam teď může předpovídat budoucí selhání a s předstihem plánovat údržbu a opravy.

> [!NOTE]
> Model řešení využívá data o opotřebení ze skutečných motorů.

Díky tomu, že společnost Fabrikam dokáže předpovědět čas potřebné údržby, může optimalizovat svůj provoz a snižovat náklady.

Koordinátoři údržby spolupracují s plánovači při:

- Plánování údržby tak, aby se časově shodovala se zastávkami letadel v konkrétních místech.
- Zajištění, aby servis letadel nezpůsoboval komplikace v časovém harmonogramu.
- Rozvržení služeb techniků pro zajištění, že servis letadel bude probíhat efektivně a bez prodlev.

Plán údržby dostávají také správci řízení zásob, kteří díky tomu mohou optimalizovat proces objednávání a skladové zásoby náhradních dílů.

Tyto aktivity umožňují společnosti Fabrikam minimalizovat prostoje letadel a snižovat provozní náklady a současně zajistit bezpečnost cestujících i posádek.

Vysvětlení, jak [akcelerátory řešení Azure IoT][lnk_iot_suite] zákazníkům umožňují využít potenciál prediktivní údržby, najdete v této [infografice][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-accelerator-is-built"></a>Jak je sestavený akcelerátor řešení prediktivní údržby

Řešení využívá existující model Azure Machine Learning, který je k dispozici jako šablona a ukazuje možnosti práce s telemetrickými daty ze zařízení shromážděnými prostřednictvím služeb akcelerátorů řešení IoT. Společnost Microsoft vytvořila [regresní model][lnk_regression_model] leteckého motoru na základě veřejně dostupných dat<sup>\[1\]</sup> a podrobné pokyny k použití modelu.

Akcelerátor řešení prediktivní údržby Azure IoT používá regresní model vytvořený z této šablony. Model je nasazený do vašeho předplatného Azure a vystavený prostřednictvím automaticky generovaného rozhraní API. Řešení obsahuje podmnožinu testovacích dat, která představují 4 motory (z celkem 100) a 4 datové proudy ze snímačů (z celkem 21). Tato data jsou dostatečná pro poskytování přesných výsledků z trénovaného modelu.

*\[1\] A. Saxena and K. Goebel (2008). „Turbofan Engine Degradation Simulation Data Set“, datové úložiště NASA Ames Prognostics Data Repository (https://c3.nasa.gov/dashlink/resources/139/)), NASA Ames Research Center, Moffett Field, CA*

## <a name="get-started-with-predictive-maintenance"></a>Začínáme s prediktivní údržbou

V tomto kurzu se dozvíte, jak zřídit akcelerátor řešení prediktivní údržby. Také se seznámíte se základními funkcemi akcelerátoru řešení prediktivní údržby. Mnohé z těchto funkcí jsou přístupné prostřednictvím řídicího panelu řešení, který se nasazuje spolu s akcelerátorem řešení.

K dokončení tohoto kurzu potřebujete mít aktivní předplatné služby Azure.

> [!NOTE]
> Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure][lnk_free_trial].

1. Pomocí svých přihlašovacích údajů k účtu Azure se přihlaste na webu [azureiotsuite.com][lnk-azureiotsuite] a kliknutím na tlačítko **+** vytvořte řešení.
1. Klikněte na **Vybrat** na dlaždici **Prediktivní údržba**.
1. Zadejte **Název řešení** pro akcelerátor řešení prediktivní údržby.
1. Vyberte **Oblast** a **Předplatné**, které chcete při zřizování řešení použít.
1. Kliknutím na tlačítko **Vytvořit řešení** zahájíte proces zřizování. Tento proces obvykle trvá několik minut.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Počkejte, dokud proces zřizování neskončí.

1. Klikněte na dlaždici s řešením, u kterého je uveden stav **Zřizování**.
1. **Stavy zřizování** umožňují sledovat, jak se služby Azure nasazují na váš účet Azure.
1. Jakmile bude zřizování dokončeno, stav se změní na **Připraveno**.
1. Kliknutím na dlaždici zobrazíte v pravém podokně informace o řešení. Z tohoto podokna můžete spustit řídicí panel řešení a využívat přístup k pracovnímu prostoru Machine Learning.

> [!NOTE]
> Pokud při nasazování akcelerátoru řešení narazíte na problémy, zkontrolujte [Oprávnění na webu azureiotsuite.com][lnk-permissions] a přečtěte si téma [Nejčastější dotazy][lnk-faq]. Pokud problémy přetrvávají, vytvořte na [portálu][lnk-portal] lístek služby.

Hledali jste informace, které se týkají vašeho řešení a nejsou zde uvedeny? Sdělte nám návrhy na funkce na webu [User Voice](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="view-the-solution"></a>Zobrazení řešení

Tato část vás provede uživatelským rozhraním řešení.

### <a name="predictive-maintenance-dashboard"></a>Řídicí panel prediktivní údržby

Tato stránka ve webové aplikaci používá ovládací prvky PowerBI v jazyce JavaScript (viz [Úložiště vizuálních prvků PowerBI][lnk-powerbi]) k vizualizaci:

* výstupních dat úlohy služby Stream Analytics v úložišti objektů blob
* zbývající doby životnosti (RUL) a počtu cyklů pro každý motor letadla

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Sledování chování cloudového řešení

Na webu Azure Portal přejděte do skupiny prostředků s názvem řešení, které jste si vybrali k zobrazení zřízených prostředků.

![][img-resource-group]

Při zřizování akcelerátoru řešení obdržíte e-mail s odkazem na pracovní prostor Machine Learning. Do pracovního prostoru Machine Learning se můžete dostat také ze stránky zřízeného řešení na webu [azureiotsuite.com][lnk-azureiotsuite]. Na této stránce je k dispozici dlaždice v případě, že je řešení ve stavu **Připraveno**.

![][img-machine-learning]

Na portálu řešení uvidíte, že ve vzorovém řešení jsou čtyři simulovaná zařízení, která představují dvě letadla se dvěma motory na každé letadlo, z nichž každý má čtyři snímače. Při první návštěvě portálu řešení dojde k zastavení simulace.

![][img-simulation-stopped]

Kliknutím na **Spustit simulaci** zahajte simulaci. Na řídicím panelu se zobrazí historie hodnot snímačů, zbývající doba životnosti (RUL), počet cyklů a historie hodnot RUL.

![][img-simulation-running]

Když je zbývající doba životnosti (RUL) nižší než 160 (libovolná prahová hodnota zvolená pro demonstrační účely), portál řešení zobrazí vedle hodnoty RUL symbol upozornění. Portál řešení také zvýrazní daný motor letadla žlutou barvou. Můžete si všimnout, že hodnoty zbývající doby životnosti (RUL) mají obecné klesající trend, ale kolísají nahoru a dolů. Toto chování vyplývá z různých délek cyklu a přesnosti modelu.

![][img-simulation-warning]

Při úplné simulaci trvá dokončení 148 cyklů asi 35 minut. Prahová hodnota 160 pro zbývající dobu životnosti (RUL) je poprvé dosažena přibližně po 5 minutách simulace a oba motory se na tuto hodnotu dostanou přibližně po 8 minutách.

Simulace zpracuje úplnou datovou sadu s údaji o 148 cyklech a vytvoří konečnou hodnotu RUL a cyklů.

Simulaci lze zastavit v libovolný okamžik, ale kliknutím na tlačítko **Start simulace** spustíte simulaci znovu od začátku datové sady.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak Azure IoT podporuje scénáře prediktivní údržby, najdete v tématu [Získání hodnoty z Internetu věcí][lnk_capture_value].

Přečtěte si [seznámení][lnk-predictive-walkthrough] s akcelerátorem řešení prediktivní údržby.

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Nejčastější dotazy k akcelerátorům řešení IoT][lnk-faq]
* [Zabezpečení IoT od počátku][lnk-security-groundup]

[img-resource-group]: media/iot-accelerators-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-accelerators-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-accelerators-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-accelerators-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-accelerators-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]:iot-accelerators-predictive-walkthrough.md
[lnk_preconfigured_solutions]:iot-accelerators-what-are-solution-accelerators.md
[lnk_iot_suite]:iot-accelerators-options.md
[lnk_infographic]: https://azure.microsoft.com/en-us/solutions/architecture/predictive-maintenance/
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-azureiotsuite]: https://www.azureiotsolutions.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsolutions.com
[lnk-permissions]: iot-accelerators-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/