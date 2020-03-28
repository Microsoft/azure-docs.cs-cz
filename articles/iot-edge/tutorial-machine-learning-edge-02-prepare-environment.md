---
title: 'Kurz: Nastavení prostředí – machine learning na Azure IoT Edge'
description: 'Kurz: Připravte své prostředí pro vývoj a nasazení modulů pro strojové učení na hraničních zařízeních.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c776d4d6c3973e7c222c9c9adf3e5105f6c84f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79296794"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Kurz: Nastavení prostředí pro strojové učení na IoT Edge

> [!NOTE]
> Tento článek je součástí řady pro kurz o používání Azure Machine Learning na IoT Edge. Pokud jste k tomuto článku dorazili přímo, doporučujeme vám začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) v sérii pro dosažení nejlepších výsledků.

Tento článek vám pomůže připravit prostředí pro vývoj a nasazení. Nejprve nastavte vývojový stroj se všemi potřebnými nástroji. Potom vytvořte potřebné cloudové prostředky v Azure.

## <a name="set-up-the-development-vm"></a>Nastavení vývojového virtuálního mísu

Tento krok obvykle provádí vývojář cloudu. Některé software může být také užitečné pro datové vědce.

Vytvořili jsme skript PowerShellu, který vytvoří virtuální počítač Azure s mnoha požadavky, které jsou už nakonfigurované. Virtuální počítač, který vytvoříme, musí být schopen zpracovat [vnořenou virtualizaci](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), a proto jsme zvolili [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) velikosti počítače.

Vývoj virtuálního virtuálního montovna se nastaví s:

* Windows 10
* [Čokoládový](https://chocolatey.org/)
* [Desktop Dockeru pro Windows](https://www.docker.com/products/docker-desktop)
* [Git pro Windows](https://gitforwindows.org/)
* [Správce přihlašovacích údajů Git u Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [Sada .NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Kód visual studia](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Rozšíření kódu VS](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Vývojářský virtuální počítač není nezbytně nutné – všechny vývojové nástroje lze spustit na místním počítači. Důrazně však doporučujeme použít virtuální hokvívač, abyste zajistili rovné podmínky.

Vytvoření a konfigurace virtuálního počítače trvá přibližně 30 minut.

1. Klonujte nebo stáhněte ukázkové úložiště [Machine Learning a IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) do místního počítače.

1. Otevřete powershell jako správce a přejděte do adresáře **\IoTEdgeAndMlSample\DevVM** umístěného pod kořenovým adresářem, do kterého jste kód stáhli. Budeme odkazovat na kořenový adresář `srcdir`pro váš zdroj jako .

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   Adresář DevVM obsahuje soubory potřebné k vytvoření virtuálního počítače Azure vhodného pro dokončení tohoto kurzu.

1. Spusťte následující příkaz, který povolí provádění skriptů. Po zobrazení výzvy zvolte **Ano pro všechny.**

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Spusťte create-AzureDevVM.ps1.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Po zobrazení výzvy zadejte následující informace:

    * **ID předplatného Azure:** ID předplatného, které najdete v [předplatných Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na portálu.
    * **Název skupiny prostředků:** Název nové nebo existující skupiny prostředků v Azure.
    * **Umístění:** Zvolte umístění Azure, kde se vytvoří virtuální počítač. Například "Západní USA 2" nebo "Severní Evropa". Další informace najdete v [tématu umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Uživatelské jméno**: Zadejte zapamatovatelné jméno pro účet správce pro virtuální ho.
    * **Heslo**: Nastavení hesla pro účet správce pro virtuální počítače.

   Skript se spouští několik minut při provádění následujících kroků:

    1. Nainstaluje [modul Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
    1. Zobrazí výzvu k přihlášení do Azure.
    1. Potvrdí informace pro vytvoření virtuálního počítače. Pokračujte stisknutím **klávesy y** nebo **Enter.**
    1. Vytvoří skupinu prostředků, pokud neexistuje.
    1. Nasazuje virtuální počítač.
    1. Povolí hyper-V na virtuálním počítači.
    1. Nainstaluje software potřeba pro vývoj a klonovat ukázkové úložiště.
    1. Restartuje virtuální počítač.
    1. Vytvoří soubor RDP na ploše pro připojení k virtuálnímu počítači.

   Pokud se zobrazí výzva k restartování názvu virtuálního počítače, můžete zkopírovat jeho název z výstupu skriptu. Výstup také zobrazuje cestu k souboru RDP pro připojení k virtuálnímu virtuálnímu soudu.

### <a name="set-auto-shutdown-schedule"></a>Nastavení plánu automatického vypínání

Abychom vám pomohli snížit náklady, vývojový virtuální počítače byl vytvořen s plánem automatického vypnutí, který je nastaven na 1900 PST. Toto nastavení může být nutné aktualizovat v závislosti na poloze a plánu. Aktualizace plánu vypnutí:

1. Na webu Azure Portal přejděte na virtuální počítač, který vytvořil skript.

1. V nabídce levého podokna vyberte v části **Operace** **možnost Automatické vypnutí**.

1. Podle potřeby upravte **plánované vypnutí** a **časové pásmo** a vyberte **Uložit**.

## <a name="connect-to-the-development-vm"></a>Připojení k vývojovému virtuálnímu virtuálnímu montovně

Teď, když jsme vytvořili virtuální hod musíme dokončit instalaci softwaru potřebného k dokončení kurzu.

1. Poklepejte na soubor RDP, který skript vytvořil na ploše.

1. Zobrazí se dialogové okno s prohlášením, že vydavatel vzdáleného připojení není znám. To je přijatelné, proto vyberte **Připojit**.

1. Zadejte heslo správce, které jste zadali k vytvoření virtuálního počítače a klepněte na tlačítko **OK**.

1. Budete vyzváni k přijetí certifikátu pro virtuální hod. Vyberte **ano**.

## <a name="install-visual-studio-code-extensions"></a>Instalace rozšíření kódu sady Visual Studio

Teď, když jste se připojili k vývojovému počítači, přidejte do kódu sady Visual Studio několik užitečných rozšíření, abyste usnadnili vývojové prostředí.

1. Připojte se k vývojovému virtuálnímu virtuálnímu mandu, otevřete okno Prostředí PowerShell a přejděte do adresáře **C:\source\IoTEdgeAndMlSample\DevVM.** Tento adresář byl vytvořen skriptem, který vytvořil virtuální ho.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Spusťte následující příkaz, který povolí provádění skriptů. Po zobrazení výzvy zvolte **Ano pro všechny.**

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Spusťte skript rozšíření kódu sady Visual Studio.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Skript bude spuštěn po dobu několika minut instalace rozšíření kódu VS:

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Nastavení služby IoT Hub a úložiště

Tyto kroky obvykle provádí vývojář cloudu.

Azure IoT Hub je srdcem každé aplikace IoT, protože zpracovává zabezpečenou komunikaci mezi zařízeními IoT a cloudem. Je hlavním koordinačním bodem pro provoz řešení strojového učení IoT Edge.

* IoT Hub používá trasy k přesměrování příchozích dat ze zařízení IoT do jiných navazujících služeb. Budeme využívat trasy služby IoT Hub k odesílání dat zařízení do Azure Storage. Ve službě Azure Storage se data zařízení spotřebovávají pomocí Azure Machine Learning k trénování našezbývající životnosti (RUL) třídění.

* Později v kurzu použijeme Službu IoT Hub ke konfiguraci a správě našeho zařízení Azure IoT Edge.

V této části použijete skript k vytvoření centra Azure IoT hub a účtu úložiště Azure. Pak na webu Azure Portal nakonfigurujete trasu, která předává data přijatá centrem do kontejneru úložiště Azure. Tyto kroky trvat asi 10 minut.

1. Připojte se k vývojovému virtuálnímu virtuálnímu provozu, otevřete okno PowerShellu a přejděte do adresáře **IoTHub.**

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Spusťte skript vytvoření. Použijte stejné hodnoty pro ID předplatného, umístění a skupinu prostředků, jako jste udělali při vytváření vývojového virtuálního mísy.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Budete vyzváni k přihlášení do Azure.
    * Skript potvrzuje informace pro vytvoření účtu Hub a storage. Pokračujte stisknutím **klávesy y** nebo **Enter.**

Skript poběží přibližně dvě minuty. Po dokončení skript uvede název služby IoT hub a účet úložiště.

## <a name="review-route-to-storage-in-iot-hub"></a>Kontrola trasy do úložiště v centru IoT Hub

V rámci vytváření služby IoT hub skript, který jsme spustili v předchozí části také vytvořil vlastní koncový bod a trasu. Trasy ioT Hubu se skládají z výrazu dotazu a koncového bodu. Pokud zpráva odpovídá výrazu, data jsou odeslána podél trasy do přidruženého koncového bodu. Koncové body mohou být centra událostí, fronty služby Service Bus a témata. V tomto případě koncový bod je kontejner objektů blob v účtu úložiště. Podívejme se na portál Azure ke kontrole trasy vytvořené naším skriptem.

1. Otevřete [portál Azure](https://portal.azure.com) a přejděte do skupiny prostředků, kterou používáte pro tento kurz.

1. V seznamu prostředků vyberte službu IoT Hub, kterou vytvořil skript. Bude mít název končící náhodnými `IotEdgeAndMlHub-jrujej6de6i7w`znaky, například .

1. V nabídce levého podokna vyberte v části **Zasílání zpráv** **položku Směrování zpráv**.

1. Na stránce **Směrování zpráv** vyberte kartu Vlastní **koncové body.**

1. Rozbalte část **Úložiště:**

   ![Ověřte, zda je turbofanDeviceStorage ve vlastním seznamu koncových bodů](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   Vidíme **turbofanDeviceStorage** je ve vlastní seznam koncových bodů. Všimněte si následujících charakteristik tohoto koncového bodu:

   * Odkazuje na kontejner úložiště objektů blob, který jste vytvořili, pojmenovaný `devicedata` podle názvu **kontejneru**.
   * Jeho **Filename formát** má oddíl jako poslední prvek v názvu. Zjistíme, že tento formát je pohodlnější pro operace se soubory budeme dělat s Poznámkovými bloky Azure později v kurzu.
   * Jeho **stav** by měl být v pořádku.

1. Vyberte kartu **Trasy**.

1. Vyberte trasu s názvem **turbofanDeviceDataToStorage**.

1. Na stránce **Podrobnosti trasy** všimněte si, že koncový bod trasy je **turbofanDeviceStorage** koncový bod.

   ![Zkontrolujte podrobnosti o trase turbodmychadlaDeviceDataToStorage](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Podívejte se na **směrovací dotaz**, který je nastaven na **hodnotu true**. Toto nastavení znamená, že všechny telemetrické zprávy zařízení budou odpovídat této trase; a proto budou všechny zprávy odeslány do koncového bodu **turbofanDeviceStorage.**

1. Vzhledem k tomu, že nebyly provedeny žádné úpravy, stačí zavřít tuto stránku.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme vytvořili službu IoT Hub a nakonfigurovali trasu k účtu úložiště Azure. Dále budeme odesílat data ze sady simulovaných zařízení prostřednictvím služby IoT Hub do účtu úložiště. Později v kurzu, poté, co jsme nakonfigurovali naše zařízení AoT Edge zařízení a moduly, budeme znovu trasy a podívat se trochu více na směrovací dotaz.

Další informace o krocích, které jsou popsány v této části kurzu Strojové učení na IoT Edge, najdete v tématu:

* [Základy Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Konfigurace směrování zpráv pomocí služby IoT Hub](../iot-hub/tutorial-routing.md)
* [Vytvoření centra IoT pomocí portálu Azure](../iot-hub/iot-hub-create-through-portal.md)

Pokračujte k dalšímu článku a vytvořte simulované zařízení ke sledování.

> [!div class="nextstepaction"]
> [Generování dat zařízení](tutorial-machine-learning-edge-03-generate-data.md)
