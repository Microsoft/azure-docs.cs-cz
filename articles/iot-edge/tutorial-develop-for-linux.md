---
title: Kurz – vývoj pro zařízení se systémem Linux pomocí Azure IoT Edge
description: Tento kurz vás provede nastavením vývojového počítače a cloudových prostředků pro vývoj IoT Edge modulů pomocí kontejnerů Linux pro zařízení se systémem Linux.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: b32fb0aa94e0fcc23edd6aae802cfd4162fa39f1
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572214"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Kurz: vývoj IoT Edgech modulů pro zařízení se systémem Linux

Pomocí Visual Studio Code můžete vyvíjet a nasazovat kód pro zařízení se systémem Linux s IoT Edge.

V rychlém startu jste vytvořili IoT Edge zařízení pomocí virtuálního počítače se systémem Linux a nasadili jste modul z Azure Marketplace. Tento kurz vás provede vývojem a nasazením vlastního kódu do zařízení IoT Edge. Tento článek je vhodným předpokladem pro ostatní kurzy, které vám pojdou podrobněji o konkrétních programovacích jazycích nebo službách Azure.

V tomto kurzu se používá příklad nasazení **modulu C# na zařízení se systémem Linux**. Tento příklad jste zvolili, protože se jedná o nejběžnější scénář pro vývojáře pro IoT Edge řešení. I v případě, že plánujete použití jiného jazyka nebo nasazení služby Azure, tento kurz je stále užitečný pro další informace o vývojářských nástrojích a konceptech. Dokončete tento Úvod do procesu vývoje a pak zvolte preferovaný jazyk nebo službu Azure, které chcete podrobně do podrobností.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Nastavte počítač pro vývoj.
> * Pomocí nástrojů IoT Edge pro Visual Studio Code vytvořte nový projekt.
> * Sestavte projekt jako kontejner a uložte ho do služby Azure Container Registry.
> * Nasaďte kód do zařízení IoT Edge.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Předpoklady

Vývojový počítač:

* V závislosti na vašich předvolbách pro vývoj můžete použít svůj vlastní počítač nebo virtuální počítač.
  * Ujistěte se, že váš vývojový počítač podporuje vnořenou virtualizaci. Tato funkce je nezbytná pro spuštění kontejnerového modulu, který nainstalujete v následující části.
* Většina operačních systémů, které mohou spustit kontejnerový modul, lze použít k vývoji IoT Edge moduly pro zařízení se systémem Linux. V tomto kurzu se používá počítač s Windows, ale odkazuje na známé rozdíly na macOS nebo Linux.
* Nainstalujte [Git](https://git-scm.com/), abyste si mohli stáhnout balíčky šablon modulů dále v tomto kurzu.  
* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

Azure IoT Edge zařízení v systému Linux:

* Doporučujeme, abyste ve vývojovém počítači nespouštěli IoT Edge, ale místo toho použijte samostatné zařízení. Toto rozlišení mezi vývojovým počítačem a IoT Edgeým zařízením přesněji odráží skutečný scénář nasazení a pomáhá udržet různé koncepty rovnou.
* Pokud nemáte k dispozici druhé zařízení, pomocí článku rychlý Start vytvořte zařízení IoT Edge v Azure pomocí [virtuálního počítače se systémem Linux](quickstart-linux.md).

Cloudové prostředky:

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) na bezplatné nebo standardní úrovni v Azure.

## <a name="key-concepts"></a>Klíčové koncepty

Tento kurz vás provede vývojem modulu IoT Edge. *IoT Edge modul*, nebo někdy jenom *modul* pro krátké, je kontejner se spustitelným kódem. Jeden nebo více modulů můžete nasadit do zařízení IoT Edge. Moduly provádějí konkrétní úkoly, jako je ingestování dat ze senzorů, čištění a analýza dat nebo posílání zpráv do služby IoT Hub. Další informace najdete v tématu [principy Azure IoT Edgech modulů](iot-edge-modules.md).

Při vývoji IoT Edgech modulů je důležité pochopit rozdíl mezi vývojovým počítačem a cílovým IoT Edge zařízením, kde se modul bude nakonec nasazovat. Kontejner, který sestavíte pro uložení kódu vašeho modulu, se musí shodovat s operačním systémem *cílového zařízení*. Nejběžnější scénář je například někdo, který vyvíjí modul na počítači s Windows, který necílí na zařízení se systémem Linux se spuštěným IoT Edge. V takovém případě by byl operační systém kontejneru Linux. Při procházení tohoto kurzu mějte na paměti rozdíl mezi *vývojovým operačním systémem* a *operačním systémem kontejneru*.

Tento kurz cílí na zařízení s Linux běžící IoT Edge. Můžete použít preferovaný operační systém, pokud váš vývojový počítač používá kontejnery Linux. Pro vývoj zařízení se systémem Linux doporučujeme použít Visual Studio Code, takže tento kurz bude používat. Můžete také použít Visual Studio, i když existují rozdíly v podpoře mezi dvěma nástroji.

V následující tabulce jsou uvedeny podporované vývojové scénáře pro **kontejnery platformy Linux** v Visual Studio Code a v aplikaci Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Architektura zařízení se systémem Linux** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Služby Azure** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Jazyky** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Další informace** | [Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Podpora pro zařízení se systémem Linux ARM64 je k dispozici ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Další informace najdete v tématu [vývoj a ladění ARM64 IoT Edgech modulů v Visual Studio Code (Preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

V tomto kurzu se naučíte postup vývoje Visual Studio Code. Pokud místo toho chcete použít aplikaci Visual Studio, přečtěte si pokyny v tématu [použití sady Visual studio 2019 k vývoji a ladění modulů pro Azure IoT Edge](how-to-visual-studio-develop-module.md).
## <a name="install-container-engine"></a>Nainstalovat modul pro kontejnery

Moduly IoT Edge jsou zabaleny jako kontejnery, takže potřebujete kontejnerový modul na svém vývojovém počítači, abyste je mohli sestavovat a spravovat. Kvůli podpoře funkcí a oblíbenosti doporučujeme použít Docker Desktop pro vývoj. Docker Desktop ve Windows umožňuje přepínat mezi kontejnery Linux a kontejnery Windows, abyste mohli snadno vyvíjet moduly pro různé typy IoT Edgech zařízení.

K instalaci na vývojový počítač použijte dokumentaci k Docker:

* [Instalace aplikace Docker Desktop pro Windows](https://docs.docker.com/docker-for-windows/install/)

  * Když nainstalujete Docker Desktop pro Windows, zobrazí se dotaz, jestli chcete použít kontejnery pro Linux nebo Windows. Toto rozhodnutí můžete kdykoli změnit pomocí snadného přepínače. Pro tento kurz používáme kontejnery Linux, protože naše moduly jsou cílené na zařízení se systémem Linux. Další informace najdete v tématu [přepínání mezi kontejnery Windows a Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Nainstalovat Docker Desktop pro Mac](https://docs.docker.com/docker-for-mac/install/)

* Informace o instalaci najdete v [tématu Docker CE](https://docs.docker.com/install/) pro různé platformy Linux.
  * Pro subsystém Windows pro Linux (WSL) nainstalujte Docker Desktop for Windows.

## <a name="set-up-vs-code-and-tools"></a>Nastavení VS Code a nástrojů

K vývoji IoT Edgech modulů použijte rozšíření IoT pro Visual Studio Code. Tato rozšíření poskytují šablony projektů, automatizují vytváření manifestu nasazení a umožňují vám monitorovat a spravovat IoT Edge zařízení. V této části nainstalujete Visual Studio Code a rozšíření IoT a pak nastavíte účet Azure pro správu prostředků IoT Hub v rámci Visual Studio Code.

1. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) na svém vývojovém počítači.

2. Po dokončení instalace vyberte **Zobrazit**  >  **rozšíření**.

3. Vyhledejte **nástroje Azure IoT Tools**, což je ve skutečnosti kolekce rozšíření, která vám pomůžou pracovat s IoT Hub a zařízeními IoT, a také vyvíjet IoT Edge moduly.

4. Vyberte **Install** (Nainstalovat). Každé zahrnuté rozšíření se nainstaluje jednotlivě.

5. Po dokončení instalace rozšíření otevřete paletu příkazů výběrem možnosti **Zobrazit**  >  **paletu příkazů**.

6. V paletě příkazů vyhledejte a vyberte **Azure: přihlásit** se. Podle pokynů se přihlaste ke svému účtu Azure.

7. V paletě příkazů znovu vyhledejte a vyberte **Azure IoT Hub: vyberte IoT Hub**. Podle pokynů vyberte předplatné Azure a centrum IoT.

8. Kliknutím na ikonu na panelu nástrojů na levé straně nebo výběrem **Zobrazit** Průzkumníka otevřete část Visual Studio Code Explorer  >  **Explorer**.

9. V dolní části okna Průzkumníka rozbalte nabídku sbalená **IoT Hub/zařízení Azure** . Měli byste vidět zařízení a zařízení IoT Edge přidružená ke službě IoT Hub, kterou jste vybrali prostřednictvím palety příkazů.

   ![Zobrazení zařízení ve službě IoT Hub](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Vytvořit nový projekt modulu

Rozšíření Azure IoT Tools poskytuje šablony projektů pro všechny podporované jazyky IoT Edge modulů v Visual Studio Code. Tyto šablony obsahují všechny soubory a kód, které potřebujete k nasazení pracovního modulu k testování IoT Edge, nebo vám poskytne výchozí bod pro přizpůsobení šablony s vlastní obchodní logikou.

Pro tento kurz používáme šablonu modulu C#, protože se jedná o nejčastěji používanou šablonu.

### <a name="create-a-project-template"></a>Vytvoření šablony projektu

V paletě příkazu Visual Studio Code vyhledejte a vyberte **Azure IoT Edge: nové IoT Edge řešení**. Postupujte podle zobrazených výzev a pomocí následujících hodnot vytvořte řešení:

   | Pole | Hodnota |
   | ----- | ----- |
   | Vybrat složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název vašeho řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Vyberte **modul C#**. |
   | Zadejte název modulu | Přijměte výchozí **SampleModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Vaše image kontejneru se předem vyplní názvem, který jste zadali v posledním kroku. Položku **localhost: 5000** nahraďte hodnotou **přihlašovacího serveru** z služby Azure Container Registry. Hodnotu přihlašovacího serveru můžete načíst ze stránky přehled v registru kontejneru v Azure Portal. <br><br> Konečné úložiště imagí vypadá jako \<registry name\> . azurecr.IO/SampleModule. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-develop-for-linux/image-repository.png)

Jakmile se nové řešení načte v okně Visual Studio Code, Seznamte se se soubory, které vytvořil:

* Složka **. VSCode** obsahuje soubor s názvem **launch.jsv**, který se používá pro moduly ladění.
* Složka **modulů** obsahuje složku pro každý modul ve vašem řešení. Nyní, který by měl být **SampleModule** nebo libovolný název, který jste přiřadili modulu. Složka SampleModule obsahuje hlavní kód programu, metadata modulu a několik souborů Docker.
* Soubor **. env** obsahuje přihlašovací údaje do vašeho registru kontejneru. Tyto přihlašovací údaje se sdílí s vaším zařízením IoT Edge, takže mají přístup k vyžádání imagí kontejneru.
* **deployment.debug.template.js** Souborová a **deployment.template.jsv** souboru jsou šablony, které vám pomohou vytvořit manifest nasazení. *Manifest nasazení* je soubor, který definuje přesně to, které moduly se mají na zařízení nasadit, jak by se měly nakonfigurovat a jak můžou komunikovat mezi sebou a cloudem. Soubory šablon používají ukazatele pro některé hodnoty. Při transformaci šablony na skutečný manifest nasazení jsou ukazatele nahrazeny hodnotami přijatými z jiných souborů řešení. V šabloně nasazení vyhledejte dva běžné zástupné symboly:

  * V části pověření registru se adresa vyplní z informací, které jste zadali při vytváření řešení. Uživatelské jméno a heslo však odkazují na proměnné uložené v souboru. env. Tato konfigurace je určena pro zabezpečení, protože soubor. ENV je git ignoruje, ale šablona nasazení ne.
  * V části SampleModule se image kontejneru nevyplní, přestože jste při vytváření řešení zadali úložiště imagí. Tento zástupný symbol odkazuje na **module.jssouboru ve** složce SampleModule. Pokud přejdete na tento soubor, uvidíte, že pole Image obsahuje úložiště, ale také hodnotu značky, která se skládá z verze a platformy kontejneru. Verzi můžete iterovat v rámci vašeho vývojového cyklu ručně a vybrat kontejnerovou platformu pomocí přepínače, který zavedeme později v této části.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Zadejte přihlašovací údaje registru pro agenta IoT Edge.

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime potřebuje tyto přihlašovací údaje pro vyžádání imagí kontejneru do zařízení IoT Edge.

>[!NOTE]
>Pokud jste nenahradili hodnotu **localhost: 5000** hodnotou přihlašovacího serveru z služby Azure Container Registry, v kroku [**Vytvoření šablony projektu**](#create-a-project-template) se zobrazí soubor **. env** a oddíl registryCredentials manifestu nasazení. 

Rozšíření IoT Edge se pokusí načíst přihlašovací údaje registru kontejneru z Azure a naplnit je do souboru prostředí. Zkontrolujte, jestli jsou vaše přihlašovací údaje už zahrnuté. Pokud ne, přidejte je nyní:

1. Otevřete soubor **. env** v řešení modulu.
2. Přidejte hodnoty **uživatelského jména** a **hesla** , které jste zkopírovali z služby Azure Container Registry.
3. Uložte změny do souboru. env.

### <a name="select-your-target-architecture"></a>Vyberte cílovou architekturu.

V současné době Visual Studio Code může vyvíjet moduly C# pro zařízení se systémem Linux AMD64 a ARM32v7. Musíte vybrat architekturu, na kterou cílíte, vzhledem k tomu, že to ovlivňuje způsob sestavení a spuštění kontejneru. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro řešení Edge** nebo vyberte ikonu zástupce na bočním panelu v dolní části okna.

   ![Vybrat ikonu architektury na bočním panelu](./media/tutorial-develop-for-linux/select-architecture.png)

2. V paletě příkazů vyberte v seznamu možností cílovou architekturu. Pro tento kurz používáme virtuální počítač s Ubuntu jako zařízení IoT Edge, takže se zachová výchozí hodnota **amd64**.

### <a name="review-the-sample-code"></a>Kontrola ukázkového kódu

Šablona řešení, kterou jste vytvořili, obsahuje vzorový kód pro modul IoT Edge. Tento vzorový modul jednoduše přijímá zprávy a pak je předává. Funkce kanálu ukazuje důležitou koncepci v IoT Edge, což je způsob, jakým vzájemně komunikují moduly.

Každý modul může mít v kódu deklarovaných víc *vstupních* a *výstupních* front. Rozbočovač IoT Edge běžící na zařízení směruje zprávy z výstupu jednoho modulu do vstupu jednoho nebo více modulů. Konkrétní kód pro deklarování vstupů a výstupů se liší mezi jazyky, ale koncept je stejný ve všech modulech. Další informace o směrování mezi moduly naleznete v tématu [Declare Routes](module-composition.md#declare-routes).

Vzorový kód jazyka C#, který je součástí šablony projektu, používá [třídu ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) ze sady SDK IoT Hub pro .NET.

1. Otevřete soubor **program.cs** , který se nachází uvnitř **modulů/SampleModule/** složka.

2. V program.cs Najděte metodu **SetInputMessageHandlerAsync** .

3. Metoda [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) nastaví vstupní frontu pro příjem příchozích zpráv. Projděte si tuto metodu a podívejte se, jak inicializuje vstupní frontu s názvem **input1**.

   ![Najít název vstupu v konstruktoru SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Dále vyhledejte metodu **SendEventAsync** .

5. Metoda [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) zpracovává přijaté zprávy a nastavuje výstupní frontu, která je předávat společně. Projděte si tuto metodu a podívejte se, že Inicializuje výstupní frontu s názvem **output1**.

   ![Najít název výstupu v SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Otevřete **deployment.template.jsv** souboru.

7. Vyhledejte vlastnost **modulů** $edgeAgent požadovaných vlastností.

   Tady by měly být uvedené dva moduly. Jedním z nich je modul **SimulatedTemperatureSensor** , který je ve výchozím nastavení součástí všech šablon, aby poskytoval Simulovaná data o teplotě, která můžete použít k otestování modulů. Druhý je modul **SampleModule** , který jste vytvořili jako součást tohoto řešení.

8. V dolní části souboru vyhledejte požadované vlastnosti pro modul **$edgeHub** .

   Jednou z funkcí modulu centra IoT Edge je směrování zpráv mezi všemi moduly v nasazení. Zkontrolujte hodnoty ve vlastnosti **Routes** . Jedna trasa, **SampleModuleToIoTHub**, používá zástupný znak (* *\** _) k označení všech zpráv přicházejících z libovolných výstupních front v modulu SampleModule. Tyto zprávy patří do _ $-Stream *, což je vyhrazený název, který označuje IoT Hub. Druhá trasa, **sensorToSampleModule**, přebírá zprávy přicházející z modulu SimulatedTemperatureSensor a směruje je do vstupní fronty *input1* , kterou jste viděli inicializováni v kódu SampleModule.

   ![Kontrola tras v deployment.template.jsna](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Sestavení a nabízení řešení

Zkontrolovali jste kód modulu a šablonu nasazení, abyste pochopili některé koncepty nasazení. Nyní jste připraveni sestavit image kontejneru SampleModule a vložit ji do registru kontejneru. S rozšířením nástrojů IoT pro Visual Studio Code tento krok také generuje manifest nasazení na základě informací v souboru šablony a informací o modulu ze souborů řešení.

### <a name="sign-in-to-docker"></a>Přihlásit se k Docker

Poskytněte přihlašovací údaje registru kontejneru do Docker, aby mohli ukládat image kontejneru do registru.

1. Otevřete Visual Studio Code Integrated Terminal výběrem možnosti **Zobrazit**  >  **terminál**.

2. Přihlaste se k Docker pomocí přihlašovacích údajů služby Azure Container Registry, které jste uložili po vytvoření registru.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití nástroje `--password-stdin` . I když se tento osvědčený postup doporučuje u produkčních scénářů, je mimo rozsah tohoto kurzu. Další informace najdete v tématu přihlašovací Reference k [Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .
   
3. Přihlášení ke službě Azure Container Registry

   ```cmd/sh
   az acr login -n <ACR registry name>
   ```

### <a name="build-and-push"></a>Sestavení a vložení

Visual Studio Code má teď přístup k vašemu registru kontejneru, takže je čas převést kód řešení na Image kontejneru.

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem na **deployment.template.jsna** soubor a vyberte **sestavení a nabízené IoT Edge řešení**.

   ![Sestavování a nabízených IoT Edgech modulů](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Příkaz Build a push spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config** , která obsahuje úplný manifest nasazení, a vyplní informace v šabloně nasazení a dalších souborech řešení. Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. Pak se spustí a nahraje `docker push` úložiště imagí do registru kontejneru.

   Tento proces může trvat několik minut poprvé, ale při příštím spuštění příkazů je rychlejší.

2. Otevřete **deployment.amd64.js** v souboru ve složce nově vytvořená konfigurace. Název souboru odráží cílovou architekturu, takže se bude lišit, pokud vyberete jinou architekturu.

3. Všimněte si, že dva parametry, které obsahovaly zástupné symboly nyní, jsou vyplněny odpovídajícími hodnotami. Část **registryCredentials** obsahuje uživatelské jméno a heslo v registru, které jste si vyžádali ze souboru. env. **SampleModule** má úplné úložiště imagí s označením název, verze a architektura z module.jsv souboru.

4. Otevřete **module.js** v souboru ve složce SampleModule.

5. Změňte číslo verze image modulu. (Verze, nikoli verze $schema.) Například Zvyšte číslo verze opravy na **0.0.2** , jako kdyby jsme v kódu modulu udělali malou opravu.

   >[!TIP]
   >Verze modulů umožňují správu verzí a umožňují testovat změny v malých sadě zařízení před nasazením aktualizací do produkčního prostředí. Pokud před vytvořením a vložením nezvýšíte verzi modulu, přepíšete úložiště v registru kontejneru.

6. Uložte změny do module.jsv souboru.

7. Znovu klikněte pravým tlačítkem na **deployment.template.jsna** soubor a pak vyberte **sestavení a nabízené IoT Edge řešení**.

8. Otevřete znovu **deployment.amd64.jsv** souboru. Všimněte si, že nový soubor nebyl vytvořen při opětovném spuštění příkazu Build a push. Místo toho byl stejný soubor aktualizován tak, aby odrážel změny. Obrázek SampleModule nyní odkazuje na verzi 0.0.2 kontejneru.

9. Chcete-li dále ověřit, co byl příkaz Build and push, přejděte na [Azure Portal](https://portal.azure.com) a přejděte do registru kontejneru.

10. V registru kontejneru vyberte **úložiště** a pak **SampleModule**. Ověřte, zda byly do registru vloženy obě verze bitové kopie.

    ![Zobrazit obě verze imagí v registru kontejnerů](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Odstranit potíže

Pokud narazíte na chyby při sestavování a vkládání image modulu, často je třeba provést konfiguraci Docker na vašem vývojovém počítači. Ke kontrole konfigurace použijte následující kontroly:

* Spustili jste `docker login` příkaz s použitím přihlašovacích údajů, které jste zkopírovali z registru kontejneru? Tyto přihlašovací údaje se liší od těch, které používáte k přihlášení do Azure.
* Je vaše úložiště kontejnerů správné? Má váš správný název registru kontejneru a správný název modulu? Pro kontrolu otevřete **module.js** v souboru ve složce SampleModule. Hodnota úložiště by měla vypadat jako **\<registry name\> . azurecr.IO/SampleModule**.
* Pokud jste pro modul použili jiný název než **SampleModule** , znamená to, že se tento název v rámci řešení konzistentně používá?
* Je váš počítač se spuštěným stejným typem kontejnerů, které vytváříte? Tento kurz je určen pro zařízení se systémem Linux IoT Edge, takže Visual Studio Code by měl být na bočním panelu **amd64** nebo **arm32v7** a Docker Desktop by měl spouštět kontejnery Linux.  

## <a name="deploy-modules-to-device"></a>Nasadit moduly do zařízení

Ověřili jste, že jsou sestavené image kontejneru uložené v registru kontejnerů, takže je čas je nasadit do zařízení. Ujistěte se, že je zařízení IoT Edge spuštěné.

1. V Průzkumníkovi Visual Studio Code v části **Azure IoT Hub** rozbalte **zařízení** , abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem na zařízení IoT Edge, do kterého chcete nasadit, a pak vyberte **vytvořit nasazení pro jedno zařízení**.

   ![Vytvoření nasazení pro jedno zařízení](./media/tutorial-develop-for-linux/create-deployment.png)

3. V Průzkumníku souborů přejděte do **konfigurační** složky a vyberte **deployment.amd64.jsv** souboru.

   Nepoužívejte deployment.template.jsv souboru, ve kterém nejsou k dispozici přihlašovací údaje registru kontejneru nebo hodnoty imagí modulu. Pokud cílíte na zařízení se systémem Linux ARM32, manifest nasazení bude pojmenován deployment.arm32v7.jsv.

4. V části zařízení rozbalte **moduly** a zobrazte seznam nasazených a spuštěných modulů. Klikněte na tlačítko pro obnovení. Měli byste vidět nové moduly SimulatedTemperatureSensor a SampleModule běžící na vašem zařízení.

   Spuštění modulů může trvat několik minut. Modul runtime IoT Edge musí přijmout nový manifest nasazení, Stáhnout image modulu z modulu runtime kontejneru a pak začít každý nový modul.

   ![Zobrazit moduly běžící na zařízení IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Zobrazit zprávy ze zařízení

Kód SampleModule přijímá zprávy přes vstupní frontu a předává je spolu s její výstupní frontou. Manifest nasazení deklaroval trasy, které předaly zprávy do SampleModule z SimulatedTemperatureSensor, a pak přesměrují zprávy od SampleModule do IoT Hub. Nástroje Azure IoT Tools for Visual Studio Code umožňují zobrazovat zprávy při jejich doručování IoT Hub z jednotlivých zařízení.

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem na zařízení IoT Edge, které chcete monitorovat, a pak vyberte **Spustit sledování integrovaného koncového bodu události**.

2. Podívejte se na okno výstup v Visual Studio Code a zobrazte zprávy přicházející ve službě IoT Hub.

   ![Zobrazení zpráv příchozího zařízení do cloudu](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Zobrazit změny na zařízení

Pokud chcete zjistit, co se děje na samotném zařízení, použijte příkazy v této části k zkontrolování modulu runtime IoT Edge a modulů spuštěných ve vašem zařízení.

Příkazy v této části jsou pro vaše zařízení IoT Edge, ne pro váš vývojový počítač. Pokud pro zařízení IoT Edge používáte virtuální počítač, připojte se k němu hned. V Azure přejděte na stránku Přehled virtuálního počítače a vyberte **připojit** pro přístup k připojení zabezpečeného prostředí.

* Zobrazte všechny moduly nasazené do zařízení a zkontrolujte jejich stav:

   ```bash
   iotedge list
   ```

   Měli byste vidět čtyři moduly: dva moduly IoT Edge runtime, SimulatedTemperatureSensor a SampleModule. Všechny čtyři by měly být uvedeny jako spuštěné.

* Zkontrolujte protokoly konkrétního modulu:

   ```bash
   iotedge logs <module name>
   ```

   V IoT Edgech modulech se rozlišují malá a velká písmena.

   V protokolech SimulatedTemperatureSensor a SampleModule by se měly zobrazovat zprávy, které zpracovávají. Modul edgeAgent je zodpovědný za spouštění jiných modulů, takže jeho protokoly budou mít informace o implementaci manifestu nasazení. Pokud některý z modulů není v seznamu nebo není spuštěný, budou pravděpodobně chyby v protokolech edgeAgent. Modul edgeHub zodpovídá za komunikaci mezi moduly a IoT Hub. Pokud jsou moduly v provozu, ale zprávy nepřicházejí do služby IoT Hub, budou pravděpodobně chyby v protokolech edgeHub.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurace a prostředky Azure, které jste použili v tomto článku, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte Visual Studio Code na svém vývojovém počítači a z něho jste nasadili první IoT Edge modul. Teď, když znáte základní koncepty, zkuste do modulu přidat funkce, aby mohli analyzovat data, která procházejí. Vyberte preferovaný jazyk:

> [!div class="nextstepaction"]
> Jazyk [C](tutorial-c-module.md) 
>  [Jazyk C#](tutorial-csharp-module.md) 
>  [Jazyk Java](tutorial-java-module.md) 
>  [Node.js](tutorial-node-module.md) 
>  [Python](tutorial-python-module.md)
