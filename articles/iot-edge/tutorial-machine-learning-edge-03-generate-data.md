---
title: 'Kurz: generování dat simulovaného zařízení – Machine Learning na Azure IoT Edge'
description: V tomto kurzu vytvoříte virtuální zařízení, která generují simulovanou telemetrii, kterou můžete později využít ke školení modelu Machine Learning.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d51a577b8a460e872b2baddcb668b13981609e40
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707742"
---
# <a name="tutorial-generate-simulated-device-data"></a>Kurz: generování dat simulovaného zařízení

> [!NOTE]
> Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Pokud jste dorazili přímo do tohoto článku, doporučujeme začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) řady, abyste dosáhli nejlepších výsledků.

V tomto článku používáme školicí data Machine Learning k simulaci zařízení odesílajícího telemetrii do IoT Hub. Jak je uvedeno v úvodu, Tento komplexní kurz používá [sadu dat simulace degradace modulu Turbofan](https://c3.nasa.gov/dashlink/resources/139/) , která simuluje data ze sady modulů pro letadlo pro účely školení a testování.

Z doprovodného souboru Readme. txt víme, že:

* Data se skládají z několika lineární časových řad.
* Každá sada dat je rozdělená na školicí a testovací podmnožiny.
* Každá časová řada pochází z jiného modulu.
* Každý stroj začíná v různých stupních počátečního opotřebení a výrobní variace.

Pro účely tohoto kurzu používáme podmnožinu výukových dat jedné sady dat (FD003).

Ve skutečnosti by byl každý stroj nezávislým zařízením IoT. Za předpokladu, že nemáte k dispozici kolekci turbofanch modulů připojených k Internetu, vytvoříme pro tato zařízení softwarový software.

Simulátor je C# program, který používá rozhraní IoT Hub API k programové registraci virtuálních zařízení pomocí IoT Hub. Pak si přečteme data pro každé zařízení z podmnožiny dat poskytované NASA a pošleme ji do služby IoT Hub pomocí simulovaného zařízení IoT. Veškerý kód této části kurzu najdete v adresáři DeviceHarness úložiště.

Projekt DeviceHarness je projekt .NET Core napsaný v C# sestávajícím ze čtyř tříd:

* **Program:** Vstupní bod pro provádění zodpovědný za zpracování vstupu uživatele a celkové koordinace.
* **TrainingFileManager:** zodpovídá za čtení a analýzu vybraného datového souboru.
* **Typu cycledata:** představuje jeden řádek dat v souboru převedený do formátu zprávy.
* **TurbofanDevice:** zodpovídá za vytvoření zařízení IoT, které odpovídá jednomu zařízení (časová řada) v datech a odesílá data IoT Hub prostřednictvím zařízení IoT.

Dokončení úkolů popsaných v tomto článku by mělo trvat přibližně 20 minut.

Real-World ekvivalent práce v tomto kroku by pravděpodobně prováděli vývojáři zařízení a cloudové vývojáře.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurace Visual Studio Code a sestavení projektu DeviceHarness

1. Otevřete relaci vzdálené plochy k virtuálnímu počítači, jak je znázorněno v předchozím článku.

1. Otevřete Visual Studio Code.

1. V Visual Studio Code vyberte **soubor** > **Otevřít složku...** .

1. Do textového pole **Složka** zadejte `C:\source\IoTEdgeAndMlSample\DeviceHarness` a klikněte na tlačítko **Vybrat složku** .

   Pokud se v okně výstup zobrazí OmniSharp chyby, budete muset C# rozšíření odinstalovat, zavřít a znovu otevřít vs Code, nainstalovat C# rozšíření a pak znovu načíst okno.

1. Vzhledem k tomu, že v tomto počítači používáte rozšíření poprvé, některá rozšíření budou aktualizovat a instalovat jejich závislosti. Může se zobrazit výzva k aktualizaci rozšíření. Pokud ano, vyberte **znovu načíst okno**.

1. Zobrazí se výzva k přidání požadovaných prostředků pro DeviceHarness. Pokud je chcete přidat, vyberte **Ano** .

   * Zobrazení oznámení může trvat několik sekund.
   * Pokud jste toto oznámení nenechali, podívejte se v pravém dolním rohu na ikonu zvonku.

   ![Místní nabídka rozšíření VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Vyberte **obnovit** a obnovte závislosti balíčku.

   ![Výzva k obnovení VS Code](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Ověřte, že je prostředí správně nastaveno aktivací sestavení, `Ctrl + Shift + B` nebo **terminálu** > **Spustit úlohu sestavení**.

1. Zobrazí se výzva k výběru úlohy sestavení, která se má spustit. Vyberte **sestavení**.

1. Sestavení se spustí a vypíše zprávu o úspěchu.

   ![Výstupní zpráva o úspěšném sestavení](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Můžete nastavit, aby se toto sestavení nastavilo jako výchozí, a to tak, že vyberete **terminál** > **nakonfigurovat výchozí úlohu sestavení...** a na příkazovém řádku zvolíte **sestavit** .

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Připojení k IoT Hub a spuštění DeviceHarness

Teď, když máme sestavení projektu, připojte se k centru IoT, abyste měli přístup k připojovacímu řetězci a mohli monitorovat průběh generování dat.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Přihlaste se k Azure v Visual Studio Code

1. Přihlaste se k předplatnému Azure v Visual Studio Code tak, že otevřete paletu příkazů `Ctrl + Shift + P` nebo **zobrazte** > **paletu příkazů...**

1. Na příkazovém řádku vyhledejte a vyberte **Azure: přihlásit**se.

1. Otevře se okno prohlížeče s výzvou k zadání přihlašovacích údajů. Když budete přesměrováni na stránku úspěchu, můžete zavřít prohlížeč.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Připojení ke službě IoT Hub a načtení připojovacího řetězce centra

1. V dolní části Průzkumníka Visual Studio Code vyberte rámec **zařízení Azure IoT Hub** a rozbalte ho.

1. V rozbaleném rámečku klikněte na **vybrat IoT Hub**.

1. Po zobrazení výzvy vyberte své předplatné Azure a potom centrum IoT.

1. Klikněte na rámec **zařízení Azure IoT Hub** a kliknutím na **...** zobrazíte další akce. Vyberte **kopírovat IoT Hub připojovací řetězec**.

   ![Kopírovat připojovací řetězec IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Spuštění projektu DeviceHarness

1. Vyberte **zobrazit** > **terminálu** a otevřete Visual Studio Code terminálu.

   Pokud se zobrazí výzva, vyberte zadat.

1. Do terminálu zadejte `dotnet run`.

1. Po zobrazení výzvy k zadání připojovacího řetězce IoT Hub vložte připojovací řetězec zkopírovaný v předchozí části.

1. V rámečku **zařízení IoT Hub Azure** klikněte na tlačítko Aktualizovat.

   ![Aktualizovat seznam zařízení IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Všimněte si, že zařízení se přidají do IoT Hub a že se zařízení zobrazují zeleně, aby označovala, že se data odesílají prostřednictvím tohoto zařízení.

1. Zprávy odesílané do centra můžete zobrazit tak, že pravým tlačítkem myši kliknete na libovolné zařízení a vyberete **zahájit sledování integrovaného koncového bodu události**. Zprávy se zobrazí v podokně výstup v Visual Studio Code.

1. Kliknutím na výstupní podokno služby **Azure IoT Hub Toolkit** ukončete monitorování a vyberte možnost **Zastavit sledování integrovaného koncového bodu události**.

1. Nechte aplikaci spuštěnou k dokončení, což trvá několik minut.

## <a name="check-iot-hub-for-activity"></a>Kontrolovat IoT Hub aktivity

Data odesílaná DeviceHarness se stala do služby IoT Hub. Pomocí Azure Portal můžete snadno ověřit, jestli data dosáhla vašeho centra.

1. Otevřete [Azure Portal](https://portal.azure.com/) a přejděte do svého centra IoT.

1. Na stránce Přehled byste měli vidět, že data byla odeslána do centra:  

   ![Zobrazení zpráv ze zařízení do cloudu v IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Ověřit data v Azure Storage

Data, která jsme právě poslali do služby IoT Hub, byla směrována do kontejneru úložiště, který jsme vytvořili v předchozím článku. Pojďme se podívat na data v našem účtu úložiště.

1. Na portálu Azure Portal přejděte k účtu úložiště.

1. V Navigátoru účtu úložiště vyberte **Průzkumník služby Storage (Preview)** .

1. V Průzkumníku služby Storage vyberte **kontejnery objektů BLOB** a potom **devicedata**.

1. V podokně obsahu klikněte na složku pro název centra IoT, pak na rok, měsíc, den a hodinu. Zobrazí se několik složek představujících minuty, kdy byla data zapsána.

   ![Zobrazení složek v úložišti objektů BLOB](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Kliknutím do jedné z těchto složek můžete najít datové soubory s označením **00** a **01** , které odpovídají oddílu.

1. Soubory jsou napsané ve formátu [Avro](https://avro.apache.org/) , ale když dvakrát kliknete na jeden z těchto souborů, otevře se další karta prohlížeče a částečně se vykreslí data. Pokud místo toho budete vyzváni k otevření souboru v programu, můžete zvolit VS Code a bude vygenerována správně.

1. Není nutné se pokusit o data číst nebo interpretovat data hned teď; provedeme ho v dalším článku.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme pomocí projektu .NET Core vytvořili sadu virtuálních zařízení a prostřednictvím našich IoT Hub a do Azure Storage kontejneru odesílali data prostřednictvím těchto zařízení. Tento projekt simuluje reálný scénář, ve kterém fyzická zařízení odesílají data, včetně čtecích senzorů, provozních nastavení, chybových signálů a režimů a tak dále, až po IoT Hub a dalších do známého úložiště. Po shromáždění dostatečného množství dat ho použijeme ke školení modelů, které předvádějí zbývající dobu životnosti (RUL) pro zařízení, což provedeme v dalším článku.

Pokračujte dalším článkem a Naučte se model strojového učení s daty.

> [!div class="nextstepaction"]
> [Výuka a nasazení Azure Machine Learningho modelu](tutorial-machine-learning-edge-04-train-model.md)
