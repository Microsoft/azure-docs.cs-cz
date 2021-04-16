---
title: Nasazení aplikace do Azure Red Hat OpenShift pomocí OpenShift bez serveru
description: Naučte se, jak nasadit aplikaci do Azure Red Hat OpenShift pomocí OpenShift bez serveru.
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: ARO, OpenShift, Red Hat, bez serveru
ms.openlocfilehash: 6db87e752745e3df919c93b2ffc8144e5886b319
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532814"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>Nasazení aplikace do Azure Red Hat OpenShift pomocí OpenShift bez serveru

V tomto článku nasadíte aplikaci do clusteru Azure Red Hat OpenShift s využitím bez [serveru OpenShift](https://www.openshift.com/learn/topics/serverless). OpenShift bez serveru pomáhá vývojářům nasazovat a spouštět aplikace, které budou škálovat nebo škálovat na nulu na vyžádání a eliminují spotřebu prostředků, pokud se nepoužívají.

Kód aplikace lze zabalit do kontejneru spolu s příslušnými modulem runtime a funkce bez serveru spustí kontejnery aplikace, když jsou aktivovány událostí. Aplikace můžou být spouštěny různými zdroji událostí, jako jsou například události z vašich vlastních aplikací, cloudové služby od různých poskytovatelů, systémy SaaS (software jako služba) a další služby.

Správa všech aspektů nasazení jakéhokoli kontejneru v rámci serveru bez serveru je integrovaná přímo do rozhraní OpenShift. Vývojáři můžou vizuálně určit, které události řídí spuštění svých kontejnerových aplikací, a několik způsobů, jak parametry událostí upravovat. OpenShift aplikace bez serveru se dají integrovat s jinými OpenShift službami, jako jsou kanály OpenShift, sítě a monitorování, a dodávat kompletní prostředí pro vývoj a nasazení aplikací bez serveru.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>Instalace nástroje příkazového řádku Knative (KN)

Můžete si stáhnout nejnovější verzi rozhraní příkazového řádku, která je vhodná pro váš počítač. <https://github.com/knative/client/releases/>

Pokud spouštíte příkazy na Azure Cloud Shell, Stáhněte si nejnovější rozhraní příkazového řádku (Knative CLI) pro Linux.

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>Spustit webovou konzolu

Najděte adresu URL webové konzoly clusteru spuštěním:

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

Měla by se zobrazit adresa URL podobná této.

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

V prohlížeči spusťte adresu URL konzoly a přihlaste se pomocí `kubeadmin` přihlašovacích údajů.

:::image type="content" source="media/login.png" alt-text="Přihlašovací obrazovka Azure Red Hat OpenShift":::

## <a name="install-the-openshift-serverless-operator"></a>Instalace OpenShift operátor bez serveru

Až budete přihlášeni ke webové konzole OpenShift, ujistěte se, že jste v perspektivě pro *správu* . Otevřete *centrum operátorů* a vyhledejte operátor bez **serveru OpenShift** a vyberte ho.

![Vyhledat OpenShift operátor bez serveru](media/serverless/serverless-operatorhub.png)

Pak otevřete stránku instalace operátor kliknutím na **instalovat**.

![Kliknutím na Nainstalovat nainstalujte operátor.](media/serverless/serverless-clickinstall.png)

Vyberte odpovídající *kanál pro aktualizaci* verze clusteru Azure Red Hat OpenShift a nainstalujte do `openshift-serverless` oboru názvů operátor. Přejděte dolů a klikněte na **nainstalovat**.

![Stránka instalace operátoru](media/serverless/serverless-installpage.png)

Během několika minut by se stránka stavu měla projevit, že je operátor nainstalovaný a je připravený k použití. Pokračujte kliknutím na tlačítko **Zobrazit operátor** .

![Operátor je nainstalován a je připraven k použití.](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>Instalace Knative obsluhující

Možnost spouštět jakýkoli kontejner bez serveru na OpenShift bez serveru je dostupná pomocí nadřazeného Knative. Knative rozšiřuje Kubernetes, aby poskytovala sadu komponent pro nasazení, spouštění a správu moderních aplikací s využitím metodologie bez serveru.

### <a name="create-an-instance-of-the-knative-serving"></a>Vytvoření instance služby Knative obsluhující

Přepněte na `knative-serving` obor názvů tak, že kliknete na rozevírací seznam projekt v levém horním rohu a potom v části *zadaná rozhraní API* kliknete na možnost **vytvořit instanci** na kartě *Knative obsluhující* .

![Kliknutím vytvoříte instanci služby Knative.](media/serverless/serverless-createknativeserving.png)

Ponechte výchozí nastavení a posuňte se dolů na stránku *vytvořit Knative obsluhující* a klikněte na tlačítko **vytvořit** .

![Ponechte výchozí nastavení a klikněte na vytvořit.](media/serverless/serverless-createknativeserving2.png)

Počkejte, dokud se ve sloupci *stav* **nebude zobrazovat OpenShift** bez serveru a Vy jste připraveni vytvořit OpenShift projekt bez serveru.

![Knative obsluhující](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>Vytvoření projektu bez serveru

Chcete-li vytvořit nový projekt s názvem `demoserverless` , spusťte příkaz:

```azurecli-interactive
oc new-project demoserverless
```

Měl by se zobrazit výstup podobný tomuto:

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

Přepněte do perspektivy pro *vývojáře* místo v perspektivě *správce* v levé nabídce a vyberte `demoserverless` v seznamu projektů. Pak byste měli být na stránce *topologie* projektu.

![Topologie projektů Azure Red Hat OpenShift](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>Nasazení pomocí webové konzoly

Z možností zobrazených pro nasazení aplikace vyberte *z Gitu*. Tím se vám bude na stránce *Import ze sady Git* nakládat. Použijte `https://github.com/sclorg/django-ex.git` jako **adresu URL úložiště Git**. Ukázková webová aplikace je implementována pomocí programovacího jazyka Python.

![Projekt Azure Red Hat OpenShift z Gitu](media/serverless/serverless-from-git.png)

> [!NOTE]
> OpenShift zjistí, že se jedná o projekt Pythonu a vybere odpovídající obrázek tvůrce.

Přejděte dolů na *prostředky* a ujistěte se, že je vybraná **Služba Knative** jako typ prostředku, který se má vygenerovat. Tato akce vytvoří službu Knative, což je typ nasazení, který umožňuje OpenShift škálování bez serveru na hodnotu nula, pokud je nečinné.

![Projekt Azure Red Hat OpenShift – Knative](media/serverless/serverless-knative.png)


Až budete připraveni, v dolní části stránky klikněte na **vytvořit**. Tím se vytvoří prostředky pro správu sestavení a nasazení aplikace. Pak budete přesměrováni na přehled topologie projektu.

Přehled topologie poskytuje vizuální reprezentaci aplikace, kterou jste nasadili. V tomto zobrazení uvidíte celkovou strukturu aplikace.

Počkejte na dokončení sestavení. Může to trvat několik minut. Po dokončení sestavení se v levém dolním rohu služby zobrazí zelené zaškrtnutí.

![Topologie projektů Azure Red Hat OpenShift – sestavení bylo úspěšné.](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>Podívejte se na měřítko vaší aplikace

V seznamu *Možnosti zobrazení* v horní části zobrazení topologie klikněte na *počet*. Počkejte, než se počet pod dostupně škáluje na nulu. Horizontální zmenšování může trvat několik minut.

![Topologie projektů Azure Red Hat OpenShift – škálované na nulu](media/serverless/serverless-scaledtozero.png)

Klikněte na ikonu *otevřít adresu URL* v pravém horním rohu panelu služby Knative. Aplikace se otevře na nové kartě. Zavřete novou kartu prohlížeče a vraťte se do zobrazení topologie. V zobrazení topologie vidíte, že vaše aplikace se škáluje až na jednu pod, aby vyhovovala vaší žádosti. Po několika minutách se vaše aplikace škáluje zpátky na nuly.

![Topologie projektu Azure Red Hat OpenShift – škálovaná na jednu](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>Vynucení nové revize a nastavení distribuce provozu

Při každé aktualizaci konfigurace služby je vytvořena nová revize služby. Ve výchozím nastavení trasuje služba všechny přenosy do nejnovější připravené revize. Toto chování můžete změnit tak, že definujete, která revize získá část provozu. Služba Knative Services povoluje mapování provozu, což znamená, že revize služby může být namapována na přidělenou část provozu. Mapování přenosů nabízí také možnost vytvořit jedinečné adresy URL pro konkrétní revize.

V *topologii* kliknutím na revizi v rámci služby zobrazíte její podrobnosti. Označení pod kroužkem pod a v horní části panelu podrobností by měla být `(REV)` . Na bočním panelu přejděte na kartě *prostředky* dolů a klikněte na konfiguraci přidruženou k vaší službě.

![Topologie projektu Azure Red Hat OpenShift – Revize](media/serverless/serverless-revdetails.png)

Vynutit aktualizaci konfigurace přepnutím na kartu *YAML* a posunutím dolů upravíte hodnotu `timeoutSeconds` `301` . Kliknutím na **Uložit** uložte aktualizovanou konfiguraci. Ve scénáři reálného světa se může tato aktualizace konfigurace aktivovat taky aktualizací značky image kontejneru.

![Vynutit novou revizi aktualizací konfigurace](media/serverless/serverless-confupdate.png)

Vraťte se do zobrazení *topologie* , uvidíte, že byla nasazena Nová revize. Klikněte na službu končící označením `(KSVC)` a pak klikněte na tlačítko **nastavit distribuci provozu** . Nyní byste měli být schopni rozdělit přenosy mezi různými revizemi služby.

![Nastavení distribuce provozu](media/serverless/serverless-trafficdist.png)

V zobrazení *topologie* se teď ukáže, jak se mezi těmito dvěma revizemi distribuuje provoz.

![Kontrola distribuce provozu](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>Použití nástroje příkazového řádku Knative (KN)

V předchozích krocích jste pomocí webové konzoly OpenShift vytvořili a nasadili aplikaci pro OpenShift bez serveru. Vzhledem k tomu, že OpenShift bez serveru běží Knative pod, můžete k vytváření služeb Knative použít taky nástroj příkazového řádku Knative (KN).

> [!NOTE]
> Pokud ještě nemáte nainstalované rozhraní `kn` příkazového řádku, nezapomeňte postupovat podle kroků v části požadavky tohoto článku. Také se ujistěte, že jste přihlášeni pomocí nástroje příkazového řádku OpenShift `oc` .

Budeme používat image kontejneru, která je už postavená na `quay.io/rhdevelopers/knative-tutorial-greeter` .

### <a name="deploy-a-service"></a>Nasazení služby

Chcete-li nasadit službu, spusťte následující příkaz:

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

Zobrazí se výstup podobný následujícímu.

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Seznam tras v projektu můžete načíst spuštěním:

```azurecli-interactive
kn route list
```

Vrátíte se zpátky seznam tras v oboru názvů. Otevřete adresu URL v prohlížeči a zobrazte nasazenou službu.

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>Nasazení nové verze služby

Nasazení nové verze aplikace spuštěním příkazu níže, předáním `:latest` značky image a proměnné prostředí `MESSAGE_PREFIX` :

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

Vrátíte se s potvrzením, že byla `greeter-v2` nasazena Nová revize.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Pokud chcete zobrazit seznam všech revizí a jejich distribuce provozu, spusťte následující příkaz:

```azurecli-interactive
kn revision list
```

Zobrazí se seznam podobný následujícímu. Všimněte si, že nová revize získává 100% provozu.

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>Nasazení na modré/zelené a Kanárské

Ve výchozím nastavení se při nasazení nové revize dostane 100% provozu. Řekněme, že chcete implementovat strategii pro nasazení modrou a zelenou, kde se můžete rychle vrátit zpátky na starší verzi aplikace. Knative to usnadňuje.

Aktualizujte službu, aby se vytvořily tři značky provozu při posílání 100% provozu do

- **aktuální**: body v aktuální nasazené verzi
- **Předchozí**: body v předchozí verzi
- **nejnovější**: vždy odkazuje na nejnovější verzi

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

Zobrazí se potvrzení podobná následujícímu.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Uveďte trasy pomocí příkazu níže:

```azurecli-interactive
kn route describe greeter
```

Zobrazí se výstup s adresami URL pro každou značku spolu s distribucí provozu.

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Řekněme, že chcete rychle vrátit zpět na předchozí verzi, můžete aktualizovat distribuci provozu a odeslat 100% provozu do předchozí značky:

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

Znovu se podívejte na seznam tras pomocí následujícího příkazu:

```azurecli-interactive
kn route describe greeter
```

Zobrazí se výstup, který ukazuje 100% distribuce provozu, který se bude nakládat na předchozí verzi.

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Při aktualizaci hlavní trasy ( `http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io` v tomto případě) v prohlížeči se přehrajte s distribucí provozu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s aplikací hotoví, můžete spuštěním následujícího příkazu projekt odstranit:

```azurecli-interactive
oc delete project demoserverless
```

Cluster můžete také odstranit podle pokynů v tomto [kurzu: odstranění clusteru Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V této příručce jste se dozvěděli, jak:
> [!div class="checklist"]
>
> * Instalace OpenShift a Knative obsluhy bez serveru
> * Nasazení projektu bez serveru pomocí webové konzoly
> * Nasazení projektu bez serveru s použitím rozhraní příkazového řádku Knative (KN)
> * Konfigurace modrých a zelených nasazení a Kanárských nasazení pomocí rozhraní příkazového řádku Knative (KN)

Další informace o tom, jak vytvářet a nasazovat aplikace založené na událostech na Azure Red Hat OpenShift pomocí [OpenShift bez serveru](https://www.openshift.com/learn/topics/serverless), najdete v dokumentaci [Začínáme s OpenShift bez serveru](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html) a v dokumentaci k [vytváření a správě aplikací bez serveru](https://docs.openshift.com/container-platform/4.6/serverless/serving-creating-managing-apps.html) .
