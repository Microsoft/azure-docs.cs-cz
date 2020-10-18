---
title: Nastavení vývojového prostředí v systému Linux
description: Nainstalujte modul runtime a sadu SDK a vytvořte místní vývojový cluster v Linuxu. Po dokončení této instalace a nastavení budete moci sestavovat aplikace.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: f8639287ea65347319cb438a5ff6e8c96c8279e1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168405"
---
# <a name="prepare-your-development-environment-on-linux"></a>Příprava vývojového prostředí v Linuxu
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

Pokud chcete nasadit a spustit (Azure Service Fabric aplikace) [Service-Fabric-Application-model.md] na vývojovém počítači se systémem Linux, nainstalujte modul runtime a běžnou sadu SDK. Můžete také nainstalovat volitelné sady SDK pro vývoj v Javě a .NET Core. 

V krocích v tomto článku se předpokládá, že nakonfigurujete nativní instalaci v systému Linux nebo použijete (Service Fabric image kontejneru OneBox) [ https://hub.docker.com/_/microsoft-service-fabric-onebox ], tj. `mcr.microsoft.com/service-fabric/onebox:u18` .

Service Fabric entit hostovaných v cloudu nebo v místním prostředí můžete spravovat pomocí rozhraní příkazového řádku (CLI) Azure Service Fabric. Informace o instalaci rozhraní příkazového řádku najdete v tématu [Nastavení rozhraní příkazového řádku Service Fabric](./service-fabric-cli.md).


## <a name="prerequisites"></a>Předpoklady

Pro vývoj jsou podporovány tyto verze operačních systémů.

* Ubuntu 16,04 ( `Xenial Xerus` ), 18,04 ( `Bionic Beaver` )

    Ujistěte se, že je nainstalován balíček `apt-transport-https`.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (podpora Service Fabric ve verzi Preview)


## <a name="installation-methods"></a>Metody instalace

<!-- markdownlint-disable MD025 -->
<!-- markdownlint-disable MD024 -->

# <a name="ubuntu"></a>[Ubuntu](#tab/sdksetupubuntu)

## <a name="update-your-apt-sources"></a>Aktualizace zdrojů APT
Pokud chcete nainstalovat sadu SDK a přidružený balíček modulu runtime pomocí nástroje pro příkazový řádek apt-get, musíte nejprve aktualizovat zdroje APT (Advanced Packaging Tool).

## <a name="script-installation"></a>Instalace skriptem

Pro usnadnění práce je k dispozici skript pro instalaci Service Fabric runtime a Service Fabric společnou sadu SDK společně s rozhraním příkazového [řádku **sfctl** ](service-fabric-cli.md). Spuštěním skriptu se předpokládá, že souhlasíte s licencemi pro veškerý instalovaný software. Případně můžete spustit kroky [Ruční instalace](#manual-installation) v další části, kde se budou vyskytovat přidružené licence a také součásti, které jsou nainstalovány.

Po úspěšném spuštění skriptu můžete přeskočit k [Nastavení místního clusteru](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

## <a name="manual-installation"></a>Ruční instalace
Pokud chcete modul runtime a běžnou sadu SDK Service Fabric nainstalovat ručně, postupujte dále podle této příručky.

1. Otevřete terminál.

2. Přidejte `dotnet` úložiště do seznamu zdroje, který odpovídá vaší distribuci.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

3. Přidejte nový klíč programu MS Open Tech GNU Privacy Guard (GnuPG nebo GPG) do svého APTového prstence.

    ```bash
    sudo curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | sudo apt-key add -
    ```

4. Přidejte do své klíčenky APT oficiální klíč GPG Dockeru.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

5. Nastavte úložiště Dockeru.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

6. Přidejte klíč JDK Azul do svého správce klíčů APT a nastavte jeho úložiště.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    ```

7. Obnovte seznamy balíčků na základě nově přidaných úložišť.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>Instalace a nastavení sady Service Fabric SDK pro místní cluster

Po aktualizaci vašich zdrojů můžete nainstalovat sadu SDK. Nainstalujte balíček sady Service Fabric SDK, potvrďte instalaci a přijměte licenční smlouvu.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   Následující příkazy automatizují přijetí licence pro balíčky Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

# <a name="red-hat-enterprise-linux-74"></a>[Red Hat Enterprise Linux 7.4](#tab/sdksetuprhel74)

## <a name="update-your-yum-repositories"></a>Aktualizace úložišť Yumu
Chcete-li nainstalovat sadu SDK a přidružený balíček modulu runtime pomocí nástroje příkazového řádku Yumu, je nutné nejprve aktualizovat zdroje balíčků.

## <a name="manual-installation-rhel"></a>Ruční instalace (RHEL)
Pokud chcete modul runtime a běžnou sadu SDK Service Fabric nainstalovat ručně, postupujte dále podle této příručky.

1. Otevřete terminál.
2. Stáhněte a nainstalujte dodatečné balíčky pro Enterprise Linux (EPEL).

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Přidejte do svého systému úložiště balíčků EfficiOS RHEL7.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Naimportujte podpisový klíč balíčku EfficiOS do místní klíčenky GPG.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Přidejte do svého systému úložiště Microsoft RHEL.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster-rhel"></a>Instalace a nastavení sady Service Fabric SDK pro místní cluster (RHEL)

Po aktualizaci vašich zdrojů můžete nainstalovat sadu SDK. Nainstalujte balíček sady Service Fabric SDK, potvrďte instalaci a přijměte licenční smlouvu.

```bash
sudo yum install servicefabricsdkcommon
```

---

## <a name="included-packages"></a>Zahrnuté balíčky
Modul runtime Service Fabric, který je součástí instalace sady SDK, obsahuje balíčky uvedené v následující tabulce. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
**Ubuntu** | 2.0.7 | AzulJDK 1,8 | Implicitně převzato z npm | nejnovější |
**RHEL** | - | OpenJDK 1.8 | Implicitně převzato z npm | nejnovější |

## <a name="set-up-a-local-cluster"></a>Nastavení místního clusteru
1. Spusťte místní Cluster Service Fabric pro vývoj.

# <a name="container-based-local-cluster"></a>[Místní cluster založený na kontejneru](#tab/localclusteroneboxcontainer)

Spusťte [Service Fabric clusteru](https://hub.docker.com/r/microsoft/service-fabric-onebox/), který je založen na kontejneru.

1. Nainstalujte Moby, abyste mohli nasazovat kontejnery Docker.
    ```bash
    sudo apt-get install moby-engine moby-cli -y
    ```
2. Aktualizujte konfiguraci démona Docker na hostiteli s následujícím nastavením a restartujte démona Docker. Podrobnosti: [Povolení podpory protokolu IPv6](https://docs.docker.com/config/daemon/ipv6/)

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```

3. Spusťte cluster.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Ve výchozím nastavení se tím přetáhne image s nejnovější verzí Service Fabric. Konkrétní revize najdete na stránce [Docker Hubu](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

# <a name="local-cluster"></a>[Místní cluster](#tab/localcluster)

Po instalaci sady SDK pomocí výše uvedených kroků spusťte místní cluster.

1. Spusťte instalační skript clusteru.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

---

2. Otevřete webový prohlížeč a přejdete na **Service Fabric Explorer** ( `http://localhost:19080/Explorer` ). Po spuštění clusteru by se měl zobrazit řídicí panel Service Fabric Exploreru. Úplné nastavení clusteru může trvat několik minut. Pokud se v prohlížeči nepodaří otevřít adresu URL nebo pokud se v Service Fabric Exploreru nezobrazí připravený systém, počkejte několik minut a zkuste to znovu.

    ![Service Fabric Explorer v Linuxu][sfx-linux]

    Teď můžete nasadit předem sestavené balíčky aplikací Service Fabric nebo nové balíčky založené na kontejnerech nebo spustitelných souborech hostů. Pokud chcete sestavit nové služby pomocí sad Java SDK nebo .NET Core SDK, postupujte podle pokynů k instalaci uvedených v dalších částech.


> [!NOTE]
> Samostatné clustery nejsou podporované v systému Linux.


> [!TIP]
> Pokud je dostupný disk SSD, pro zajištění špičkového výkonu se doporučuje předat cestu ke složce SSD pomocí `--clusterdataroot` s devclustersetup.sh.

## <a name="set-up-the-service-fabric-cli"></a>Nastavení rozhraní příkazového řádku Service Fabric

[Rozhraní příkazového řádku Service Fabric](service-fabric-cli.md) obsahuje příkazy pro komunikaci s entitami služby Service Fabric, včetně clusterů a aplikací. Při instalaci rozhraní příkazového řádku postupujte podle pokynů v tématu [Service Fabric CLI](service-fabric-cli.md).


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Nastavení generátorů Yeoman pro kontejnery a spustitelné soubory hosta
Service Fabric nabízí nástroje pro generování uživatelského rozhraní, které vám pomohou vytvářet aplikace Service Fabric z terminálu s využitím generátorů šablon Yeoman. Podle těchto pokynů nastavte generátory šablon Service Fabric Yeoman:

1. Nainstalujte si na počítač Node.js a npm.

    ```bash
    sudo add-apt-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. Nainstalujte na svém počítači generátor šablon [Yeoman](https://yeoman.io/) z npm.

    ```bash
    sudo npm install -g yo
    ```
3. Nainstalujte generátory pro kontejnery a spustitelné soubory hosta Service Fabric Yeo z npm.

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

Po nainstalování generátorů vytvořte spustitelné soubory hosta nebo služby kontejneru spuštěním příkazu `yo azuresfguest` nebo `yo azuresfcontainer`.

## <a name="set-up-net-core-31-development"></a>Nastavení vývoje .NET Core 3,1

Pokud chcete začít [vytvářet aplikace Service Fabric C#](service-fabric-create-your-first-linux-application-with-csharp.md), nainstalujte [sadu .NET Core 3,1 SDK pro Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) . Balíčky pro aplikace .NET Core Service Fabric se hostují na NuGet.org.

## <a name="set-up-java-development"></a>Nastavení pro vývoj v Javě

Chcete-li vytvořit služby Service Fabric pomocí jazyka Java, nainstalujte Gradle a spusťte úlohy sestavení. Spusťte následující příkaz pro instalaci Gradle. Knihovny Service Fabric Java se berou z Mavenu.


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7.4 (podpora Service Fabric ve verzi Preview)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

Potřebujete také nainstalovat generátor Service Fabric Yeo pro spustitelné soubory Java. Ověřte, že máte [nainstalovaný Yeoman](#set-up-yeoman-generators-for-containers-and-guest-executables), a potom spusťte následující příkaz:

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Instalace modulu plug-in Eclipse (volitelné)

Modul plug-in Eclipse pro Service Fabric můžete nainstalovat z integrovaného vývojového prostředí Eclipse pro vývojáře v Javě nebo v Javě EE. Mimo aplikací Service Fabric v Javě můžete k vytvoření aplikací spustitelných souborů hosta a aplikací kontejneru Service Fabric použít Eclipse.

> [!IMPORTANT]
> Modul plug-in Service Fabric vyžaduje verzi Eclipse Neon nebo novější. Návod k ověření verze Eclipse najdete v pokynech pod touto poznámkou. Pokud máte nainstalovanou starší verzi Eclipse, můžete si stáhnout novější verzi z [webu Eclipse](https://www.eclipse.org). Instalace přes stávající instalaci Eclipse (její přepsání) se nedoporučuje. Před spuštěním instalačního programu ji odeberte nebo nainstalujte novou verzi do jiného adresáře.
> 
> V Ubuntu doporučujeme provést instalaci přímo z webu Eclipse, a nepoužívat instalační program balíčků (`apt` nebo `apt-get`). Tím zajistíte, že budete mít nejnovější verzi Eclipse. Můžete nainstalovat integrované vývojové prostředí Eclipse pro vývojáře v Javě nebo v Javě EE.

1. V Eclipse se ujistěte, že máte nainstalovanou verzi Eclipse Neon nebo novější a Buildship verze 2.2.1 nebo novější. Verze nainstalovaných komponent zkontrolujete tak, že vyberete **nápovědu**  >  **o**  >  **podrobnostech instalace**na zatmění. Buildship můžete aktualizovat pomocí pokynů v článku [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Moduly plug-in Eclipse pro Gradle).

2. Pokud chcete nainstalovat modul plug-in Service Fabric, vyberte **help**  >  **instalovat nový software**.

3. Do pole **work with (pracovat s** ) zadejte **https: \/ /dl.Microsoft.com/Eclipse**.

4. Vyberte **Přidat**.

    ![Stránka Available Software (Dostupný software)][sf-eclipse-plugin]

5. Vyberte modul plug-in **ServiceFabric** a potom klikněte na **Next** (Další).

6. Proveďte kroky instalace. Potom přijměte licenční smlouvu s koncovým uživatelem.

Pokud už máte modul plug-in Service Fabric Eclipse nainstalovaný, ověřte, že používáte nejnovější verzi. Vyberte **nápovědu**  >  **o**  >  **podrobnostech instalace**zatmění. Pak vyhledejte Service Fabric v seznamu nainstalovaných modulů plug-in. Pokud je k dispozici novější verze, vyberte **aktualizovat** .

Další informace najdete v tématu [Modul plug-in Service Fabric pro vývoj aplikací v Eclipse Javě](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Aktualizace sady SDK a modulu runtime

Pokud chcete aktualizovat sadu SDK a modul runtime na nejnovější verze, spusťte následující příkazy.

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Pokud chcete aktualizovat binární soubory sady Java SDK z Mavenu, je potřeba aktualizovat podrobnosti o verzi příslušného binárního souboru v souboru ``build.gradle`` tak, aby odkazovaly na nejnovější verzi. Abyste přesně zjistili, kde potřebujete aktualizovat verzi, můžete se podívat na jakýkoli soubor ``build.gradle`` v [úvodních příkladech Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Aktualizace balíčků může způsobit zastavení místního vývojového clusteru. Po provedení upgradu restartujte místní cluster podle pokynů v tomto článku.

## <a name="remove-the-sdk"></a>Odebrání sady SDK
Pokud chcete odebrat sady Service Fabric SDK, spusťte následující příkazy.

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4 (podpora Service Fabric ve verzi Preview)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>Další kroky

* [Vytvoření a nasazení první aplikace Service Fabric v Javě v Linuxu pomocí Yeomana](service-fabric-create-your-first-linux-application-with-java.md)
* [Vytvoření a nasazení první aplikace Service Fabric v Javě v Linuxu pomocí modulu plug-in Service Fabric pro Eclipse](service-fabric-get-started-eclipse.md)
* [Vytvoření první aplikace v CSharp v Linuxu](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Příprava vývojového prostředí v OSX](service-fabric-get-started-mac.md)
* [Příprava linuxového vývojového prostředí ve Windows](service-fabric-local-linux-cluster-windows.md)
* [Správa aplikací pomocí rozhraní příkazového řádku Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Rozdíly Service Fabric pro Windows a Linux](service-fabric-linux-windows-differences.md)
* [Začínáme s rozhraním příkazového řádku Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
