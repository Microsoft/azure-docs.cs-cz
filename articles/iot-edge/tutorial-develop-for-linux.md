---
title: Výuka – vývoj modulu pro linuxová zařízení pomocí Azure IoT Edge
description: Tento kurz vás provede nastavením vývojových strojů a cloudových prostředků pro vývoj modulů IoT Edge pomocí linuxových kontejnerů pro linuxová zařízení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a127c8c77f97708729cf91d6ced214e98924fb6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78943014"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Kurz: Vývoj modulů IoT Edge pro linuxová zařízení

Pomocí kódu Visual Studia můžete vyvíjet a nasazovat kód do linuxových zařízení se systémem IoT Edge.

V rychlém startu jste vytvořili zařízení IoT Edge pomocí virtuálního počítače s Linuxem a nasadili modul z Azure Marketplace. Tento kurz vás provede vývojem a nasazením vlastního kódu do zařízení IoT Edge. Tento článek je užitečným předpokladem pro další kurzy, které jdou do podrobnějších informací o konkrétních programovacích jazycích nebo službách Azure.

Tento kurz používá příklad nasazení **modulu C# do zařízení S IP**. Tento příklad byl vybrán, protože je nejběžnější vývojářský scénář pro řešení IoT Edge. I v případě, že máte v plánu na použití jiného jazyka nebo nasazení služby Azure, tento kurz je stále užitečné se dozvědět o vývoji nástrojů a konceptů. Dokončete tento úvod do procesu vývoje a pak zvolte preferovaný jazyk nebo službu Azure a ponořte se do podrobností.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Nastavte svůj vývojový stroj.
> * Pomocí nástrojů IoT Edge pro visual studio kód k vytvoření nového projektu.
> * Sestavte si svůj projekt jako kontejner a uložte ho do registru kontejnerů Azure.
> * Nasaďte kód do zařízení IoT Edge.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Klíčové koncepty

Tento kurz vás provede vývojem modulu IoT Edge. *Modul IoT Edge*, nebo někdy jen *modul* pro krátké, je kontejner s spustitelný kód. Do zařízení IoT Edge můžete nasadit jeden nebo více modulů. Moduly provádějí specifické úlohy, jako je ingestování dat ze senzorů, čištění a analýza dat nebo odesílání zpráv do centra IoT Hub. Další informace najdete [v tématu Principy modulů Azure IoT Edge](iot-edge-modules.md).

Při vývoji modulů IoT Edge je důležité pochopit rozdíl mezi vývojovým počítačem a cílovým zařízením IoT Edge, kde bude modul nakonec nasazen. Kontejner, který vytvoříte pro uložení kódu modulu, musí odpovídat operačnímu systému (OS) *cílového zařízení*. Nejběžnějším scénářem je například někdo, kdo vyvíjí modul v počítači se systémem Windows, který má v úmyslu cílit na zařízení S Linuxem se systémem IoT Edge. V takovém případě by operační systém kontejneru linux. Jak budete procházet tento výukový program, mějte na paměti rozdíl mezi *os vývojového stroje* a *kontejneru OS*.

Tento kurz se zaměřuje na linuxová zařízení se systémem IoT Edge. Můžete použít preferovaný operační systém tak dlouho, dokud váš vývojový počítač běží linuxové kontejnery. Doporučujeme používat Visual Studio Code k vývoji pro zařízení s Linuxem, takže to je to, co tento kurz bude používat. Můžete také použít Visual Studio, i když existují rozdíly v podpoře mezi dvěma nástroji.

V následující tabulce jsou uvedeny podporované scénáře vývoje **pro kontejnery Linuxu** v jazyce Visual Studio Code a Visual Studiu.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Architektura zařízení Linux** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Služby Azure** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Jazyky** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Více informací** | [Azure IoT Edge pro kód Visual Studia](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Nástroje Azure IoT Edge pro Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Nástroje Azure IoT Edge pro Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Podpora pro zařízení Linux ARM64 je k dispozici ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Další informace naleznete v [tématu Vývoj a ladění modulů ARM64 IoT Edge v kódu Visual Studio (preview).](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)

Tento kurz učí kroky vývoje pro kód sady Visual Studio. Pokud byste raději použít Visual Studio, naleznete v pokynech v [použití Visual Studio 2019 k vývoji a ladění modulů pro Azure IoT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Požadavky

Vývojový stroj:

* V závislosti na předvolbách vývoje můžete použít vlastní počítač nebo virtuální počítač.
  * Ujistěte se, že váš vývojový počítač podporuje vnořené virtualizace. Tato funkce je nezbytná pro spuštění kontejnerového motoru, který nainstalujete v další části.
* Většina operačních systémů, které mohou spustit modul kontejneru lze použít k vývoji modulů IoT Edge pro zařízení s Linuxem. Tento kurz používá počítač se systémem Windows, ale poukazuje na známé rozdíly v systému MacOS nebo Linux.
* Nainstalujte [Git](https://git-scm.com/), chcete-li později v tomto kurzu vytáhnout balíčky šablon modulu.  
* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

Zařízení Azure IoT Edge v Linuxu:

* Doporučujeme nespouštět IoT Edge ve vývojovém počítači, ale místo toho použít samostatné zařízení. Tento rozdíl mezi vývojovým počítačem a zařízením IoT Edge přesněji odráží skutečný scénář nasazení a pomáhá udržovat různé koncepty rovně.
* Pokud nemáte k dispozici druhé zařízení, použijte článek quickstart k vytvoření zařízení IoT Edge v Azure s [virtuálním počítačem S Linuxem](quickstart-linux.md).

Cloudové prostředky:

* Bezplatné nebo standardní služby [IoT hub](../iot-hub/iot-hub-create-through-portal.md) v Azure.

## <a name="install-container-engine"></a>Instalace kontejnerového motoru

Moduly IoT Edge jsou zabaleny jako kontejnery, takže potřebujete modul kontejneru ve vývojovém počítači k jejich sestavení a správě. Doporučujeme Docker Desktop pro vývoj z důvodu jeho funkce podporu a popularitu. Docker Desktop v systému Windows umožňuje přepínat mezi kontejnery Linuxu a kontejnery windows, takže můžete snadno vyvíjet moduly pro různé typy zařízení IoT Edge.

K instalaci do vývojového počítače použijte dokumentaci dockeru:

* [Instalace aplikace Docker Desktop pro Windows](https://docs.docker.com/docker-for-windows/install/)

  * Při instalaci Docker Desktop pro Windows, budete dotázáni, zda chcete používat Linux nebo Windows kontejnery. Toto rozhodnutí lze kdykoli změnit pomocí jednoduchého přepínače. Pro účely tohoto kurzu používáme linuxové kontejnery, protože naše moduly cílí na linuxová zařízení. Další informace naleznete v [tématu Přepínání mezi kontejnery Windows a Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Instalace Docker desktopu pro Mac](https://docs.docker.com/docker-for-mac/install/)

* Přečtěte si [o Docker CE](https://docs.docker.com/install/) informace o instalaci na několika platformách Linuxu.

## <a name="set-up-vs-code-and-tools"></a>Nastavení kódu VS a nástrojů

Pomocí rozšíření IoT pro Visual Studio Code k vývoji modulů IoT Edge. Tato rozšíření poskytují šablony projektů, automatizují vytváření manifestu nasazení a umožňují monitorovat a spravovat zařízení IoT Edge. V této části nainstalujete visual studio kód a rozšíření IoT a pak nastavte účet Azure pro správu prostředků Služby IoT Hub z visual studio kódu.

1. Nainstalujte [kód sady Visual Studio](https://code.visualstudio.com/) do vývojového počítače.

2. Po dokončení instalace vyberte **zobrazit** > **rozšíření**.

3. Vyhledejte **nástroje Azure IoT ,** což je ve skutečnosti kolekce rozšíření, které vám pomůžou pracovat se zařízeními IoT Hub a IoT a také vyvíjet moduly IoT Edge.

4. Vyberte **Install** (Nainstalovat). Každé zahrnuté rozšíření se nainstaluje jednotlivě.

5. Po dokončení instalace rozšíření otevřete paletu příkazů výběrem**příkazu** **Paleta** > příkazů .

6. V paletě příkazů vyhledejte a vyberte **Azure: Přihlásit se**. Podle pokynů se přihlaste ke svému účtu Azure.

7. V paletě příkazů znovu vyhledejte a vyberte **Azure IoT Hub: Vyberte IoT Hub**. Podle pokynů vyberte předplatné Azure a službu IoT hub.

8. Otevřete část průzkumníka kódu sady Visual Studio tak, že vyberete ikonu na panelu aktivit vlevo nebo vyberete **Průzkumníka** > **zobrazení**.

9. V dolní části části průzkumníka rozbalte sbalenou nabídku **zařízení Azure IoT Hub.** Měli byste vidět zařízení a zařízení IoT Edge přidružené k centru IoT, které jste vybrali prostřednictvím palety příkazů.

   ![Zobrazení zařízení ve vašem centru IoT hub](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Vytvoření nového modulového projektu

Rozšíření Nástrojů Azure IoT poskytuje šablony projektů pro všechny podporované jazyky modulu IoT Edge v kódu Visual Studia. Tyto šablony mají všechny soubory a kód, které potřebujete k nasazení pracovní modul pro testování IoT Edge, nebo vám výchozí bod pro přizpůsobení šablony s vlastní obchodní logiku.

Pro účely tohoto kurzu používáme šablonu modulu C#, protože se jedná o nejčastěji používanou šablonu.

### <a name="create-a-project-template"></a>Vytvoření šablony projektu

V paletě příkazů kódu Visual Studia vyhledejte a vyberte **Azure IoT Edge: New IoT Edge Solution**. Postupujte podle pokynů a k vytvoření řešení použijte následující hodnoty:

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název řešení nebo přijměte výchozí **edgesolution**. |
   | Vyberte šablonu modulu | Zvolte **modul Jazyka C#**. |
   | Zadejte název modulu | Přijměte výchozí **samplemodule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněna z názvu, který jste zadali v posledním kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br> Konečné úložiště bitových \<obrázků\>vypadá jako název registru .azurecr.io/samplemodule. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-develop-for-linux/image-repository.png)

Jakmile se vaše nové řešení načte v okně kódu sady Visual Studio, věnujte chvilku seznámení se soubory, které vytvořilo:

* Složka **.vscode** obsahuje soubor s názvem **launch.json**, který se používá pro ladění modulů.
* Složka **modulů** obsahuje složku pro každý modul ve vašem řešení. Právě teď, to by mělo být pouze **SampleModule**, nebo bez ohledu na název, který dal do modulu. Složka SampleModule obsahuje hlavní kód programu, metadata modulu a několik souborů Dockeru.
* Soubor **.env** obsahuje pověření do registru kontejneru. Tato pověření jsou sdíleny s vaším zařízením IoT Edge tak, aby má přístup k vyžádat image kontejneru.
* Soubor **deployment.debug.template.json** a **deployment.template.json** jsou šablony, které vám pomohou vytvořit manifest nasazení. *Manifest nasazení* je soubor, který přesně definuje, které moduly chcete nasadit na zařízení, jak by měly být nakonfigurovány a jak mohou komunikovat mezi sebou navzájem a v cloudu. Soubory předloh používají ukazatele pro některé hodnoty. Při transformaci šablony do skutečného manifestu nasazení jsou ukazatele nahrazeny hodnotami převzatými z jiných souborů řešení. Vyhledejte dva běžné zástupné symboly v šabloně nasazení:

  * V části pověření registru je adresa automaticky vyplněna z informací, které jste zadali při vytváření řešení. Uživatelské jméno a heslo však odkazují na proměnné uložené v souboru ENV. Tato konfigurace je pro zabezpečení, protože soubor ENV je ignorován, ale šablona nasazení není.
  * V části SampleModule není obraz kontejneru vyplněn, i když jste při vytváření řešení poskytli úložiště bitové kopie. Tento zástupný symbol odkazuje na soubor **module.json** uvnitř složky SampleModule. Pokud přejdete do tohoto souboru, uvidíte, že pole obrázku obsahuje úložiště, ale také hodnotu značky, která se skládá z verze a platformy kontejneru. Můžete iterovat verzi ručně jako součást vývojového cyklu a můžete vybrat platformu kontejneru pomocí přepínače, který zavádíme později v této části.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Zadejte pověření registru agentovi IoT Edge

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Runtime potřebuje tato pověření k vyžádat image kontejneru na zařízení IoT Edge.

Rozšíření IoT Edge se pokusí navěsit pověření registru kontejneru z Azure a naplnit je v souboru prostředí. Zkontrolujte, zda jsou vaše přihlašovací údaje již zahrnuty. Pokud ne, přidejte je nyní:

1. Otevřete soubor **ENV** v modulovém řešení.
2. Přidejte hodnoty **uživatelského jména** a **hesla,** které jste zkopírovali z registru kontejnerů Azure.
3. Uložte změny do souboru ENV.

### <a name="select-your-target-architecture"></a>Vyberte si cílovou architekturu

V současné době Visual Studio Code můžete vyvíjet moduly Jazyka C# pro linuxová zařízení AMD64 a ARM32v7. Musíte vybrat architekturu, na kterou cílíte s každým řešením, protože to ovlivňuje způsob, jakým je kontejner sestaven a spuštěn. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro edge řešení**nebo vyberte ikonu zástupce v bočním panelu v dolní části okna.

   ![Vybrat ikonu architektury v postranním panelu](./media/tutorial-develop-for-linux/select-architecture.png)

2. V paletě příkazů vyberte cílovou architekturu ze seznamu možností. Pro účely tohoto kurzu používáme jako zařízení IoT Edge virtuální stroj Ubuntu, takže zachováme výchozí **amd64**.

### <a name="review-the-sample-code"></a>Kontrola ukázkového kódu

Šablona řešení, kterou jste vytvořili, obsahuje ukázkový kód pro modul IoT Edge. Tento ukázkový modul jednoduše přijímá zprávy a pak je předá dál. Funkce kanálu ukazuje důležitý koncept v IoT Edge, což je způsob, jakým moduly vzájemně komunikují.

Každý modul může mít více *vstupních* a *výstupních* front deklarovaných v jejich kódu. Rozbočovač IoT Edge spuštěný na zařízení směruje zprávy z výstupu jednoho modulu do vstupu jednoho nebo více modulů. Konkrétní jazyk pro deklarování vstupů a výstupů se liší mezi jazyky, ale koncept je stejný ve všech modulech. Další informace o směrování mezi moduly naleznete v [tématu Deklarování tras](module-composition.md#declare-routes).

Ukázkový kód Jazyka C#, který je součástí šablony projektu, používá [třídu ModuleClient class](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) ze sady SDK služby IoT Hub pro rozhraní .NET.

1. Otevřete **soubor Program.cs,** který je uvnitř složky **modules/SampleModule/.**

2. V program.cs vyhledejte metodu **SetInputMessageHandlerAsync.**

3. Metoda [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) nastaví vstupní frontu pro příjem příchozích zpráv. Zkontrolujte tuto metodu a podívejte se, jak inicializuje vstupní frontu nazvanou **input1**.

   ![Vyhledání vstupního názvu v konstruktoru SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Dále vyhledejte metodu **SendEventAsync.**

5. Metoda [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) zpracovává přijaté zprávy a nastavuje výstupní frontu, která je předá. Zkontrolujte tuto metodu a uvidíte, že inicializuje výstupní frontu nazvanou **output1**.

   ![Vyhledání výstupního názvu v sendeventtooutputasync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Otevřete soubor **deployment.template.json.**

7. Najděte vlastnost **modulů** $edgeAgent požadovaných vlastností.

   Měly by zde být uvedeny dva moduly. Prvním z nich je **SimulatedTemperatureSensor**, který je součástí všech šablon ve výchozím nastavení poskytnout simulované údaje o teplotě, které můžete použít k testování modulů. Druhým je **samplemodule** modul, který jste vytvořili jako součást tohoto řešení.

8. V dolní části souboru vyhledejte požadované vlastnosti modulu **$edgeHub.**

   Jednou z funkcí modulu centra IoT Edge je směrování zpráv mezi všemi moduly v nasazení. Zkontrolujte hodnoty ve **vlastnosti routes.** První trasa **SampleModuleToIoTHub**, používá zástupný znak (**\***) k označení všech zpráv přicházejících z libovolné výstupní fronty v modulu SampleModule. Tyto zprávy přejdou do *$upstream*, což je vyhrazený název, který označuje ioT Hub. Druhá trasa, sensorToSampleModule, přebírá zprávy přicházející z modulu SimulatedTemperatureSensor a směruje je do vstupní fronty *input1,* kterou jste viděli inicializováni v kódu SampleModule.

   ![Kontrola tras v souboru deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Sestavte a stlačte své řešení

Zkontrolovali jste kód modulu a šablonu nasazení, abyste pochopili některé klíčové koncepty nasazení. Nyní jste připraveni k vytvoření image kontejneru SampleModule a push to do registru kontejneru. S rozšířením nástrojů IoT pro kód sady Visual Studio tento krok také generuje manifest nasazení na základě informací v souboru šablony a informací o modulu ze souborů řešení.

### <a name="sign-in-to-docker"></a>Přihlášení do Dockeru

Zadejte pověření registru kontejneru do Dockeru tak, aby můžete tlačit image kontejneru, které mají být uloženy v registru.

1. Otevřete integrovaný terminál Visual Studio Code výběrem možnosti **Zobrazit** > **terminál**.

2. Přihlaste se k Dockeru pomocí přihlašovacích údajů registru kontejneru Azure, které jste uložili po vytvoření registru.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití programu `--password-stdin`. Zatímco tento osvědčený postup se doporučuje pro produkční scénáře, je mimo rozsah tohoto kurzu. Další informace naleznete v odkazu na [přihlášení dockeru.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Sestavení a tlačení

Visual Studio Code má teď přístup k registru kontejneru, takže je čas změnit kód řešení na image kontejneru.

1. V průzkumníku kódu Visual Studia klikněte pravým tlačítkem myši na soubor **deployment.template.json** a vyberte **příkaz Build and Push IoT Edge Solution**.

   ![Vytváření a nabízení modulů IoT Edge](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Příkaz sestavení a nabízení spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config,** která obsahuje úplný manifest nasazení, sestavený z informací v šabloně nasazení a dalších souborech řešení. Za druhé `docker build` spustí k vytvoření image kontejneru na základě příslušné dockerfile pro cílovou architekturu. Potom spustí `docker push` k nabízení úložiště bitové kopie do registru kontejneru.

   Tento proces může trvat několik minut při prvním, ale je rychlejší při příštím spuštění příkazů.

2. Otevřete soubor **deployment.amd64.json** v nově vytvořené složce konfigurace. Název souboru odráží cílovou architekturu, takže se bude lišit, pokud zvolíte jinou architekturu.

3. Všimněte si, že dva parametry, které měly zástupné symboly nyní jsou vyplněny jejich správné hodnoty. V části **registryCredentials** je z souboru ENV odebráno uživatelské jméno a heslo registru. **SampleModule** má úplné úložiště bitové kopie s názvem, verzí a značkou architektury ze souboru module.json.

4. Otevřete soubor **module.json** ve složce SampleModule.

5. Změňte číslo verze pro bitovou kopii modulu. (Verze, ne $schema verze.) Například zvýšení číslo verze opravy na **0.0.2,** jako bychom provedli malou opravu v kódu modulu.

   >[!TIP]
   >Verze modulů umožňují správu verzí a umožňují testovat změny na malé sadě zařízení před nasazením aktualizací do produkčního prostředí. Pokud nechcete přírůstek verze modulu před sestavením a odesílání, pak přepsat úložiště v registru kontejneru.

6. Uložte změny do souboru module.json.

7. Znovu klikněte pravým tlačítkem myši na soubor **deployment.template.json** a znovu vyberte **příkaz Build and Push IoT Edge Solution**.

8. Znovu otevřete soubor **deployment.amd64.json.** Všimněte si, že nový soubor nebyl vytvořen při spuštění příkazu sestavení a nabízení znovu. Spíše stejný soubor byl aktualizován tak, aby odrážely změny. Ukázkový modul bitové kopie nyní odkazuje na verzi 0.0.2 kontejneru.

9. Pokud chcete dále ověřit, co příkaz sestavení a nabízená tisková příkaz udělal, přejděte na [portál Azure](https://portal.azure.com) a přejděte do registru kontejnerů.

10. V registru kontejnerů vyberte **úložiště** a potom **ukázkový modul**. Ověřte, zda obě verze bitové kopie byly zasunuty do registru.

    ![Zobrazení obou verzí bitové kopie v registru kontejneru](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na chyby při vytváření a odesílání image modulu, často má co do činění s konfigurací Dockeru na vývojovém počítači. Ke kontrole konfigurace použijte následující kontroly:

* Spouštěli `docker login` jste příkaz pomocí pověření, která jste zkopírovali z registru kontejneru? Tato pověření se liší od těch, které používáte k přihlášení do Azure.
* Je úložiště kontejnerů správné? Má správný název registru kontejneru a správný název modulu? Chcete-li zkontrolovat, otevřete soubor **module.json** ve složce SampleModule. Hodnota úložiště by měla vypadat jako ** \<název\>registru .azurecr.io/samplemodule**.
* Pokud jste pro modul použili jiný název než **SampleModule,** je tento název konzistentní v celém řešení?
* Je váš stroj spuštěn stejný typ kontejnerů, které stavíte? Tento kurz je pro zařízení Linux IoT Edge, takže Visual Studio Code by měl říkat **amd64** nebo **arm32v7** v postranním panelu a Docker Desktop by měl být spuštěn kontejnery Linux.  

## <a name="deploy-modules-to-device"></a>Nasazení modulů do zařízení

Ověřili jste, že vytvořené image kontejneru jsou uloženy v registru kontejneru, takže je čas nasadit je do zařízení. Ujistěte se, že vaše zařízení IoT Edge je v provozu.

1. V průzkumníku kódu Visual Studia rozbalte část Zařízení služby Azure IoT Hub.

2. Klikněte pravým tlačítkem myši na zařízení IoT Edge, do kterého chcete nasadit, a pak vyberte **Vytvořit nasazení pro jedno zařízení**.

   ![Vytvoření nasazení pro jedno zařízení](./media/tutorial-develop-for-linux/create-deployment.png)

3. V průzkumníku souborů přejděte do složky **config** a vyberte soubor **deployment.amd64.json.**

   Nepoužívejte soubor deployment.template.json, který v něm nejsou pověření registru kontejneru ani hodnoty bitové kopie modulu. Pokud cílíte na zařízení SIP 22, manifest nasazení se bude jmenovat deployment.arm32v7.json.

4. Rozbalte podrobnosti o zařízení IoT Edge a pak rozbalte seznam **Moduly** pro vaše zařízení.

5. Pomocí tlačítka aktualizace aktualizujte zobrazení zařízení, dokud se v zařízení nezobrazí moduly SimulatedTemperatureSensor a SampleModule spuštěné v zařízení.

   Spuštění obou modulů může trvat několik minut. Modul runtime IoT Edge potřebuje přijímat svůj nový manifest nasazení, strhnout ifotky modulu z modulu runtime a pak spustit každý nový modul.

   ![Zobrazení modulů spuštěných na zařízení IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Zobrazení zpráv ze zařízení

Ukázkový modul kód přijímá zprávy prostřednictvím vstupní fronty a předává je prostřednictvím své výstupní fronty. Manifest nasazení deklarované trasy, které předaly zprávy SampleModule z SimulatedTemperatureSensor a pak předá zprávy z SampleModule do služby IoT Hub. Nástroje Azure IoT pro kód Visual Studia umožňují zobrazit zprávy při jejich doručení do služby IoT Hub z vašich jednotlivých zařízení.

1. V průzkumníku kódu Visual Studia klikněte pravým tlačítkem myši na zařízení IoT Edge, které chcete monitorovat, a pak vyberte **Spustit sledování integrovaného koncového bodu událostí**.

2. Podívejte se na výstupní okno v kódu Visual Studia a podívejte se na zprávy přicházející do vašeho centra IoT hub.

   ![Zobrazení příchozích zpráv z zařízení do cloudu](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Zobrazení změn na zařízení

Pokud chcete vidět, co se děje na samotném zařízení, použijte příkazy v této části ke kontrole modulu runtime IoT Edge a modulů spuštěných na vašem zařízení.

Příkazy v této části jsou pro vaše zařízení IoT Edge, ne pro váš vývojový počítač. Pokud používáte virtuální počítač pro vaše zařízení IoT Edge, připojte se k němu teď. V Azure přejděte na stránku s přehledem virtuálního počítače a vyberte **Připojit,** abyste měli přístup k zabezpečenému připojení prostředí.

* Prohlédněte si všechny moduly nasazené do zařízení a zkontrolujte jejich stav:

   ```bash
   iotedge list
   ```

   Měli byste vidět čtyři moduly: dva moduly runtime IoT Edge, SimulatedTemperatureSensor a SampleModule. Všechny čtyři by měly být uvedeny jako spuštěné.

* Zkontrolujte protokoly pro konkrétní modul:

   ```bash
   iotedge logs <module name>
   ```

   Moduly IoT Edge rozlišují malá a velká písmena.

   Protokoly SimulatedTemperatureSensor a SampleModule by měly zobrazovat zprávy, které zpracovávají. Modul edgeAgent je zodpovědný za spuštění ostatních modulů, takže jeho protokoly budou mít informace o implementaci manifestu nasazení. Pokud žádný modul není uveden nebo není spuštěn, protokoly edgeAgent budou pravděpodobně mít chyby. Modul edgeHub je zodpovědný za komunikaci mezi moduly a IoT Hub. Pokud jsou moduly v provozu, ale zprávy nejsou příchozí do centra IoT, protokoly edgeHub bude pravděpodobně mít chyby.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte kód Visual Studio ve vývojovém počítači a nasadíte z něj první modul IoT Edge. Nyní, když znáte základní pojmy, zkuste přidat funkce do modulu tak, aby mohl analyzovat data, která procházejí. Vyberte si preferovaný jazyk:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)
