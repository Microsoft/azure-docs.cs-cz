---
title: Vývoj pro tým v Kubernetes
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: V tomto rychlém startu se dozvíte, jak provádět vývoj týmu Kubernetes pomocí kontejnerů a mikroslužeb pomocí Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 0fe177db420913e5d68807dd803df791653c0914
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "78244939"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Rychlý Start: vývoj pro týmy v Kubernetes – Azure Dev Spaces

V tomto průvodci se naučíte:

- Nastavte Azure Dev Spaces ve spravovaném clusteru Kubernetes v Azure.
- Nasaďte rozsáhlou aplikaci s více mikroslužbami do vývojového prostoru.
- Otestujte jednu mikroslužbu v izolovaném prostoru pro vývoj v rámci celé aplikace.

![Azure Dev Spaces vývoj týmu](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)
- Je [nainstalovaná Helm 3][helm-installed].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Cluster AKS musíte vytvořit v [podporované oblasti][supported-regions]. Níže uvedené příkazy vytvoří skupinu prostředků s názvem *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí `use-dev-spaces` příkazu povolte v clusteru AKS vývojářské prostory a postupujte podle pokynů. Následující příkaz povolí v *MyAKS* ve skupině *MyResourceGroup* vývojářské prostory a vytvoří vývojové místo s názvem *dev*.

> [!NOTE]
> `use-dev-spaces` Příkaz nainstaluje taky Azure dev Spaces CLI, pokud ještě není nainstalovaný. Azure Dev Spaces CLI nelze nainstalovat do Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Získat ukázkový kód aplikace

V tomto článku se používá [ukázková aplikace Azure dev Spaces pro sdílení kol](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) k předvedení používání Azure dev Spaces.

Naklonujte aplikaci z GitHubu a přejděte do jejího adresáře:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Načtení HostSuffix pro *vývoj*

Pomocí `azds show-context` příkazu můžete zobrazit HostSuffix pro *vývoj*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Aktualizace grafu Helm pomocí HostSuffix

Otevřete [grafy/hodnoty. yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) a nahraďte všechny výskyty `<REPLACE_ME_WITH_HOST_SUFFIX>` hodnotou HostSuffix, kterou jste získali dříve. Uložte změny a zavřete soubor.

## <a name="run-the-sample-application-in-kubernetes"></a>Spuštění ukázkové aplikace v Kubernetes

Příkazy pro spuštění ukázkové aplikace v Kubernetes jsou součástí stávajícího procesu a nemají žádné závislosti na Azure Dev Spaces nástrojů. V tomto případě je Helm nástroje, které slouží ke spuštění této ukázkové aplikace, ale jiné nástroje lze použít ke spuštění celé aplikace v oboru názvů v rámci clusteru. Příkazy Helm cílí na vývojové místo s názvem *vývoj* , který jste vytvořili dříve, ale toto vývojové místo je také oborem názvů Kubernetes. V důsledku toho mohou být vývojové prostory cíleny jinými nástroji, které jsou stejné jako jiné obory názvů.

Po spuštění aplikace v clusteru můžete použít Azure Dev Spaces pro týmový vývoj, a to bez ohledu na to, který nástroj používá k jeho nasazení.

Pomocí `helm install` příkazu nastavte a nainstalujte ukázkovou aplikaci do clusteru.

```cmd
cd charts/
helm install bikesharingsampleappsampleapp . --dependency-update --namespace dev --atomic
```

Dokončení `helm install` příkazu může trvat několik minut. Po instalaci ukázkové aplikace do clusteru a vzhledem k tomu, že máte v clusteru povolené vývojářské prostory, použijte `azds list-uris` příkaz k zobrazení adres URL pro ukázkovou aplikaci v aktuálně vybraném *vývojovém* prostředí.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Přejděte do služby *bikesharingweb* otevřením veřejné adresy URL z `azds list-uris` příkazu. Ve výše uvedeném příkladu je `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`veřejná adresa URL pro službu *bikesharingweb* . Jako uživatel vyberte *Aurelia Briggs (zákazník)* . Ověřte, že se zobrazí text *dobrý Aurelia Briggs | Odhlaste* se v horní části.

![Ukázková aplikace pro sdílení kol Azure Dev Spaces](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Vytvoření podřízených vývojových prostorů

Pomocí `azds space select` příkazu vytvořte dva podřízené prostory v části *dev*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Výše uvedené příkazy vytvářejí dva podřízené prostory ve *vývojech* s názvem *azureuser1* a *azureuser2*. Tyto dva podřízené prostory reprezentují různé vývojové prostory pro vývojáře *azureuser1* a *azureuser2* , které se používají k provádění změn v ukázkové aplikaci.

Pomocí `azds space list` příkazu vypište všechny vývojové prostory a potvrďte možnost *dev/azureuser2* .

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

Použijte `azds list-uris` k zobrazení adres URL pro ukázkovou aplikaci v aktuálně vybraném prostoru, který je určen pro *vývoj/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Potvrďte, že adresy URL zobrazované `azds list-uris` příkazem mají předponu *azureuser2. s. dev* . Tato předpona potvrdí, že aktuální vybraný prostor je *azureuser2*, což je podřízený objekt pro *vývoj*.

Přejděte ke službě *bikesharingweb* pro vývojové místo pro *vývoj/azureuser2* . Otevřete veřejnou adresu URL z `azds list-uris` příkazu. Ve výše uvedeném příkladu je `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`veřejná adresa URL pro službu *bikesharingweb* . Jako uživatel vyberte *Aurelia Briggs (zákazník)* . Ověřte, že se zobrazí text *dobrý Aurelia Briggs | Odhlaste* se v horní části.

## <a name="update-code"></a>Aktualizace kódu

Otevřete *BikeSharingWeb/Components/Header. js* pomocí textového editoru a změňte text v [elementu span pomocí `userSignOut` ClassName](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Uložte změny a zavřete soubor.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Sestavení a spuštění aktualizované služby bikesharingweb v prostoru pro vývoj */azureuser2* vývoj

Přejděte do složky *BikeSharingWeb/* Directory a spusťte `azds up` příkaz.

```cmd
$ cd ../BikeSharingWeb/
$ azds up

Using dev space 'dev/azureuser2' with target 'MyAKS'
Synchronizing files...2s
...
Service 'bikesharingweb' port 'http' is available at http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/
Service 'bikesharingweb' port 80 (http) is available at http://localhost:54256
...
```

Tento příkaz sestaví a spustí službu *bikesharingweb* ve vývojovém prostoru *vývoj/azureuser2* . Tato služba běží kromě služby *bikesharingweb* spuštěné v *dev* a používá se pouze pro požadavky s předponou adresy URL *azureuser2. s* . Další informace o tom, jak funguje směrování mezi nadřazeným a podřízeným vývojovým prostorem, najdete v tématu [jak Azure dev Spaces fungují a jsou nakonfigurované](how-dev-spaces-works.md).

Přejděte ke službě *bikesharingweb* pro vývojové místo pro *vývoj/azureuser2* otevřením veřejné adresy URL zobrazené ve výstupu `azds up` příkazu. Jako uživatel vyberte *Aurelia Briggs (zákazník)* . Ověřte, že se v pravém horním rohu zobrazil aktualizovaný text. Je možné, že budete muset aktualizovat stránku nebo vymazat mezipaměť prohlížeče, pokud tuto změnu neuvidíte okamžitě.

![Byla aktualizována ukázková aplikace Azure Dev Spaces sdílení kol.](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Když při spuštění `azds up`přejdete ke službě, ve výstupu `azds up` příkazu se zobrazí také trasování požadavků HTTP. Tyto trasování vám můžou pomoct při řešení potíží a ladění vaší služby. Tato trasování můžete zakázat pomocí `--disable-http-traces` aplikace při spuštění `azds up`.

## <a name="verify-other-dev-spaces-are-unchanged"></a>Ověřit, jestli nejsou jiné vývojové prostory beze změny

Pokud je `azds up` příkaz stále spuštěný, stiskněte klávesy *Ctrl + c*.

```cmd
$ azds list-uris --all
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser1.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser1.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://dev.bikesharingweb.fedcab0987.eus.azds.io/               Available
http://dev.gateway.fedcab0987.eus.azds.io/                      Available
```

V prohlížeči přejděte do *vývojové* verze *bikesharingweb* , jako uživatel vyberte *Aurelia Briggs (zákazník)* a ověřte, že se v pravém horním rohu zobrazí původní text. Opakujte tyto kroky s adresou URL pro *vývoj/azureuser1* . Všimněte si, že změny se aplikují pouze na verzi *bikesharingweb*pro *vývoj/azureuser2* . Tato izolace změn pro *dev/azureuser2* umožňuje *azureuser2* provádění změn bez ovlivnění *azureuser1*.

Aby se tyto změny projevily ve *vývojových* a *vývojových/azureuser1*, měli byste postupovat podle existujícího pracovního postupu nebo kanálu CI/CD svého týmu. Tento pracovní postup může například zahrnovat potvrzení změny v systému správy verzí a nasazení aktualizace pomocí kanálu CI/CD nebo nástrojů, jako je Helm.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak vám může služba Azure Dev Spaces pomoct s vývojem složitějších aplikací používajících více kontejnerů a jak si můžete zjednodušit spolupráci na vývoji díky práci s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-nodejs.md)

[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
