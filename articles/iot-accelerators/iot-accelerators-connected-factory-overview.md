---
title: Přehled řešení propojené továrny – Azure | Microsoft Docs
description: Popis akcelerátoru řešení propojené továrny Azure IoT.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 1dbaf4978bd23058d37239ba97bfe8583dc1f2c9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627086"
---
# <a name="get-started-with-the-connected-factory-solution-accelerator"></a>Začínáme s akcelerátorem řešení propojené továrny

[Akcelerátory řešení Azure IoT][lnk-preconfigured-solutions] kombinují více služeb Azure IoT, aby mohly poskytovat komplexní řešení implementující běžné obchodní scénáře IoT. Akcelerátor řešení *propojené továrny* se připojuje k průmyslovým zařízením a monitoruje je. Řešení můžete použít k analýze proudu dat z vašich zařízení a ke zvýšení produktivity a ziskovosti provozu.

V tomto kurzu se dozvíte, jak zřídit akcelerátor řešení propojené továrny. Také se seznámíte se základními funkcemi akcelerátoru řešení. Mnohé z těchto funkcí jsou přístupné z *řídicího panelu* řešení, který se nasazuje jako součást akcelerátoru řešení:

![Řídicí panel akcelerátoru řešení propojené továrny][img-cf-home]

K dokončení tohoto kurzu potřebujete mít aktivní předplatné služby Azure.

> [!NOTE]
> Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure][lnk_free_trial].

## <a name="provision-the-solution"></a>Zřízení řešení

1. Pomocí svých přihlašovacích údajů k účtu Azure se přihlaste na webu azureiotsuite.com a kliknutím na **+** vytvořte řešení.
2. Na dlaždici **Propojená továrna** klikněte na **Vybrat**.
3. Zadejte **Název řešení** pro akcelerátor řešení propojené továrny.
4. Vyberte **Předplatné** a **Oblast**, které chcete při zřizování řešení použít.
5. Kliknutím na tlačítko **Vytvořit řešení** zahájíte proces zřizování. Tento proces obvykle trvá několik minut.

### <a name="while-you-wait-for-the-provisioning-process-to-complete"></a>Zatímco čekáte na dokončení procesu zřizování

1. Klikněte na dlaždici s řešením, u kterého je uveden stav **Zřizování**.
2. **Stavy zřizování** umožňují sledovat, jak se služby Azure nasazují na váš účet Azure.
3. Jakmile bude zřizování dokončeno, stav se změní na **Připraveno**.
4. Kliknutím na dlaždici zobrazíte v pravém podokně informace o řešení.

> [!NOTE]
> Pokud při nasazování akcelerátoru řešení narazíte na problémy, zkontrolujte [Oprávnění na webu azureiotsuite.com][lnk-permissions] a přečtěte si téma [Propojená továrna – Nejčastější dotazy](iot-accelerators-faq-cf.md). Pokud problémy přetrvávají, vytvořte na [portálu][lnk-portal] lístek služby.

Hledali jste informace, které se týkají vašeho řešení a nejsou zde uvedeny? Sdělte nám návrhy na funkce na webu [User Voice](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="scenario-overview"></a>Přehled scénáře

Když nasadíte akcelerátor řešení propojené továrny, bude předem naplněný prostředky, které vám umožní projít běžným scénářem z oblasti průmyslu. V tomto scénáři několik továren propojených s řešením hlásí hodnoty dat potřebné k výpočtu celkové efektivity zařízení (OEE) a klíčových ukazatelů výkonu (KPI). V následujících částech se dozvíte, jak:

* Monitorovat továrnu, výrobní linky, celkovou efektivitu zařízení stanic a hodnoty klíčových ukazatelů výkonu
* Analyzovat telemetrická data generovaná z těchto zařízení pomocí Azure Time Series Insights
* Reagovat na alarmy a řešit potíže

Klíčovou vlastností tohoto scénáře je, že všechny tyto akce můžete provádět vzdáleně z řídicího panelu řešení. Nemusíte mít fyzický přístup k zařízením.

## <a name="view-the-solution-dashboard"></a>Zobrazení řídicího panelu řešení

Přes řídicí panel řešení můžete spravovat nasazené řešení. Je to hierarchická reprezentace globální konfigurace továrny. Můžete například zobrazit celkovou efektivitu zařízení a klíčové ukazatele výkonu nebo publikovat nové uzly pro alarmy akcí a telemetrie.

1. Až bude zřizování dokončeno a dlaždice akcelerátoru řešení bude hlásit **Připraveno**, zvolte **Spustit**. Na nové kartě se otevře portál řešení propojené továrny.

    ![Spuštění akcelerátoru řešení][img-launch-solution]

1. Ve výchozím nastavení se na portálu řešení zobrazuje *řídicí panel*. K přecházení do jiných oblastí portálu použijte nabídku na levé straně stránky.

    ![Řídicí panel akcelerátoru řešení propojené továrny][cf-img-menu]

Řídicí panel obsahuje tyto informace:

* Panel **Umístění továren**, na kterém se zobrazuje stav, umístění a aktuální konfigurace výroby továren v řešení. Když řešení poprvé spustíte, zahrnuje několik simulovaných zařízení. Simulace výrobní linky se skládá ze tří skutečných serverů OPC UA na každou výrobní linku, které provádí simulované úlohy a sdílí data. Další informace o OPC UA najdete v tématu [Propojená továrna – Nejčastější dotazy](iot-accelerators-faq-cf.md).
* **Mapu**, která zobrazuje umístění každého zařízení připojeného k řešení. Řešení může pomocí rozhraní API pro Mapy Bing vykreslovat na mapě informace. Pokud je ve vašem předplatném povolené podnikové rozhraní API pro Mapy Bing, tato funkce se použije automaticky. Pokud ne, přečtěte si [Nejčastější dotazy][lnk-faq], kde se dozvíte, jak zařídit dynamickou mapu.
* Panel **Alarmy**, na kterém se zobrazují alarmy generované při překročení konkrétních mezních hodnot pro telemetrii nebo hodnoty celkové efektivity zařízení nebo klíčových ukazatelů výkonu.
* Panel **Celková efektivita zařízení**, na kterém se zobrazují hodnoty celkové efektivity zařízení pro celý podnik, nebo továrnu/výrobní linku/stanici, kterou si právě prohlížíte. Tato hodnota se shromažďuje od zobrazení stanice až po úroveň podniku. Hodnotu celkové efektivity zařízení a elementy, ze kterých se skládá, můžete dále analyzovat.
* Panel **Klíčové ukazatele výkonu**, na kterém se zobrazuje počet vyrobených jednotek a spotřeba energie pro celý podnik, nebo továrnu/výrobní linku/stanici, kterou si právě prohlížíte. Tyto hodnoty se shromažďují od zobrazení stanice až po úroveň podniku.

## <a name="view-factories"></a>Zobrazení továren

Panel *Umístění továren* uvádí zeměpisné umístění všech továren v řešení, jejich stav a aktuální konfiguraci výroby. Ze seznamu umístění můžete přejít na další úrovně v hierarchii řešení. Řádky tohoto seznamu jsou hypertextové odkazy na podrobnosti o výrobních linkách v příslušném umístění. Je tedy možné procházet úrovněmi podrobností o výrobní lince, a to až na úroveň zobrazení stanice. Na seznam můžete také použít filtr.

![Továrny v akcelerátoru řešení propojené továrny][cf-img-factories]

1. **Panel Továrny** uvádí seznam továren v tomto řešení.

2. V seznamu továren je zpočátku uvedeno šest továren vytvořených během procesu zřizování. Do řešení můžete přidat další simulovaná a fyzická zařízení.

3. Chcete-li zobrazit podrobnosti o továrně, klikněte na odpovídající řádek v seznamu továren.

4. Chcete-li zobrazit podrobnosti o výrobní lince, klikněte na odpovídající řádek v seznamu.

5. Chcete-li zobrazit publikované uzly OPC UA některé stanice na výrobní lince, klikněte na odpovídající řádek v seznamu.

6. Chcete-li zobrazit podrobnosti o konkrétním uzlu stanice, klikněte na odpovídající řádek v seznamu. Tato akce otevře místní panel s vizualizacemi služby Time Series Insights. Po kliknutí na tyto grafy můžete provádět hlubší analýzu v prostředí průzkumníka Time Series Insights.

## <a name="view-map"></a>Zobrazení mapy

Pokud má vaše předplatné přístup k rozhraní API pro Mapy Bing, na mapě *Továrny* se zobrazí zeměpisné umístění a stav všech továren v řešení. Pokud chcete zobrazit podrobnosti o umístění, klikněte na umístění zobrazená na mapě.

![Mapa v akcelerátoru řešení propojené továrny][cf-img-map]

## <a name="view-alarms"></a>Zobrazení alarmů

Panel **Alarmy** zobrazuje alarmy generované v případě, že ohlášená hodnota nebo vypočítaná hodnota celkové efektivity zařízení nebo klíčového ukazatele výkonu překročí jejich nakonfigurovanou mezní hodnotu. Tento panel zobrazuje alarmy na všech úrovních hierarchie, od úrovně zobrazení stanice po globální zobrazení. Alarmy obsahují popis alarmu, datum, čas, umístění a počet výskytů. Informace o datech, která způsobila alarm, můžete získat pomocí dat služby Time Series Insights. Data služby Time Series Insights jsou vizualizována u alarmů, u kterých je to možné. Pokud jste správce, můžete na alarmy reagovat výchozími akcemi, jako například:

* Zavřít alarm.
* Potvrdit alarm.

Volitelně můžete provést složitější akce. Například pro uzel OPC UA pro kontrolu tlaku na montážní stanici můžete:

* Zobrazit v novém okně prohlížeče webovou stránku s podpůrnými informacemi.
* Zmírnit příčinu alarmu zavoláním metody OPC UA na zařízení.
* Potlačit dostupnost výchozích akcí.

    ![Alarmy v akcelerátoru řešení propojené továrny][cf-img-alerts]

> [!NOTE]
> Tyto alarmy se generují podle pravidel zadaných v konfiguračním souboru v akcelerátoru řešení. Tato pravidla můžou generovat alarmy v případě, že hodnoty celkové efektivity zařízení, klíčového ukazatele výkonu nebo uzlu OPC UA překračují jejich nakonfigurovanou mezní hodnotu.

1. **Panel Alarmy** zobrazuje alarmy vygenerované v tomto řešení.

2. Pokud chcete zobrazit podrobnosti o alarmu, klikněte na alarm na panelu alarmů.

3. Pokud chcete hlouběji analyzovat data alarmu, kliknutím na graf na panelu alarmů otevřete prostředí průzkumníka služby Time Series Insights.

4. Pro vyřešení alarmu máte k dispozici několik akcí na panelu alarmů. Vyberte možnost, která je pro vás vhodná, a klikněte na tlačítko Provést akci.

## <a name="view-overall-equipment-efficiency"></a>Zobrazení celkové efektivity zařízení

Celková efektivita zařízení hodnotí efektivitu výrobního procesu pomocí klíčových provozních parametrů souvisejících s výrobou. Celková efektivita zařízení (OEE) je v oboru standardním měřítkem a počítá se vynásobením míry dostupnosti, míry výkonu a míry kvality: OEE = dostupnost × výkon × kvalita.

![OEE v akcelerátoru řešení propojené továrny][cf-img-oee]

1. Pokud chcete zobrazit celkovou efektivitu zařízení na libovolné úrovni hierarchie, přejděte do konkrétního zobrazení, které potřebujete. Na panelu se zobrazí celková efektivita zařízení pro dané zobrazení spolu se všemi elementy, které společně tvoří procento celkové efektivity zařízení.

2. Pokud chcete hlouběji analyzovat celkovou efektivitu zařízení na libovolné úrovni hierarchie, klikněte na procento celkové efektivity zařízení, dostupnosti, výkonu nebo kvality. Otevře se místní panel s vizualizacemi ze služby Time Series Insights, které zobrazují data za poslední hodinu, posledních 24 hodin a posledních 7 dnů.

    ![Vizualizace TSI v akcelerátoru řešení propojené továrny][cf-img-tsi-visualization]

3. Pokud chcete hlouběji analyzovat data alarmu, klikněte na graf na panelu alarmů. Tato akce otevře prostředí průzkumníka Time Series Insights.

    ![Průzkumník TSI v akcelerátoru řešení propojené továrny][cf-img-tsi-explorer]

## <a name="view-key-performance-indicators"></a>Zobrazení klíčových ukazatelů výkonu

Řešení poskytuje dva klíčové ukazatele výkonu – *jednotky za hodinu* a *spotřebovaná energie v kWh*.

![Klíčové ukazatele výkonu v akcelerátoru řešení propojené továrny][cf-img-kpi]

1. Pokud chcete zobrazit jednotky za hodinu nebo spotřebovanou energii na libovolné úrovni hierarchie, přejděte do konkrétního zobrazení, které potřebujete. Na panelu se zobrazí jednotky za hodinu a spotřebovaná energie.

2. Pokud chcete analyzovat jednotky za hodinu nebo spotřebovanou energii na libovolné úrovni hierarchie, klikněte na měřidlo na panelu **Klíčové ukazatele výkonu**. Otevře se místní panel s vizualizacemi ze služby Time Series Insights a můžete si prohlédnout data za poslední hodinu, posledních 24 hodin a posledních 7 dnů.

## <a name="scenario-review"></a>Revize scénáře

V tomto scénáři jste na řídicím panelu monitorovali hodnoty celkové efektivity zařízení a klíčových ukazatelů výkonu vašich továren. Pak jste použili službu Time Series Insights k poskytnutí dalších informací, pomocí kterých jste zjistili další podrobnosti o telemetrických datech a celková efektivita zařízení a klíčové ukazatele výkonu vám pomohly se zjišťováním anomálií. Také jste použili panel alarmů k zobrazení problémů s vašimi továrnami a pomocí dostupných akcí jste vyřešili alarm.

## <a name="other-features"></a>Další funkce

Následující části popisují některé další funkce řešení propojené továrny, které nebyly popsány v předchozím scénáři.

## <a name="apply-filters"></a>Použití filtrů

1. Kliknutím na ikonu **trychtýře** na panelu umístění továren nebo na panelu alarmů zobrazte seznam dostupných filtrů.

2. Zobrazí se panel filtrů.

    ![Filtry v akcelerátoru řešení propojené továrny][cf-img-alert-filter]

3. Zvolte požadovaný filtr. Do polí filtru je možné zadat také libovolný text.

4. Filtr se pak použije. Stav filtru je zobrazen také na řídicím panelu v podobě trychtýře v tabulkách továren a alarmů.

    ![Filtry v akcelerátoru řešení propojené továrny][cf-img-alert-filter-funnel]

    > [!NOTE]
    > Aktivní filtr nemá vliv na zobrazené hodnoty celkové efektivity zařízení a klíčových ukazatelů výkonu, pouze filtruje obsah seznamu.

5. Pokud chcete filtr vymazat, klikněte na trychtýř a na místním panelu filtru klikněte na filtr. V tabulkách továren a alarmů se zobrazí text **Vše**.

## <a name="browse-an-opc-ua-server"></a>Procházení serveru OPC UA

Při nasazení akcelerátoru řešení automaticky zřídíte simulované servery OPC UA, které můžete procházet přes prohlížeč řešení. Tyto servery jsou *simulované servery OPC UA*. Simulované servery umožňují snadno experimentovat s akcelerátorem řešení, aniž by bylo nutné nasazovat skutečné fyzické servery. Pokud chcete k řešení připojit skutečný server OPC UA, přečtěte si kurz [Připojení zařízení OPC UA k akcelerátoru řešení propojené továrny][lnk-connect-cf].

1. Klikněte na **ikonu prohlížeče** na navigačním panelu řídicího panelu.

    ![Prohlížeč serveru v akcelerátoru řešení propojené továrny][cf-img-server-browser]

2. Vyberte jeden ze serverů z předkonfigurovaného seznamu. Tento seznam ukazuje servery, které jsou pro vás nasazeny v akcelerátoru řešení.

    ![Výběr serveru v akcelerátoru řešení propojené továrny][cf-img-server-choice]

3. Klikněte na **Připojit**, zobrazí se dialogové okno zabezpečení. Pro tuto simulaci můžete bez obav kliknout na **Pokračovat**.

4. Pokud chcete rozbalit některý z uzlů ve stromu serveru, klikněte na něj. Vedle uzlů, které publikují telemetrii, je značka.

    ![Strom serveru v akcelerátoru řešení propojené továrny][cf-img-server-tree]

5. Pokud chcete s uzlem provést operaci čtení, zápisu, publikování nebo volání, klikněte na něj pravým tlačítkem myši. Akce, které máte k dispozici, závisí na vašich oprávněních a atributech uzlu. Možnost čtení otevře místní panel se zobrazenou hodnotou konkrétního uzlu. Možnost zápisu otevře místní panel, na kterém můžete zadat novou hodnotu. Možnost volání zobrazí uzel, kde můžete zadat parametry volání.

## <a name="publish-a-node"></a>Publikování uzlu

Při procházení *simulovaného serveru OPC UA* máte také možnost publikovat nové uzly. V řešení můžete analyzovat telemetrii z těchto uzlů. Tyto *simulované servery OPC UA* umožňují snadno experimentovat s akcelerátorem řešení bez nasazování skutečných fyzických zařízení.

1. Ve stromové struktuře prohlížeče serveru OPC UA přejděte k uzlu, který chcete publikovat.

2. Klikněte na uzel pravým tlačítkem myši.

3. Zvolte **Publikovat**.

    ![Publikování uzlu v propojené továrně][cf-img-publish-node]

4. Otevře se místní panel s oznámením o úspěšném publikování. Uzel se objeví v zobrazení úrovně stanice a vedle něj bude značka zaškrtnutí.

    ![Úspěšné publikování v předkonfigurovaném řešení propojené továrny][cf-img-publish-success]

## <a name="command-and-control"></a>Příkazy a ovládání

Propojená továrna umožňuje ovládat a odesílat příkazy do průmyslových zařízení přímo z cloudu. Tuto funkci můžete použít při reakci na alarmy vygenerované zařízením. Například můžete z cloudu odeslat příkaz do zařízení. Dostupné příkazy najdete v uzlu **StationCommands** ve stromové struktuře serveru OPC UA. V tomto scénáři otevřete ventil pro uvolnění tlaku na montážní stanici výrobní linky v Mnichově. Abyste mohli používat funkce příkazů a ovládání, musíte mít roli **Správce** pro toto nasazení akcelerátoru řešení.

1. Ve stromové struktuře prohlížeče serveru OPC UA přejděte do uzlu **StationCommands**.

2. Vyberte příkaz, který chcete použít.

3. Klikněte na uzel pravým tlačítkem myši.

4. Zvolte **Volání**.

    ![Příkaz volání v akcelerátoru řešení propojené továrny][cf-img-call-command]

5. Zobrazí se místní panel informující o metodě, kterou se chystáte zavolat, a podrobnostech o parametrech, pokud jsou k dispozici.

6. Zvolte **Volání**.

    ![Kontext volání v akcelerátoru řešení propojené továrny][cf-img-call-context]

7. Místní panel se aktualizuje a zobrazí informaci o úspěšném zavolání metody. Ověřit, že bylo volání úspěšné, můžete také přečtením hodnoty uzlu tlaku, který se aktualizovat v důsledku volání.

    ![Úspěšné volání v akcelerátoru řešení propojené továrny][cf-img-call-success]

## <a name="behind-the-scenes"></a>Informace pro pokročilé uživatele

Když nasadíte akcelerátor řešení, proces nasazení vytvoří ve vybraném předplatném Azure několik prostředků. Tyto prostředky můžete zobrazit na webu [Azure Portal][lnk-portal]. Proces nasazení vytváří **skupinu prostředků**. Její název bude vycházet z názvu, který jste vybrali pro akcelerátor řešení:

![Akcelerátor řešení na webu Azure Portal][img-cf-portal]

Nastavení každého prostředku se zobrazí, když jej vyberete v seznamu ve skupině prostředků.

Můžete také zobrazit zdrojový kód akcelerátoru řešení. Zdrojový kód akcelerátoru řešení propojené továrny najdete v úložišti GitHub [azure-iot-connected-factory][lnk-cfgithub]:

Jakmile budete hotovi, můžete akcelerátor řešení z vašeho předplatného Azure odstranit na webu [azureiotsuite.com][lnk-azureiotsuite]. Tento web umožňuje snadno odstranit všechny prostředky, které se zřídily při vytváření akcelerátoru řešení.

> [!NOTE]
> Abyste zajistili, že jste odstranili opravdu vše spojené s akcelerátorem řešení, odstraňte řešení na webu [azureiotsuite.com][lnk-azureiotsuite]. Skupinu prostředků na portálu neodstraňujte.

## <a name="next-steps"></a>Další kroky

Když jste teď nasadili fungující akcelerátor řešení, můžete pokračovat v seznamování se s akcelerátory řešení IoT přečtením následujících článků:

* [Seznámení s akcelerátorem řešení propojené továrny][lnk-rm-walkthrough]
* [Připojení zařízení k akcelerátoru řešení propojené továrny][lnk-connect-cf]
* [Oprávnění na webu azureiotsuite.com][lnk-permissions]

[img-cf-home]:media/iot-accelerators-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-accelerators-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-accelerators-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-accelerators-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-accelerators-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-accelerators-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-accelerators-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-accelerators-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-accelerators-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-accelerators-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-accelerators-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-accelerators-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-accelerators-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-accelerators-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-accelerators-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-accelerators-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-accelerators-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-accelerators-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-accelerators-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-accelerators-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-accelerators-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]:iot-accelerators-what-are-solution-accelerators.md
[lnk-azureiotsuite]: https://www.azureiotsolutions.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]:iot-accelerators-connected-factory-sample-walkthrough.md
[lnk-connect-cf]:iot-accelerators-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md
