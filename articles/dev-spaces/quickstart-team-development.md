---
title: Vývoj týmu na Kubernetes
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: Tento rychlý start ukazuje, jak provést vývoj týmu Kubernetes s kontejnery a mikroslužbami pomocí Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 0fe177db420913e5d68807dd803df791653c0914
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244939"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Úvodní příručka: Týmový vývoj na Kubernetes – Azure Dev Spaces

V tomto průvodci se naučíte:

- Nastavte Azure Dev Spaces ve spravovaném clusteru Kubernetes v Azure.
- Nasazení velké aplikace s více mikroslužeb do prostoru pro vývoj.
- Otestujte jednu mikroslužbu v izolovaném vývojovém prostoru v kontextu úplné aplikace.

![Vývoj týmu Azure Dev Spaces](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3 nainstalován][helm-installed].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Je nutné vytvořit cluster AKS v [podporované oblasti][supported-regions]. Níže uvedené příkazy vytvoří skupinu prostředků nazvanou *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí `use-dev-spaces` příkazu povolte funkci Dev Spaces v clusteru AKS a postupujte podle pokynů. Příkaz níže povolí funkce Dev Spaces v clusteru *MyAKS* ve skupině *MyResourceGroup* a vytvoří dev prostor nazvaný *dev*.

> [!NOTE]
> Příkaz `use-dev-spaces` také nainstaluje příkaz CLI Azure Dev Spaces, pokud ještě není nainstalován. V prostředí Azure Cloud Shell nelze nainstalovat příkaz příkazpříkaz ový příkaz Azure Dev Spaces.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Získat ukázkový kód aplikace

V tomto článku použijete [ukázkovou aplikaci Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) k předvedení pomocí Azure Dev Spaces.

Klonujte aplikaci z GitHubu a přejděte do jejího adresáře:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Načtení služby HostSuffix pro *dev*

Pomocí `azds show-context` příkazu zobrazte hostsuffix pro *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Aktualizace grafu Helm pomocí služby HostSuffix

Otevřete [grafy/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) a `<REPLACE_ME_WITH_HOST_SUFFIX>` nahraďte všechny instance hodnoty HostSuffix, kterou jste načetli dříve. Uložte změny a zavřete soubor.

## <a name="run-the-sample-application-in-kubernetes"></a>Spuštění ukázkové aplikace v Kubernetes

Příkazy pro spuštění ukázkové aplikace na Kubernetes jsou součástí existujícího procesu a nemají žádnou závislost na nástrojích Azure Dev Spaces. V tomto případě Helm je nástroj používaný ke spuštění této ukázkové aplikace, ale jiné nástroje lze použít ke spuštění celé aplikace v oboru názvů v clusteru. Příkazy Helmu cílí na dev prostor s názvem *dev,* který jste vytvořili dříve, ale tento prostor pro změny v názvu je také oborem názvů Kubernetes. V důsledku toho mohou být dev mezery cíleny jinými nástroji, které jsou stejné jako jiné obory názvů.

Azure Dev Spaces můžete použít pro vývoj týmu po spuštění aplikace v clusteru bez ohledu na nástroje používané k jeho nasazení.

Pomocí `helm install` příkazu nastavte a nainstalujte ukázkovou aplikaci do clusteru.

```cmd
cd charts/
helm install bikesharingsampleappsampleapp . --dependency-update --namespace dev --atomic
```

Dokončení `helm install` příkazu může trvat několik minut. Po instalaci ukázkové aplikace v clusteru a vzhledem k tomu, `azds list-uris` že máte v clusteru povoleno funkce Dev Spaces, použijte příkaz k zobrazení adres URL ukázkové aplikace ve *vývoji,* který je aktuálně vybraný.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Přejděte na službu *bikesharingweb* otevřením `azds list-uris` veřejné adresy URL z příkazu. Ve výše uvedeném příkladu je `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`veřejná adresa URL služby *bikesharingweb* . Jako uživatele vyberte *Aurelia Briggs (zákazník).* Ověřte, zda se zobrazí text *Hi Aurelia Briggs | Odhlásit* se nahoře.

![Ukázková aplikace sdílení kole Azure Dev Spaces](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Vytvoření podřízených vývojoprostorů

Pomocí `azds space select` příkazu vytvořte dvě podřízené mezery pod *dev*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Výše uvedené příkazy vytvořit dva podřízené mezery pod *dev* s názvem *azureuser1* a *azureuser2*. Tyto dva podřízené prostory představují odlišné dev prostory pro vývojáře *azureuser1* a *azureuser2* použít pro provádění změn v ukázkové aplikaci.

Pomocí `azds space list` příkazu zobrazíte seznam všech dev mezer a potvrďte, že je vybrán *dev/azureuser2.*

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

Použijte `azds list-uris` k zobrazení adres URL pro ukázkovou aplikaci v aktuálně vybraném prostoru, který je *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Zkontrolujte, zda adresy URL `azds list-uris` zobrazené příkazem mají předponu *azureuser2.s.dev.* Tato předpona potvrzuje, že aktuální vybrané místo je *azureuser2*, což je podřízený *dev*.

Přejděte na *bikesharingweb* služby *pro dev/azureuser2* dev prostoru `azds list-uris` otevřením veřejné URL z příkazu. Ve výše uvedeném příkladu je `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`veřejná adresa URL služby *bikesharingweb* . Jako uživatele vyberte *Aurelia Briggs (zákazník).* Ověřte, zda se zobrazí text *Hi Aurelia Briggs | Odhlaste se* nahoře.

## <a name="update-code"></a>Aktualizace kódu

Otevřete *BikeSharingWeb/components/Header.js* s textovým editorem a změňte text v [prvku span s `userSignOut` názvem class](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Uložte změny a zavřete soubor.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Sestavení a spuštění aktualizované služby bikesharingweb v *dev/azureuser2* dev prostoru

Přejděte do adresáře *BikeSharingWeb/* a spusťte `azds up` příkaz.

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

Tento příkaz sestavuje a spouští službu *bikesharingweb* v *dev/azureuser2* dev prostoru. Tato služba běží kromě *bikesharingweb* služby spuštěné v *dev* a používá se pouze pro požadavky s předponou URL *azureuser2.s.* Další informace o tom, jak směrování funguje mezi nadřazené a podřízené dev prostory, naleznete v [tématu Jak Azure Dev Spaces funguje a je nakonfigurován](how-dev-spaces-works.md).

Přejděte na *bikesharingweb* služby *pro dev/azureuser2* dev prostoru otevřením veřejné `azds up` adresy URL zobrazené ve výstupu příkazu. Jako uživatele vyberte *Aurelia Briggs (zákazník).* Ověřte, zda se aktualizovaný text zobrazí v pravém horním rohu. Pokud tuto změnu okamžitě neuvidíte, bude pravděpodobně nutné aktualizovat stránku nebo vymazat mezipaměť prohlížeče.

![Aktualizována ukázková aplikace sdílení virtuálních kol Azure Dev Spaces](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Při přechodu na službu při spuštění `azds up`jsou ve výstupu `azds up` příkazu zobrazeny také trasování požadavků HTTP. Tyto trasování vám může pomoci při řešení potíží a ladění služby. Tyto trasování můžete `--disable-http-traces` zakázat `azds up`při spuštění .

## <a name="verify-other-dev-spaces-are-unchanged"></a>Ověření, že ostatní dev prostory se nezměnily

Pokud `azds up` je příkaz stále spuštěn, stiskněte *kombinaci kláves Ctrl+c*.

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

Přejděte na *dev* verzi *bikesharingweb* ve vašem prohlížeči, zvolte *Aurelia Briggs (zákazník)* jako uživatel, a ověřte, zda vidíte původní text v pravém horním rohu. Opakujte tyto kroky s *adresuurldeí dev/azureuser1.* Všimněte si, že změny jsou použity pouze *pro dev/azureuser2* verze *bikesharingweb*. Tato izolace změn *dev/azureuser2* umožňuje *azureuser2* provádět změny bez *ovlivnění azureuser1*.

Chcete-li mít tyto změny projeví v *vývoj* a *vývoj/azureuser1*, měli byste sledovat stávající pracovní postup vašeho týmu nebo CI/CD kanálu. Tento pracovní postup může například zahrnovat potvrzení změny systému správy verzí a nasazení aktualizace pomocí kanálu CI/CD nebo nástrojů, jako je helm.

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
