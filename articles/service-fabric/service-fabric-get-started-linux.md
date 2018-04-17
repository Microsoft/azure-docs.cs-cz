---
title: Nastavení vývojového prostředí v Linuxu | Dokumentace Microsoftu
description: Nainstalujte modul runtime a sadu SDK a vytvořte místní vývojový cluster v Linuxu. Po dokončení této instalace a nastavení budete moci sestavovat aplikace.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 804bc3f3708a6b5e70c91d68f954ebc10c477831
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="prepare-your-development-environment-on-linux"></a>Příprava vývojového prostředí v Linuxu
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Pokud chcete sestavovat a spouštět [aplikace Azure Service Fabric](service-fabric-application-model.md) na vývojovém počítači s Linuxem, musíte nainstalovat modul runtime a běžnou sadu SDK. Můžete také nainstalovat volitelné sady SDK pro vývoj v Javě a .NET Core. 

Kroky v tomto článku předpokládají, že provádíte nativní instalaci v Linuxu nebo používáte image kontejneru Service Fabric OneBox `microsoft/service-fabric-onebox`. 

Instalace sady SDK a modulu runtime Service Fabric v subsystému Windows pro Linux se nepodporuje. Podporuje se však rozhraní příkazového řádku Azure Service Fabric, které umožňuje správu entit Service Fabric hostovaných jinde v clusteru nebo místním prostředí. Informace o instalaci rozhraní příkazového řádku najdete v tématu [Nastavení rozhraní příkazového řádku Service Fabric](./service-fabric-cli.md).


## <a name="prerequisites"></a>Požadavky

* Pro vývoj jsou podporovány tyto verze operačních systémů:

    * Ubuntu 16.04 (`Xenial Xerus`)

* Ujistěte se, že je nainstalovaný balíček `apt-transport-https`:

      ```bash
      sudo apt-get install apt-transport-https
      ```


## <a name="installation-methods"></a>Metody instalace

### <a name="1-script-installation"></a>1. Instalace skriptem

Společně s rozhraním příkazového řádku **sfctl** je poskytován skript pro usnadnění instalace modulu runtime Service Fabric a běžné sady SDK Service Fabric. Proveďte postup ruční instalace v další části a zjistěte, co se instaluje a se kterými licencemi se vyjadřuje souhlas. Spuštěním skriptu se předpokládá, že souhlasíte s licencemi pro veškerý instalovaný software. 

Po úspěšném provedení skriptu můžete přeskočit přímo k [Nastavení místního clusteru](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="2-manual-installation"></a>2. Ruční instalace
Pokud chcete modul runtime a běžnou sadu SDK Service Fabric nainstalovat ručně, postupujte dále podle této příručky.

## <a name="update-your-apt-sources"></a>Aktualizace zdrojů APT
Pokud chcete nainstalovat sadu SDK a přidružený balíček modulu runtime pomocí nástroje pro příkazový řádek apt-get, musíte nejprve aktualizovat zdroje APT (Advanced Packaging Tool).

1. Otevřete terminál.
2. Přidejte do seznamu zdrojů úložiště Service Fabric.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Přidejte do seznamu zdrojů úložiště `dotnet`.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Přidejte do své klíčenky APT nový klíč GPG (Gnu Privacy Guard neboli GnuPG).

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Přidejte do své klíčenky APT oficiální klíč GPG Dockeru.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Nastavte úložiště Dockeru.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Obnovte seznamy balíčků na základě nově přidaných úložišť.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-local-cluster-setup"></a>Instalace a nastavení sady Service Fabric SDK pro nastavení místního clusteru

Po aktualizaci vašich zdrojů můžete nainstalovat sadu SDK. Nainstalujte balíček sady Service Fabric SDK, potvrďte instalaci a vyjádřete souhlas s licenční smlouvou.

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   Následující příkazy automatizují přijetí licence pro balíčky Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

Modul runtime Service Fabric, který je součástí uvedené instalace, obsahuje balíčky uvedené v následující tabulce. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
Ubuntu | 2.0.0 | OpenJDK 1.8 | Implicitně převzato z npm | nejnovější |

## <a name="set-up-a-local-cluster"></a>Nastavení místního clusteru
  Po dokončení instalace byste měli být schopni spustit místní cluster.

  1. Spusťte instalační skript clusteru.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Otevřete webový prohlížeč a přejděte do [Service Fabric Exploreru](http://localhost:19080/Explorer). Pokud se cluster spustil, měl by se zobrazit řídicí panel Service Fabric Exploreru.

      ![Service Fabric Explorer v Linuxu][sfx-linux]

  V tuto chvíli můžete nasadit předem sestavené balíčky aplikací Service Fabric nebo nové balíčky založené na kontejnerech nebo spustitelných souborech hostů. Pokud chcete sestavit nové služby pomocí sad Java SDK nebo .NET Core SDK, postupujte podle pokynů k instalaci uvedených v dalších částech.


  > [!NOTE]
  > Samostatné clustery nejsou podporované v systému Linux.
  >


>   [!TIP]
    Pokud je dostupný disk SSD, pro zajištění špičkového výkonu se doporučuje předat cestu ke složce SSD pomocí `--clusterdataroot` s devclustersetup.sh.

## <a name="set-up-the-service-fabric-cli"></a>Nastavení rozhraní příkazového řádku Service Fabric

[Rozhraní příkazového řádku Service Fabric](service-fabric-cli.md) obsahuje příkazy pro komunikaci s entitami služby Service Fabric, včetně clusterů a aplikací.
Pokud chcete nainstalovat rozhraní příkazového řádku, postupujte podle pokynů v tématu [Service Fabric CLI](service-fabric-cli.md).


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Nastavení generátorů Yeoman pro kontejnery a spustitelné soubory hosta
Service Fabric nabízí nástroje pro generování uživatelského rozhraní, které vám pomohou vytvářet aplikace Service Fabric z terminálu s využitím generátorů šablon Yeoman. Podle těchto pokynů nastavte generátory šablon Service Fabric Yeoman:

1. Instalace nodejs a NPM na počítači

Ubuntu
  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```

2. Instalace generátoru šablon [Yeoman](http://yeoman.io/) na počítač z NPM

  ```bash
  sudo npm install -g yo
  ```
3. Instalace generátoru pro kontejnery a spustitelné soubory hosta Service Fabric Yeo z NPM

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

Po nainstalování generátorů byste měli být schopni vytvářet spustitelné soubory hosta nebo služby kontejneru spuštěním příkazu `yo azuresfguest` nebo `yo azuresfcontainer`.

## <a name="set-up-net-core-20-development"></a>Nastavení pro vývoj v .NET Core 2.0

Pokud chcete začít [vytvářet aplikace Service Fabric v jazyce C#](service-fabric-create-your-first-linux-application-with-csharp.md), nainstalujte sadu [.NET Core 2.0 SDK pro Ubuntu](https://www.microsoft.com/net/core#linuxubuntu). Balíčky pro aplikace Service Fabric v .NET Core 2.0 jsou hostované na NuGet.org a aktuálně ve verzi Preview.

## <a name="set-up-java-development"></a>Nastavení pro vývoj v Javě

Pokud chcete vytvářet služby Service Fabric pomocí Javy, nainstalujte sadu JDK 1.8 a Gradle pro spouštění úloh sestavení. Následující fragment kódu nainstaluje otevřenou sadu JDK 1.8 společně s Gradlem. Knihovny Service Fabric Java se berou z Mavenu.

 ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-plug-in-optional"></a>Instalace modulu plug-in Eclipse (volitelné)

Modul plug-in Eclipse pro Service Fabric můžete nainstalovat z integrovaného vývojového prostředí Eclipse pro vývojáře v Javě. Mimo aplikací Service Fabric v Javě můžete k vytvoření aplikací spustitelných souborů hosta a aplikací kontejneru Service Fabric použít Eclipse.

> [!IMPORTANT]
> Modul plug-in Service Fabric vyžaduje verzi Eclipse Neon nebo novější. Návod k ověření verze Eclipse najdete v pokynech pod touto poznámkou. Pokud máte nainstalovanou starší verzi Eclipse, můžete si stáhnout novější verzi z [webu Eclipse](https://www.eclipse.org). Instalace přes stávající instalaci Eclipse (její přepsání) se nedoporučuje. Před spuštěním instalačního programu ji můžete odebrat nebo můžete novou verzi nainstalovat do jiného adresáře. 
> 
> V Ubuntu doporučujeme provést instalaci přímo z webu Eclipse, a nepoužívat instalační program balíčků (`apt` nebo `apt-get`). Tím zajistíte, že budete mít nejnovější verzi Eclipse. 

1. V Eclipse se ujistěte, že používáte verzi Eclipse Neon nebo novější a že máte nainstalovanou nejnovější verzi Buildship (1.0.17 nebo novější). Verze nainstalovaných komponent můžete zkontrolovat tak, že vyberete **Help** (Nápověda) > **Installation Details** (Podrobnosti o instalaci). Buildship můžete aktualizovat pomocí pokynů v článku [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Moduly plug-in Eclipse pro Gradle).

2. Pokud chcete nainstalovat modul plug-in Service Fabric, vyberte **Help** (Nápověda) > **Install New Software** (Instalace nového softwaru).

3. Do pole **Work with** (Pracovat s) zadejte **http://dl.microsoft.com/eclipse**.

4. Klikněte na tlačítko **Add** (Přidat).

    ![Stránka Available Software (Dostupný software)][sf-eclipse-plugin]

5. Vyberte modul plug-in **ServiceFabric** a potom klikněte na **Next** (Další).

6. Dokončete instalaci a potom vyjádřete souhlas s Licenční smlouvou s koncovým uživatelem.

Pokud už máte modul plug-in Service Fabric Eclipse nainstalovaný, ověřte, že používáte nejnovější verzi. Kontrolu můžete provést výběrem **Help** (Nápověda) > **Installation Details** (Podrobnosti o instalaci) a vyhledáním Service Fabric v seznamu nainstalovaných modulů plug-in. Pokud je k dispozici novější verze, vyberte **Update** (Aktualizovat).

Další informace najdete v tématu [Modul plug-in Service Fabric pro vývoj aplikací v Eclipse Javě](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Aktualizace sady SDK a modulu runtime

Pokud chcete aktualizovat sadu SDK a modul runtime na nejnovější verze, spusťte následující příkazy:

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Pokud chcete aktualizovat binární soubory sady Java SDK z Mavenu, je potřeba aktualizovat podrobnosti o verzi příslušného binárního souboru v souboru ``build.gradle`` tak, aby odkazovaly na nejnovější verzi. Abyste přesně zjistili, kde potřebujete aktualizovat verzi, můžete se podívat na jakýkoli soubor ``build.gradle`` v úvodních příkladech Service Fabric, které jsou uvedené [tady](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Aktualizace balíčků může způsobit zastavení místního vývojového clusteru. Po provedení upgradu restartujte místní cluster podle pokynů na této stránce.

## <a name="remove-the-sdk"></a>Odebrání sady SDK
Pokud chcete odebrat sady Service Fabric SDK, spusťte následující příkazy:

```bash
sudo apt-get remove servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
sudo apt-get install -f
```

## <a name="next-steps"></a>Další kroky

* [Vytvoření a nasazení první aplikace Service Fabric v Javě v Linuxu pomocí Yeomana](service-fabric-create-your-first-linux-application-with-java.md)
* [Vytvoření a nasazení první aplikace Service Fabric v Javě v Linuxu pomocí modulu plug-in Service Fabric pro Eclipse](service-fabric-get-started-eclipse.md)
* [Vytvoření první aplikace v CSharp v Linuxu](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Příprava vývojového prostředí v OSX](service-fabric-get-started-mac.md)
* [Příprava linuxového vývojového prostředí ve Windows](service-fabric-local-linux-cluster-windows.md)
* [Správa aplikací pomocí Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
* [Rozdíly Service Fabric pro Windows a Linux](service-fabric-linux-windows-differences.md)
* [Automatizace oprav operačního systému v clusteru s Linuxem](service-fabric-patch-orchestration-application-linux.md)
* [Začínáme s rozhraním příkazového řádku Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
