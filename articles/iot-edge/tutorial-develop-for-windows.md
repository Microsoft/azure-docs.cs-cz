---
title: Vývoj modulu pro zařízení s Windows – Azure IoT Edge | Microsoft Docs
description: Tento kurz vás provede nastavením vývojového počítače a cloudových prostředků pro vývoj IoT Edgech modulů pomocí kontejnerů Windows pro zařízení s Windows.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/15/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 66fa7c2f61af250e4b63b67f6941bed768bd94c4
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541923"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Kurz: Vývoj modulů IoT Edge pro zařízení s Windows

Pomocí sady Visual Studio můžete vyvíjet a nasazovat kód do zařízení s Windows, na kterých běží IoT Edge.

V rychlém startu jste vytvořili zařízení IoT Edge pomocí virtuálního počítače s Windows a nasadili jste předem sestavený modul z Azure Marketplace. Tento kurz vás provede vývojem a nasazením vlastního kódu do zařízení IoT Edge. Tento kurz je vhodný pro ostatní kurzy, které vám pojdou podrobněji o konkrétních programovacích jazycích nebo službách Azure. 

V tomto kurzu se používá příklad nasazení  **C# modulu do zařízení s Windows**. Tento příklad jste zvolili, protože se jedná o nejběžnější scénář vývoje. Pokud vás zajímá vývoj v jiném jazyce nebo Naplánování nasazení služeb Azure jako modulů, bude tento kurz stále užitečný získat informace o vývojářských nástrojích. Jakmile porozumíte konceptům vývoje, můžete zvolit preferovaný jazyk nebo službu Azure, která bude podrobně do podrobností. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte počítač pro vývoj.
> * Pomocí nástrojů IoT Edge pro Visual Studio vytvořte nový projekt.
> * Sestavte projekt jako kontejner a uložte ho do služby Azure Container Registry.
> * Nasaďte kód do zařízení IoT Edge. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Klíčové koncepty

Tento kurz vás provede vývojem modulu IoT Edge. *IoT Edge modul*, nebo někdy jenom *modul* pro krátké, je kontejner, který obsahuje spustitelný kód. Jeden nebo více modulů můžete nasadit do zařízení IoT Edge. Moduly provádějí konkrétní úkoly, jako je ingestování dat ze senzorů, provádění analýz dat nebo operací čištění dat nebo posílání zpráv do služby IoT Hub. Další informace najdete v tématu [principy Azure IoT Edgech modulů](iot-edge-modules.md).

Při vývoji IoT Edgech modulů je důležité pochopit rozdíl mezi vývojovým počítačem a cílovým IoT Edge zařízením, kde se modul bude nakonec nasazovat. Kontejner, který sestavíte pro uložení kódu vašeho modulu, se musí shodovat s operačním systémem *cílového zařízení*. Pro vývoj kontejnerů Windows je tento koncept jednodušší, protože kontejnery Windows se spouštějí pouze v operačních systémech Windows. Můžete například použít počítač pro vývoj ve Windows k vytváření modulů pro zařízení se systémem Linux IoT Edge. V takovém případě byste se museli ujistit, že na vašem vývojovém počítači byly spuštěné kontejnery Linux. Při procházení tohoto kurzu mějte na paměti rozdíl mezi *vývojovým operačním systémem* a *operačním systémem kontejneru*.

V tomto kurzu se cílí na zařízení s Windows, která používají IoT Edge. Zařízení s Windows IoT Edge používají kontejnery Windows. Doporučujeme používat Visual Studio pro vývoj pro zařízení s Windows, takže to je to, co tento kurz bude používat. Můžete použít i Visual Studio Code, i když existují rozdíly v podpoře mezi dvěma nástroji.

V následující tabulce jsou uvedeny podporované vývojářské scénáře pro **kontejnery Windows** v Visual Studio Code a v aplikaci Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Služby Azure** | Azure Functions <br> Azure Stream Analytics |   |
| **Jazyky** | C#(ladění není podporováno.) | C <br> C# |
| **Další informace** | [Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Požadavky

Vývojový počítač:

* Windows 10 s aktualizací 1809 nebo novější.
* V závislosti na vašich předvolbách pro vývoj můžete použít svůj vlastní počítač nebo virtuální počítač.
* Nainstalujte [Git](https://git-scm.com/). 

Zařízení Azure IoT Edge ve Windows:

* Doporučujeme, abyste ve vývojovém počítači nespouštěli IoT Edge, ale místo toho použijte samostatné zařízení. Toto rozlišení mezi vývojovým počítačem a IoT Edgeým zařízením přesněji odráží skutečný scénář nasazení a pomáhá udržet různé koncepty rovnou.
* Pokud nemáte k dispozici druhé zařízení, pomocí článku rychlý Start vytvořte zařízení IoT Edge v Azure pomocí [virtuálního počítače s Windows](quickstart.md).

Cloudové prostředky:

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) na bezplatné nebo standardní úrovni v Azure. 

## <a name="install-container-engine"></a>Nainstalovat modul pro kontejnery

Moduly IoT Edge jsou zabaleny jako kontejnery, takže potřebujete kontejnerový modul na svém vývojovém počítači pro sestavování a správu kontejnerů. Pro vývoj doporučujeme použít Docker Desktop, protože je k disřadě funkcí a oblíbenou jako modul kontejnerů. S Docker desktopem na počítači s Windows můžete přepínat mezi kontejnery Linux a kontejnery Windows, abyste mohli snadno vyvíjet moduly pro různé typy IoT Edgech zařízení. 

K instalaci na vývojový počítač použijte dokumentaci k Docker: 

* [Nainstalovat Docker Desktop pro Windows](https://docs.docker.com/docker-for-windows/install/)

  * Když nainstalujete Docker Desktop pro Windows, zobrazí se dotaz, jestli chcete použít kontejnery pro Linux nebo Windows. Pro tento kurz použijte **kontejnery Windows**. Další informace najdete v tématu [přepínání mezi kontejnery Windows a Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).


## <a name="set-up-visual-studio-and-tools"></a>Nastavení sady Visual Studio a nástrojů

Rozšíření IoT pro Visual Studio vám pomůžou vyvíjet IoT Edge moduly. Tato rozšíření poskytují šablony projektů, automatizují vytváření manifestu nasazení a umožňují vám monitorovat a spravovat IoT Edge zařízení. V této části nainstalujete Visual Studio a rozšíření IoT Edge a pak nastavíte účet Azure pro správu prostředků IoT Hub v rámci sady Visual Studio. 

V tomto kurzu se naučíte postup vývoje pro Visual Studio 2019. Pokud používáte Visual Studio 2017 (verze 15,7 nebo novější), postup je velmi podobný. Pokud místo toho chcete použít Visual Studio Code, přečtěte si pokyny v tématu [použití Visual Studio Code k vývoji a ladění modulů pro Azure IoT Edge](how-to-vs-code-develop-module.md). 

1. Připravte Visual Studio 2019 na svém vývojovém počítači. 

   * Pokud ještě nemáte Visual Studio ve vývojovém počítači, [nainstalujte Visual studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) s následujícími úlohami: 

      * Vývoj pro Azure
      * Vývoj desktopových aplikací pomocí C++
      * Vývoj pro různé platformy .NET core

   * Pokud již máte Visual Studio 2019 ve vývojovém počítači, postupujte podle kroků v části [Změna sady Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) a přidejte požadované úlohy.

2. Stáhněte a nainstalujte rozšíření [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) pro Visual Studio 2019. 

   Pokud používáte Visual Studio 2017 (verze 15,7 nebo novější), Stáhněte a nainstalujte [Azure IoT Edge nástroje pro Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Po dokončení instalace otevřete Visual Studio 2019 a vyberte **pokračovat bez kódu**.

4. Vyberte **Zobrazit** > **Průzkumníka cloudu**. 

5. Vyberte ikonu profilu v Průzkumníkovi cloudu a přihlaste se k účtu Azure, pokud ještě nejste přihlášení. 

6. Po přihlášení se zobrazí vaše předplatná Azure. Rozbalte předplatné, které má centrum IoT. 

7. V rámci vašeho předplatného rozbalte položku **centra IoT** a potom centrum IoT. Měl by se zobrazit seznam zařízení IoT a můžou je spravovat pomocí tohoto Průzkumníka. 

   ![Přístup k prostředkům IoT Hub v Průzkumníkovi cloudu](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Vytvořit nový projekt modulu

Rozšíření nástrojů Azure IoT Edge poskytuje šablony projektů pro všechny podporované jazyky IoT Edge modulů v aplikaci Visual Studio. Tyto šablony obsahují všechny soubory a kód, které potřebujete k nasazení pracovního modulu k testování IoT Edge, nebo vám poskytne výchozí bod pro přizpůsobení šablony s vlastní obchodní logikou. 

1. Vybrat **soubor** > novýprojekt >  **...**

2. V okně Nový projekt vyhledejte **IoT Edge** a vyberte projekt **Azure IoT Edge (Windows amd64)** . Klikněte na **Další**. 

   ![Vytvoření nového projektu Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. V okně Konfigurovat nový projekt přejmenujte projekt a řešení na něco popisného, jako je **CSharpTutorialApp**. Kliknutím na **vytvořit** vytvořte projekt.

   ![Konfigurace nového projektu Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)

4. V okně Přidat modul nakonfigurujte projekt pomocí následujících hodnot: 

   | Pole | Value |
   | ----- | ----- |
   | Šablona sady Visual Studio | Vyberte  **C# modul**. | 
   | Název modulu | Přijměte výchozí **IotEdgeModule1**. | 
   | Adresa URL úložiště | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Vaše image kontejneru je předem vyplněná z hodnoty název projektu modulu. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br> Finální úložiště imagí vypadá jako \<název\>registru. azurecr.IO/iotedgemodule1. |

      ![Konfigurace projektu pro cílové zařízení, typ modulu a registr kontejnerů](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Vyberte **Přidat** a vytvořte modul. 

Jakmile se nový projekt načte v okně aplikace Visual Studio, je nutné se seznámit se soubory, které vytvořil: 

* IoT Edge projekt s názvem **CSharpTutorialApp**.
    * Složka **moduly** obsahuje ukazatele na moduly zahrnuté v projektu. V takovém případě by měl být pouze IotEdgeModule1. 
    * Soubor **Deployment. template. JSON** je šablona, která vám může vytvořit manifest nasazení. *Manifest nasazení* je soubor, který definuje přesně to, které moduly se mají na zařízení nasadit, jak by se měly nakonfigurovat a jak můžou komunikovat mezi sebou a cloudem. 
* Projekt modulu IoT Edge s názvem **IotEdgeModule1**.
    * Soubor **program.cs** obsahuje výchozí C# kód modulu, který je součástí šablony projektu. Výchozí modul přebírá ze zdroje vstup a předá ho IoT Hub. 
    * Soubor **Module. JSON** obsahuje podrobnosti o modulu, včetně úplného úložiště imagí, verze image a souboru Dockerfile, který se má použít pro každou podporovanou platformu.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Zadejte přihlašovací údaje registru pro agenta IoT Edge.

Modul runtime IoT Edge potřebuje přihlašovací údaje registru pro vyžádání imagí kontejneru do IoT Edge zařízení. Přidejte tato pověření do šablony nasazení. 

1. Otevřete soubor **Deployment. template. JSON** .

2. V $edgeAgent požadovaných vlastnostech Najděte vlastnost **registryCredentials** . 

3. Aktualizujte tuto vlastnost pomocí vašich přihlašovacích údajů, a to v následujícím formátu: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Uložte soubor Deployment. template. JSON. 

### <a name="review-the-sample-code"></a>Revize ukázkového kódu

Šablona řešení, kterou jste vytvořili, obsahuje vzorový kód pro modul IoT Edge. Tento vzorový modul jednoduše přijímá zprávy a pak je předává. Funkce kanálu ukazuje důležitou koncepci v IoT Edge, což je způsob, jakým vzájemně komunikují moduly.

Každý modul může mít v kódu deklarovaných víc *vstupních* a *výstupních* front. Rozbočovač IoT Edge běžící na zařízení směruje zprávy z výstupu jednoho modulu do vstupu jednoho nebo více modulů. Konkrétní jazyk pro deklarování vstupů a výstupů se liší mezi jazyky, ale koncept je stejný ve všech modulech. Další informace o směrování mezi moduly naleznete v tématu [Declare Routes](module-composition.md#declare-routes).

Vzorový C# kód, který je součástí šablony projektu, používá [třídu MODULECLIENT](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) ze sady SDK IoT Hub pro .NET. 

1. V souboru **program.cs** Najděte metodu **SetInputMessageHandlerAsync** .

2. Metoda [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) nastaví vstupní frontu pro příjem příchozích zpráv. Projděte si tuto metodu a podívejte se, jak inicializuje vstupní frontu s názvem **input1**. 

   ![Najít název vstupu v konstruktoru SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Dále vyhledejte metodu **SendEventAsync** .

4. Metoda [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) zpracovává přijaté zprávy a nastavuje výstupní frontu, která je předávat společně. Projděte si tuto metodu a podívejte se, že Inicializuje výstupní frontu s názvem **output1**. 

   ![Najít název výstupu v konstruktoru SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Otevřete soubor **Deployment. template. JSON** .

6. Vyhledejte vlastnost **modulů** $edgeAgent požadovaných vlastností. 

   Tady by měly být uvedené dva moduly. První je **tempSensor**, který je ve výchozím nastavení součástí všech šablon, aby poskytovala Simulovaná data o teplotě, která můžete použít k otestování modulů. Druhým je modul **IotEdgeModule1** , který jste vytvořili jako součást tohoto projektu.

   Tato vlastnost modulů deklaruje, které moduly by měly být součástí nasazení do zařízení nebo zařízení. 

7. Vyhledejte vlastnost **routes** $edgeHub požadovaných vlastností. 

   Jedna z funkcí, pokud je modul IoT Edge hub směrování zpráv mezi všemi moduly v nasazení. Zkontrolujte hodnoty ve vlastnosti Routes. První trasa **IotEdgeModule1ToIoTHub**používá zástupný znak ( **\*** ) k zahrnutí jakékoli zprávy přicházející z libovolné výstupní fronty v modulu IotEdgeModule1. Tyto zprávy se přejdou do *$upstream*, což je vyhrazený název, který označuje IoT Hub. Druhá trasa, **sensorToIotEdgeModule1**, přebírá zprávy z modulu tempSensor a směruje je do vstupní fronty *input1* modulu IotEdgeModule1. 

   ![Kontrola tras v nasazení. template. JSON](./media/tutorial-develop-for-windows/deployment-routes.png)


## <a name="build-and-push-your-solution"></a>Vytváření a nasdílení změn vašeho řešení

Zkontrolovali jste kód modulu a šablonu nasazení, abyste pochopili některé koncepty nasazení. Nyní jste připraveni sestavit image kontejneru IotEdgeModule1 a vložit ji do registru kontejneru. S rozšířením nástrojů IoT pro Visual Studio tento krok také generuje manifest nasazení na základě informací v souboru šablony a informací o modulu ze souborů řešení. 

### <a name="sign-in-to-docker"></a>Přihlásit se k Docker

Poskytněte přihlašovací údaje registru kontejneru do Docker na svém vývojovém počítači, aby bylo možné vložit image kontejneru do registru. 

1. Otevřete PowerShell nebo příkazový řádek.

2. Přihlaste se k Docker pomocí přihlašovacích údajů služby Azure Container Registry, které jste uložili po vytvoření registru. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití `--password-stdin`nástroje. I když se tento osvědčený postup doporučuje u produkčních scénářů, je mimo rozsah tohoto kurzu. Další informace najdete v tématu [přihlašovací](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) Reference k Docker.

### <a name="build-and-push"></a>Sestavení a vložení

Váš vývojový počítač má teď přístup k vašemu registru kontejnerů a vaše IoT Edgeá zařízení budou taky. Je čas převést kód projektu na Image kontejneru. 

1. Klikněte pravým tlačítkem na složku projektu **CSharpTutorialApp** a vyberte sestavování **a nabízených IoT Edgech modulů**. 

   ![Sestavování a nabízených IoT Edgech modulů](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Příkaz Build a push spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config** , která obsahuje úplný manifest nasazení, a vyplní informace v šabloně nasazení a dalších souborech řešení. Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. Pak se spustí a `docker push` nahraje úložiště imagí do registru kontejneru. 

   Tento proces může trvat několik minut poprvé, ale při příštím spuštění příkazů je rychlejší. 

2. Otevřete soubor **Deployment. Windows-amd64. JSON** ve složce nově vytvořená konfigurace. (Konfigurační složka se nemusí zobrazit v Průzkumník řešení v aplikaci Visual Studio. Pokud se jedná o tento případ, vyberte ikonu **Zobrazit všechny soubory** na hlavním panelu Průzkumník řešení.)

3. Vyhledejte parametr **Image** oddílu IotEdgeModule1. Všimněte si, že image obsahuje úplné úložiště imagí se značkou název, verze a architektura ze souboru Module. JSON.

4. Otevřete soubor **Module. JSON** ve složce IotEdgeModule1. 

5. Změňte číslo verze image modulu. (Verze, nikoli verze $schema.) Například Zvyšte číslo verze opravy na **0.0.2** , jako kdyby jsme v kódu modulu udělali malou opravu. 

   >[!TIP]
   >Verze modulů umožňují správu verzí a umožňují testovat změny v malých sadě zařízení před nasazením aktualizací do produkčního prostředí. Pokud před vytvořením a vložením nezvýšíte verzi modulu, přepíšete úložiště v registru kontejneru. 

6. Uložte změny do souboru Module. JSON.

7. Znovu klikněte pravým tlačítkem na složku projektu **CSharpTutorialApp** a znovu vyberte sestavování **a IoT Edge moduly pro vložení** . 

8. Otevřete soubor **nasazení. Windows-amd64. JSON** znovu. Všimněte si, že nový soubor nebyl vytvořen při opětovném spuštění příkazu Build a push. Místo toho byl stejný soubor aktualizován tak, aby odrážel změny. Obrázek IotEdgeModule1 nyní odkazuje na verzi 0.0.2 kontejneru. Tato změna v manifestu nasazení je způsob, jakým zařízení IoT Edge, že existuje nová verze modulu, který se má načíst. 

9. Chcete-li dále ověřit, co byl příkaz Build and push, přejděte na [Azure Portal](https://portal.azure.com) a přejděte do registru kontejneru. 

10. V registru kontejneru vyberte **úložiště** a pak **iotedgemodule1**. Ověřte, zda byly do registru vloženy obě verze bitové kopie.

    ![Zobrazit obě verze imagí v registru kontejnerů](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na chyby při sestavování a vkládání image modulu, často je třeba provést konfiguraci Docker na vašem vývojovém počítači. Ke kontrole konfigurace použijte následující kontroly: 

* Spustili `docker login` jste příkaz s použitím přihlašovacích údajů, které jste zkopírovali z registru kontejneru? Tyto přihlašovací údaje se liší od těch, které používáte k přihlášení do Azure. 
* Je vaše úložiště kontejnerů správné? Má váš správný název registru kontejneru a správný název modulu? Otevřete soubor **Module. JSON** ve složce IotEdgeModule1, abyste zkontrolovali. Hodnota úložiště by měla vypadat jako  **\<název\>registru. azurecr.IO/iotedgemodule1**. 
* Pokud jste pro modul použili jiný název než **IotEdgeModule1** , znamená to, že se tento název v rámci řešení konzistentně používá?
* Je váš počítač se spuštěným stejným typem kontejnerů, které vytváříte? Tento kurz je určen pro zařízení s Windows IoT Edge, takže soubory sady Visual Studio by měly mít rozšíření **Windows-amd64** a Docker Desktop by měl používat kontejnery Windows. 

## <a name="deploy-modules-to-device"></a>Nasadit moduly do zařízení

Ověřili jste, že jsou sestavené image kontejneru uložené v registru kontejnerů, takže je čas je nasadit do zařízení. Ujistěte se, že je zařízení IoT Edge spuštěné. 

1. Otevřete Průzkumníka cloudu v aplikaci Visual Studio a rozbalte podrobnosti pro Centrum IoT. 

2. Vyberte název zařízení, do kterého chcete nasadit. V seznamu **Akce** vyberte **vytvořit nasazení**.

   ![Vytvoření nasazení pro jedno zařízení](./media/tutorial-develop-for-windows/create-deployment.png)


3. V Průzkumníku souborů přejděte do konfigurační složky vašeho projektu a vyberte soubor **Deployment. Windows-amd64. JSON** . Tento soubor se často nachází v`C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Nepoužívejte soubor Deployment. template. JSON, ve kterém nejsou k dispozici úplné hodnoty imagí modulu. 

4. Rozbalením podrobností pro zařízení IoT Edge v Průzkumníkovi cloudu zobrazíte moduly na svém zařízení.

5. Pomocí tlačítka **aktualizovat** aktualizujte stav zařízení, abyste viděli, že moduly TempSensor a IotEdgeModule1 se nasazují na vaše zařízení. 


   ![Zobrazit moduly běžící na zařízení IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Zobrazit zprávy ze zařízení

Kód IotEdgeModule1 přijímá zprávy přes vstupní frontu a předává je spolu s její výstupní frontou. Manifest nasazení deklaroval trasy, které předaly zprávy z tempSensor do IotEdgeModule1, a pak přesměrují zprávy od IotEdgeModule1 do IoT Hub. Nástroje Azure IoT Edge pro Visual Studio umožňují zobrazovat zprávy při doručování IoT Hub z jednotlivých zařízení. 

1. V Průzkumníku cloudu sady Visual Studio vyberte název zařízení IoT Edge, které jste nasadili na. 

2. V nabídce **Akce** vyberte **Spustit sledování předdefinovaného koncového bodu události**.

3. Podívejte se do části **Output** v aplikaci Visual Studio, ve které se zobrazí zprávy přicházející ve službě IoT Hub. 

   Spuštění obou modulů může trvat několik minut. Modul runtime IoT Edge musí přijmout nový manifest nasazení, Stáhnout image modulu z modulu runtime kontejneru a pak začít každý nový modul. Pokud jste 

   ![Zobrazení zpráv příchozího zařízení do cloudu](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Zobrazit změny na zařízení

Pokud chcete zjistit, co se děje na samotném zařízení, použijte příkazy v této části k zkontrolování modulu runtime IoT Edge a modulů spuštěných ve vašem zařízení. 

Příkazy v této části jsou pro vaše zařízení IoT Edge, ne pro váš vývojový počítač. Pokud pro zařízení IoT Edge používáte virtuální počítač, připojte se k němu hned. V Azure přejděte na stránku Přehled virtuálního počítače a vyberte **připojit** pro přístup k připojení ke vzdálené ploše. Na zařízení otevřete příkaz nebo okno PowerShellu a spusťte `iotedge` příkazy.

* Zobrazte všechny moduly nasazené do zařízení a zkontrolujte jejich stav:

   ```cmd
   iotedge list
   ```

   Měli byste vidět čtyři moduly: dva moduly IoT Edge runtime, tempSensor a IotEdgeModule1. Všechny čtyři by měly být uvedeny jako spuštěné.

* Zkontrolujte protokoly konkrétního modulu:

   ```cmd
   iotedge logs <module name>
   ```

   V IoT Edgech modulech se rozlišují malá a velká písmena. 

   V protokolech tempSensor a IotEdgeModule1 by se měly zobrazovat zprávy, které zpracovávají. Modul edgeAgent je zodpovědný za spouštění jiných modulů, takže jeho protokoly budou mít informace o implementaci manifestu nasazení. Pokud některý z modulů není v seznamu nebo není spuštěný, budou pravděpodobně chyby v protokolech edgeAgent. Modul edgeHub zodpovídá za komunikaci mezi moduly a IoT Hub. Pokud jsou moduly v provozu, ale zprávy nepřicházejí do služby IoT Hub, budou pravděpodobně chyby v protokolech edgeHub. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nastavili Visual Studio 2019 na svém vývojovém počítači a nasadili do něj první IoT Edge modul. Teď, když znáte základní koncepty, zkuste do modulu přidat funkce, aby mohli analyzovat data, která procházejí. Vyberte preferovaný jazyk: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
