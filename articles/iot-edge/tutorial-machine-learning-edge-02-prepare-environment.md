---
title: 'Kurz: nastavení prostředí – Machine Learning v Azure IoT Edge'
description: 'Kurz: Příprava prostředí pro vývoj a nasazení modulů pro strojové učení na hraničních zařízeních.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3a3518ba68c9474fc4a34390e6fd9a7d1e88f6c6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959590"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Kurz: nastavení prostředí pro strojové učení na IoT Edge

Tento článek vám pomůže připravit vaše prostředí pro vývoj a nasazení. Nejdřív nastavte vývojový počítač pro všechny nástroje, které potřebujete. Pak vytvořte potřebné cloudové prostředky v Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Každý článek v sérii vychází z práce v předchozím článku. Pokud jste dorazili přímo do tohoto článku, přejděte na [první článek](tutorial-machine-learning-edge-01-intro.md) v řadě.

## <a name="set-up-the-development-vm"></a>Nastavení vývojového virtuálního počítače

Tento krok obvykle provádí vývojář cloudu. Některý z těchto softwaru může být užitečný i pro odborníky na data.

Vytvořili jsme PowerShellový skript, který vytvoří virtuální počítač Azure s řadou nezbytných součástí, které už jsou nakonfigurované. Virtuální počítač, který vytváříme, potřebuje mít možnost zpracovat [vnořenou virtualizaci](../virtual-machines/windows/nested-virtualization.md), což je důvod, proč jsme zvolili velikost [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) počítače.

Vývojový virtuální počítač se nastaví pomocí:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop pro Windows](https://www.docker.com/products/docker-desktop)
* [Git pro Windows](https://gitforwindows.org/)
* [Správce přihlašovacích údajů Git pro Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [Sada .NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](/powershell/azure/?view=azps-1.1.0)
* [Rozšíření VS Code](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Virtuální počítač vývojářů není nezbytně nutný – všechny vývojové nástroje je možné spustit na místním počítači. Důrazně však doporučujeme použít virtuální počítač k zajištění, aby se zajistilo pole pro hrací úrovni.

Vytvoření a konfigurace virtuálního počítače trvá přibližně 30 minut.

1. Naklonujte nebo stáhněte [Machine Learning a IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) ukázkové úložiště do místního počítače.

1. Otevřete PowerShell jako správce a přejděte do adresáře **\IoTEdgeAndMlSample\DevVM** , který se nachází v kořenovém adresáři, kam jste stáhli kód. V kořenovém adresáři pro váš zdroj budeme odkazovat jako na `srcdir` .

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   Adresář DevVM obsahuje soubory potřebné k vytvoření virtuálního počítače Azure, který je vhodný pro dokončení tohoto kurzu.

1. Spusťte následující příkaz, který povolí spouštění skriptů. Po zobrazení výzvy vyberte možnost **Ano pro všechny** .

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Spusťte Create-AzureDevVM.ps1.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Po zobrazení výzvy zadejte následující informace:

    * **ID předplatného Azure**: ID vašeho předplatného, které najdete v [předplatných Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na portálu.
    * **Název skupiny prostředků**: název nové nebo existující skupiny prostředků v Azure.
    * **Umístění**: vyberte umístění Azure, ve kterém se virtuální počítač vytvoří. Například ' Západní USA 2 ' nebo ' Severní Evropa '. Další informace najdete v tématu věnovaném [umístěním Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Username (uživatelské jméno**): zadejte si zapamatovatelné jméno pro účet správce virtuálního počítače.
    * **Heslo**: nastavte heslo pro účet správce pro virtuální počítač.

   Skript se spustí několik minut, protože provede následující kroky:

    1. Nainstaluje [Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
    1. Vyzve vás, abyste se přihlásili k Azure.
    1. Potvrdí informace pro vytvoření virtuálního počítače. Pokračujte stisknutím klávesy **y** nebo **ENTER** .
    1. Vytvoří skupinu prostředků, pokud neexistuje.
    1. Nasadí virtuální počítač.
    1. Povolí technologii Hyper-V na virtuálním počítači.
    1. Nainstaluje software potřebný pro vývoj a klonování ukázkového úložiště.
    1. Restartuje virtuální počítač.
    1. Vytvoří soubor RDP na ploše pro připojení k virtuálnímu počítači.

   Pokud se zobrazí výzva k zadání názvu virtuálního počítače, který chcete restartovat, můžete zkopírovat jeho název z výstupu skriptu. Výstup taky zobrazuje cestu k souboru RDP pro připojení k virtuálnímu počítači.

### <a name="set-auto-shutdown-schedule"></a>Nastavit plán automatického vypínání

Aby vám pomohla snižovat náklady, byl vytvořen vývojový virtuální počítač s plánem automatického vypnutí, který je nastaven na 1900 PST. Toto nastavení možná budete muset aktualizovat v závislosti na vašem umístění a plánu. Aktualizace plánu vypnutí:

1. V Azure Portal přejděte na virtuální počítač, který skript vytvořil.

1. V nabídce v levém podokně v části **operace** vyberte **Automatické vypnutí**.

1. Podle potřeby upravte **naplánované vypínání** a **časové pásmo** a vyberte **Uložit**.

## <a name="connect-to-the-development-vm"></a>Připojení k vývojovému virtuálnímu počítači

Teď, když jsme vytvořili virtuální počítač, potřebujeme dokončit instalaci softwaru potřebného k dokončení tohoto kurzu.

1. Dvakrát klikněte na soubor RDP, který skript vytvořil na vašem počítači.

1. Zobrazí se dialogové okno oznamující, že vydavatel vzdáleného připojení není znám. To je přijatelné, takže vyberte **připojit**.

1. Zadejte heslo správce, které jste zadali pro vytvoření virtuálního počítače, a klikněte na **OK**.

1. Zobrazí se výzva, abyste přijali certifikát pro virtuální počítač. Vyberte **Ano**.

## <a name="install-visual-studio-code-extensions"></a>Instalace rozšíření pro Visual Studio Code

Teď, když jste se připojili k vývojovému počítači, přidejte k Visual Studio Code několik užitečných rozšíření, která usnadňují vývojové prostředí.

1. Připojte se k vývojovému virtuálnímu počítači, otevřete okno PowerShellu a přejděte do adresáře **C:\source\IoTEdgeAndMlSample\DevVM** . Tento adresář byl vytvořen skriptem, který vytvořil virtuální počítač.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Spusťte následující příkaz, který povolí spouštění skriptů. Po zobrazení výzvy vyberte možnost **Ano pro všechny** .

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Spusťte skript rozšíření Visual Studio Code.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Skript se spustí během několika minut instalace rozšíření VS Code Extensions:

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Nastavení IoT Hub a úložiště

Tyto kroky obvykle provádí vývojář cloudu.

Azure IoT Hub je srdcem jakékoli aplikace IoT, protože zajišťuje zabezpečenou komunikaci mezi zařízeními IoT a cloudem. Je hlavním koordinačním bodem pro provoz řešení IoT Edge Machine Learning.

* IoT Hub používá trasy k přímému směrování příchozích dat ze zařízení IoT do jiných služeb pro příjem dat. Budeme využívat výhod IoT Hubch tras pro posílání dat zařízení do Azure Storage. V Azure Storage se data zařízení spotřebují Azure Machine Learning ke školení našeho zbývajícího RULho života.

* Později v tomto kurzu použijeme IoT Hub ke konfiguraci a správě našeho Azure IoT Edge zařízení.

V této části použijete skript k vytvoření služby Azure IoT Hub a účtu Azure Storage. Pak v Azure Portal nakonfigurujete trasu, která předává data přijatá centrem do kontejneru Azure Storage. Dokončení těchto kroků trvá přibližně 10 minut.

1. Připojte se k vývojovému virtuálnímu počítači, otevřete okno PowerShellu a přejděte do adresáře **IoTHub** .

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Spusťte skript pro vytváření. Pro ID předplatného, umístění a skupinu prostředků použijte stejné hodnoty jako při vytváření vývojového virtuálního počítače.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Budete vyzváni k přihlášení do Azure.
    * Skript potvrdí informace pro vytvoření vašeho centra a účtu úložiště. Pokračujte stisknutím klávesy **y** nebo **ENTER** .

Skript poběží přibližně dvě minuty. Po dokončení skript vytvoří výstup názvu služby IoT Hub a účtu úložiště.

## <a name="review-route-to-storage-in-iot-hub"></a>Kontrola trasy do úložiště v IoT Hub

V rámci vytváření služby IoT Hub vytvořil skript, který jsme spustili v předchozí části, také vlastní koncový bod a trasu. Trasy IoT Hub se skládají z výrazu dotazu a koncového bodu. Pokud zpráva odpovídá výrazu, data se odesílají podél trasy do přidruženého koncového bodu. Koncové body můžou být Event Hubs, Service Bus fronty a témata. V tomto případě je koncový bod kontejnerem objektů BLOB v účtu úložiště. Pojďme použít Azure Portal ke kontrole trasy vytvořené pomocí našeho skriptu.

1. Otevřete [Azure Portal](https://portal.azure.com) a v tomto kurzu vyhledejte skupinu prostředků, kterou používáte.

1. V seznamu prostředků vyberte IoT Hub vytvoření skriptu. Bude mít název končící náhodnými znaky, jako je například `IotEdgeAndMlHub-jrujej6de6i7w` .

1. V nabídce v levém podokně v části **zasílání zpráv** vyberte **směrování zpráv**.

1. Na stránce **směrování zpráv** vyberte kartu **vlastní koncové body** .

1. Rozbalte část **úložiště** :

   ![Ověřte, že turbofanDeviceStorage je v seznamu vlastních koncových bodů.](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   V seznamu vlastní koncové body se zobrazí **turbofanDeviceStorage** . Všimněte si následujících vlastností tohoto koncového bodu:

   * Odkazuje na kontejner úložiště objektů blob, který jste vytvořili s názvem `devicedata` , jak je uvedeno v **názvu kontejneru**.
   * **Formát názvu souboru** má jako poslední prvek v názvu oddíl. Pro operace se soubory, které provedeme v Azure Notebooks později v tomto kurzu, zjistíme, že tento formát je pohodlnější.
   * Jeho **stav** by měl být v pořádku.

1. Vyberte kartu **Trasy**.

1. Vyberte trasu s názvem **turbofanDeviceDataToStorage**.

1. Na stránce **Podrobnosti o trasách** si všimněte, že koncovým bodem trasy je koncový bod **turbofanDeviceStorage** .

   ![Přečtěte si podrobnosti o trase turbofanDeviceDataToStorage.](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Podívejte se na **dotaz směrování**, který je nastaven na **hodnotu true**. Toto nastavení znamená, že všechny zprávy telemetrie zařízení budou odpovídat této trase; a proto se všechny zprávy odešlou do koncového bodu **turbofanDeviceStorage** .

1. Vzhledem k tomu, že se neudělaly žádné úpravy, stačí tuto stránku zavřít.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme vytvořili IoT Hub a nakonfigurovali trasu k účtu Azure Storage. V dalším kroku pošleme data ze sady simulovaných zařízení prostřednictvím IoT Hub do účtu úložiště. Později v tomto kurzu až nakonfigurujeme naše IoT Edge zařízení a moduly, budeme znovu navštěvovat trasy a v dotazu směrování budete hledat trochu víc.

Další informace o krocích popsaných v této části Machine Learning v IoT Edge kurzu najdete v tématu:

* [Základy Azure IoT](../iot-fundamentals/index.yml)
* [Konfigurace směrování zpráv pomocí IoT Hub](../iot-hub/tutorial-routing.md)
* [Vytvoření centra IoT pomocí Azure Portal](../iot-hub/iot-hub-create-through-portal.md)

Pokračujte dalším článkem a vytvořte simulované zařízení, které chcete monitorovat.

> [!div class="nextstepaction"]
> [Generování dat zařízení](tutorial-machine-learning-edge-03-generate-data.md)