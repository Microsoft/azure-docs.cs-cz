---
title: Vývoj modulu pro Linux - zařízení Azure IoT Edge | Dokumentace Microsoftu
description: Tento kurz vás provede nastavením vaše vývojové počítače a cloudové prostředky pro vývoj modulů IoT Edge pro zařízení s Linuxem pomocí kontejnery Linuxu
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 41589dccae4e1259e4dc9368d5825ba770b4bdcc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146707"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Kurz: Vývoj modulů IoT Edge pro zařízení s Linuxem

Použití Visual Studio Code pro vývoj a nasazení kódu do Linuxu zařízení IoT Edge. 

V článcích rychlý start jste vytvořili zařízení IoT Edge pomocí virtuálního počítače s Linuxem a nasazení předem sestavených modulu z Azure Marketplace. Tento kurz vás provede všechno potřebné k vývoji a nasazení do zařízení IoT Edge vlastní kód. V tomto kurzu je předpokladem užitečné pro všechny ostatní kurzy, které začnou více podrobností o konkrétní programovací jazyky nebo služeb Azure. 

Tento kurz používá v příkladu nasazení **modulu jazyka C k Linuxovému zařízení**. V tomto příkladu byla zvolena, protože má nejmíň požadavky tak, aby informace o nástroje pro vývoj bez starostí o tom, jestli máte správné knihovny nainstalované. Až porozumíte konceptům vývoj, pak můžete použít upřednostňovaný jazyk nebo služby Azure, chcete-li přejít k podrobnostem. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení vývojového počítače.
> * Pomocí IoT Edge nástrojů pro Visual Studio Code k vytvoření nového projektu.
> * Sestavení projektu jako kontejner a uložit ho do služby Azure container registry.
> * Nasazení kódu do zařízení IoT Edge. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Klíčové koncepty

Tento kurz vás provede vývoj modul IoT Edge. *Modul IoT Edge*, nebo někdy právě *modulu* krátký, je kontejner, který obsahuje spustitelný kód. Jeden nebo více modulů můžete nasadit do zařízení IoT Edge. Moduly provádějí konkrétní úlohy, jako je ingestuje data ze senzorů, provádění analýz dat nebo dat čištění nebo odesílání zpráv do služby IoT hub. Další informace najdete v tématu [moduly pochopit Azure IoT Edge](iot-edge-modules.md).

Při vývoji moduly IoT Edge, je důležité pochopit rozdíl mezi vývojového počítače a cílové zařízení IoT Edge, kde modul nakonec se nasadí. Kontejner, který jste vytvořili pro uložení kód modulu musí odpovídat operačního systému (OS) *cílové zařízení*. Nejběžnější scénář je například někdo vývoj modulu v počítači Windows hodláte cíl na nějaké zařízení Linux s IoT Edge. V takovém případě bude kontejner operačního systému Linux. Při procházení tohoto kurzu, mějte na paměti rozdíl mezi *vývojovém počítači s operačním systémem* a *kontejneru OS*.

V tomto kurzu cílí na Linux zařízení s IoT Edge. Oblíbeným vývojovým operačního systému počítače, můžete použít tak dlouho, dokud svého vývojového počítače mohli spouštět kontejnery Linux. Doporučujeme použít Visual Studio Code pro vývoj pro Linux zařízení, tak, aby se, co bude používat v tomto kurzu. Visual Studio 2017 můžete použít, i když existují rozdíly mezi dva nástroje podpory.

V následující tabulce jsou uvedeny podporované vývojové scénáře pro **kontejnery Linuxu** v aplikaci Visual Studio Code a Visual Studio 2017.

|   | Visual Studio Code | Visual Studio 2017 |
| - | ------------------ | ------------------ |
| **Architektura zařízení Linux** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Služby Azure** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Jazyky** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Další informace** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) |

V tomto kurzu se dozvíte, jaké kroky vývoje pro Visual Studio Code. Pokud místo toho můžete využít Visual Studio 2017, použijte pokyny v [použít Visual Studio 2017 na vývoj a ladění modulů Azure IoT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Požadavky

Vývojovém počítači:

* V závislosti na vašich předvolbách vývoje můžete použít vlastní počítač nebo virtuální počítač.
* Většina operačních systémů, které lze spustit kontejner modulu je možné k vývoji moduly IoT Edge pro zařízení s Linuxem. Tento kurz používá počítače Windows, ale poukazuje na známé rozdíly v systému MacOS nebo Linux. 
* Nainstalujte [Git](https://git-scm.com/), aby se načetla modulu balíčky šablon později v tomto kurzu.  

Zařízení Azure IoT Edge v Linuxu:

* Doporučujeme vám, že nejsou spuštěné IoT Edge na vývojovém počítači, ale místo toho použijte samostatné zařízení. Tento rozdíl mezi vývojového počítače a zařízení IoT Edge přesně odráží scénář true nasazení a pomáhá udržovat různé koncepty přímo.
* Pokud nemáte druhé zařízení k dispozici, použijte k vytvoření zařízení IoT Edge v Azure pomocí tohoto článku rychlý Start [virtuálního počítače s Linuxem](quickstart-linux.md).

Cloudové prostředky:

* A standard – úroveň free nebo [služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) v Azure. 

## <a name="install-container-engine"></a>Nainstalujte modul container

Moduly IoT Edge jsou zabalené jako kontejnery, takže vám stačí modul kontejneru na vývojovém počítači vytvářet a spravovat kontejnery. Doporučujeme používat Docker Desktop pro vývoj z důvodu mnoho funkcí a popularita jako modul kontejneru. V Desktopu Dockeru na zařízení s Windows můžete přepínat mezi kontejnery Linux a kontejnery Windows tak, aby vám umožní snadno vytvářet moduly pro různé typy zařízení IoT Edge. 

Dokumentace k Dockeru použijte k instalaci na vývojovém počítači: 

* [Instalace Desktopu Docker pro Windows](https://docs.docker.com/docker-for-windows/install/)

  * Při instalaci Dockeru plocha pro Windows, budete vyzváni, zda chcete používat kontejnery Linux nebo Windows. Toto rozhodnutí lze změnit kdykoli snadno přepínačem. Pro účely tohoto kurzu používáme kontejnery Linuxu, protože naše moduly cílí na Linux zařízení. Další informace najdete v tématu [přepínání mezi kontejnery Windows a Linuxem](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Nainstalujte Docker Desktop pro Mac](https://docs.docker.com/docker-for-mac/install/)

* Čtení [Docker CE](https://docs.docker.com/install/) pro informace o instalaci na několika platformách Linux.

## <a name="set-up-vs-code-and-tools"></a>Nastavení nástroji VS Code a nástroje

Použití rozšíření IoT pro Visual Studio Code k vývoji moduly IoT Edge. Tato rozšíření poskytují šablony projektu, automatizovat vytváření manifestu nasazení a vám umožní monitorovat a spravovat zařízení IoT Edge. V této části Nainstalujte Visual Studio Code a rozšíření IoT pak nastavení účtu Azure ke správě prostředků služby IoT Hub z v rámci Visual Studio Code. 

1. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) na vývojovém počítači. 

2. Po dokončení instalace, vybrat **zobrazení** > **rozšíření**. 

3. Vyhledejte **nástroje Azure IoT**, který je ve skutečnosti kolekci rozšíření, které vám pomohou pracovat s IoT Hub a zařízení IoT, jakož i vývoj modulů IoT Edge. 

4. Vyberte **Install** (Nainstalovat). Každé zahrnutých rozšíření nainstaluje samostatně. 

5. Po dokončení rozšíření nainstalovat, otevřete paletu příkazů tak, že vyberete **zobrazení** > **paletu příkazů**. 

6. V paletu příkazů, vyhledejte a vyberte **Azure: Přihlaste se**. Podle pokynů se přihlaste ke svému účtu Azure. 

7. Paleta příkazů znovu, vyhledejte a vyberte **Azure IoT Hub: Vyberte službu IoT Hub**. Postupujte podle výzev a vyberte své předplatné Azure a IoT hub. 

7. Otevřete část Průzkumníka Visual Studio Code, tak, že vyberete ikonu na panelu aktivit na levé straně, nebo výběrem **zobrazení** > **Explorer**. 

8. V dolní části Průzkumníku rozbalit sbalený **zařízení Azure IoT Hub** nabídky. Měli byste vidět, zařízení a zařízení IoT Edge, které jsou přidružené k službě IoT hub, kterou jste vybrali prostřednictvím paletu příkazů. 

   ![Zobrazit zařízení ve službě IoT hub](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Vytvoření nového projektu modulu

Rozšíření nástroje Azure IoT nabízí šablony projektů pro všechny podporované IoT Edge modulu jazyky ve Visual Studio Code. Tyto šablony mají všechny soubory a kód, který musíte nasadit pracovní modul pro testování IoT Edge, nebo získáte výchozí bod pro přizpůsobení šablony s vlastní obchodní logikou. 

Pro účely tohoto kurzu používáme šablony modulu jazyka C, protože má nejmíň požadavky pro instalaci. 

### <a name="create-a-project-template"></a>Vytvoření šablony projektu

V aplikaci Visual Studio Code paletu příkazů, vyhledejte a vyberte **Azure IoT Edge: Nové řešení IoT Edge**. Postupujte podle pokynů a vytvořte řešení pomocí následujících hodnot: 

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Zvolte **modulu C**. |
   | Zadejte název modulu | Přijměte výchozí nastavení **SampleModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Svou image kontejneru je předem z názvu, který jste zadali v předchozím kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br> Finální bitové kopie úložiště bude vypadat jako \<název registru\>.azurecr.io/samplemodule. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-develop-for-linux/image-repository.png)

Po načtení nového řešení v okně Visual Studio Code, věnujte chvíli seznámení se soubory, které vytvořil: 

* **.Vscode** složka obsahuje soubor s názvem **launch.json**, který se používá pro ladění modulů.
* **Moduly** složka obsahuje složku pro každý modul v rámci vašeho řešení. Právě teď, který by měly být jenom **SampleModule**, nebo cokoli, co jste název zadali do modulu. Složka SampleModule obsahuje hlavní programový kód, metadata modulu a několik souborů Docker. 
* **.Env** soubor uchovává přihlašovací údaje do vašeho registru kontejneru. Tyto přihlašovací údaje jsou sdíleny s vaším zařízením IoT Edge, tak, aby měl přístup k přetahování imagí kontejneru. 
* **Deployment.debug.template.json** souboru a **deployment.template.json** souboru jsou šablony, které vám pomůžou vytvořit manifest nasazení. A *manifest nasazení* je soubor, který definuje přesně požadované moduly nasazené na zařízení, jak by měla být nakonfigurovaná a jak by mohl komunikovat mezi sebou a cloudu. Soubory šablon pro některé hodnoty použít ukazatele. Při transformaci šablony do manifestu nasazení true ukazatelů jsou nahrazeny hodnoty z jiných souborů řešení. Vyhledejte dvě běžné zástupné symboly v šabloně nasazení: 

  * V části přihlašovací údaje registru je adresa autofilled z informací, které jste zadali při vytváření řešení. Nicméně uživatelské jméno a heslo referenční proměnné uloženy v souboru .env. To je pro zabezpečení, protože souboru .env je git, které jsou ignorovány, ale není šablona nasazení. 
  * V části SampleModule není vyplněno image kontejneru i v případě, že jste zadali při vytváření řešení úložiště imagí. Tento zástupný text odkazuje **module.json** souboru ve složce SampleModule. Pokud přejdete do tohoto souboru, uvidíte, že pole image neobsahuje úložiště, ale také určitou hodnotou značky, který se skládá z verze a platformu kontejneru. Můžete iterovat verze ručně jako součást cyklu vývoje a vyberte platformu pro kontejnery pomocí přepínače, který zavedeme později v této části. 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Zadejte svoje přihlašovací údaje registru do agenta IoT Edge

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime potřebuje tyto přihlašovací údaje pro o přijetí změn imagí kontejnerů do zařízení IoT Edge. 

Rozšíření IoT Edge se pokusí o přijetí změn vašeho kontejneru přihlašovacích údajů registru z Azure a naplníte je v souboru prostředí. Zkontrolujte, jestli vaše přihlašovací údaje jsou již zahrnuty. Pokud ne, přidejte je nyní:

1. Otevřít **.env** souboru ve vašem řešení modulu. 
2. Přidat **uživatelské jméno** a **heslo** hodnoty, které jste zkopírovali ze služby Azure container registry.
3. Uložte provedené změny do souboru .env. 

### <a name="select-your-target-architecture"></a>Vyberte Cílová architektura

Visual Studio Code v současné době můžete vyvíjet C moduly pro Linux AMD64 a Linux ARM32v7 zařízení. Budete muset vybrat jakou architekturu vývoji cílíte s jednotlivých řešení, protože, která ovlivňuje, jak je založená kontejner a používá. Výchozí hodnota je Linux AMD64. 

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavit výchozí Cílová platforma pro řešení**, nebo vyberte ikonu zástupce v bočním panelu v dolní části okna. 

   ![V bočním panelu vyberte ikonu architektury](./media/tutorial-develop-for-linux/select-architecture.png)

2. Vyberte Cílová architektura v paletu příkazů v seznamu možností. Pro účely tohoto kurzu používáme virtuálního počítače s Ubuntu jako zařízení IoT Edge, budou mít výchozí **amd64**. 

### <a name="review-the-sample-code"></a>Revize ukázkového kódu

Šablona řešení, který jste vytvořili obsahuje ukázkový kód pro modul IoT Edge. Tento modul bude jednoduše dostávat zprávy a předává je. Funkce kanálu ukazuje důležitý koncept v IoT Edge, což je, jak moduly komunikovat mezi sebou.

Každý modul může mít více *vstupní* a *výstup* fronty deklarovat ve svém kódu. IoT Edge hub spuštěného v příslušném zařízení provádí směrování zpráv ve výstupu jeden modul do vstup jednu nebo více modulů. Konkrétní jazyk pro deklarování vstupy a výstupy se pohybuje mezi jazyky, ale je stejný koncept přes všechny moduly. Další informace o směrování mezi moduly, naleznete v tématu [trasy deklarovat](module-composition.md#declare-routes).

1. Otevřít **main.c** soubor, který se nachází uvnitř **moduly/SampleModules/** složky. 

2. Sady SDK IoT Hub C používá funkci [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback) se inicializovat modul vstupní fronty. Vyhledávání v rámci souboru main.c pro tuto funkci.

3. Zkontrolovat SetInputMessageCallback funkce konstruktoru a zjistit, že vstupní fronta s názvem **vstup1** je inicializována v kódu. 

   ![V konstruktoru SetInputMessageCallback najít název vstupu](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Modul výstupní fronty jsou inicializovány podobným způsobem. Hledat [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync) funkce v souboru main.c. 

5. Zkontrolovat SendEventToOutputAsync funkce konstruktoru a zjistit, že výstupní fronty s názvem **output1** je inicializována v kódu. 

   ![V SendEventToOutputAsync najít název výstupu](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Otevřít **deployment.template.json** souboru.

7. Najít **moduly** vlastnost $edgeAgent požadované vlastnosti. 

   Měla by existovat dva moduly, které jsou tady uvedené. První je **tempSensor**, které je součástí všech šablon ve výchozím nastavení poskytovat Simulovaná data o teplotě, můžete použít k otestování modulů. Druhým je **SampleModule** modul, který jste vytvořili v rámci tohoto řešení.

7. V dolní části souboru vyhledejte požadované vlastnosti pro **$edgeHub** modulu. 

   Jednou z funkcí modulu centra IoT Edge je pro směrování zpráv mezi všechny moduly v nasazení. Zkontrolujte hodnoty v **trasy** vlastnost. První trasa **SampleModuleToIoTHub**, používá zástupný znak (**\***) k označení jakékoli zprávy přicházející z žádné výstupní fronty v modulu SampleModule. Tyto zprávy přejít do *$upstream*, který je vyhrazený název, který označuje služby IoT Hub. Druhá trasa sensorToSampleModule, přijímá zprávy přicházející z modulu tempSensor a směruje je do *vstup1* vstupní fronty, které jste viděli v kódu SampleModule inicializovat. 

   ![Projděte si v deployment.template.json trasy](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Vytváření a nasdílení změn vašeho řešení

Můžete si kód modulu a šablonu nasazení některé koncepce klíče nasazení. Teď budete připraveni k sestavení image kontejneru SampleModule a nasdílejte ji do vašeho registru kontejneru. Pomocí rozšíření IoT tools pro Visual Studio Code tento krok také vygeneruje manifest nasazení na základě informací v souboru šablony a informace o modulu ze souborů řešení. 

### <a name="sign-in-to-docker"></a>Přihlaste se k Dockeru

Zadejte svůj kontejner přihlašovacích údajů registru Dockeru tak, aby ji nasdílet image kontejnerů mají být uloženy v registru. 

1. Integrovaný terminál aplikace Visual Studio Code otevřete výběrem **zobrazení** > **terminálu**.

2. Přihlaste se k Dockeru pomocí přihlašovacích údajů registru kontejnerů Azure, které jste si uložili po vytvoření registru. 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití `--password-stdin`. Tento osvědčený postup doporučuje pro produkční scénáře, je mimo rámec tohoto návodu. Další informace najdete v tématu [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odkaz.

### <a name="build-and-push"></a>Vytváření a nasdílení změn 

Visual Studio Code teď má přístup do vašeho registru kontejneru, tak, aby byl čas, chcete-li kód řešení do image kontejneru. 

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem myši **deployment.template.json** a vyberte možnost **sestavení a Push řešení IoT Edge**. 

   ![Vytváření a nasdílení změn moduly IoT Edge](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Sestavení a odeslání příkaz spustí tři operace. Nejprve vytvoří novou složku řešení, nazývá **config** , který obsahuje soubory manifestu, sestavení si informace při nasazení šablony a dalších řešení úplné nasazení. Za druhé, poběží `docker build` k sestavení image kontejneru, který je založen na příslušný soubor dockerfile pro cílové architektury. Pak spustí `docker push` tak, aby nabízel úložiště imagí do vašeho registru kontejneru. 

   Tento proces může trvat několik minut, než poprvé, ale je rychlejší při příštím spuštění příkazů. 

2. Otevřít **deployment.amd64.json** souboru ve složce nově vytvořený config. Název souboru odráží Cílová architektura, proto se být různé, pokud jste zvolili jinou architekturu.

3. Všimněte si, že jsou tyto dva parametry, které měly zástupné symboly nyní vyplní jejich odpovídajícími hodnotami. **RegistryCredentials** oddíl má registru jména a hesla získaných ze souboru .env. **SampleModule** má úplnou bitovou kopii úložiště se značkou název, verzi a architekturu ze souboru module.json. 

4. Otevřít **module.json** souboru ve složce SampleModule. 

5. Změňte číslo verze bitové kopie modulu. (Verze, ne $schema – verze.) Například zvýší číslo verze opravy na **0.0.2** jakoby jsme měli provedení malé opravy v modulu kódu. 

   >[!TIP]
   >Verze modulu povolit správu verzí a využijete k otestování změn na malou sadu zařízení před nasazením aktualizací do produkčního prostředí. Pokud není zvýšení verze modulu před vytváření a odesílání, přepíšete úložiště do vašeho registru kontejneru. 

6. Uložte provedené změny do souboru module.json.

7. Klikněte pravým tlačítkem myši **deployment.template.json** soubor znovu a znovu vyberte **sestavení a Push řešení IoT Edge**.

8. Otevřít **deployment.amd64.json** soubor znovu. Všimněte si, že nebyl vytvořen nový soubor, když jste spustili příkaz sestavení a odeslání. Místo toho stejného souboru byla aktualizována tak, aby odrážely změny. Obrázek SampleModule nyní odkazovalo 0.0.2 verzi kontejneru. 

9. Dále ověřte, co sestavení a odeslání příkaz provedl, přejděte na [webu Azure portal](https://portal.azure.com) a přejděte do vašeho registru kontejneru. 

10. Do vašeho registru kontejneru, vyberte **úložišť** pak **samplemodule**. Ověřte, že obě verze bitové kopie byly nahrány do registru.

   ![Zobrazit obě verze image do registru kontejneru](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na chyby při vytváření a odesílání bitové kopie modulu, často je provést v konfiguraci Dockeru na vývojovém počítači. Zkontrolujte konfiguraci pomocí následující kontroly: 

* Narazili jste `docker login` příkaz pomocí přihlašovacích údajů, které jste zkopírovali ze služby container registry? Tyto přihlašovací údaje se liší od těch, které používáte k přihlášení do Azure. 
* Správnost vašeho kontejneru úložiště? Nemá název vašeho registru správný kontejner a název vaší správný modul? Otevřít **module.json** souboru ve složce SampleModule ke kontrole. Hodnota úložiště by měl vypadat jako  **\<název registru\>.azurecr.io/samplemodule**. 
* Pokud jste použili jiný název než **SampleModule** modul, je tento název konzistentní v rámci řešení?
* Je váš počítač používá stejný typ kontejnerů, které vytváříte? V tomto kurzu je určená pro Linux IoT Edge, tak by mělo být uvedeno Visual Studio Code **amd64** nebo **arm32v7** v postranní panel a Docker Desktop by měla být spuštěná kontejnery Linuxu. C moduly ve službě Visual Studio Code nepodporují kontejnery Windows. 

## <a name="deploy-modules-to-device"></a>Do zařízení nasadit moduly

Jste ověřili, že Image kontejneru sestavené jsou uloženy v vašeho registru kontejneru, tak, aby byl čas je nasadit do zařízení. Ujistěte se, že zařízení IoT Edge je zprovozněný. 

1. V Průzkumníku Visual Studio Code rozbalte část zařízení Azure IoT Hub. 

2. Klikněte pravým tlačítkem na zařízení IoT Edge, které chcete nasadit na a pak vyberte **vytvoření nasazení pro jedno zařízení**. 

   ![Vytvoření nasazení pro jedno zařízení](./media/tutorial-develop-for-linux/create-deployment.png)

3. V Průzkumníku souborů přejděte do **config** vyberte složku **deployment.amd64.json** souboru. 

   Nepoužívejte deployment.template.json soubor, který v sobě nemá přihlašovací údaje registru kontejneru nebo hodnoty bitové kopie modulu. Pokud cílíte Linux ARM32 zařízení, budou mít názvy deployment.arm32v7.json manifest nasazení. 

4. Rozbalit podrobnosti pro vaše zařízení IoT Edge a potom rozbalte položku **moduly** seznamu pro vaše zařízení. 

5. Pomocí tlačítka Aktualizovat zobrazení zařízení aktualizovat, dokud se nezobrazí tempSensor a moduly SampleModule běžícího ve vašem zařízení. 

   Může trvat několik minut, než se oba moduly spustit. Modul runtime IoT Edge musí obdržet svůj nový manifest nasazení, stáhněte bitové kopie modulu z modulu runtime kontejneru a pak spusťte každý nový modul. 

   ![Zobrazení modulů běžícího ve vašem zařízení IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Zobrazení zpráv ze zařízení

Kód SampleModule přijímá zprávy do vstupní fronty a předává je podél prostřednictvím jeho výstupní fronty. Manifest nasazení deklaraci trasy, která předán SampleModule zprávy z tempSensor a pak se předávají zprávy z SampleModule do služby IoT Hub. Nástroje Azure IoT pro Visual Studio Code umožňují zobrazit zprávy při jejich doručení na IoT Hub z jednotlivých zařízeních. 

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem na zařízení IoT Edge, který chcete monitorovat a pak vyberte **spustit monitorování zpráv D2C**. 

2. Podívejte se v okně výstupu ve Visual Studio Code, chcete-li zobrazit zprávy přicházející v jiném ve službě IoT hub. 

   ![Zobrazit příchozí zařízení na cloud zprávy](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Zobrazení změn v zařízení

Pokud chcete zobrazit, co se děje ve vašem zařízení samotné, použijte příkazy v této části ke kontrole modul runtime IoT Edge a modulů spuštěných ve vašem zařízení. 

Příkazy v této části jsou určené pro zařízení IoT Edge, ne vývojovém počítači. Pokud používáte virtuální počítač pro vaše zařízení IoT Edge, připojte se k němu nyní. V Azure, přejděte na stránku přehled virtuálního počítače a vyberte **připojit** pro přístup k prostředí pro zabezpečené připojení. 

* Zobrazit všechny moduly nasazené do zařízení a zkontrolovat jejich stav:

   ```bash
   iotedge list
   ```

   Měli byste vidět čtyři moduly: dva moduly runtime IoT Edge, tempSensor a SampleModule. Všechny čtyři by měly být uvedeny jako spuštění.

* Zkontrolujte protokoly pro konkrétní modul:

   ```bash
   iotedge logs <module name>
   ```

   Moduly IoT Edge jsou malá a velká písmena. 

   TempSensor a SamplModule protokoly by měly vykazovat zprávy, které jste zpracování. Modul edgeAgent je zodpovědný za spouštění dalších modulů, aby protokolům bude mít informace o implementaci manifest nasazení. Pokud jakýkoli modul není uvedené nebo není spuštěn, protokoly edgeAgent pravděpodobně chyby. Modul edgeHub zodpovídá za komunikaci mezi moduly a IoT Hub. Pokud moduly nastavené a spuštěné, ale zprávy nejsou přicházejících u služby IoT hub, protokoly edgeHub pravděpodobně chyby. 

## <a name="next-steps"></a>Další postup

V tomto kurzu se nastavit Visual Studio Code na počítači pro vývoj a nasazení prvního modulu IoT Edge z něj. Teď, když znáte základní koncepty, vyzkoušejte přidávání funkcí do modulu tak, aby ho můžete analyzovat data procházejících. Vyberte upřednostňovaný jazyk: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)