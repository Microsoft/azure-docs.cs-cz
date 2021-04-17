---
title: Nasazení aplikace ze zdrojového kódu do Azure Red Hat OpenShift
description: Naučte se, jak nasadit aplikaci do Azure Red Hat OpenShift ze zdrojového kódu pomocí strategie sestavení source to-Image (S2I).
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: ARO, OpenShift, Red Hat, s2i, source to Image
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558993"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>Nasazení aplikace ze zdrojového kódu do Azure Red Hat OpenShift

V tomto článku nasadíte aplikaci do clusteru Azure Red Hat OpenShift ze zdrojového kódu pomocí sestavení S2I (Source-to-Image). [Source-to-Image (S2I)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds) je proces sestavení pro vytváření reprodukovatelné image kontejneru ze zdrojového kódu. S2I vytváří image připravené k spuštění vložením zdrojového kódu do image kontejneru a umožněním kontejneru připravit tento zdrojový kód na provedení. Můžete mít OpenShift sestavení aplikace ze zdroje, abyste ho mohli nasadit, takže nemusíte vytvářet kontejner ručně při každé změně. OpenShift může pak automaticky vytvářet a nasazovat nové verze při upozornění na změny zdrojového kódu.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>Vytvoření projektu

Chcete-li vytvořit nový projekt s názvem `demoproject` , spusťte příkaz:

```azurecli-interactive
oc new-project demoproject
```

Měl by se zobrazit výstup podobný tomuto:

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>Spustit webovou konzolu

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

Přepněte do perspektivy pro *vývojáře* místo v perspektivě *správce* v levé nabídce a vyberte `demoproject` v seznamu projektů. Pak byste měli být na stránce *topologie* projektu.

:::image type="content" source="media/s2i/project-topology.png" alt-text="Topologie projektů Azure Red Hat OpenShift":::

Jelikož je projekt prázdný, nenašly se žádné úlohy a zobrazí se vám různé možnosti, jak můžete nasadit aplikaci.

## <a name="deploying-using-the-web-console"></a>Nasazení pomocí webové konzoly

Z možností zobrazených pro nasazení aplikace vyberte *z Gitu*. Tím se vám bude na stránce *Import ze sady Git* nakládat. Použijte `https://github.com/sclorg/django-ex.git` jako **adresu URL úložiště Git**. Ukázková webová aplikace je implementována pomocí programovacího jazyka Python.

:::image type="content" source="media/s2i/from-git.png" alt-text="Projekt Azure Red Hat OpenShift z Gitu":::

> [!NOTE]
> OpenShift zjistí, že se jedná o projekt Pythonu a vybere odpovídající obrázek tvůrce.

Posuňte se dolů k *pokročilým možnostem* a ujistěte se, že je zaškrtnuté políčko **vytvořit trasu k aplikaci** . Tato akce vytvoří trasu OpenShift, způsob, jak vystavit službu tím, že jí přidělí externě dosažitelný název hostitele.

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Projekt Azure Red Hat OpenShift z Gitu-Route Setup":::

Až budete připraveni, v dolní části stránky klikněte na **vytvořit**. Tím se vytvoří prostředky pro správu sestavení a nasazení aplikace. Pak budete přesměrováni na přehled topologie projektu.

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Projekt Azure Red Hat OpenShift z Gitu – topologie":::

Přehled topologie poskytuje vizuální reprezentaci aplikace, kterou jste nasadili. V tomto zobrazení uvidíte celkovou strukturu aplikace.

Kliknutím na ikonu Git můžete provést přechod do úložiště Git, ze kterého se vytvořil zdrojový kód aplikace. Ikona zobrazená v levém dolním rohu zobrazuje stav sestavení aplikace. Kliknutím na tuto ikonu přejdete do části Podrobnosti o sestavení. Pokud má aplikace vystavené trasy, můžete kliknutím na ikonu vpravo nahoře otevřít adresu URL pro vytvořenou trasu aplikace.

I když je aplikace rozdělená nahoru nebo dolů, spouští se uvádění a znovu vytváří lusky, takže reprezentace aplikace v zobrazení topologie bude animovaná a poskytne vám přehled o tom, co se chystá.

Kliknutím na ikonu aplikace zobrazíte další podrobnosti, jak vidíte níže.

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Projekt Azure Red Hat OpenShift z Gitu – podrobnosti":::

## <a name="viewing-the-builder-logs"></a>Zobrazení protokolů Tvůrce

Po zahájení sestavování klikněte na odkaz *Zobrazit protokoly* , který je zobrazený na panelu *prostředky* .

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Projekt Azure Red Hat OpenShift z Gitu – protokoly sestavení":::

To vám umožní monitorovat průběh sestavení při jeho spuštění. Obrázek tvůrce, Python v tomto případě, vloží zdrojový kód aplikace do finální image, a to před tím, než ji odešle do registru interních imagí OpenShift. Po zobrazení poslední zprávy o úspěšném vložení se sestavení úspěšně dokončí.

## <a name="accessing-the-application"></a>Přístup k aplikaci

Po dokončení sestavení image aplikace bude nasazena.

Kliknutím na *topologie* v levém panelu nabídek se vrátíte do zobrazení topologie projektu. Pokud jste aplikaci vytvořili pomocí webové konzole, pro aplikaci se automaticky vytvořila *trasa* a bude vystavena mimo cluster. Adresu URL, která se dá použít pro přístup k aplikaci z webového prohlížeče, se zobrazila na kartě *prostředky* aplikace, kterou jste si prohlíželi dříve.

V zobrazení topologie můžete získat přístup k adrese URL nasazené aplikace kliknutím na ikonu vpravo nahoře na prstence. Po dokončení nasazení klikněte na ikonu a měli byste vidět aplikaci, kterou jste nasadili.

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Projekt Azure Red Hat OpenShift z Gitu – Procházet aplikaci":::

## <a name="deploying-using-the-command-line"></a>Nasazení pomocí příkazového řádku

Naučíte se, jak nasadit aplikaci pomocí webové konzole, teď umožňuje nasadit stejnou webovou aplikaci, ale tentokrát pomocí `oc` nástroje příkazového řádku.

Spuštěním následujícího příkazu projekt odstraňte a začněte znovu:

```azurecli-interactive
oc delete project demoproject
```

Měla by se zobrazit potvrzovací zpráva, že se `demoproject` odstranila.

```output
project.project.openshift.io "demoproject" deleted
```

Vytvořte `demoproject` znovu spuštěním:

```azurecli-interactive
oc new-project demoproject
```

V rámci projektu vytvořte novou aplikaci ze zdroje na GitHubu a určete tak S2I tvůrce pro nejnovější verzi jazyka Python.

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

Mělo by se zobrazit výstup podobný tomuto:

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

OpenShift použije název úložiště Git jako název aplikace. Zkontrolujte stav sestavení a nasazení spuštěním:

```azurecli-interactive
oc status
```

Po dokončení sestavení a nasazení by se měl zobrazit podobný výstup.

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

K vystavení aplikace mimo cluster OpenShift budete muset vytvořit trasu spuštěním:

```azurecli-interactive
oc expose service/django-ex
```

Měli byste obdržet potvrzení.

```output
route.route.openshift.io/django-ex exposed
```

Načtěte adresu URL spuštěním:

```azurecli-interactive
oc get route django-ex
```

Měli byste získat zpátky název hostitele přiřazený k trase, kterou můžete použít k procházení nasazené aplikace.

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>Aktivace nového binárního sestavení

Při práci na aplikaci budete pravděpodobně chtít provést změny a zobrazit je nasazené. Můžete snadno nastavit Webhook, který aktivuje nové sestavení a nasazení s každým potvrzením kódu. To však nemusí být žádoucí, protože někdy byste chtěli zobrazit změny, aniž byste museli do úložiště doručovat všechna změny kódu.

V případech, kdy rychle provádíte iteraci při změnách, můžete použít, co se nazývá binární sestavení. Chcete-li Ukázat tento scénář, naklonujte úložiště Git pro aplikaci místně spuštěním:

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

Tím se vytvoří podadresář `django-ex` obsahující zdrojový kód aplikace:

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

Přejděte do podadresáře:

```azurecli-interactive
cd django-ex
```

Otevřete Editor integrovaných Azure Cloud Shell:

```azurecli-interactive
code welcome/templates/welcome/index.html
```

Posuňte se dolů a změňte řádek, který říká, že `Welcome to your Django application on OpenShift` se říká `Welcome to Azure Red Hat OpenShift` . Uložte soubor a zavřete Editor prostřednictvím `...` nabídky v pravém horním rohu.

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Projekt Azure Red Hat OpenShift z Gitu-upravit aplikaci v Azure Cloud Shellovém editoru":::

Spusťte nové sestavení spuštěním příkazu:

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

Předáním `--from-dir=.` příznaku OpenShift příkazového řádku odešle zdrojový kód ze zadaného adresáře a potom zahájí proces sestavení a nasazení. Měl by se zobrazit výstup podobný následujícímu a po několika minutách by se sestavení mělo dokončit.

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

Pokud aktualizujete prohlížeč pomocí aplikace, měl by se zobrazit aktualizovaný název.

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Projekt Azure Red Hat OpenShift z Gitu – Projděte si aktualizovanou aplikaci":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s aplikací hotoví, můžete spuštěním následujícího příkazu projekt odstranit:

```azurecli-interactive
oc delete project demoproject
```

Cluster můžete také odstranit podle pokynů v tomto [kurzu: odstranění clusteru Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V této příručce jste se dozvěděli, jak:
> [!div class="checklist"]
>
> * Vytvoření projektu
> * Nasazení aplikace ze zdrojového kódu pomocí webové konzole
> * Nasazení aplikace ze zdrojového kódu pomocí příkazového řádku OpenShift
> * Spuštění binárního sestavení pomocí příkazového řádku OpenShift

Přečtěte si další informace o tom, jak vytvářet a nasazovat aplikace s využitím imagí od zdrojů do image a [dalších strategií sestavení](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html).
