---
title: 'Kurz: Generování dat simulovaných zařízení – machine learning na Azure IoT Edge'
description: Vytvořte virtuální zařízení, která generují simulovanou telemetrii, která lze později použít k trénování modelu strojového učení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8f7a971315183e867ae06b58801d5855f90462a1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76722361"
---
# <a name="tutorial-generate-simulated-device-data"></a>Kurz: Generování simulovaných dat zařízení

> [!NOTE]
> Tento článek je součástí řady pro kurz o používání Azure Machine Learning na IoT Edge. Pokud jste k tomuto článku dorazili přímo, doporučujeme vám začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) v sérii pro dosažení nejlepších výsledků.

V tomto článku používáme data školení strojového učení k simulaci zařízení odesílání telemetrie do služby Azure IoT Hub. Jak je uvedeno v úvodu, tento kurz používá [sadu dat simulace degradace motoru Turbofan](https://c3.nasa.gov/dashlink/resources/139/) k simulaci dat ze sady leteckých motorů pro výcvik a testování.

V našem experimentálním scénáři víme, že:

* Data se skládají z více časových řad s více odchylkami.
* Každá datová sada je rozdělena do trénovacích a testovacích podmnožin.
* Každá časová řada je z jiného motoru.
* Každý motor začíná s různým stupněm počátečního opotřebení a výrobních variací.

Pro účely tohoto kurzu používáme trénovací datovou sadu jedné datové sady (FD003).

Ve skutečnosti by každý motor byl nezávislé zařízení IoT. Za předpokladu, že nemáte k dispozici kolekci proudových motorů připojených k internetu, vytvoříme pro tato zařízení softwarový stand-in.

Simulátor je c# program, který používá ioT Hub API programově zaregistrovat virtuální zařízení s IoT Hub. Potom si přečteme data pro každé zařízení z podmnožiny dat poskytnuté NASA a odešleme je do vašeho centra IoT pomocí simulovaného zařízení IoT. Veškerý kód pro tuto část kurzu lze nalézt v adresáři DeviceHarness úložiště.

Projekt DeviceHarness je základní projekt .NET napsaný v c# skládající se ze čtyř tříd:

* **Program:** Vstupní bod pro provádění odpovědný za zpracování vstupu uživatele a celkovou koordinaci.
* **TrainingFileManager:** Odpovědný za čtení a analýzu vybraného datového souboru.
* **CycleData:** Představuje jeden řádek dat v souboru převedeném do formátu zprávy.
* **TurbofanDevice:** Zodpovědný za vytvoření zařízení IoT, který odpovídá jedinému zařízení (časové řady), v datech a přenosu dat do ioT hubu.

Úkoly popsané v tomto článku by měla trvat přibližně 20 minut.

Skutečný ekvivalent práce v tomto kroku by pravděpodobně provedli vývojáři zařízení a vývojáři cloudu.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurace kódu Sady Visual Studio a sestavení projektu DeviceHarness

1. Otevřete relaci vzdálené plochy pro vývojový virtuální počítač.

1. V kódu Visual Studia `C:\source\IoTEdgeAndMlSample\DeviceHarness` otevřete složku.

1. Vzhledem k tomu, že používáte rozšíření v tomto počítači poprvé, některá rozšíření budou aktualizovat a instalovat jejich závislosti. Může být vyzváni k aktualizaci rozšíření. Pokud ano, vyberte **znovu načíst okno**.

   Pokud omnisharp chyby se zobrazí ve výstupním okně, budete muset odinstalovat rozšíření C#.

1. Budete vyzváni k přidání požadovaných datových zdrojů pro DeviceHarness. Chcete-li je přidat, vyberte **ano.**

   * Zobrazení oznámení může trvat několik sekund.
   * Pokud jste toto oznámení zmeškali, zkontrolujte ikonu zvonku v pravém dolním rohu.

   ![VS Kód rozšíření pop-up](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Chcete-li obnovit závislosti balíčku, vyberte **obnovit.**

   ![Výzva k obnovení kódu VS](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Pokud tato oznámení nedostanete, zavřete kód sady Visual Studio, odstraňte přihrádku a adresáře obj v `C:\source\IoTEdgeAndMlSample\DeviceHarness`aplikaci , otevřete kód sady Visual Studio a znovu otevřete složku DeviceHarness.

1. Ověřte, zda je vaše prostředí správně nastaveno spuštěním sestavení, **klávesy Ctrl** + **Shift** + **B**nebo**úlohy sestavení spuštění** **terminálu** > .

1. Budete vyzváni k výběru úlohy sestavení, kterou chcete spustit. Vyberte **Sestavit**.

1. Sestavení spustí a výstupy zprávy o úspěchu.

   ![Sestavení úspěšné výstupní zprávy](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Toto sestavení můžete nastavit jako výchozí úlohu sestavení tak, že vyberete možnost**Konfigurovat výchozí úlohu sestavení** **terminálu** > a z výzvy **zvolíte Sestavit.**

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Připojení k centru IoT Hub a spuštění služby DeviceHarness

Teď, když máme sestavení projektu, připojte se k centru IoT pro přístup k připojovacímu řetězci a sledujte průběh generování dat.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Přihlášení k Azure v kódu Visual Studia

1. Přihlaste se k předplatnému Azure v `Ctrl + Shift + P` kódu Visual Studia otevřením palety příkazů nebo **zobrazením** > **palety příkazů**.

1. Vyhledejte příkaz **Azure: Přihlásit se.**

   Otevře se okno prohlížeče a zobrazí výzvu k zadání přihlašovacích údajů. Když budete přesměrováni na stránku úspěchu, můžete prohlížeč zavřít.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Připojení k rozbočovači IoT hub a načtení připojovacího řetězce rozbočovače

1. V dolní části průzkumníka kódu Visual Studia vyberte rámec **azure iot hub** u rozšířit.

1. V rozbaleném rámečku klikněte na **Vybrat IoT Hub**.

1. Po zobrazení výzvy vyberte předplatné Azure a pak ioT hub.

1. Klikněte na **...** napravo od **Služby Azure IoT Hub** pro další akce. Vyberte **kopírovat připojovací řetězec centra IoT**.

   ![Kopírování připojovacího řetězce centra IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Spuštění projektu DeviceHarness

1. Výběrem **možnosti Zobrazit** > **terminál** otevřete terminál kódu sady Visual Studio.

   Pokud se výzva nezobrazí, stiskněte Enter.

1. Zadejte v terminálu příkaz `dotnet run`.

1. Po zobrazení výzvy k zadání připojovacího řetězce centra IoT hub vložte připojovací řetězec zkopírovaný v předchozí části.

1. V rámci **zařízení Azure IoT Hub** klikněte na tlačítko aktualizovat.

   ![Aktualizovat seznam zařízení centra IoT](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Všimněte si, že zařízení jsou přidány do služby IoT Hub a že zařízení se zobrazí zeleně, což znamená, že data jsou odesílána prostřednictvím tohoto zařízení. Poté, co zařízení odesílají zprávy do centra IoT hubu, odpojí se a zobrazí se modře.

1. Zprávy odeslané do centra můžete zobrazit tak, že kliknete pravým tlačítkem myši na libovolné zařízení a vyberete **spustit sledování vestavěného koncového bodu událostí**. Zprávy se zobrazí ve výstupním podokně v kódu sady Visual Studio.

1. Zastavení monitorování kliknutím ve výstupním podokně **centra Azure IoT Hub** a zvolte Zastavit monitorování **integrovaného koncového bodu událostí**.

1. Nechte aplikaci spustit až do dokončení, což trvá několik minut.

## <a name="check-iot-hub-for-activity"></a>Kontrola aktivity v centru IoT Hub

Data odeslaná deviceharness šel do služby IoT hub, kde můžete ověřit na webu Azure Portal.

1. Otevřete [portál Azure](https://portal.azure.com/) a přejděte do centra IoT vytvořeného pro tento kurz.

1. V nabídce levého podokna vyberte v části **Sledování** **položku Metriky**.

1. Na stránce definice grafu klikněte na rozevírací seznam **Metrika,** posuňte se v seznamu dolů a vyberte **Možnost Směrování: data dodaná do úložiště**. Graf by měl zobrazovat špičku, kdy byla data směrována do úložiště.

   ![Graf ukazuje špičku při doručení dat do úložiště](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Ověření dat ve službě Azure Storage

Data, která jsme právě odeslali do vašeho centra IoT, byla směrována do kontejneru úložiště, který jsme vytvořili v předchozím článku. Podívejme se na data v našem účtu úložiště.

1. Na portálu Azure Portal přejděte k účtu úložiště.

1. V navigátoru účtu úložiště vyberte **Průzkumník úložiště (preview).**

1. V průzkumníku úložiště vyberte `devicedata` **kontejnery objektů blob** potom .

1. V podokně obsahu klikněte na složku pro název služby IoT hub následovaný rokem, měsícem, dnem a hodinou. Zobrazí se několik složek představujících minuty při zápisu dat.

   ![Zobrazení složek v úložišti objektů blob](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Klikněte do jedné z těchto složek najít datové soubory označené **00** a **01** odpovídající oddílu.

1. Soubory jsou napsány ve formátu [Avro.](https://avro.apache.org/) Poklepáním na jeden z těchto souborů otevřete jinou kartu prohlížeče a částečně vykreslujete data. Pokud se zobrazí výzva k otevření souboru v programu, můžete zvolit Kód VS a vykreslí se správně.

1. Není třeba se pokoušet číst nebo interpretovat data právě teď; uděláme to v dalším článku.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme použili projekt .NET Core k vytvoření sady virtuálních zařízení IoT a odesílání dat přes ně do našeho centra IoT a do kontejneru úložiště Azure. Tento projekt simuluje reálný scénář, kdy fyzická zařízení IoT odesílají data do služby IoT Hub a dále do kurátorského úložiště. Tato data zahrnují údaje ze senzorů, provozní nastavení, signály selhání a režimy a tak dále. Jakmile je shromážděno dostatečné množství dat, použijeme je k trénování modelů, které předpovídají zbývající životnost (RUL) pro zařízení. Toto strojové učení předvedeme v dalším článku.

Pokračujte k dalšímu článku trénovat model strojového učení s daty.

> [!div class="nextstepaction"]
> [Trénování a nasazení modelu služby Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
