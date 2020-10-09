---
title: 'Kurz: generování dat simulovaného zařízení – Machine Learning na Azure IoT Edge'
description: Kurz – vytvoření virtuálních zařízení, která generují simulovanou telemetrii, kterou můžete později využít ke studiu modelu strojového učení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c40f7d988f2b5f206f42eae787efcdce786948a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857074"
---
# <a name="tutorial-generate-simulated-device-data"></a>Kurz: generování dat simulovaného zařízení

> [!NOTE]
> Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Pokud jste dorazili přímo do tohoto článku, doporučujeme začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) řady, abyste dosáhli nejlepších výsledků.

V tomto článku používáme školicí data Machine Learning k simulaci zařízení odesílajícího telemetrii do Azure IoT Hub. Jak je uvedeno v úvodu, tento kurz používá [sadu dat simulace degradace modulu Turbofan](https://c3.nasa.gov/dashlink/resources/139/) , která simuluje data ze sady modulů v letadlech pro účely školení a testování.

V našem experimentálním scénáři víme, že:

* Data se skládají z několika lineární časových řad.
* Každá sada dat je rozdělená na školicí a testovací podmnožiny.
* Každá časová řada je z jiného stroje.
* Každý stroj začíná různými stupni počáteční a výrobní variací.

Pro účely tohoto kurzu používáme podmnožinu výukových dat jedné sady dat (FD003).

Ve skutečnosti by byl každý stroj nezávislým zařízením IoT. Za předpokladu, že nemáte k dispozici kolekci turbofanch modulů připojených k Internetu, vytvoříme pro tato zařízení softwarový software.

Simulátor je program v jazyce C#, který používá rozhraní IoT Hub API k programové registraci virtuálních zařízení pomocí IoT Hub. Pak si přečteme data pro každé zařízení z podmnožiny dat poskytované NASA a pošleme ji do služby IoT Hub pomocí simulovaného zařízení IoT. Veškerý kód této části kurzu najdete v adresáři DeviceHarness úložiště.

Projekt DeviceHarness je projekt .NET Core napsaný v jazyce C#, který se skládá ze čtyř tříd:

* **Program:** Vstupní bod pro provádění zodpovědný za zpracování vstupu uživatele a celkové koordinace.
* **TrainingFileManager:** Zodpovídá za čtení a analýzu vybraného datového souboru.
* **Typu cycledata:** Představuje jeden řádek dat v souboru převedený do formátu zprávy.
* **TurbofanDevice:** Zodpovídá za vytvoření zařízení IoT, které odpovídá jednomu zařízení (časová řada), v datech a přenáší data do IoT Hub.

Dokončení úkolů popsaných v tomto článku by mělo trvat přibližně 20 minut.

Real-World ekvivalent práce v tomto kroku by pravděpodobně prováděli vývojáři zařízení a cloudové vývojáře.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurace Visual Studio Code a sestavení projektu DeviceHarness

1. Otevřete relaci vzdálené plochy k VIRTUÁLNÍmu počítači pro vývoj.

1. Ve Visual Studio Code otevřete `C:\source\IoTEdgeAndMlSample\DeviceHarness` složku.

1. Vzhledem k tomu, že v tomto počítači používáte rozšíření poprvé, některá rozšíření budou aktualizovat a instalovat jejich závislosti. Může se zobrazit výzva k aktualizaci rozšíření. Pokud ano, vyberte **znovu načíst okno**.

   Pokud se v okně výstup zobrazí chyby OmniSharp, budete muset odinstalovat rozšíření C#.

1. Zobrazí se výzva k přidání požadovaných prostředků pro DeviceHarness. Pokud je chcete přidat, vyberte **Ano** .

   * Zobrazení oznámení může trvat několik sekund.
   * Pokud jste toto oznámení nenechali, podívejte se na ikonu zvonku v pravém dolním rohu.

   ![Místní nabídka rozšíření VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Vyberte **obnovit** a obnovte závislosti balíčku.

   ![Výzva k obnovení VS Code](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Pokud tato oznámení neobdržíte, zavřete Visual Studio Code, odstraňte adresáře bin a obj v `C:\source\IoTEdgeAndMlSample\DeviceHarness` , otevřete Visual Studio Code a znovu otevřete složku DeviceHarness.

1. Ověřte, že je prostředí správně nastavené spuštěním sestavení, **kombinace kláves CTRL**  +  **SHIFT**  +  **B**nebo **Terminal**  >  **úlohy sestavení pro spuštění**terminálu.

1. Zobrazí se výzva k výběru úlohy sestavení, která se má spustit. Vyberte **sestavení**.

1. Sestavení se spustí a vypíše zprávu o úspěchu.

   ![Výstupní zpráva o úspěšném sestavení](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Můžete nastavit, aby se toto sestavení nastavilo jako výchozí, a to tak, že vyberete **terminál**  >  **konfigurovat výchozí sestavení úloha...** a z příkazového řádku zvolíte **sestavit** .

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Připojení k IoT Hub a spuštění DeviceHarness

Teď, když máme sestavení projektu, připojte se k centru IoT, abyste měli přístup k připojovacímu řetězci a mohli monitorovat průběh generování dat.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Přihlaste se k Azure v Visual Studio Code

1. Přihlaste se k předplatnému Azure v Visual Studio Code tak, že otevřete paletu příkazů `Ctrl + Shift + P` nebo **zobrazíte**  >  **paletu příkazů**.

1. Vyhledejte příkaz **Azure: Sign in** .

   Otevře se okno prohlížeče s výzvou k zadání přihlašovacích údajů. Když budete přesměrováni na stránku úspěchu, můžete zavřít prohlížeč.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Připojení ke službě IoT Hub a načtení připojovacího řetězce centra

1. V dolní části Průzkumníka Visual Studio Code vyberte rámec **Azure IoT Hub** a rozbalte ho.

1. V rozbaleném rámečku klikněte na **vybrat IoT Hub**.

1. Po zobrazení výzvy vyberte své předplatné Azure a potom centrum IoT.

1. Další akce získáte, když kliknete na **...** napravo od **Azure IoT Hub** . Vyberte **kopírovat IoT Hub připojovací řetězec**.

   ![Kopírovat připojovací řetězec IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Spuštění projektu DeviceHarness

1. Vyberte **Zobrazit**  >  **terminál** a otevřete Visual Studio Code terminálu.

   Pokud se výzva nezobrazí, stiskněte klávesu ENTER.

1. Zadejte v terminálu příkaz `dotnet run`.

1. Po zobrazení výzvy k zadání připojovacího řetězce IoT Hub vložte připojovací řetězec zkopírovaný v předchozí části.

1. V rámečku **zařízení IoT Hub Azure** klikněte na tlačítko Aktualizovat.

   ![Aktualizovat seznam zařízení IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Všimněte si, že zařízení se přidají do IoT Hub a že se zařízení zobrazují zeleně, aby označovala, že se data odesílají prostřednictvím tohoto zařízení. Jakmile zařízení odešlou zprávy do centra IoT, odpojí se a zobrazí modře.

1. Zprávy odeslané do centra můžete zobrazit tak, že kliknete pravým tlačítkem na libovolné zařízení a vyberete **Spustit sledování integrovaného koncového bodu události**. Zprávy se zobrazí v podokně výstup v Visual Studio Code.

1. Kliknutím do podokna výstup v **Azure IoT Hub** ukončete monitorování a vyberte **zastavit monitorování integrovaný koncový bod události**.

1. Nechte aplikaci spuštěnou k dokončení, což trvá několik minut.

## <a name="check-iot-hub-for-activity"></a>Kontrolovat IoT Hub aktivity

Data odesílaná DeviceHarness přešla do vašeho centra IoT, kde můžete ověřit ve Azure Portal.

1. Otevřete [Azure Portal](https://portal.azure.com/) a přejděte do služby IoT Hub vytvořené pro tento kurz.

1. V nabídce v levém podokně v části **monitorování**vyberte **metriky**.

1. Na stránce definice grafu klikněte na rozevírací nabídku **metrika** , přejděte dolů na seznam a vyberte možnost **směrování: data Doručená do úložiště**. Graf by měl zobrazovat špičku, kdy byla data směrována do úložiště.

   ![Graf zobrazuje špičku při doručování dat do úložiště.](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Ověřit data v Azure Storage

Data, která jsme právě poslali do služby IoT Hub, byla směrována do kontejneru úložiště, který jsme vytvořili v předchozím článku. Pojďme se podívat na data v našem účtu úložiště.

1. Na portálu Azure Portal přejděte k účtu úložiště.

1. V Navigátoru účtu úložiště vyberte **Průzkumník služby Storage (Preview)**.

1. V Průzkumníku služby Storage vyberte **kontejnery objektů BLOB** a potom `devicedata` .

1. V podokně Obsah klikněte na složku pro název centra IoT a potom na rok, měsíc, den a hodinu. Zobrazí se několik složek představujících minuty, kdy byla data zapsána.

   ![Zobrazení složek v úložišti objektů BLOB](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Kliknutím do jedné z těchto složek můžete najít datové soubory s označením **00** a **01** , které odpovídají oddílu.

1. Soubory jsou zapsané ve formátu [Avro](https://avro.apache.org/) . Dvojím kliknutím na jeden z těchto souborů otevřete jinou kartu prohlížeče a částečně vykreslete data. Pokud se zobrazí výzva k otevření souboru v programu, můžete zvolit VS Code a správně se vykreslí.

1. Není nutné se pokusit o data číst nebo interpretovat data hned teď; provedeme ho v dalším článku.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme pomocí projektu .NET Core vytvořili sadu virtuálních zařízení IoT a prostřednictvím nich odesíláme data do našeho centra IoT a do kontejneru Azure Storage. Tento projekt simuluje reálný scénář, ve kterém fyzické zařízení IoT odesílají data do IoT Hub a dále do známého úložiště. Tato data zahrnují čtení senzorů, provozní nastavení, signály chyb a režimy atd. Po shromáždění dostatečného množství dat ho použijeme ke školení modelů, které předpovídá zbývající dobu životnosti (RUL) pro dané zařízení. Tento strojové učení vám ukážeme v dalším článku.

Pokračujte dalším článkem a Naučte se model strojového učení s daty.

> [!div class="nextstepaction"]
> [Trénování a nasazení modelu služby Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
