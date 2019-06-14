---
title: Nastavení prostředí – Machine Learning v Azure IoT Edge | Dokumentace Microsoftu
description: Příprava prostředí pro vývoj a nasazení modulů pro strojové učení na hraničních zařízeních.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f8652dab5db8bbd59982cb1dda727698868df23c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057740"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Kurz: Nastavení prostředí pro machine learningu na hraničních zařízeních IoT

> [!NOTE]
> Tento článek je součástí série kurz o používání Azure Machine learningu na hraničních zařízeních IoT. Pokud jste jste dostali přímo v tomto článku, doporučujeme vám začneme [nejprve článek](tutorial-machine-learning-edge-01-intro.md) v řadě nejlepších výsledků.

Tento článek od začátku do konce Azure Machine learningu na hraničních zařízeních IoT kurz pomůže připravit vaše prostředí pro vývoj a nasazení. Nejprve nastavení vývojového počítače se všemi nástroji, které potřebujete. Vytvořte nezbytné cloudovým prostředkům v Azure.

## <a name="set-up-a-development-machine"></a>Nastavení vývojového počítače

Tento krok obvykle provádí vývojář cloudu. Některý software může být užitečné pro mezi odborníky přes data.

V průběhu tohoto článku jsme provádění různých úloh developer, včetně psaní kódu, kompilaci, konfigurace a nasazení modulů IoT Edge a IoT zařízení. Snadné použití jsme vytvořili Powershellový skript, který vytvoří virtuální počítač Azure s mnoha z požadovaných součástí už nakonfigurovaná. Virtuální počítač, který vytvoříme musí být schopna zpracovávat [vnořená virtualizace](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), což je důvod, proč jsme zvolili DS8V3 velikost počítače.

Vývoj virtuálního počítače se dá nastavit pomocí:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Plocha docker pro Windows](https://www.docker.com/products/docker-desktop)
* [Git pro Windows](https://gitforwindows.org/)
* [Správce přihlašovacích údajů Git pro Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [Sada .NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Rozšíření pro VS Code](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Pro vývojáře virtuálního počítače není nezbytně nutné – všechny nástroje pro vývoj, může běžet na místním počítači. Však důrazně doporučujeme používat virtuální počítač k zajištění stejných podmínek.

Vytvoření a konfigurace virtuálního počítače trvá přibližně 30 minut.

### <a name="get-the-script"></a>Získat skript

Klonovat nebo stáhnout Poweshellový skript z [strojového učení a IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) ukázkové úložiště.

### <a name="create-an-azure-virtual-machine"></a>Vytvoření virtuálního počítače Azure

Adresář DevVM obsahuje soubory potřebné k vytvoření virtuálního počítače Azure vhodná pro dokončení tohoto kurzu.

1. Otevřete Powershell jako správce a přejděte do adresáře, kam jste stáhli kód. Označujeme do kořenového adresáře pro zdroj jako `<srcdir>`.

    ```powershell
    cd \<srcdir\>\IoTEdgeAndMlSample\DevVM
    ```

2. Spusťte následující příkaz, aby bylo možné spouštění skriptů. Zvolte **Ano všem** po zobrazení výzvy.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Spusťte AzureDevVM.ps1 vytvořit z tohoto adresáře.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * Po zobrazení výzvy zadejte následující informace:
      * **ID předplatného Azure**: ID vašeho předplatného, které najdete na webu Azure Portal
      * **Název skupiny prostředků**: Název nové nebo existující prostředek skupiny v Azure
      * **Umístění**: Vyberte umístění Azure, ve kterém se vytvoří virtuální počítač. Například westus2 nebo northeurope. Další informace najdete v tématu [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: Poskytují snadno zapamatovatelné jméno pro účet správce, který chcete vytvořit a používat ve virtuálním počítači.
      * **AdminPassword**: Nastavte heslo pro účet správce na virtuálním počítači.

    * Pokud nemáte nainstalovaný Azure PowerShell, skript nainstaluje [modulu Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * Budete vyzváni k přihlášení do Azure.

    * Skript potvrdí informace pro vytvoření virtuálního počítače. Stisknutím klávesy `y` nebo `Enter` pokračujte.

Skript se spustí pro několik minut, než jak je provede následující kroky:

* Vytvořte skupinu prostředků, pokud neexistuje
* Nasazení virtuálního počítače
* Povolení technologie Hyper-V na virtuálním počítači
* Instalace softwaru nutné pro vývoj a naklonujte ukázkové úložiště
* Restartujte virtuální počítač
* Vytvořit soubor protokolu RDP na vašem počítači pro připojení k virtuálnímu počítači

### <a name="set-auto-shutdown-schedule"></a>Nastaveného plánu automatické vypnutí

Která vám pomůže snížit náklady, byl vytvořen virtuální počítač s plán automatické vypnutí, který je nastaven k hodnotě 1900 PST. Budete muset aktualizovat tento časování v závislosti na umístění a plán. Chcete-li aktualizovat plán vypnutí:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Přejděte ke svému virtuálnímu počítači ve skupině prostředků, které jste zadali v předchozí části.

3. Vyberte **automatického vypínání** navigátoru straně.

4. Zadejte nový čas ukončení v **naplánované vypnutí** nebo změnit **časové pásmo** klikněte **Uložit**.

### <a name="connect-and-configure-development-machine"></a>Připojte a nakonfigurujte počítač pro vývoj

Teď, když jsme vytvořili virtuální počítač budeme potřebovat k dokončení instalace software potřebný k dokončení tohoto kurzu.

#### <a name="start-a-remote-desktop-session"></a>Spustit relaci vzdálené plochy

1. Vytvoření virtuálního počítače skript vytvořený soubor RDP na ploše.

2. Poklikejte na soubor s názvem  **\<název virtuálního počítače Azure\>RDP**.

3. Zobrazí se dialogové okno oznamující, že vydavatele vzdáleného připojení neznámý. Klikněte na tlačítko **nezobrazovat dotaz pro připojení k tomuto počítači** vyberte zaškrtávací políčko **připojit**.

4. Po zobrazení výzvy zadejte AdminPassword, který jste použili při spuštění skriptu pro nastavení virtuálního počítače a klikněte na tlačítko **OK**.

5. Zobrazí výzva k přijetí certifikátu pro virtuální počítač. Vyberte **nezobrazovat dotaz pro připojení k tomuto počítači** a zvolte **Ano**.

#### <a name="install-visual-studio-code-extensions"></a>Instalace rozšíření Visual Studio Code

Teď, když jste připojili do vývojového počítače, přidejte některá užitečná rozšíření pro Visual Studio Code, chcete-li usnadnit vývoj.

1. V okně Powershellu přejděte do **C:\\zdroj\\IoTEdgeAndMlSample\\DevVM**.

2. Povolit skripty, které se spouští na virtuálním počítači tak, že zadáte.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Spusťte skript.

    ```powershell
    .\\Enable-CodeExtensions.ps1
    ```

4. Skript se spustí na několik minut, instalaci rozšíření VS code:

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Nastavení služby IoT Hub a úložiště

Tyto kroky jsou obvykle provádí vývojář cloudu.

Azure IoT Hub je základem jakékoli aplikace IoT. Zpracovává zabezpečenou komunikaci mezi zařízeními IoT a cloudem. Je hlavní koordinace bod pro operaci hraničních zařízeních IoT řešení machine learningu.

* IoT Hub používá trasy pro přesměrování příchozích dat ze zařízení IoT k jiné navazujících službách. My podnikneme výhod služby IoT Hub trasy k odesílání dat zařízení do služby Azure Storage, kde mohou být využívány službou Azure Machine Learning k trénování naše zbývající dobu životnosti (RUL) třídění.

* Později v tomto kurzu použijeme službu IoT Hub ke konfiguraci a správě našich zařízení Azure IoT Edge.

V této části použijete skript k vytvoření služby Azure IoT hub a účet služby Azure Storage. Potom nakonfigurujte trasy, který předává data přijatá službou IOT hub a kontejneru úložiště objektů Blob v Azure pomocí webu Azure portal. Tyto kroky trvat asi 10 minut.

### <a name="create-cloud-resources"></a>Vytvoření prostředků cloudu

1. Na svém vývojovém počítači otevřete okno Powershellu.

1. Přejděte do adresáře, IOT hub.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Spuštění skriptu pro vytváření. Použijte stejné hodnoty pro předplatné ID, umístění a skupině prostředků, jako jste to udělali při vytváření vývojový virtuální počítač.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Budete vyzváni k přihlášení do Azure.
    * Skript potvrdí informace pro vytvoření vašeho centra a účet úložiště. Stisknutím klávesy `y` nebo `Enter` pokračujte.

Skript spusťte trvá asi 2 minuty. Jakmile budete hotovi, skript vypíše název rozbočovače a účet úložiště.

### <a name="review-route-to-storage-in-iot-hub"></a>Projděte si postup úložiště ve službě IoT Hub

Při vytváření služby IoT hub skript, který jsme spustili v předchozí části také vytvořit vlastní koncový bod a trasy. IoT Hub trasy se skládají z výrazu dotazu a koncový bod. Pokud zpráva odpovídá výrazu, data se odesílají na trase přidruženého koncového bodu. Koncové body může být Služba Event Hubs, fronty Service Bus a témat. Koncový bod v tomto případě je kontejner objektů Blob v účtu úložiště. Na webu Azure portal umožňuje použít ke kontrole vytvořen skriptem naše trasy.

1. Otevřete web [Azure Portal](http://portal.azure.com).

1. Vyberte všechny služby z levé tabulky, do vyhledávacího pole zadejte IoT a vyberte **služby IoT Hub**.

1. Zvolte, že služby IoT Hub vytvořili v předchozím kroku.

1. V části Navigátor na straně služby IoT Hub, zvolte **směrování zpráv**.

1. Zpráva směrovací stránky obsahuje dvě karty **trasy** a **vlastní koncové body**. Vyberte **vlastní koncové body** kartu.

1. V části **úložiště objektů Blob**vyberte **turbofanDeviceStorage**.

1. Názvem Všimněte si, že tento koncový bod odkazuje na kontejner objektů blob **devicedata** v účtu úložiště, vytvořili v předchozím kroku, který se nazývá **iotedgeandml\<jedinečnou příponu\>** .

1. Všimněte si také, **formát názvu souboru objektu Blob** se změnil z výchozího formátu místo toho umístit jako poslední prvek v názvu oddílu. Se nám najít, že tento formát je vhodnější pro operace souboru, které budeme provádět pomocí poznámkových bloků Azure v pozdější části kurzu.

1. Zavřete okno Podrobnosti o koncový bod se vrátíte **směrování zpráv** stránky.

1. Vyberte **trasy** kartu.

1. Vyberte trasa s názvem **turbofanDeviceDataToStorage**.

1. Všimněte si, že je koncový bod trasy **turbofanDeviceStorage** vlastní koncový bod.

1. Podívejte se na směrování dotaz, který je nastaven na **true**. To znamená, že všechny telemetrické zprávy typu zařízení budou odpovídat tuto trasu, a proto všechny zprávy se odešlou do **turbofanDeviceStorage** koncového bodu.

1. Zavřete podrobnosti trasy.

## <a name="next-steps"></a>Další postup

V tomto článku jsme vytvořili službu IoT Hub a nakonfigurovali trasu k účtu služby Azure Storage. V následujícím článku pošleme data ze sady simulovaných zařízení prostřednictvím služby IoT Hub do účtu úložiště. Později v tomto kurzu poté, co jsme nakonfigurovali našich zařízení IoT Edge a moduly, budeme návštěvě trasy a vypadat trochu více na směrování dotazů.

Další informace o krocích, které jsou popsané v této části služby Machine Learning na hraničních zařízeních IoT kurz najdete v tématu:

* [Základy Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Konfigurace směrování zpráv pomocí služby IoT Hub](../iot-hub/tutorial-routing.md)
* [Vytvoření IoT hubu pomocí webu Azure portal](../iot-hub/iot-hub-create-through-portal.md)

Pokračujte k dalšímu článku k vytvoření simulovaného zařízení k monitorování.

> [!div class="nextstepaction"]
> [Generovat data o zařízení](tutorial-machine-learning-edge-03-generate-data.md)
