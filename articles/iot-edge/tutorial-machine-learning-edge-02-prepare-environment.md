---
title: 'Kurz: nastavení prostředí – Machine Learning v Azure IoT Edge'
description: 'Kurz: Příprava prostředí pro vývoj a nasazení modulů pro strojové učení na hraničních zařízeních.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2ea4248ebaedd318e4112e41169f72bc80b1120f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114070"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Kurz: nastavení prostředí pro strojové učení na IoT Edge

> [!NOTE]
> Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Pokud jste dorazili přímo do tohoto článku, doporučujeme začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) řady, abyste dosáhli nejlepších výsledků.

Tento článek z kompletního Azure Machine Learning v IoT Edge kurzu vám pomůže připravit prostředí pro vývoj a nasazení. Nejdřív nastavte vývojový počítač pro všechny nástroje, které potřebujete. Pak vytvořte potřebné cloudové prostředky v Azure.

## <a name="set-up-a-development-machine"></a>Nastavení vývojového počítače

Tento krok obvykle provádí vývojář cloudu. Některý z těchto softwaru může být užitečný i pro odborníky na data.

V průběhu tohoto článku provádíme různé vývojářské úlohy, včetně kódování, kompilování, konfigurace a nasazení IoT Edgech modulů a zařízení IoT. Pro snadné použití jsme vytvořili skript PowerShellu, který vytvoří virtuální počítač Azure s řadou nezbytných součástí, které už jsou nakonfigurované. Virtuální počítač, který vytváříme, potřebuje mít možnost zpracovat [vnořenou virtualizaci](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), což je důvod, proč jsme zvolili velikost [Standard_D8s_v3](../virtual-machines/windows/sizes-general.md#dsv3-series-1) počítače.

Vývojový virtuální počítač se nastaví pomocí:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop pro Windows](https://www.docker.com/products/docker-desktop)
* [Git pro Windows](https://gitforwindows.org/)
* [Správce přihlašovacích údajů Git pro Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [Sada .NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Rozšíření VS Code](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Virtuální počítač vývojářů není nezbytně nutný – všechny vývojové nástroje je možné spustit na místním počítači. Důrazně však doporučujeme použít virtuální počítač k zajištění, aby se zajistilo pole pro hrací úrovni.

Vytvoření a konfigurace virtuálního počítače trvá přibližně 30 minut.

### <a name="get-the-script"></a>Získat skript

Naklonujte nebo Stáhněte PowerShellový skript z ukázkového úložiště [Machine Learning a IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) .

### <a name="create-an-azure-virtual-machine"></a>Vytvoření virtuálního počítače Azure

Adresář DevVM obsahuje soubory potřebné k vytvoření virtuálního počítače Azure, který je vhodný pro dokončení tohoto kurzu.

1. Otevřete PowerShell jako správce a přejděte do adresáře, kam jste stáhli kód. V kořenovém adresáři pro váš zdroj budeme odkazovat jako na `<srcdir>`.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. Spusťte následující příkaz, který povolí spouštění skriptů. Po zobrazení výzvy vyberte možnost **Ano pro všechny** .

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Spusťte Create-AzureDevVM. ps1 z tohoto adresáře.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * Po zobrazení výzvy zadejte následující informace:
      * **ID předplatného Azure**: ID vašeho předplatného, které najdete v Azure Portal
      * **Název skupiny prostředků**: název nové nebo existující skupiny prostředků v Azure
      * **Umístění**: vyberte umístění Azure, ve kterém se virtuální počítač vytvoří. Například westus2 nebo northeurope. Další informace najdete v tématu věnovaném [umístěním Azure](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: zadejte zapamatovatelné jméno účtu správce, který chcete vytvořit a použít na virtuálním počítači.
      * **AdminPassword**: nastavte heslo pro účet správce ve virtuálním počítači.

    * Pokud nemáte nainstalované Azure PowerShell, skript se nainstaluje [Azure PowerShell AZ Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * Budete vyzváni k přihlášení do Azure.

    * Skript potvrdí informace pro vytvoření virtuálního počítače. Pokračujte stisknutím `y` nebo `Enter`.

Skript se spustí několik minut, protože provede následující kroky:

* Vytvořte skupinu prostředků, pokud neexistuje.
* Nasazení virtuálního počítače
* Povolení technologie Hyper-V na virtuálním počítači
* Instalace softwaru je potřeba pro vývoj a klonování ukázkového úložiště.
* Restartování virtuálního počítače
* Vytvoření souboru RDP na ploše pro připojení k virtuálnímu počítači

### <a name="set-auto-shutdown-schedule"></a>Nastavit plán automatického vypínání

Aby vám pomohla snížit náklady, vytvořil se virtuální počítač s plánem automatického vypnutí, který je nastavený na 1900 PST. Možná budete muset tento čas aktualizovat v závislosti na vašem umístění a plánu. Aktualizace plánu vypnutí:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).

2. Přejděte na virtuální počítač ve skupině prostředků, kterou jste zadali v předchozí části.

3. V Navigátoru vedle sebe vyberte **Automatické vypnutí** .

4. Zadejte novou dobu vypnutí v části **naplánované vypnutí** nebo změňte **časové pásmo** a klikněte na **Uložit**.

### <a name="connect-and-configure-development-machine"></a>Připojit a nakonfigurovat vývojový počítač

Teď, když jsme vytvořili virtuální počítač, potřebujeme dokončit instalaci softwaru potřebného k dokončení tohoto kurzu.

#### <a name="start-a-remote-desktop-session"></a>Spustit relaci vzdálené plochy

1. Skript pro vytvoření virtuálního počítače vytvořil soubor RDP na vašem počítači.

2. Dvakrát klikněte na soubor s názvem **\<název virtuálního počítače Azure\>. RDP**.

3. Zobrazí se dialogové okno oznamující, že vydavatel vzdáleného připojení není znám. Zaškrtněte políčko příště nezobrazovat **výzvu pro připojení k tomuto počítači** a pak vyberte **připojit**.

4. Po zobrazení výzvy zadejte hodnotu AdminPassword, kterou jste použili při spouštění skriptu pro nastavení virtuálního počítače, a klikněte na **OK**.

5. Zobrazí se výzva, abyste přijali certifikát pro virtuální počítač. Vyberte příště nezobrazovat **dotaz pro připojení k tomuto počítači** a zvolte **Ano**.

#### <a name="install-visual-studio-code-extensions"></a>Instalace rozšíření Visual Studio Code

Teď, když jste se připojili k vývojovému počítači, přidejte k Visual Studio Code několik užitečných rozšíření, která usnadňují vývojové prostředí.

1. V okně PowerShellu přejděte do **C:\\source\\IoTEdgeAndMlSample\\DevVM**.

2. Povolte spouštění skriptů na virtuálním počítači zadáním příkazu.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Spusťte skript.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. Skript se spustí během několika minut instalace rozšíření VS Code Extensions:

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Nastavení IoT Hub a úložiště

Tyto kroky obvykle provádí vývojář cloudu.

Azure IoT Hub je srdcem jakékoli aplikace IoT. Zpracovává zabezpečenou komunikaci mezi zařízeními IoT a cloudem. Je hlavním koordinačním bodem pro provoz řešení IoT Edge Machine Learning.

* IoT Hub používá trasy k přímému směrování příchozích dat ze zařízení IoT do jiných služeb pro příjem dat. Využijeme výhod IoT Hubch tras k posílání dat zařízení do Azure Storage, kde je může využívat Azure Machine Learning k tomu, abyste mohli naučit náš zbývající užitečný život (RUL) klasifikátor.

* Později v tomto kurzu použijeme IoT Hub ke konfiguraci a správě našeho Azure IoT Edge zařízení.

V této části použijete skript k vytvoření služby Azure IoT Hub a účtu Azure Storage. Potom nakonfigurujete trasu, která předává data přijímaná centrem do kontejneru Azure Storage Blob pomocí Azure Portal. Dokončení těchto kroků trvá přibližně 10 minut.

### <a name="create-cloud-resources"></a>Vytvoření cloudových prostředků

1. Na svém vývojovém počítači otevřete okno PowerShellu.

1. Přejděte do adresáře IoTHub.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Spusťte skript pro vytváření. Pro ID předplatného, umístění a skupinu prostředků použijte stejné hodnoty jako při vytváření vývojového virtuálního počítače.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Budete vyzváni k přihlášení do Azure.
    * Skript potvrdí informace pro vytvoření vašeho centra a účtu úložiště. Pokračujte stisknutím `y` nebo `Enter`.

Spuštění skriptu trvá přibližně dvě minuty. Po dokončení skript vypíše název centra a účtu úložiště.

### <a name="review-route-to-storage-in-iot-hub"></a>Kontrola trasy do úložiště v IoT Hub

V rámci vytváření služby IoT Hub vytvořil skript, který jsme spustili v předchozí části, také vlastní koncový bod a trasu. Trasy IoT Hub se skládají z výrazu dotazu a koncového bodu. Pokud zpráva odpovídá výrazu, data se odesílají podél trasy do přidruženého koncového bodu. Koncové body můžou být Event Hubs, Service Bus fronty a témata. V tomto případě je koncový bod kontejnerem objektů BLOB v účtu úložiště. Pojďme použít Azure Portal ke kontrole trasy vytvořené pomocí našeho skriptu.

1. Otevřete web [Azure Portal](https://portal.azure.com).

1. V levém navigátoru zvolte všechny služby, do vyhledávacího pole zadejte IoT a vyberte **IoT Hub**.

1. Vyberte IoT Hub vytvořenou v předchozím kroku.

1. V Navigátoru IoT Hub stranu vyberte **směrování zpráv**.

1. Stránka směrování zpráv obsahuje dvě karty, **trasy** a **vlastní koncové body**. Vyberte kartu **vlastní koncové body** .

1. V části **BLOB Storage**vyberte **turbofanDeviceStorage**.

1. Všimněte si, že tento koncový bod odkazuje na kontejner objektů BLOB s názvem **devicedata** v účtu úložiště vytvořeném v posledním kroku, který má název **iotedgeandml\<jedinečných přípon\>** .

1. Všimněte si také, že **Formát názvu souboru objektu BLOB** byl změněn z výchozího formátu na místo toho umístit oddíl jako poslední prvek v názvu. Pro operace se soubory, které provedeme v Azure Notebooks později v tomto kurzu, zjistíme, že tento formát je pohodlnější.

1. Zavřete okno Podrobnosti koncového bodu a vraťte se na stránku **směrování zpráv** .

1. Vyberte kartu **trasy** .

1. Vyberte trasu s názvem **turbofanDeviceDataToStorage**.

1. Všimněte si, že koncový bod trasy je vlastní koncový bod **turbofanDeviceStorage** .

1. Podívejte se na dotaz směrování, který je nastaven na **hodnotu true**. To znamená, že všechny zprávy telemetrie zařízení budou odpovídat této trase, a proto se všechny zprávy odešlou do koncového bodu **turbofanDeviceStorage** .

1. Zavřete Podrobnosti o trasách.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme vytvořili IoT Hub a nakonfigurovali trasu k účtu Azure Storage. V dalším článku pošleme data ze sady simulovaných zařízení prostřednictvím IoT Hub do účtu úložiště. Později v tomto kurzu až nakonfigurujeme naše IoT Edge zařízení a moduly, budeme znovu navštěvovat trasy a v dotazu směrování budete hledat trochu víc.

Další informace o krocích popsaných v této části Machine Learning v IoT Edge kurzu najdete v tématu:

* [Základy Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Konfigurace směrování zpráv pomocí IoT Hub](../iot-hub/tutorial-routing.md)
* [Vytvoření centra IoT pomocí Azure Portal](../iot-hub/iot-hub-create-through-portal.md)

Pokračujte dalším článkem a vytvořte simulované zařízení, které chcete monitorovat.

> [!div class="nextstepaction"]
> [Generování dat zařízení](tutorial-machine-learning-edge-03-generate-data.md)
