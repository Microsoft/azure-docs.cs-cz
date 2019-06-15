---
title: Vývoj modulu pro zařízení s Windows – Azure IoT Edge | Dokumentace Microsoftu
description: Tento kurz vás provede nastavením vaše vývojové počítače a cloudové prostředky pro vývoj modulů IoT Edge pro zařízení s Windows pomocí kontejnerů Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 94a287cd996bd18b757620254540f8dc0df499e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051811"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Kurz: Vývoj modulů IoT Edge pro zařízení s Windows

Pomocí sady Visual Studio k vývoji a nasazení kódu do zařízení Windows s IoT Edge.

V rychlém startu jste vytvořili zařízení IoT Edge pomocí virtuálního počítače s Windows a nasazení předem sestavených modulu z Azure Marketplace. Tento kurz vás provede všechno potřebné k vývoji a nasazení do zařízení IoT Edge vlastní kód. V tomto kurzu je předpokladem užitečné pro všechny ostatní kurzy, které začnou více podrobností o konkrétní programovací jazyky nebo služeb Azure. 

Tento kurz používá v příkladu nasazení  **C# modulu do zařízení Windows**. V tomto příkladu byla vybrána, protože je nejběžnější scénáře vývoje. Pokud vás zajímá vývoj v jiném jazyce nebo plánujete nasazení služeb Azure jako moduly, budou se v tomto kurzu dál užitečné informace týkající se nástroje pro vývoj. Až porozumíte konceptům vývoj, pak můžete použít upřednostňovaný jazyk nebo služby Azure, chcete-li přejít k podrobnostem. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení vývojového počítače.
> * Pomocí IoT Edge nástrojů pro Visual Studio k vytvoření nového projektu.
> * Sestavení projektu jako kontejner a uložit ho do služby Azure container registry.
> * Nasazení kódu do zařízení IoT Edge. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Klíčové koncepty

Tento kurz vás provede vývoj modul IoT Edge. *Modul IoT Edge*, nebo někdy právě *modulu* krátký, je kontejner, který obsahuje spustitelný kód. Jeden nebo více modulů můžete nasadit do zařízení IoT Edge. Moduly provádějí konkrétní úlohy, jako je ingestuje data ze senzorů, provádění analýz dat nebo dat čištění nebo odesílání zpráv do služby IoT hub. Další informace najdete v tématu [moduly pochopit Azure IoT Edge](iot-edge-modules.md).

Při vývoji moduly IoT Edge, je důležité pochopit rozdíl mezi vývojového počítače a cílové zařízení IoT Edge, kde modul nakonec se nasadí. Kontejner, který jste vytvořili pro uložení kód modulu musí odpovídat operačního systému (OS) *cílové zařízení*. Pro vývoj v kontejneru Windows tento koncept je jednodušší, protože kontejnery Windows spustit pouze v operačních systémech Windows. Ale svém vývojovém počítači s Windows můžete například použít k vytvoření modulů pro Linux hraniční zařízení IoT. V tomto scénáři je třeba Ujistěte se, že vývojovém počítači byl spuštěn kontejnery Linuxu. Při procházení tohoto kurzu, mějte na paměti rozdíl mezi *vývojovém počítači s operačním systémem* a *kontejneru OS*.

V tomto kurzu cílí na zařízení Windows s IoT Edge. Zařízení Windows IoT Edge používat kontejnery Windows. Doporučujeme použít Visual Studio k vývoji pro zařízení s Windows, tak, aby se, co bude používat v tomto kurzu. Visual Studio Code můžete použít, i když existují rozdíly mezi dva nástroje podpory.

V následující tabulce jsou uvedeny podporované vývojové scénáře pro **kontejnery Windows** v aplikaci Visual Studio Code a Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Služby Azure** | Azure Functions <br> Azure Stream Analytics |   |
| **Jazyky** | C#(ladění není podporované) | C <br> C# |
| **Další informace** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Požadavky

Vývojovém počítači:

* Windows 10 s aktualizací update 1809 nebo novější.
* V závislosti na vašich předvolbách vývoje můžete použít vlastní počítač nebo virtuální počítač.
* Nainstalujte [Git](https://git-scm.com/). 

Zařízení Azure IoT Edge ve Windows:

* Doporučujeme vám, že nejsou spuštěné IoT Edge na vývojovém počítači, ale místo toho použijte samostatné zařízení. Tento rozdíl mezi vývojového počítače a zařízení IoT Edge přesně odráží scénář true nasazení a pomáhá udržovat různé koncepty přímo.
* Pokud nemáte druhé zařízení k dispozici, použijte k vytvoření zařízení IoT Edge v Azure pomocí tohoto článku rychlý Start [Windows virtuální počítač](quickstart.md).

Cloudové prostředky:

* A standard – úroveň free nebo [služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) v Azure. 

## <a name="install-container-engine"></a>Nainstalujte modul container

Moduly IoT Edge jsou zabalené jako kontejnery, takže vám stačí modul kontejneru na vývojovém počítači vytvářet a spravovat kontejnery. Doporučujeme používat Docker Desktop pro vývoj z důvodu mnoho funkcí a popularita jako modul kontejneru. V Desktopu Dockeru na počítači Windows můžete přepínat mezi kontejnery Linux a kontejnery Windows tak, aby vám umožní snadno vytvářet moduly pro různé typy zařízení IoT Edge. 

Dokumentace k Dockeru použijte k instalaci na vývojovém počítači: 

* [Instalace Desktopu Docker pro Windows](https://docs.docker.com/docker-for-windows/install/)

  * Při instalaci Dockeru plocha pro Windows, budete vyzváni, zda chcete používat kontejnery Linux nebo Windows. Pro účely tohoto kurzu použijte **kontejnery Windows**. Další informace najdete v tématu [přepínání mezi kontejnery Windows a Linuxem](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).


## <a name="set-up-visual-studio-and-tools"></a>Nastavení sady Visual Studio a nástroje

Rozšíření IoT pro Visual Studio vám pomohou s vývojem modulů IoT Edge. Tato rozšíření poskytují šablony projektu, automatizovat vytváření manifestu nasazení a vám umožní monitorovat a spravovat zařízení IoT Edge. V této části Nainstalujte Visual Studio a rozšíření IoT Edge pak nastavení účtu Azure ke správě prostředků služby IoT Hub z Visual Studia. 

V tomto kurzu se dozvíte, jaké kroky vývoje pro Visual Studio 2019. Pokud používáte Visual Studio 2017 (verze 15.7 nebo novější), kroky jsou velmi podobné. Pokud místo toho můžete využít Visual Studio Code, použijte pokyny v [použít Visual Studio Code k vývoji a ladění modulů Azure IoT Edge](how-to-vs-code-develop-module.md). 

1. Příprava Visual Studio 2019 na svém vývojovém počítači. 

   * Pokud ještě nemáte Visual Studio na svém vývojovém počítači [nainstalovat Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) s následujícími sadami funkcí: 

      * Vývoj pro Azure
      * Vývoj desktopových aplikací pomocí C++
      * Vývoj pro různé platformy .NET core

   * Pokud již máte Visual Studio 2019 na vývojovém počítači, postupujte podle kroků v [upravit Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) přidat požadované úlohy.

2. Stáhněte a nainstalujte [nástroje Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) rozšíření pro Visual Studio 2019. 

   Pokud používáte Visual Studio 2017 (verze 15.7 nebo novější), stáhněte a nainstalujte [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Po dokončení instalace otevřete Visual Studio 2019 a vyberte **pokračovat bez kódu**.

4. Vyberte **zobrazení** > **Cloud Explorer**. 

5. Vyberte ikonu profilu v Průzkumníkovi cloudu a přihlásit ke svému účtu Azure, pokud jste ještě nejste přihlášení. 

6. Jakmile se přihlásíte, jsou uvedeny vaše předplatná Azure. Rozbalte předplatné obsahující službu IoT hub. 

7. V rámci vašeho předplatného, rozbalte **centra IoT hub** pak služby IoT hub. By měl zobrazit seznam zařízení IoT a tohoto Průzkumníka můžete použít k jejich správě. 

   ![Přístup k prostředkům služby IoT Hub v Průzkumníkovi cloudu](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Vytvoření nového projektu modulu

Rozšíření nástroje Azure IoT Edge poskytuje šablony projektů pro všechny podporované IoT Edge module jazyků v sadě Visual Studio. Tyto šablony mají všechny soubory a kód, který musíte nasadit pracovní modul pro testování IoT Edge, nebo získáte výchozí bod pro přizpůsobení šablony s vlastní obchodní logikou. 

1. Vyberte **souboru** > **nové** > **projektu...**

2. V okně Nový projekt vyhledat **IoT Edge** a zvolte **Azure IoT Edge (Windows amd64)** projektu. Klikněte na **Další**. 

   ![Vytvořte nový projekt Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. Konfigurací okna Nový projekt přejmenovat projektu a řešení na něco popisného jako **CSharpTutorialApp**. Klikněte na tlačítko **vytvořit** pro vytvoření projektu.

   ![Konfigurovat nový projekt Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)
 

4. V okně aplikace IoT Edge a modul konfiguraci projektu s použitím následujících hodnot: 

   | Pole | Hodnota |
   | ----- | ----- |
   | Vybrat šablonu | Vyberte  **C# modulu**. | 
   | Název modulu projektu | Přijměte výchozí nastavení **IoTEdgeModule1**. | 
   | Úložiště imagí dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Svou image kontejneru je předem z hodnoty názvu modulu projektu. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br> Finální bitové kopie úložiště bude vypadat jako \<název registru\>.azurecr.io/iotedgemodule1. |

   ![Konfigurace projektu pro cílové zařízení, typ modulu a container registry](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Vyberte **Ano** změny. 

Po načtení nového projektu v okně aplikace Visual Studio, věnujte chvíli seznámení se soubory, které vytvořil: 

* Projekt aplikace IoT Edge nazývá **CSharpTutorialApp**.
    * **Moduly** složka obsahuje odkazy na moduly, které jsou součástí projektu. V takovém případě je třeba pouze IoTEdgeModule1. 
    * **Deployment.template.json** soubor je šablonu, která vám pomůžou vytvořit manifest nasazení. A *manifest nasazení* je soubor, který definuje přesně požadované moduly nasazené na zařízení, jak by měla být nakonfigurovaná a jak by mohl komunikovat mezi sebou a cloudu. 
* Volá se projekt modul IoT Edge **IoTEdgeModule1**.
    * **Program.cs** soubor obsahuje výchozí C# kód modulu, který je součástí šablony projektu. Výchozí modul přijímá vstupní ze zdroje a předá jej společně do služby IoT Hub. 
    * **Module.json** souboru blokování podrobnosti o modulu úplnou bitovou kopii úložiště, včetně obrázků verze a které soubor Dockerfile pro použití u každého podporovaných platforem.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Zadejte svoje přihlašovací údaje registru do agenta IoT Edge

Modul runtime IoT Edge potřebuje vaše přihlašovací údaje registru přetahování imagí kontejnerů do zařízení IoT Edge. Přidejte tyto přihlašovací údaje pro nasazení šablony. 

1. Otevřít **deployment.template.json** souboru.

2. Najít **registryCredentials** vlastnost $edgeAgent požadované vlastnosti. 

3. Aktualizujte vlastnost pomocí svých přihlašovacích údajů, za tento formát: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Uložte soubor deployment.template.json. 

### <a name="review-the-sample-code"></a>Revize ukázkového kódu

Šablona řešení, který jste vytvořili obsahuje ukázkový kód pro modul IoT Edge. Tento modul bude jednoduše dostávat zprávy a předává je. Funkce kanálu ukazuje důležitý koncept v IoT Edge, což je, jak moduly komunikovat mezi sebou.

Každý modul může mít více *vstupní* a *výstup* fronty deklarovat ve svém kódu. IoT Edge hub spuštěného v příslušném zařízení provádí směrování zpráv ve výstupu jeden modul do vstup jednu nebo více modulů. Konkrétní jazyk pro deklarování vstupy a výstupy se pohybuje mezi jazyky, ale je stejný koncept přes všechny moduly. Další informace o směrování mezi moduly, naleznete v tématu [trasy deklarovat](module-composition.md#declare-routes).

Ukázka C# používá kód, který je součástí šablony projektu [ModuleClient třídy](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) z IoT Hub SDK pro .NET. 

1. V **program.cs** souboru, vyhledejte **SetInputMessageHandlerAsync** metody.

2. [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) metoda nastaví Vstupní fronta pro příjem příchozí zprávy. Tato metoda zkontrolovat a zjistit, jak inicializuje Vstupní fronta volá **vstup1**. 

   ![V konstruktoru SetInputMessageHandlserAsync najít název vstupu](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Dále vyhledejte **SendEventAsync** metody.

4. [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) metoda zpracuje přijaté zprávy a nastaví výstupní fronty předávat společně. Tato metoda zkontrolovat a zjistit, inicializuje do výstupní fronty volá **output1**. 

   ![V konstruktoru SendEventAsync najít název výstupu](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Otevřít **deployment.template.json** souboru.

6. Najít **moduly** vlastnost $edgeAgent požadované vlastnosti. 

   Měla by existovat dva moduly, které jsou tady uvedené. První je **tempSensor**, které je součástí všech šablon ve výchozím nastavení poskytovat Simulovaná data o teplotě, můžete použít k otestování modulů. Druhým je **IotEdgeModule1** modul, který jste vytvořili jako součást tohoto projektu.

   Tato vlastnost moduly deklaruje, které moduly by měl být součástí nasazení, aby vaše zařízení nebo zařízení. 

7. Najít **trasy** vlastnost $edgeHub požadované vlastnosti. 

   Jedna z funkcí, pokud je modul IoT Edge hub ke směrování zpráv mezi všechny moduly v nasazení. Zkontrolujte hodnoty ve vlastnosti trasy. První trasa **IotEdgeModule1ToIoTHub**, používá zástupný znak ( **\*** ) Chcete-li zahrnout všechny zprávy z libovolné výstupní fronty v modulu IoTEdgeModule1. Tyto zprávy přejít do *$upstream*, který je vyhrazený název, který označuje služby IoT Hub. Druhá trasa **sensorToIotEdgeModule1**, přijímá zprávy přicházející z modulu tempSensor a směruje je do *vstup1* vstupní fronty IotEdgeModule1 modulu. 

   ![Projděte si v deployment.template.json trasy](./media/tutorial-develop-for-windows/deployment-routes.png)


## <a name="build-and-push-your-solution"></a>Vytváření a nasdílení změn vašeho řešení

Můžete si kód modulu a šablonu nasazení některé koncepce klíče nasazení. Teď budete připraveni k sestavení image kontejneru IotEdgeModule1 a nasdílejte ji do vašeho registru kontejneru. Pomocí rozšíření IoT tools for Visual Studio tento krok také vygeneruje manifest nasazení na základě informací v souboru šablony a informace o modulu ze souborů řešení. 

### <a name="sign-in-to-docker"></a>Přihlaste se k Dockeru

Zadejte svůj kontejner registru přihlašovací údaje pro Docker na svém vývojovém počítači tak, aby ji nasdílet image kontejnerů mají být uloženy v registru. 

1. Otevřete prostředí PowerShell nebo příkazového řádku.

2. Přihlaste se k Dockeru pomocí přihlašovacích údajů registru kontejnerů Azure, které jste si uložili po vytvoření registru. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití `--password-stdin`. Tento osvědčený postup doporučuje pro produkční scénáře, je mimo rámec tohoto návodu. Další informace najdete v tématu [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odkaz.

### <a name="build-and-push"></a>Vytváření a nasdílení změn

Vývojovém počítači má nyní přístup k registru kontejneru a zařízení IoT Edge bude příliš. Je čas, chcete-li projekt kódu do image kontejneru. 

1. Klikněte pravým tlačítkem myši **CSharpTutorialApp** složky projektu a vyberte **sestavení a Push moduly IoT Edge**. 

   ![Vytváření a nasdílení změn moduly IoT Edge](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Sestavení a odeslání příkaz spustí tři operace. Nejprve vytvoří novou složku řešení, nazývá **config** , který obsahuje soubory manifestu, sestavení si informace při nasazení šablony a dalších řešení úplné nasazení. Za druhé, poběží `docker build` k sestavení image kontejneru, který je založen na příslušný soubor dockerfile pro cílové architektury. Pak spustí `docker push` tak, aby nabízel úložiště imagí do vašeho registru kontejneru. 

   Tento proces může trvat několik minut, než poprvé, ale je rychlejší při příštím spuštění příkazů. 

2. Otevřít **deployment.windows amd64.json** souboru ve složce nově vytvořený config. (Složce Konfigurace nemůže nacházet v Průzkumníku řešení v sadě Visual Studio. Pokud je to tento případ, vyberte **zobrazit všechny soubory** ikonu na hlavním panelu Průzkumníku řešení.)

3. Najít **image** parametr IotEdgeModule1 oddílu. Všimněte si, že bitová kopie obsahuje úplnou bitovou kopii úložiště se název, verzi a architekturu značkou ze souboru module.json.

4. Otevřít **module.json** souboru ve složce IotEdgeModule1. 

5. Změňte číslo verze bitové kopie modulu. (Verze, ne $schema – verze.) Například zvýší číslo verze opravy na **0.0.2** jakoby jsme měli provedení malé opravy v modulu kódu. 

   >[!TIP]
   >Verze modulu povolit správu verzí a využijete k otestování změn na malou sadu zařízení před nasazením aktualizací do produkčního prostředí. Pokud není zvýšení verze modulu před vytváření a odesílání, přepíšete úložiště do vašeho registru kontejneru. 

6. Uložte provedené změny do souboru module.json.

7. Klikněte pravým tlačítkem myši **CSharpTutorialApp** složky projekt znovu a vyberte **sestavení a Push IoT Edge moduly** znovu. 

8. Otevřít **deployment.windows amd64.json** soubor znovu. Všimněte si, že nebyl vytvořen nový soubor, když jste spustili příkaz sestavení a odeslání. Místo toho stejného souboru byla aktualizována tak, aby odrážely změny. Obrázek IotEdgeModule1 nyní odkazovalo 0.0.2 verzi kontejneru. Tato změna v manifestu nasazení je, jak zjistit zařízení IoT Edge, která je dostupná nová verze modulu, který o přijetí změn. 

9. Dále ověřte, co sestavení a odeslání příkaz provedl, přejděte na [webu Azure portal](https://portal.azure.com) a přejděte do vašeho registru kontejneru. 

10. Do vašeho registru kontejneru, vyberte **úložišť** pak **iotedgemodule1**. Ověřte, že obě verze bitové kopie byly nahrány do registru.

    ![Zobrazit obě verze image do registru kontejneru](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na chyby při vytváření a odesílání bitové kopie modulu, často je provést v konfiguraci Dockeru na vývojovém počítači. Zkontrolujte konfiguraci pomocí následující kontroly: 

* Narazili jste `docker login` příkaz pomocí přihlašovacích údajů, které jste zkopírovali ze služby container registry? Tyto přihlašovací údaje se liší od těch, které používáte k přihlášení do Azure. 
* Správnost vašeho kontejneru úložiště? Nemá název vašeho registru správný kontejner a název vaší správný modul? Otevřít **module.json** souboru ve složce IotEdgeModule1 ke kontrole. Hodnota úložiště by měl vypadat jako  **\<název registru\>.azurecr.io/iotedgemodule1**. 
* Pokud jste použili jiný název než **IotEdgeModule1** modul, je tento název konzistentní v rámci řešení?
* Je váš počítač používá stejný typ kontejnerů, které vytváříte? Tento kurz je určen pro zařízení s Windows IoT Edge, takže by měl mít soubory sady Visual Studio **windows amd64** rozšíření a Docker Desktop by měla být spuštěná kontejnery Windows. 

## <a name="deploy-modules-to-device"></a>Do zařízení nasadit moduly

Jste ověřili, že Image kontejneru sestavené jsou uloženy v vašeho registru kontejneru, tak, aby byl čas je nasadit do zařízení. Ujistěte se, že zařízení IoT Edge je zprovozněný. 

1. V sadě Visual Studio, otevřete Průzkumníka cloudu a Rozbalit podrobnosti o Centru IoT. 

2. Vyberte název, který chcete nasadit do zařízení. V **akce** seznamu vyberte **vytvořit nasazení**.

   ![Vytvoření nasazení pro jedno zařízení](./media/tutorial-develop-for-windows/create-deployment.png)


3. V Průzkumníku souborů přejděte do složky Konfigurace projektu a vyberte **deployment.windows amd64.json** souboru. Tento soubor je často v `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Nepoužívejte deployment.template.json soubor, který nemá hodnoty úplné modulu image v něm. 

4. Rozbalte podrobnosti pro vaše zařízení IoT Edge v Průzkumníkovi cloudu zobrazíte moduly na vašem zařízení.

5. Použití **aktualizovat** tlačítko pro aktualizaci stavu zařízení zobrazíte nasazené tempSensor a IotEdgeModule1 moduly vašeho zařízení. 


   ![Zobrazení modulů běžícího ve vašem zařízení IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Zobrazení zpráv ze zařízení

Kód IotEdgeModule1 přijímá zprávy do vstupní fronty a předává je podél prostřednictvím jeho výstupní fronty. Manifest nasazení deklaraci trasy, která předána zprávy z tempSensor IotEdgeModule1 a pak se předávají zprávy z IotEdgeModule1 do služby IoT Hub. Nástroje Azure IoT Edge pro Visual Studio umožňují zobrazovat zprávy při jejich doručení na IoT Hub z jednotlivých zařízeních. 

1. V Průzkumníku cloudu sady Visual Studio vyberte název zařízení IoT Edge, které jste nasadili. 

2. V **akce** nabídce vyberte možnost **spustit monitorování integrovaných událostí koncový bod**.

3. Podívejte **výstup** části v sadě Visual Studio zobrazíte zprávy přicházející v jiném ve službě IoT hub. 

   Může trvat několik minut, než se oba moduly spustit. Modul runtime IoT Edge musí obdržet svůj nový manifest nasazení, stáhněte bitové kopie modulu z modulu runtime kontejneru a pak spusťte každý nový modul. Pokud jste 

   ![Zobrazit příchozí zařízení na cloud zprávy](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Zobrazení změn v zařízení

Pokud chcete zobrazit, co se děje ve vašem zařízení samotné, použijte příkazy v této části ke kontrole modul runtime IoT Edge a modulů spuštěných ve vašem zařízení. 

Příkazy v této části jsou určené pro zařízení IoT Edge, ne vývojovém počítači. Pokud používáte virtuální počítač pro vaše zařízení IoT Edge, připojte se k němu nyní. V Azure, přejděte na stránku přehled virtuálního počítače a vyberte **připojit** pro přístup k připojení ke vzdálené ploše. Na zařízení, otevřete příkazu nebo okno Powershellu pro spuštění `iotedge` příkazy.

* Zobrazit všechny moduly nasazené do zařízení a zkontrolovat jejich stav:

   ```cmd
   iotedge list
   ```

   Měli byste vidět čtyři moduly: dva moduly runtime IoT Edge, tempSensor a IotEdgeModule1. Všechny čtyři by měly být uvedeny jako spuštění.

* Zkontrolujte protokoly pro konkrétní modul:

   ```cmd
   iotedge logs <module name>
   ```

   Moduly IoT Edge jsou malá a velká písmena. 

   TempSensor a IotEdgeModule1 protokoly by měly vykazovat zprávy, které jste zpracování. Modul edgeAgent je zodpovědný za spouštění dalších modulů, aby protokolům bude mít informace o implementaci manifest nasazení. Pokud jakýkoli modul není uvedené nebo není spuštěn, protokoly edgeAgent pravděpodobně chyby. Modul edgeHub zodpovídá za komunikaci mezi moduly a IoT Hub. Pokud moduly nastavené a spuštěné, ale zprávy nejsou přicházejících u služby IoT hub, protokoly edgeHub pravděpodobně chyby. 

## <a name="next-steps"></a>Další postup

V tomto kurzu nastavte Visual Studio 2019 na počítači pro vývoj a nasazení prvního modulu IoT Edge z něj. Teď, když znáte základní koncepty, vyzkoušejte přidávání funkcí do modulu tak, aby ho můžete analyzovat data procházejících. Vyberte upřednostňovaný jazyk: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
