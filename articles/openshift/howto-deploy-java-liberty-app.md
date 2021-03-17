---
title: Nasazení aplikace v jazyce Java s otevřeným nástrojem svobody/WebSphere Svoboda v clusteru Azure Red Hat OpenShift 4
description: Nasaďte aplikaci Java s otevřeným a WebSpherem v clusteru Azure Red Hat OpenShift 4.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: Java, jakartaee, JavaEE, mikroprofile, Open-svoboda, WebSphere-svoboda, ARO, OpenShift, Red Hat
ms.openlocfilehash: 08fd3ab112498a983b438d5ba1f1f100816cbf5d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212990"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Nasazení aplikace v jazyce Java s otevřeným nástrojem svobody/WebSphere Svoboda v clusteru Azure Red Hat OpenShift 4

V této příručce se dozvíte, jak spustit aplikaci v jazyce Java, Java EE, [Jakarta EE](https://jakarta.ee/)nebo [mikroprofile](https://microprofile.io/) v otevřeném prostředí svobody/WebSphere svobody a pak nasadit kontejnerové aplikace do clusteru Azure Red Hat OpenShift (ARO) 4 pomocí otevřeného operátoru svoboda. Tento článek vás provede přípravou aplikace, sestavením image Docker aplikace a spuštěním aplikace s podporou kontejnerů v clusteru ARO 4.  Další informace o otevřeném počítači můžete zobrazit [na stránce otevřít a projekt](https://openliberty.io/). Další podrobnosti o IBM WebSphere svobody najdete [na stránce produktu WebSphere svobody](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Požadavky

K úspěšnému procházení tohoto průvodce dokončete následující požadavky.

> [!NOTE]
> Pro vytvoření a spuštění clusteru OpenShift vyžaduje Azure Red Hat OpenShift minimálně 40 jader. Výchozí kvóta prostředků Azure pro nové předplatné Azure nesplňuje tento požadavek. Pokud chcete požádat o zvýšení limitu prostředků, přečtěte si část [standardní kvóta: zvýšení limitů podle řady virtuálních počítačů](../azure-portal/supportability/per-vm-quota-requests.md). Všimněte si, že předplatné bezplatné zkušební verze nemá nárok na zvýšení kvóty, [upgradujte na předplatné](../cost-management-billing/manage/upgrade-azure-subscription.md) s průběžnými platbami a teprve potom si vyžádáte zvýšení kvóty.

1. Připravte místní počítač s nainstalovaným operačním systémem, který má systém UNIX (například Ubuntu, macOS).
1. Nainstalujte implementaci Java SE systémem (například [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
1. Nainstalujte [Maven](https://maven.apache.org/download.cgi) 3.5.0 nebo vyšší.
1. Nainstalujte [Docker](https://docs.docker.com/get-docker/) pro váš operační systém.
1. Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) 2.0.75 nebo novější.
1. Kontrolu a instalaci, [`envsubst`](https://command-not-found.com/envsubst) Pokud není předem nainstalován v operačním systému.
1. Naklonujte kód pro tuto ukázku v místním systému. Ukázka je na [GitHubu](https://github.com/Azure-Samples/open-liberty-on-aro).
1. Postupujte podle pokynů v tématu [Vytvoření clusteru Azure Red Hat OpenShift 4](./tutorial-create-cluster.md).

   I když je krok získat tajný klíč pro získání dat Red Hat označený jako volitelný, **je tento článek povinný**.  Tajný klíč pro vyžádání umožňuje vašemu clusteru Azure Red Hat OpenShift najít otevřený operátor svoboda.

   Pokud máte v úmyslu spouštět aplikace náročné na paměť v clusteru, určete správnou velikost virtuálního počítače pro pracovní uzly pomocí `--worker-vm-size` parametru. Například `Standard_E4s_v3` minimální velikost virtuálního počítače pro instalaci operátoru Elasticsearch v clusteru. Další informace naleznete v tématu:

   * [Rozhraní příkazového řádku Azure pro vytvoření clusteru](/cli/azure/aro#az-aro-create)
   * [Podporované velikosti virtuálních počítačů pro paměťově optimalizované](./support-policies-v4.md#memory-optimized)
   * [Předpoklady pro instalaci operátoru Elasticsearch](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Pomocí postupu v části [připojení k clusteru Azure Red Hat OpenShift 4](./tutorial-connect-cluster.md)se připojte ke clusteru.
   * Nezapomeňte postupovat podle kroků v části "instalace rozhraní CLI OpenShift", protože použijete `oc` příkaz později v tomto článku.
   * Poznamenejte si adresu URL konzoly clusteru, která vypadá nějak takto `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` :.
   * Poznamenejte si `kubeadmin` přihlašovací údaje.

1. Ověřte, že se můžete přihlásit k rozhraní příkazového řádku OpenShift pomocí tokenu pro uživatele `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Povolení integrovaného registru kontejnerů pro OpenShift

Kroky v tomto kurzu vytvoří bitovou kopii Docker, která musí být vložena do registru kontejneru dostupného pro OpenShift. Nejjednodušší možností je použít integrovaný registr poskytovaný nástrojem OpenShift. Pokud chcete povolit integrovaný registr kontejnerů, postupujte podle kroků v části [Konfigurace integrovaného registru kontejnerů pro Azure Red Hat OpenShift 4](built-in-container-registry.md). V tomto článku se používají tři položky z těchto kroků.

* Uživatelské jméno a heslo uživatele Azure AD pro přihlášení ke webové konzole OpenShift
* Výstup `oc whoami` následujících kroků po provedení postupu pro přihlášení k rozhraní příkazového řádku OpenShift  Tato hodnota se nazývá **AAD-User** for diskuze.
* Adresa URL registru kontejneru.

Všimněte si, že tyto položky vycházejí z těchto položek po dokončení kroků pro povolení integrovaného registru kontejnerů.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Vytvoření oboru názvů OpenShift pro aplikaci Java

1. Přihlaste se k webové konzole OpenShift z prohlížeče pomocí `kubeadmin` přihlašovacích údajů.
2. Přejděte do **správy**  >  **obory názvů**  >  **vytvořit obor názvů**.
3. Vyplňte pole `open-liberty-demo` **název** a vyberte **vytvořit**, jak je uvedeno dále.

   ![vytvořit obor názvů](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Vytvořit správce pro ukázkový projekt

Kromě správy imagí bude **uživateli AAD** taky udělené oprávnění správce pro správu prostředků v ukázkovém projektu clusteru ARO 4.  Přihlaste se k rozhraní příkazového řádku OpenShift a udělte **uživateli AAD** potřebná oprávnění pomocí následujících kroků.

1. Přihlaste se k webové konzole OpenShift z prohlížeče pomocí `kubeadmin` přihlašovacích údajů.
1. V pravém horním rohu webové konzoly rozbalte kontextovou nabídku přihlášeného uživatele a pak vyberte **příkaz Kopírovat přihlašovací údaje**.
1. V případě potřeby se přihlaste k novému oknu s kartami stejným uživatelem.
1. Vyberte možnost **Zobrazit token**.
1. Zkopírujte níže uvedenou hodnotu **Přihlásit se pomocí tohoto tokenu** do schránky a spusťte ji v prostředí, jak je znázorněno zde.
1. Spuštěním následujících příkazů udělte `admin` roli v oboru názvů **AAD-uživatel** `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Instalace otevřeného operátoru svobody OpenShift

Po vytvoření a připojení ke clusteru nainstalujte operátor Open.  Hlavní počáteční stránka pro otevřený operátor svoboda je na [GitHubu](https://github.com/OpenLiberty/open-liberty-operator).

1. Přihlaste se k webové konzole OpenShift z prohlížeče pomocí `kubeadmin` přihlašovacích údajů.
2. Přejděte na **operátory**  >  **OperatorHub** a vyhledejte **otevřený operátor svoboda**.
3. Ve výsledcích hledání vyberte **otevřít z operátora** .
4. Vyberte **Nainstalovat**.
5. V místní nabídce **Vytvořit předplatné operátora** ověřte **všechny obory názvů v clusteru (výchozí)** pro **režim instalace**, **beta verzi** **kanálu pro aktualizaci** a **automatickou** **strategii schvalování**:

   ![vytvořit odběr operátoru pro otevření operátoru svoboda](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Vyberte možnost **přihlásit k odběru** a počkejte minutu nebo dvě, dokud nebude zobrazený operátor Open svoboda.
7. Podívejte se na otevřený operátor svoboda se stavem "úspěch".  Pokud to neuděláte, před pokračováním Diagnostikujte a vyřešte problém.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Nainstalované operátory, na kterých je nainstalovaná otevřená osoba.":::

## <a name="prepare-the-liberty-application"></a>Příprava aplikace svobody

Jako náš příklad v této příručce použijeme aplikaci Java EE 8. Open svoboda je server kompatibilní s [úplným profilem Java EE 8](https://javaee.github.io/javaee-spec/javadocs/) , takže může aplikaci snadno spustit.  Open svoboda je také [Jakarta EE 8 úplný profil kompatibilní](https://jakarta.ee/specifications/platform/8/apidocs/).

### <a name="run-the-application-on-open-liberty"></a>Spustit aplikaci na otevřeném počítači

Pokud chcete aplikaci spustit na otevřeném počítači, budete muset vytvořit otevřený konfigurační soubor s právy pro instalaci, aby mohl [modul plug-in Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) zabalit aplikaci pro nasazení. Modul plug-in Maven není nutný k nasazení aplikace do OpenShift.  Budeme ho ale v tomto příkladu používat s režimem Open (dev) pro vývojáře.  Vývojářský režim umožňuje snadno spustit aplikaci místně. Na svém místním počítači proveďte následující kroky.

1. Kopírovat `2-simple/src/main/liberty/config/server.xml` do `1-start/src/main/liberty/config` a přepsat existující soubor s nulovou délkou. Tím se `server.xml` nakonfiguruje Open svoboda Server s funkcemi Java EE.
1. Kopírovat `2-simple/pom.xml` do `1-start/pom.xml` .  Tento krok přidá `liberty-maven-plugin` do pom.
1. Změňte adresář na `1-start` svůj místní klon.
1. Spusťte `mvn clean package` v konzole pro vygenerování balíčku War `javaee-cafe.war` v adresáři `./target` .
1. Spusťte `mvn liberty:dev` si Start Open v režimu pro vývoj.
1. Počkejte, dokud se Server nespustí. Výstup konzoly by měl končit následující zprávou:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Otevřete `http://localhost:9080/` v prohlížeči a přejděte na domovskou stránku aplikace. Aplikace bude vypadat podobně jako na následujícím obrázku:

   ![Webové uživatelské rozhraní JavaEE Cafe](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Stisknutím klávesy **CTRL-C** zastavte aplikaci a otevřete Server svobody.

Adresář `2-simple` vašeho místního klonu zobrazuje projekt Maven s výše uvedenými změnami, které již byly aplikovány.

## <a name="prepare-the-application-image"></a>Příprava image aplikace

Pokud chcete nasadit a spustit vaši aplikaci v rámci clusteru ARO 4, kontejnerizace svou aplikaci jako image Docker pomocí [otevřených imagí kontejnerů](https://github.com/OpenLiberty/ci.docker) nebo [WebSphere oblíbených imagí](https://github.com/WASdev/ci.docker).

### <a name="build-application-image"></a>Sestavit obrázek aplikace

Provedením následujících kroků sestavíte image aplikace:

1. Změňte adresář na `2-simple` svůj místní klon.
2. Spusťte příkaz `mvn clean package` pro zabalení aplikace.
3. Spusťte jeden z následujících příkazů pro sestavení image aplikace.
   * Sestavit s otevřeným základním obrázkem svobody:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Sestavit pomocí WebSphereu svobody Base Image:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Spustit aplikaci místně pomocí Docker

Před nasazením kontejnerové aplikace do vzdáleného clusteru spusťte s místním Docker a ověřte, jestli funguje:

1. Spusťte `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` v konzole nástroje.
2. Počkejte, až se z libovolného serveru spustí a aplikace se úspěšně nasadí.
3. Otevřete `http://localhost:9080/` v prohlížeči a přejděte na domovskou stránku aplikace.
4. Stisknutím **CTRL-C** zastavte aplikaci a k libovolnému serveru.

### <a name="push-the-image-to-the-container-image-registry"></a>Nahrání image do registru imagí kontejneru

Až budete spokojeni se stavem aplikace, nahrajte ho do integrovaného registru imagí kontejneru pomocí následujících pokynů.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Přihlaste se k rozhraní příkazového řádku OpenShift jako uživatel Azure AD.

1. Přihlaste se k webové konzole OpenShift z prohlížeče pomocí přihlašovacích údajů uživatele Azure AD.

   1. K přihlášení do konzoly použijte funkci InPrivate, anonymním nebo jiné podobné funkce okna prohlížeče.
   1. Vybrat **OpenID**

   > [!NOTE]
   > Poznamenejte si uživatelské jméno a heslo, které používáte k přihlášení. Toto uživatelské jméno a heslo budou fungovat jako správce pro další akce v tomto a dalších článcích.
1. Přihlaste se pomocí OpenShift CLI pomocí následujících kroků.  Pro účely diskuze se tento proces nazývá `oc login` .
   1. V pravém horním rohu webové konzoly rozbalte kontextovou nabídku přihlášeného uživatele a pak vyberte **příkaz Kopírovat přihlašovací údaje**.
   1. V případě potřeby se přihlaste k novému oknu s kartami stejným uživatelem.
   1. Vyberte možnost **Zobrazit token**.
   1. Zkopírujte níže uvedenou hodnotu **Přihlásit se pomocí tohoto tokenu** do schránky a spusťte ji v prostředí, jak je znázorněno zde.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>Vložení image kontejneru do registru kontejneru pro OpenShift

Spusťte tyto příkazy a nahrajte image do registru kontejneru pro OpenShift.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

Úspěšný výstup bude vypadat podobně jako v následujícím příkladu.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Dodejte image do integrovaného registru imagí kontejneru pomocí následujícího příkazu.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Nasazení aplikace v clusteru ARO 4

Nyní můžete nasadit ukázkovou aplikaci do clusteru Azure Red Hat OpenShift 4, který jste vytvořili dříve při práci s požadavky.

### <a name="deploy-the-application-from-the-web-console"></a>Nasazení aplikace z webové konzoly

Vzhledem k tomu, že používáme operátor Open svoboda ke správě aplikací svobody, musíme vytvořit instanci své *vlastní definice prostředku* typu "OpenLibertyApplication". Operátor se pak postará o všechny aspekty správy prostředků OpenShift potřebných pro nasazení.

1. Přihlaste se k webové konzole OpenShift z prohlížeče pomocí přihlašovacích údajů uživatele Azure AD.
1. Rozbalte **Domů**, vyberte **projekty**  >  **otevřít-svoboda-demo**.
1. Přejděte na **operátory**  >  **nainstalované** operátory.
1. Uprostřed stránky vyberte **otevřít operátor**.
1. Uprostřed stránky vyberte možnost **otevřít aplikaci svobody**.  Navigace položek v uživatelském rozhraní zrcadlí skutečnou hierarchii obsahující používané technologie.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![Zahrnutí do Java pro ARO Java](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Vyberte **vytvořit OpenLibertyApplication**
1. Nahraďte vygenerovanou YAML svým, který je umístěný na adrese `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Vyberte **Vytvořit**. Vrátíte se do seznamu OpenLibertyApplications.
1. Vyberte **JavaEE-kavárna-jednoduchá**.
1. Uprostřed stránky vyberte **prostředky**.
1. V tabulce vyberte odkaz pro **JavaEE-kavárny – jednoduchý** s **typem** **trasy**.
1. Na stránce, která se otevře, vyberte **odkaz níže.**

V prohlížeči se zobrazí domovská stránka aplikace.

### <a name="delete-the-application-from-the-web-console"></a>Odstranění aplikace z webové konzole

Až budete s aplikací hotovi, postupujte podle těchto kroků a odstraňte aplikaci z otevřeného Shift.

1. V levém navigačním podokně rozbalte položku pro **operátory**.
1. Vyberte **nainstalované operátory**.
1. Vyberte **otevřít operátor svoboda**.
1. Uprostřed stránky vyberte **otevřená aplikace**.
1. Vyberte svislé tečky (tři svislé tečky) a pak vyberte **Odstranit aplikaci OpenLiberty**.

### <a name="deploy-the-application-from-cli"></a>Nasazení aplikace z CLI

Místo používání grafického uživatelského rozhraní webové konzoly můžete aplikaci nasadit z rozhraní příkazového řádku. Pokud jste to ještě neudělali, Stáhněte a nainstalujte `oc` Nástroj příkazového řádku pomocí následujících pokynů k instalaci Red Hat [Začínáme pomocí rozhraní](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)příkazového řádku.

1. Přihlaste se k webové konzole OpenShift z prohlížeče pomocí přihlašovacích údajů uživatele Azure AD.
2. Přihlaste se k rozhraní příkazového řádku OpenShift pomocí tokenu pro uživatele Azure AD.
3. Změňte adresář na `2-simple` svůj místní klon a spusťte následující příkazy, které do clusteru ARO 4 nasadí vaši aplikaci svobody.  Výstup příkazu je také zobrazen jako vložený.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. `1/1`Než budete pokračovat, zkontrolujte, jestli je ve sloupci zobrazená `READY` .  Pokud ne, před pokračováním Prozkoumejte a vyřešte problém.
5. Pomocí příkazu vyhledejte hostitele trasy k aplikaci `oc get route` , jak je znázorněno zde.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Jakmile je aplikace svobody v provozu, otevřete výstup **hostitele trasy** v prohlížeči a přejděte na domovskou stránku aplikace.

### <a name="delete-the-application-from-cli"></a>Odstranění aplikace z CLI

Odstraňte aplikaci z rozhraní příkazového řádku spuštěním tohoto příkazu.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí kroků v tomto kurzu odstraňte cluster ARO [: odstranění clusteru Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Další kroky

V této příručce jste se dozvěděli, jak:
> [!div class="checklist"]
>
> * Příprava aplikace svobody
> * Sestavení image aplikace
> * Spuštění kontejnerových aplikací v clusteru ARO 4 pomocí grafického uživatelského rozhraní a rozhraní příkazového řádku

Další informace můžete získat z odkazů používaných v tomto průvodci:

* [Otevřená svoboda](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Otevřít operátor svoboda](https://github.com/OpenLiberty/open-liberty-operator)
* [Otevřít nastavení serveru svobody](https://openliberty.io/docs/ref/config/)
* [Modul plug-in svobody Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Otevřít soubory z libovolného kontejneru](https://github.com/OpenLiberty/ci.docker)
* [Image kontejnerů WebSphere svobody](https://github.com/WASdev/ci.docker)