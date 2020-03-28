---
title: Výuka – vývoj modulu pro zařízení s Windows pomocí Azure IoT Edge
description: Tento kurz vás provede nastavením vývojových strojů a cloudových prostředků pro vývoj modulů IoT Edge pomocí kontejnerů Windows pro zařízení s Windows.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 58a63c9e11cf86318f0e9f051d034cbbaf7c40a9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76772250"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Kurz: Vývoj modulů IoT Edge pro zařízení s Windows

Pomocí Visual Studia můžete vyvíjet a nasazovat kód do zařízení s Windows se systémem IoT Edge.

V rychlém startu jste vytvořili zařízení IoT Edge pomocí virtuálního počítače s Windows a nasadili předem sestavený modul z Azure Marketplace. Tento kurz vás provede, co je zapotřebí k vývoji a nasazení vlastního kódu do zařízení IoT Edge. Tento kurz je užitečným předpokladem pro další kurzy, které jdou do podrobnějších informací o konkrétních programovacích jazycích nebo službách Azure.

Tento kurz používá příklad nasazení **modulu C# do zařízení se systémem Windows**. Tento příklad byl vybrán, protože je nejběžnější vývojový scénář. Pokud máte zájem o vývoj v jiném jazyce nebo plánujete nasazení služeb Azure jako modulů, tento kurz bude stále užitečný, aby se dozvěděl o vývojových nástrojích. Jakmile porozumíte konceptům vývoje, můžete si vybrat preferovaný jazyk nebo službu Azure a ponořit se do podrobností.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Nastavte svůj vývojový stroj.
> * Pomocí nástrojů IoT Edge pro Visual Studio vytvořte nový projekt.
> * Sestavte si svůj projekt jako kontejner a uložte ho do registru kontejnerů Azure.
> * Nasaďte kód do zařízení IoT Edge.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Klíčové koncepty

Tento kurz vás provede vývojem modulu IoT Edge. *Modul IoT Edge*, nebo někdy jen *modul* pro krátké, je kontejner, který obsahuje spustitelný kód. Do zařízení IoT Edge můžete nasadit jeden nebo více modulů. Moduly provádějí specifické úlohy, jako je ingestování dat ze senzorů, provádění analýzy dat nebo operace čištění dat nebo odesílání zpráv do centra IoT hub. Další informace najdete [v tématu Principy modulů Azure IoT Edge](iot-edge-modules.md).

Při vývoji modulů IoT Edge je důležité pochopit rozdíl mezi vývojovým počítačem a cílovým zařízením IoT Edge, kde bude modul nakonec nasazen. Kontejner, který vytvoříte pro uložení kódu modulu, musí odpovídat operačnímu systému (OS) *cílového zařízení*. Pro vývoj kontejnerů systému Windows je tento koncept jednodušší, protože kontejnery systému Windows běží pouze v operačních systémech Windows. Ale můžete například použít vývojový počítač windows k vytváření modulů pro zařízení Linux IoT Edge. V tomto scénáři byste se museli ujistit, že váš vývojový počítač používá kontejnery Linux. Jak budete procházet tento výukový program, mějte na paměti rozdíl mezi *vývojový stroj OS* a *kontejneru OS*.

Tento kurz se zaměřuje na zařízení se systémem Windows se systémem IoT Edge. Zařízení Windows IoT Edge používají kontejnery Windows. Doporučujeme používat Visual Studio k vývoji pro zařízení se systémem Windows, takže to je to, co tento kurz bude používat. Můžete také použít Visual Studio Kód, i když existují rozdíly v podpoře mezi dvěma nástroji.

V následující tabulce jsou uvedeny podporované scénáře vývoje **kontejnerů systému Windows** v aplikacích Visual Studio Code a Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Služby Azure** | Azure Functions <br> Azure Stream Analytics |   |
| **Jazyky** | C# (ladění není podporováno) | C <br> C# |
| **Více informací** | [Azure IoT Edge pro kód Visual Studia](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Nástroje Azure IoT Edge pro Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Nástroje Azure IoT Edge pro Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Požadavky

Vývojový stroj:

* Windows 10 s aktualizací 1809 nebo novější.
* V závislosti na předvolbách vývoje můžete použít vlastní počítač nebo virtuální počítač.
  * Ujistěte se, že váš vývojový počítač podporuje vnořené virtualizace. Tato funkce je nezbytná pro spuštění kontejnerového motoru, který nainstalujete v další části.
* Nainstalujte [Git](https://git-scm.com/).

Zařízení Azure IoT Edge ve Windows:

* Doporučujeme nespouštět IoT Edge ve vývojovém počítači, ale místo toho použít samostatné zařízení. Tento rozdíl mezi vývojovým počítačem a zařízením IoT Edge přesněji odráží skutečný scénář nasazení a pomáhá udržovat různé koncepty rovně.
* Pokud nemáte k dispozici druhé zařízení, použijte článek o rychlém startu k vytvoření zařízení IoT Edge v Azure s [virtuálním počítačem s Windows](quickstart.md).

Cloudové prostředky:

* Bezplatné nebo standardní služby [IoT hub](../iot-hub/iot-hub-create-through-portal.md) v Azure.

## <a name="install-container-engine"></a>Instalace kontejnerového motoru

Moduly IoT Edge jsou zabaleny jako kontejnery, takže potřebujete modul kontejneru ve vývojovém počítači k vytváření a správě kontejnerů. Doporučujeme používat Docker Desktop pro vývoj, protože jeho mnoho funkcí a popularitu jako kontejnerový modul. S Docker Desktop na počítači se systémem Windows, můžete přepínat mezi linuxovými kontejnery a kontejnery windows, takže můžete snadno vyvíjet moduly pro různé typy zařízení IoT Edge.

K instalaci do vývojového počítače použijte dokumentaci dockeru:

* [Instalace aplikace Docker Desktop pro Windows](https://docs.docker.com/docker-for-windows/install/)

  * Při instalaci Docker Desktop pro Windows, budete dotázáni, zda chcete používat Linux nebo Windows kontejnery. V tomto kurzu použijte **kontejnery systému Windows**. Další informace naleznete v [tématu Přepínání mezi kontejnery Windows a Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="set-up-visual-studio-and-tools"></a>Nastavení sady Visual Studio a nástrojů

Rozšíření IoT pro Visual Studio vám pomůžou vyvíjet moduly IoT Edge. Tato rozšíření poskytují šablony projektů, automatizují vytváření manifestu nasazení a umožňují monitorovat a spravovat zařízení IoT Edge. V této části nainstalujete Visual Studio a rozšíření IoT Edge a pak nastavíte účet Azure pro správu prostředků Služby IoT Hub z visual studia.

Tento kurz učí kroky vývoje pro Visual Studio 2019. Pokud používáte Visual Studio 2017 (verze 15.7 nebo vyšší), kroky jsou podobné. Pokud byste raději použít Visual Studio Kód, naleznete v pokynech v [použití kódu Visual Studio k vývoji a ladění modulů pro Azure IoT Edge](how-to-vs-code-develop-module.md).

1. Připravte si Visual Studio 2019 na vývojovém počítači.

   * Pokud ještě nemáte Visual Studio na vývojovém počítači, [nainstalujte Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) s následujícími úlohami:

      * Vývoj pro Azure
      * Vývoj desktopových aplikací pomocí C++
      * Vývoj aplikací pro různé platformy pomocí rozhraní .NET Core

   * Pokud už máte Visual Studio 2019 ve vývojovém počítači, postupujte podle pokynů v [části Úprava Visual Studia](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) a přidejte požadované úlohy.

2. Stáhněte a nainstalujte rozšíření [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) pro Visual Studio 2019.

   Pokud používáte Visual Studio 2017 (verze 15.7 nebo vyšší), stáhněte a nainstalujte [nástroje Azure IoT Edge Tools pro Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Po dokončení instalací otevřete Visual Studio 2019 a vyberte **Pokračovat bez kódu**.

4. Vyberte **zobrazit** > **Průzkumníka cloudu**.

5. Vyberte ikonu profilu v průzkumníku cloudu a přihlaste se ke svému účtu Azure, pokud ještě nejste přihlášení.

6. Po přihlášení jsou uvedena vaše předplatná Azure. Rozbalte předplatné, které má vaše centrum IoT.

7. V rámci předplatného rozbalte **IoT Hubs** a pak své Centrum IoT. Měli byste vidět seznam vašich zařízení IoT a můžete použít tento průzkumník k jejich správě.

   ![Přístup k prostředkům služby IoT Hub v Průzkumníkovi Cloud](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Vytvoření nového modulového projektu

Rozšíření Nástroje Azure IoT Edge poskytuje šablony projektů pro všechny podporované jazyky modulu IoT Edge v sadě Visual Studio. Tyto šablony mají všechny soubory a kód, které potřebujete k nasazení pracovní modul pro testování IoT Edge, nebo vám výchozí bod pro přizpůsobení šablony s vlastní obchodní logiku.

1. Vybrat **Nový projekt** > **New** > **soubor...**

2. V novém okně projektu vyhledejte **IoT Edge** a zvolte projekt **Azure IoT Edge (Windows amd64).** Klikněte na **Další**.

   ![Vytvoření nového projektu Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. V okně konfigurace nového projektu přejmenujte projekt a řešení na něco popisného, jako je **CSharpTutorialApp**. Chcete-li vytvořit projekt, klepněte na **tlačítko Vytvořit.**

   ![Konfigurace nového projektu Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)

4. V okně Přidat modul nakonfigurujte projekt s následujícími hodnotami:

   | Pole | Hodnota |
   | ----- | ----- |
   | Šablona sady Visual Studio | Vyberte **modul Jazyka C#**. |
   | Název modulu | Přijměte výchozí **modul IotEdgeModule1**. |
   | Adresa URL úložiště | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněna z hodnoty názvu projektu modulu. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Hodnotu **přihlašovacího serveru** můžete načíst ze stránky **Přehled** registru kontejnerů na webu Azure Portal. <br><br> Konečné úložiště bitových \<obrázků\>vypadá jako název registru .azurecr.io/iotedgemodule1. |

      ![Konfigurace projektu pro cílové zařízení, typ modulu a registr kontejneru](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Chcete-li modul vytvořit, vyberte **přidat.**

Jakmile se váš nový projekt načte v okně sady Visual Studio, věnujte chvíli seznámení se soubory, které vytvořil:

* Projekt IoT Edge s názvem **CSharpTutorialApp**.
  * Složka **Moduly** obsahuje ukazatele na moduly zahrnuté v projektu. V tomto případě by mělo být jen IotEdgeModule1.
  * Skrytý soubor **ENV** obsahuje pověření do registru kontejneru. Tato pověření jsou sdíleny s vaším zařízením IoT Edge tak, aby má přístup k vyžádat image kontejneru.
  * Soubor **deployment.template.json** je šablona, která vám pomůže vytvořit manifest nasazení. *Manifest nasazení* je soubor, který přesně definuje, které moduly chcete nasadit na zařízení, jak by měly být nakonfigurovány a jak mohou komunikovat mezi sebou navzájem a v cloudu.
    > [!TIP]
    > V části pověření registru je adresa automaticky vyplněna z informací, které jste zadali při vytváření řešení. Uživatelské jméno a referenční proměnné hesla jsou však uloženy v souboru ENV. To to je pro zabezpečení, protože soubor ENV je ignorován, ale šablona nasazení není.
* Projekt modulu IoT Edge s názvem **IotEdgeModule1**.
  * Soubor **program.cs** obsahuje výchozí kód modulu C#, který je dodáván se šablonou projektu. Výchozí modul přebírá vstup ze zdroje a předává ho do IoT Hubu.
  * Soubor **module.json** obsahuje podrobnosti o modulu, včetně úplného úložiště bitové kopie, verze bitové kopie a souboru Dockerfile, který má být používán pro každou podporovanou platformu.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Zadejte pověření registru agentovi IoT Edge

Runtime IoT Edge potřebuje vaše přihlašovací údaje registru k vytáčení iniciálních kopií kontejneru na zařízení IoT Edge. Rozšíření IoT Edge se pokusí navádět informace o registru kontejnerů z Azure a naplnit je v šabloně nasazení.

1. Otevřete soubor **deployment.template.json** v modulovém řešení.

1. Najděte vlastnost **registryCredentials** v $edgeAgent požadované vlastnosti. Adresa registru by měla být automaticky vyplněna z informací, které jste zadali při vytváření projektu, a pole uživatelského jména a hesla by měla obsahovat názvy proměnných. Například:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Otevřete soubor **ENV** v modulovém řešení. (Ve výchozím nastavení je v Průzkumníku řešení skrytá, takže možná budete muset vybrat tlačítko **Zobrazit všechny soubory,** abyste ho zobrazili.)

1. Přidejte hodnoty **uživatelského jména** a **hesla,** které jste zkopírovali z registru kontejnerů Azure.

1. Uložte změny do souboru ENV.

### <a name="review-the-sample-code"></a>Kontrola ukázkového kódu

Šablona řešení, kterou jste vytvořili, obsahuje ukázkový kód pro modul IoT Edge. Tento ukázkový modul jednoduše přijímá zprávy a pak je předá dál. Funkce kanálu ukazuje důležitý koncept v IoT Edge, což je způsob, jakým moduly vzájemně komunikují.

Každý modul může mít více *vstupních* a *výstupních* front deklarovaných v jejich kódu. Rozbočovač IoT Edge spuštěný na zařízení směruje zprávy z výstupu jednoho modulu do vstupu jednoho nebo více modulů. Konkrétní jazyk pro deklarování vstupů a výstupů se liší mezi jazyky, ale koncept je stejný ve všech modulech. Další informace o směrování mezi moduly naleznete v [tématu Deklarování tras](module-composition.md#declare-routes).

Ukázkový kód Jazyka C#, který je součástí šablony projektu, používá [třídu ModuleClient class](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) ze sady SDK služby IoT Hub pro rozhraní .NET.

1. V **souboru program.cs** najděte metodu **SetInputMessageHandlerAsync.**

2. Metoda [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) nastaví vstupní frontu pro příjem příchozích zpráv. Zkontrolujte tuto metodu a podívejte se, jak inicializuje vstupní frontu nazvanou **input1**.

   ![Vyhledání vstupního názvu v konstruktoru SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Dále vyhledejte metodu **SendEventAsync.**

4. Metoda [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) zpracovává přijaté zprávy a nastavuje výstupní frontu, která je předá. Zkontrolujte tuto metodu a uvidíte, že inicializuje výstupní frontu nazvanou **output1**.

   ![Vyhledání výstupního názvu v konstruktoru SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Otevřete soubor **deployment.template.json.**

6. Najděte vlastnost **modulů** $edgeAgent požadovaných vlastností.

   Měly by zde být uvedeny dva moduly. Prvním z nich je **SimulatedTemperatureSensor**, který je součástí všech šablon ve výchozím nastavení poskytnout simulované údaje o teplotě, které můžete použít k testování modulů. Druhým je modul **IotEdgeModule1,** který jste vytvořili jako součást tohoto projektu.

   Tato vlastnost modulů deklaruje, které moduly by měly být zahrnuty do nasazení do vašeho zařízení nebo zařízení.

7. Najděte vlastnost **tras** $edgeHub požadovaných vlastností.

   Jednou z funkcí modulu centra IoT Edge je směrování zpráv mezi všemi moduly v nasazení. Zkontrolujte hodnoty ve vlastnosti routes. První trasa, **IotEdgeModule1ToIoTHub**, používá**\*** zástupný znak ( ) zahrnout všechny zprávy přicházející z libovolné výstupní fronty v modulu IotEdgeModule1. Tyto zprávy přejdou do *$upstream*, což je vyhrazený název, který označuje ioT Hub. Druhá trasa, **sensorToIotEdgeModule1**, přebírá zprávy přicházející z modulu SimulatedTemperatureSensor a směruje je do vstupní fronty *vstup1* modulu IotEdgeModule1.

   ![Kontrola tras v souboru deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Sestavte a stlačte své řešení

Zkontrolovali jste kód modulu a šablonu nasazení, abyste pochopili některé klíčové koncepty nasazení. Nyní jste připraveni vytvořit image kontejneru IotEdgeModule1 a zasunout ji do registru kontejnerů. S rozšířením nástrojů IoT pro Visual Studio tento krok také generuje manifest nasazení na základě informací v souboru šablony a informací o modulu ze souborů řešení.

### <a name="sign-in-to-docker"></a>Přihlášení do Dockeru

Zadejte pověření registru kontejneru dockeru ve vývojovém počítači tak, aby mohl tlačit image kontejneru, které mají být uloženy v registru.

1. Otevřete PowerShell nebo příkazový řádek.

2. Přihlaste se k Dockeru pomocí přihlašovacích údajů registru kontejneru Azure, které jste uložili po vytvoření registru.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití programu `--password-stdin`. Zatímco tento osvědčený postup se doporučuje pro produkční scénáře, je mimo rozsah tohoto kurzu. Další informace naleznete v odkazu na [přihlášení dockeru.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Sestavení a tlačení

Váš vývojový počítač má teď přístup k registru kontejnerů a vaše zařízení IoT Edge taky. Je čas změnit kód projektu na image kontejneru.

1. Klepněte pravým tlačítkem myši na složku projektu **CSharpTutorialApp** a vyberte **příkaz Build and Push IoT Edge Modules**.

   ![Vytváření a nabízení modulů IoT Edge](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Příkaz sestavení a nabízení spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config,** která obsahuje úplný manifest nasazení, sestavený z informací v šabloně nasazení a dalších souborech řešení. Za druhé `docker build` spustí k vytvoření image kontejneru na základě příslušné dockerfile pro cílovou architekturu. Potom spustí `docker push` k nabízení úložiště bitové kopie do registru kontejneru.

   Tento proces může trvat několik minut při prvním, ale je rychlejší při příštím spuštění příkazů.

2. Otevřete soubor **deployment.windows-amd64.json** v nově vytvořené složce konfigurace. (Složka config se nemusí zobrazit v Průzkumníku řešení v sadě Visual Studio. V takovém případě vyberte na hlavním panelu Průzkumníka řešení ikonu **Zobrazit všechny soubory.)**

3. Najděte parametr **obrázku** v části IotEdgeModule1. Všimněte si, že obraz obsahuje úplné úložiště obrázků s názvem, verzí a značkou architektury ze souboru module.json.

4. Otevřete soubor **module.json** ve složce IotEdgeModule1.

5. Změňte číslo verze pro bitovou kopii modulu. (Verze, ne $schema verze.) Například zvýšení číslo verze opravy na **0.0.2,** jako bychom provedli malou opravu v kódu modulu.

   >[!TIP]
   >Verze modulů umožňují správu verzí a umožňují testovat změny na malé sadě zařízení před nasazením aktualizací do produkčního prostředí. Pokud nechcete přírůstek verze modulu před sestavením a odesílání, pak přepsat úložiště v registru kontejneru.

6. Uložte změny do souboru module.json.

7. Znovu klikněte pravým tlačítkem myši na složku projektu **CSharpTutorialApp** a znovu vyberte **build a push ioT edge moduly.**

8. Znovu otevřete soubor **deployment.windows-amd64.json.** Všimněte si, že nový soubor nebyl vytvořen při spuštění příkazu sestavení a nabízení znovu. Spíše stejný soubor byl aktualizován tak, aby odrážely změny. Bitová kopie IotEdgeModule1 nyní odkazuje na verzi 0.0.2 kontejneru. Tato změna v manifestu nasazení je způsob, jakým řeknete zařízení IoT Edge, že existuje nová verze modulu, který má být vytahován.

9. Pokud chcete dále ověřit, co příkaz sestavení a nabízená tisková příkaz udělal, přejděte na [portál Azure](https://portal.azure.com) a přejděte do registru kontejnerů.

10. V registru kontejnerů vyberte **úložiště** a **potom iotedgemodule1**. Ověřte, zda obě verze bitové kopie byly zasunuty do registru.

    ![Zobrazení obou verzí bitové kopie v registru kontejneru](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na chyby při vytváření a odesílání image modulu, často má co do činění s konfigurací Dockeru na vývojovém počítači. Ke kontrole konfigurace použijte následující kontroly:

* Spouštěli `docker login` jste příkaz pomocí pověření, která jste zkopírovali z registru kontejneru? Tato pověření se liší od těch, které používáte k přihlášení do Azure.
* Je úložiště kontejnerů správné? Má správný název registru kontejneru a správný název modulu? Chcete-li zkontrolovat, otevřete soubor **module.json** ve složce IotEdgeModule1. Hodnota úložiště by měla vypadat jako ** \<název\>registru .azurecr.io/iotedgemodule1**.
* Pokud jste pro svůj modul použili jiný název než **IotEdgeModule1,** je tento název konzistentní v celém řešení?
* Je váš stroj spuštěn stejný typ kontejnerů, které stavíte? Tento kurz je určen pro zařízení Windows IoT Edge, takže vaše soubory Sady Visual Studio by měly mít rozšíření **windows-amd64** a Docker Desktop by měl být spuštěn kontejnery Windows.

## <a name="deploy-modules-to-device"></a>Nasazení modulů do zařízení

Ověřili jste, že vytvořené image kontejneru jsou uloženy v registru kontejneru, takže je čas nasadit je do zařízení. Ujistěte se, že vaše zařízení IoT Edge je v provozu.

1. Otevřete Průzkumníka Cloud v Visual Studiu a rozbalte podrobnosti pro vaše centrum IoT.

2. Vyberte název zařízení, do kterého chcete nasadit. V seznamu **Akce** vyberte **Vytvořit nasazení**.

   ![Vytvoření nasazení pro jedno zařízení](./media/tutorial-develop-for-windows/create-deployment.png)

3. V průzkumníku souborů přejděte do konfigurační složky projektu a vyberte soubor **deployment.windows-amd64.json.** Tento soubor je často umístěn na adrese`C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Nepoužívejte soubor deployment.template.json, který v něm není úplné hodnoty bitové kopie modulu.

4. Rozbalte podrobnosti o zařízení IoT Edge v Průzkumníkovi Cloudu a podívejte se na moduly na vašem zařízení.

5. Pomocí tlačítka **Aktualizovat** aktualizujte stav zařízení, abyste viděli, že jsou vaše zařízení nasazeny moduly SimulatedTemperatureSensor a IotEdgeModule1.

   ![Zobrazení modulů spuštěných na zařízení IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Zobrazení zpráv ze zařízení

Kód IotEdgeModule1 přijímá zprávy prostřednictvím vstupní fronty a předává je prostřednictvím výstupní fronty. Manifest nasazení deklarované trasy, které předávaly zprávy z SimulatedTemperatureSensor na IotEdgeModule1 a pak předal zprávy z IotEdgeModule1 do služby IoT Hub. Nástroje Azure IoT Edge pro Visual Studio umožňují zobrazit zprávy při jejich doručení do služby IoT Hub z vašich jednotlivých zařízení.

1. V průzkumníku cloudu Visual Studia vyberte název zařízení IoT Edge, na které jste nasadili.

2. V nabídce **Akce** vyberte **Spustit sledování předdefinovaného koncového bodu událostí**.

3. Podívejte se na část **Výstup** v Sadě Visual Studio zobrazíte zprávy přicházející do vašeho centra IoT hub.

   Spuštění obou modulů může trvat několik minut. Modul runtime IoT Edge potřebuje přijímat svůj nový manifest nasazení, strhnout ifotky modulu z modulu runtime a pak spustit každý nový modul.

   ![Zobrazení příchozích zpráv z zařízení do cloudu](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Zobrazení změn na zařízení

Pokud chcete vidět, co se děje na samotném zařízení, použijte příkazy v této části ke kontrole modulu runtime IoT Edge a modulů spuštěných na vašem zařízení.

Příkazy v této části jsou pro vaše zařízení IoT Edge, ne pro váš vývojový počítač. Pokud používáte virtuální počítač pro vaše zařízení IoT Edge, připojte se k němu teď. V Azure přejděte na stránku s přehledem virtuálního počítače a vyberte **Připojit** pro přístup k připojení ke vzdálené ploše. Na zařízení otevřete příkaz nebo okno PowerShellu a spusťte `iotedge` příkazy.

* Prohlédněte si všechny moduly nasazené do zařízení a zkontrolujte jejich stav:

   ```cmd
   iotedge list
   ```

   Měli byste vidět čtyři moduly: dva moduly runtime IoT Edge, SimulatedTemperatureSensor a IotEdgeModule1. Všechny čtyři by měly být uvedeny jako spuštěné.

* Zkontrolujte protokoly pro konkrétní modul:

   ```cmd
   iotedge logs <module name>
   ```

   Moduly IoT Edge rozlišují malá a velká písmena.

   SimulatedTemperatureSensor a IotEdgeModule1 protokoly by měly zobrazovat zprávy, které jsou zpracování. Modul edgeAgent je zodpovědný za spuštění ostatních modulů, takže jeho protokoly budou mít informace o implementaci manifestu nasazení. Pokud žádný modul není uveden nebo není spuštěn, protokoly edgeAgent budou pravděpodobně mít chyby. Modul edgeHub je zodpovědný za komunikaci mezi moduly a IoT Hub. Pokud jsou moduly v provozu, ale zprávy nejsou příchozí do centra IoT, protokoly edgeHub bude pravděpodobně mít chyby.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte Visual Studio 2019 ve vývojovém počítači a nasadíte z něj svůj první modul IoT Edge. Nyní, když znáte základní pojmy, zkuste přidat funkce do modulu tak, aby mohl analyzovat data, která procházejí. Vyberte si preferovaný jazyk:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
