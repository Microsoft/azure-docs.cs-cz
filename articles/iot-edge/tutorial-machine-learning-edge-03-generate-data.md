---
title: Vytvořit data s Simulovaná zařízení – Machine Learning v Azure IoT Edge | Dokumentace Microsoftu
description: Vytvořte virtuální zařízení, která generuje Simulovaná telemetrická data, která můžete později použít k trénování model strojového učení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a93b1128fe1ea0e03efc9060f2c3c4a93145f838
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432861"
---
# <a name="tutorial-generate-simulated-device-data"></a>Kurz: Generování dat simulovaného zařízení

> [!NOTE]
> Tento článek je součástí série kurz o používání Azure Machine learningu na hraničních zařízeních IoT. Pokud jste jste dostali přímo v tomto článku, doporučujeme vám začneme [nejprve článek](tutorial-machine-learning-edge-01-intro.md) v řadě nejlepších výsledků.

V tomto článku jsme pomocí služby machine learning Cvičná data pro simulaci zařízení odesílá telemetrická data do služby IoT Hub. Jak jsme uvedli v úvodu, tento kurz začátku do konce používá [Turbofan engine snížení simulace datovou sadu](https://c3.nasa.gov/dashlink/resources/139/) Simulace dat ze sady letadle modulů pro trénování a testování.

Z doprovodných readme.txt víme, že:

* Data se skládá z několika s množstvím proměnných časovými řadami
* Každou sadu dat je rozdělen na trénování a testování podmnožiny
* Každá Časová řada se jiný modul
* Každý motor začíná různých stupňů počáteční wear a vývoje a výrobního variaci

Pro účely tohoto kurzu používáme podmnožinu školení dat jedné datové sady (FD003).

Ve skutečnosti by každý motor nezávislé zařízení IoT. Za předpokladu, že nemáte kolekci modulů turbofan připojeného k Internetu k dispozici, bude při sestavování softwaru stand-in pro tato zařízení.

Simulátor není C# program, který používá rozhraní API pro Centrum IoT prostřednictvím kódu programu zaregistrovat virtuální zařízení služby IoT Hub. Jsme pak čtení dat pro každé zařízení z část data poskytnutá NASA a jeho odeslání do služby IoT hub pomocí simulovaného zařízení IoT. Veškerý kód pro tuto část kurzu, najdete v adresáři DeviceHarness úložiště.

DeviceHarness projekt je projekt .NET core v C# skládající se z čtyři třídy:

* **Program:** Vstupní bod pro spuštění zodpovědná za zpracování vstupu uživatele a celkové koordinace.
* **TrainingFileManager:** zodpovídáte za přečtení a analýze vybraný datový soubor.
* **Hodnotu typu CycleData:** představuje jeden řádek dat v souboru převedeny na formát zprávy.
* **TurbofanDevice:** zodpovědná za vytvoření zařízení IoT, která odpovídá na jediné zařízení (časové řady) v datech a přenosu dat do služby IoT Hub prostřednictvím zařízení IoT.

Úkoly popsané v tomto článku by měla trvat asi 20 minut na dokončení.

Ekvivalent skutečná práce v tomto kroku by pravděpodobně provést tak, že vývojáři zařízení a vývojářům pro cloud.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurace Visual Studio Code a sestavíte projekt DeviceHarness

1. Otevřete relaci vzdálené plochy k virtuálnímu počítači, jak je uvedeno v předchozím článku.

1. Otevřete Visual Studio Code.

1. Ve Visual Studio Code, vyberte **souboru** > **otevřít složku...** .

1. V **složky** textového pole zadejte `C:\source\IoTEdgeAndMlSample\DeviceHarness` a klikněte na tlačítko **vybrat složku** tlačítko.

   V okně výstupu se zobrazí chyby OmniSharp, budete muset odinstalovat C# rozšíření, zavřete a znovu ji spusťte VS Code, nainstalujte C# rozšíření a pak znovu načíst okno.

1. Vzhledem k tomu, že používáte rozšíření na tomto počítači poprvé, se některá rozšíření aktualizace a nainstalujte jejich závislosti. Můžete být vyzváni k aktualizaci rozšíření. Pokud ano, vyberte **znovu načíst okno**.

1. Zobrazí se výzva k přidání požadované prostředky pro DeviceHarness. Vyberte **Ano** je přidat.

   * Oznámení může trvat několik sekund.
   * Pokud jste toto oznámení, zkontrolujte "zvonku" ikonu v pravém dolním rohu.

   ![VS Code příponou automaticky otevíraného okna](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Vyberte **obnovení** Obnovit závislosti balíčků.

   ![VS Code obnovení řádku](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Ověřte, že vaše prostředí je správně nastavené aktivací sestavení, `Ctrl + Shift + B` nebo **terminálu** > **spustit úlohu sestavení**.

1. Zobrazí se výzva k výběru spuštění úkolu sestavení. Vyberte **sestavení**.

1. Sestavení běží a uloží zprávu o úspěšném dokončení.

   ![Zpráva výstupního sestavení bylo úspěšně dokončeno](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Můžete zvolit výchozí sestavení úlohu sestavení tak, že vyberete **terminálu** > **nakonfigurovat výchozí sestavení úlohu...**  a zvolíte **sestavení** z příkazového řádku.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Připojení ke službě IoT Hub a spustit DeviceHarness

Po sestavení projektu, připojte se ke službě IoT hub pro přístup k připojovací řetězec a sledování průběhu generování dat.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Přihlaste se k Azure v sadě Visual Studio Code

1. Přihlaste se k předplatnému Azure ve Visual Studio Code otevřete paletu příkazů `Ctrl + Shift + P` nebo **zobrazení** > **paleta příkazů...** .

1. Na příkazový řádek vyhledávání pro a vyberte **Azure: Přihlaste se**.

1. Okno prohlížeče se otevře a zobrazí výzvu k zadání pověření. Když budete přesměrováni na stránku úspěch, můžete zavřít prohlížeč.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Připojení ke službě IoT hub a načíst připojovací řetězec centra

1. V dolní části Průzkumníku Visual Studio Code, vyberte **zařízení Azure IoT Hub** rámce a rozbalte ho.

1. V rámci rozšířené klikněte na **vyberte službu IoT Hub**.

1. Po zobrazení výzvy vyberte své předplatné Azure a služby IoT hub.

1. Kliknutím na **zařízení Azure IoT Hub** snímků a klikněte na tlačítko **...**  zobrazíte další akce. Vyberte **připojovací řetězec služby IoT Hub kopírování**.

   ![Zkopírujte připojovací řetězec služby IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Spusťte projekt DeviceHarness

1. Vyberte **zobrazení** > **terminálu** otevřete terminál aplikace Visual Studio Code.

   Pokud se nezobrazí výzva, stiskněte klávesu Enter.

1. Zadejte `dotnet run` v terminálu.

1. Po zobrazení výzvy k připojovacího řetězce centra IoT, vložte připojovací řetězec zkopírovaný v předchozím oddílu.

1. V **zařízení Azure IoT Hub** frame, klikněte na tlačítko Aktualizovat.

   ![Aktualizace seznamu zařízení služby IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Všimněte si, že zařízení jsou přidány do služby IoT Hub a, že zařízení zobrazí zeleně udávajících těchto dat je odesíláno přes toto zařízení.

1. Můžete zobrazit zprávy odesílané do centra tak, že kliknete pravým tlačítkem na libovolném zařízení a vyberete **spustit monitorování zpráv D2C**. Zprávy se zobrazí v podokně výstupů ve Visual Studio Code.

1. Zastavit monitorování kliknutím **Azure IoT Hub Toolkit** výstupní podokna a zvolte **zastavit monitorování zpráv D2C**.

1. Umožní aplikaci dokončen, což trvá několik minut.

## <a name="check-iot-hub-for-activity"></a>Zkontrolujte službu IoT Hub pro aktivitu

Dat odesílaných DeviceHarness přešel do služby IoT hub. Je snadné k ověření, že bylo dosaženo data vašeho centra pomocí webu Azure portal.

1. Otevřít [webu Azure portal](https://portal.azure.com/) a přejděte do služby IoT hub.

1. Na stránce Přehled byste měli vidět, že se data odeslala do centra:  

   ![Zobrazení zařízení zprávy typu cloud ve službě IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Ověření dat ve službě Azure Storage

Data, která se právě jsme odeslali do služby IoT hub, byla směrována do kontejneru úložiště, kterou jsme vytvořili v předchozím článku. Pojďme se podívat na data v našich účtu úložiště.

1. Na portálu Azure Portal přejděte k účtu úložiště.

1. V části Navigátor účtu úložiště vyberte **Storage Exploreru (preview)** .

1. Ve storage Exploreru vyberte **kontejnery objektů Blob** pak **devicedata**.

1. V podokně obsahu klikněte na složku pro název služby IoT hub a rok, měsíc, den a hodina. Zobrazí se několik složek představující dobu, kdy byla zapsána data.

   ![Zobrazit složky v úložišti objektů blob](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Kliknutím na jednu z těchto složek k vyhledání souborů dat označených **00** a **01** odpovídající oddíl.

1. Soubory jsou napsané v [Avro](https://avro.apache.org/) formátu, ale dvojitým kliknutím na některý z těchto souborů otevřete nové kartě prohlížeče a částečně vykreslit data. Pokud místo toho se zobrazí výzva k otevření souboru v aplikaci, můžete použít VS Code a vykreslí správně.

1. Není nutné se pokouší číst a interpretovat data teď; Uděláme to v následujícím článku.

## <a name="next-steps"></a>Další postup

V tomto článku jsme použili projektu .NET Core vytvořit sadu virtuálních zařízení a odesílala data prostřednictvím těchto zařízení prostřednictvím naší služby IoT Hub a do kontejneru služby Azure Storage. Tento projekt simuluje reálný scénář, kde fyzické zařízení odesílají data, včetně údajů snímačů přes, provozní nastavení, selhání signály a režimy, a tak dále do služby IoT Hub a dále do kurátorované úložiště. Jakmile byla shromážděna dostatek dat, jsme ho použít k trénování modelů, které předpovídat zbývající životnosti (RUL) pro zařízení, které jsme vám ukáže, v následujícím článku.

Pokračujte k dalšímu článku pro trénování model strojového učení s daty.

> [!div class="nextstepaction"]
> [Trénování a nasadit model ve službě Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
