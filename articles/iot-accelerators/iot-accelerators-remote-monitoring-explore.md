---
title: Začínáme s řešení vzdáleného monitorování - Azure | Microsoft Docs
description: Tento kurz používá simulované scénáře zavádět vzdálené monitorování akcelerátoru řešení. Tyto scénáře jsou vytvořeny při nasazení vzdálené monitorování akcelerátoru řešení poprvé.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: e354e110df8d168e153c50de6f7aad08dd416464
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627803"
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-solution-accelerator"></a>Prozkoumejte možnosti akcelerátoru řešení vzdáleného monitorování

Tento kurz ukazuje klíčové funkce řešení vzdáleného monitorování. K zavedení tyto možnosti, kurz umožňující prezentovat běžné scénáře zákazníka pomocí simulované aplikace IoT pro společnosti nazývané Contoso.

Kurzu vám pomůže pochopit typické scénáře IoT řešení vzdáleného monitorování poskytuje se na pole.

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Vizualizace a filtrování zařízení na řídicím panelu
> * Reakce na výstrahy
> * Aktualizace firmwaru v zařízení
> * Uspořádání vaše prostředky
> * Zastavit a spustit Simulovaná zařízení

Následující video ukazuje postup řešení vzdáleného monitorování:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-28-An-introduction-to-Azure-IoT-through-the-new-Remote-Monitoring-Preconfigured-Solution/Player]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete nasazenou instanci řešení vzdáleného monitorování ve vašem předplatném Azure.

Pokud jste nenasadili řešení vzdáleného monitorování ještě by se měla Dokončit [nasazení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-deploy.md) kurzu.

## <a name="the-contoso-sample-iot-deployment"></a>Nasazení IoT ukázkové společnosti Contoso

Contoso ukázka IoT nasazení můžete použít k pochopení základních scénářů vzdálené monitorování řešení poskytuje se na pole. Tyto scénáře jsou založeny na skutečném IoT nasazení. S největší pravděpodobností zvolíte přizpůsobit podle konkrétních požadavků řešení vzdáleného monitorování, ale ukázkové společnosti Contoso umožňuje získejte základní informace.

> [!NOTE]
> Pokud jste použili rozhraní příkazového řádku k nasazení řešení akcelerátoru, soubor `deployment-{your deployment name}-output.json` obsahuje informace o nasazení, jako je například adresa URL pro přístup k nasazené vzorku.

Ukázka Contoso zřídí sadu simulované zařízení a pravidel tak, aby fungoval s nimi. Jakmile porozumíte základní scénáře, můžete dál zkoumat více funkcí řešení v [provést rozšířené monitorování zařízení pomocí řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-monitor.md).

Contoso je společnost, která spravuje různých prostředků v různých prostředích. Contoso plánuje použít sílu cloudové aplikace či aplikace IoT vzdáleně monitorovat a spravovat více prostředků z centralizované aplikace. Následující části obsahují souhrn počáteční konfigurace Contoso vzorku:

> [!NOTE]
> Ukázkové společnosti Contoso je pouze jeden způsob, jak zřídit Simulovaná zařízení a vytvářet pravidla. Další možnosti zřizování zahrnují vytváření vlastní zařízení. Další informace o tom, jak vytvořit si vlastní zařízení a pravidel najdete v tématu [spravovat a nakonfigurovat zařízení](iot-accelerators-remote-monitoring-manage.md) a [zjistit problémy při použití pravidla na základě prahové hodnoty](iot-accelerators-remote-monitoring-automate.md).

### <a name="contoso-devices"></a>Zařízení společnosti Contoso

Společnost Contoso využívá inteligentní zařízení různého typu. Tato zařízení splnit různé role pro společnosti, odesílání různé datové proudy telemetrie. Kromě toho každý typ zařízení má jiné zařízení vlastnosti a podporované metody.

Následující tabulka obsahuje souhrn typů zřízené zařízení:

| Typ zařízení        | Telemetrická data                                  | Vlastnosti                                  | Značky                    | Metody                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| Chladič            | Přetížení teploty, vlhkosti,            | Typ, verze firmwaru modelu               | Umístění, Floor, kanceláře | Restartování, firmwaru aktualizace, verze nouzový ventil zvýšení nároků                          |
| Při vytváření prototypu zařízení | Teplotní, zatížení, geografického umístění        | Typ, verze firmwaru modelu               | Umístění, režimu          | Restartování, aktualizaci firmwaru zařízení přesunutí, zastavte zařízení, teploty vydání, zvýšení teploty |
| Modul             | Nádrž paliva úrovně, chladicí senzor, vibrace | Typ, verze firmwaru modelu               | Umístění, Floor, kanceláře | Aktualizace firmwaru, prázdný nádrž, výplň nádrž                                              |
| Vůz              | Teplotní nákladní geografického umístění, rychlosti,     | Typ, verze firmwaru modelu               | Umístění, zatížení          | Nižší nákladní teploty, zvýšení nákladní teploty, aktualizaci firmwaru                         |
| Hodnocení           | Floor, vibrace, teploty              | Typ, verze firmwaru, Model, geografického umístění | Umístění kanceláře        | Hodnocení zastavení, spuštění hodnocení, aktualizaci firmwaru                                               |

> [!NOTE]
> Ukázkové společnosti Contoso ukázkové zřizuje dvě zařízení podle typu. Pro každý typ jeden správně funguje v rámci hranic definované jako normální Contoso a ten má nějaký druh nepracuje správně. V části Další informace o pravidlech, která definuje společnosti Contoso pro zařízení. Tato pravidla definujte hranice správné chování.

### <a name="contoso-rules"></a>Pravidla contoso

Operátory společnosti Contoso znát prahové hodnoty, které určují, jestli zařízení správně funguje. Například není chladič funguje správně, pokud je větší než 250 PSI přetížení, které oznámí. V následující tabulce jsou uvedeny na základě prahové hodnoty pravidla, která definuje společnosti Contoso pro každý typ zařízení:

| Název pravidla | Popis | Prahová hodnota | Severity | Ovlivněné zařízení |
| --------- | ----------- | --------- | -------- | ---------------- |
| Přetížení chladič příliš vysoká. | Výstrahy, pokud dochlazovače dosáhnout vyšší než normální přetížení úrovně   |P > 250 psi       | Důležité | Dochlazovače            |
| Při vytváření prototypu zařízení temp příliš vysoká.  | Výstrahy, pokud při vytváření prototypu zařízení dosáhnout vyšší než normální teploty úrovně  |T>80&deg; F |Důležité | Při vytváření prototypu zařízení |
| Modul nádrž prázdný  | Výstrahy, pokud modul nádrž přejde prázdný                     | F < 5 galonech. | Informace     | Moduly             |
| Vyšší než normální nákladní teploty | Výstrahy, pokud je vyšší než normální teploty nákladní vůz společnosti                 | T &LT; 45&deg; F |Upozornění  | Dodávky              |
| Hodnocení vibrace zastavena      | Výstrahy, pokud hodnocení zcela zastaví (podle vibrace úroveň)                     | V < 0,1 mm |Upozornění  | Výtahy           |

### <a name="operate-the-contoso-sample-deployment"></a>Provoz nasazení ukázkové společnosti Contoso

Nyní jste viděli počáteční nastavení v ukázce Contoso. Následující části popisují v ukázce Contoso tři scénáře, které ilustrují, jak použít operátor akcelerátoru řešení.

## <a name="respond-to-a-pressure-alert"></a>Reagovat na upozornění na přetížení

Tento scénář popisuje, jak identifikovat a reagovat na upozornění, že se aktivuje chladič zařízení. Chladič se nachází v Redmondu s sebou, při vytváření 43, floor 2.

Jakožto Obsluha zobrazí v řídicím panelu, je výstrahy související s tlak chladič. Můžete posouvání a přiblížení na mapě zobrazíte další podrobnosti.

1. Na **řídicí panel** stránky v **výstrahy** mřížky, můžete zobrazit **chladič přetížení příliš vysoké** výstrahy. Chladič také zvýrazní na mapě:

    ![Řídicí panel zobrazuje výstrahy zatížení a zařízení na mapě](./media/iot-accelerators-remote-monitoring-explore/dashboardalarm.png)

1. Přejděte na **údržby** vyberte **údržby** v navigační nabídce. Na **údržby** stránky, můžete zobrazit podrobnosti o pravidlo, který aktivoval výstrahu chladič přetížení.

1. Počet, kolikrát má výstraha, potvrzování a otevřené a uzavřené výstrahy jsou uvedeny v seznamu výstrah.

    ![Stránka údržby zobrazuje seznam výstrah, které mají aktivaci](./media/iot-accelerators-remote-monitoring-explore/maintenancealarmlist.png)

1. Poslední upozornění v seznamu je nejnovějšího. Klikněte na tlačítko **chladič přetížení příliš vysoké** výstrahy zobrazíte přiřazená zařízení a telemetrie. Telemetrii ukazuje špičky přetížení pro chladič:

    ![Stránka údržby zobrazuje telemetrická data pro vybrané výstrahy](./media/iot-accelerators-remote-monitoring-explore/maintenancetelemetry.png)

Určili jste nyní problém, který aktivoval výstrahu a přidružené zařízení. Jakožto Obsluha další kroky jsou výstrahy na vědomí a zmírnit problém.

1. Označuje, zda jste nyní pracuje na výstrahu, změňte **výstrahy stavu** k **potvrzeno**:

    ![Vyberte a potvrdit výstrahy](./media/iot-accelerators-remote-monitoring-explore/maintenanceacknowledge.png)

1. Tak, aby fungoval na chladič, vyberte ho a potom zvolte **úlohy**. Vyberte **Run – metoda**, pak **EmergencyValveRelease**, přidejte název úlohy **ChillerPressureRelease**a zvolte **použít**. Tato nastavení vytvořit úlohu, která okamžitě spustí:

    ![Vyberte zařízení a naplánovat akci.](./media/iot-accelerators-remote-monitoring-explore/maintenanceschedule.png)

1. Chcete-li zobrazit stav úlohy, vraťte se k **údržby** a zobrazte seznam úloh v **úlohy** zobrazení. Uvidíte, že úloha byla spuštěna k uvolnění tlak na ventil chladič:

    ![Stav úlohy v zobrazení úloh](./media/iot-accelerators-remote-monitoring-explore/maintenancerunningjob.png)

Nakonec ověřte, zda hodnoty telemetrie z chladič zpět na normální.

1. Chcete-li zobrazit mřížky výstrahy, přejděte na **řídicí panel** stránky.

1. Zobrazení telemetrie zařízení, vyberte zařízení, pro původní výstrahu na mapě a potvrďte, že je zpět na normální.

1. Incident zavřete, přejděte na **údržby** vyberte výstrahu a nastaví stav na **uzavřeno**:

    ![Vyberte a zavřete výstrahu](./media/iot-accelerators-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>Aktualizaci firmwaru zařízení

Contoso je testování nový typ zařízení, v poli. V rámci testování cyklus potřebujete zajistěte, aby byl že tento firmwaru zařízení aktualizuje pracovní správně. Následující kroky ukazují, jak k aktualizaci firmwaru na několika zařízeních použít řešení vzdáleného monitorování.

K provádění úloh správy nutné zařízení, použijte **zařízení** stránky. Spusťte filtrování pro všechna zařízení při vytváření prototypu:

1. Přejděte na **zařízení** stránky. Vyberte **zařízení při vytváření prototypu** filtrovat v **filtry** rozevíracího seznamu:

    ![Použijte filtr na stránce zařízení](./media/iot-accelerators-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > Klikněte na tlačítko **spravovat** pro správu k dispozici tyto filtry.

1. Vyberte jednu z při vytváření prototypu zařízení:

    ![Vyberte zařízení, na stránce zařízení](./media/iot-accelerators-remote-monitoring-explore/devicesselect.png)

1. Klikněte **úlohy** tlačítko, zvolte **Run – metoda**a potom vyberte **aktualizaci firmwaru**. Zadejte hodnoty pro **název úlohy**, **verzi firmwaru**, a **Firmware URI**. Zvolte **použít** naplánování úlohy spustit nyní:

    ![Naplánovat aktualizaci firmwaru na zařízení](./media/iot-accelerators-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > V Simulovaná zařízení můžete použít libovolná adresa URL se vám líbí jako **Firmware URI** hodnoty a všechny hodnoty, které chcete pro **verzi firmwaru**. Simulovaná zařízení není přístup k adrese URL.

1. Všimněte si, kolik zařízení ovlivňuje úlohy a zvolte **použít**:

    ![Odeslání úlohy aktualizace firmwaru](./media/iot-accelerators-remote-monitoring-explore/devicessubmitupdate.png)

Můžete použít **údržby** stránky sledovat úlohu při jeho spuštění.

1. Chcete-li zobrazit seznam úloh, přejděte na **údržby** a klikněte na tlačítko **úlohy**.

1. Vyhledejte události související s úkol, který jste vytvořili. Ověřte, že byl proces aktualizace firmwaru správně spuštěné.

<!-- 05/01 broken 
You can create a filter to verify the firmware version updated correctly.

1. To create a filter, navigate to the **Devices** page and select **Manage device groups**:

    ![Manage device groups](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. Create a filter that includes only devices with the new firmware version:

    ![Create device filter](./media/iot-accelerators-remote-monitoring-explore/devicescreatefilter.png)

1. Return to the **Devices** page and verify that the device has the new firmware version. -->

## <a name="organize-your-assets"></a>Uspořádání vaše prostředky

Contoso má dva různé týmy pro aktivity služby pole:

* Týmem čipové vytváření spravuje dochlazovače, v silech a moduly.
* Inteligentní Vehicle team spravuje dodávky a při vytváření prototypu zařízení.

Aby bylo snazší jako operátor k uspořádání a správě svých zařízení, budete chtít označit je název příslušné týmu.

Názvy značek pro použití s zařízení můžete vytvořit.

1. Chcete-li zobrazit všechna zařízení, přejděte na **zařízení** stránce a vyberte položku **všechna zařízení** filtru:

    ![Zobrazit všechna zařízení](./media/iot-accelerators-remote-monitoring-explore/devicesalldevices.png)

1. Vyberte **dodávky** a **při vytváření prototypu** zařízení. Zvolte **úlohy**:

    ![Vyberte zařízení, která prototypu a vůz](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect.png)

1. Zvolte **značka** a poté vytvořit novou značku text názvem **FieldService** s hodnotou **ConnectedVehicle**. Zvolte název pro úlohu. Pak klikněte na tlačítko **použít**:

    ![Přidání značky na prototypu a vůz zařízení](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag.png)

1. Vyberte **chladič**, **hodnocení**, a **modul** zařízení. Zvolte **úlohy**:

    ![Vyberte zařízení, která chladič, modul a hodnocení](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect2.png)

1. Zvolte **značka** a poté vytvořit novou značku text názvem **FieldService** s hodnotou **SmartBuilding**. Zvolte název pro úlohu. Pak klikněte na tlačítko **použít**:

    ![Přidání značky do zařízení chladič, modul a hodnocení](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag2.png)

Hodnoty značky můžete použít pro vytvoření filtrů.

1. Na **zařízení** vyberte **Správa skupin zařízení**:

    ![Správa skupin zařízení](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. Vytvořit nový filtr, který používá název značky **FieldService** a hodnotu **SmartBuilding**. Uložení se filtr jako **čipové vytváření**.

1. Vytvořit nový filtr, který používá název značky **FieldService** a hodnotu **ConnectedVehicle**. Uložení se filtr jako **připojené Vehicle**.

Operátor Contoso teď můžete dotazovat zařízení podle operačního team aniž by bylo nutné změnit všechno na zařízeních.

## <a name="stop-simulated-devices"></a>Zastavit Simulovaná zařízení

V nabídce nastavení můžete použít k zastavení Simulovaná zařízení. To pomáhá snížit náklady na testování a prohlížení řešení. Spuštění nebo zastavení Simulovaná zařízení:

1. Vyberte **nastavení** ikonu.

1. Potom přepněte **Flowing** zapnout nebo vypnout:

    ![Nabídka Nastavení](./media/iot-accelerators-remote-monitoring-explore/settings.png)

## <a name="customize-the-ui"></a>Přizpůsobení uživatelského rozhraní

V nabídce nastavení můžete použít základní customisations na akcelerátoru řešení vzdáleného monitorování. Můžete:

- Přepínat mezi světlým a tmavým motivů.
- Změňte název řešení.
- Nahrajte vlastní logo.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli na:

>[!div class="checklist"]
> * Vizualizace a filtrování zařízení na řídicím panelu
> * Reakce na výstrahy
> * Aktualizace firmwaru v zařízení
> * Uspořádání vaše prostředky
> * Zastavit a spustit Simulovaná zařízení

Teď, když jste prozkoumali řešení vzdáleného monitorování, další informace o pokročilých funkcí řešení vzdáleného monitorování jsou navrhované další kroky:

* [Monitorování zařízení](./iot-accelerators-remote-monitoring-monitor.md).
* [Správa zařízení](./iot-accelerators-remote-monitoring-manage.md).
* [Automatizovat řešení s pravidly](./iot-accelerators-remote-monitoring-automate.md).
* [Udržovat řešení](iot-accelerators-remote-monitoring-maintain.md).
