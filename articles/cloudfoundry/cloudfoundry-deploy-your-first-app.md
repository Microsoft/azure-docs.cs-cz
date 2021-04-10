---
title: Nasazení první aplikace pro Cloud Foundry v Microsoft Azure
description: Nasazení aplikace pro Cloud Foundry v Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 037ac972dca49484f7b8c0ad8eed6942c901b997
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562924"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Nasazení první aplikace pro Cloud Foundry v Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) je oblíbená Open-Source aplikační platforma dostupná na Microsoft Azure. V tomto článku ukážeme, jak nasadit a spravovat aplikaci na Cloud Foundry v prostředí Azure.

## <a name="create-a-cloud-foundry-environment"></a>Vytvoření prostředí Cloud Foundry

Pro vytváření Cloud Foundryho prostředí v Azure existuje několik možností:

- Pomocí [nabídky pivot Cloud Foundry][pcf-azuremarketplace] v Azure Marketplace můžete vytvořit standardní prostředí, které zahrnuje PCF OPS Manager a Azure Service Broker. Podrobné [pokyny][pcf-azuremarketplace-pivotaldocs] k nasazení nabídky Marketplace najdete v dokumentaci k pivotu.
- Vytvořte vlastní prostředí [nasazením pivot Cloud Foundry ručně][pcf-custom].
- [Open source Cloud Foundry balíčky nasaďte přímo][oss-cf-bosh] nastavením [BOSHového](https://bosh.io) ředitele a virtuálního počítače, který koordinuje nasazení Cloud Foundry prostředí.

> [!IMPORTANT] 
> Pokud PCF nasazujete z Azure Marketplace, poznamenejte si SYSTEMDOMAINURL a přihlašovací údaje správce potřebné pro přístup ke Správci Pivot Apps, jak jsou popsané v Průvodci nasazením Marketplace. Jsou potřebné k dokončení tohoto kurzu. V případě nasazení Marketplace je SYSTEMDOMAINURL ve formě `https://system.*ip-address*.cf.pcfazure.com` .

## <a name="connect-to-the-cloud-controller"></a>Připojit ke cloud Controller

Cloud Controller je primárním vstupním bodem pro Cloud Foundry prostředí pro nasazení a správu aplikací. Rozhraní API základního cloudového kontroleru (CCAPI) je REST API, ale je dostupné prostřednictvím různých nástrojů. V tomto případě interakci s ní provedeme prostřednictvím [Cloud Foundry CLI][cf-cli]. Můžete nainstalovat rozhraní příkazového řádku v systému Linux, macOS nebo Windows, ale pokud byste ho raději nenainstalovali, je k dispozici v [Azure Cloud Shell][cloudshell-docs].

Pokud se chcete přihlásit, přihlaste se `api` k SYSTEMDOMAINURL, který jste získali z nasazení na Marketplace. Vzhledem k tomu, že výchozí nasazení používá certifikát podepsaný svým držitelem, měli byste také použít `skip-ssl-validation` přepínač.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Zobrazí se výzva, abyste se přihlásili ke cloud Controller. Použijte přihlašovací údaje účtu správce, které jste získali z kroků nasazení na Marketplace.

Cloud Foundry poskytuje *organizace* a *prostory* jako obory názvů k izolaci týmů a prostředí v rámci sdíleného nasazení. Nasazení na webu PCF Marketplace zahrnuje výchozí organizační *systém* a sadu prostorů vytvořených pro vytváření základních součástí, jako je služba automatického škálování a služba Azure Service Broker. Prozatím vyberte *systémové* místo.


## <a name="create-an-org-and-space"></a>Vytvoření org a prostoru

Pokud zadáte `cf apps` , zobrazí se sada systémových aplikací, které byly nasazeny v systémovém prostoru v rámci organizace systému. 

Organizaci *systému* byste měli ponechat rezervovanou pro systémové aplikace, takže vytvořte organizaci a prostor pro domácí ukázkovou aplikaci.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Pomocí příkazu cíl přepněte na novou organizační adresu a místo:

```bash
cf target -o testorg -s dev
```

Když teď nasadíte aplikaci, automaticky se vytvoří v nové organizaci a prostoru. Pokud chcete potvrdit, že v nové organizaci nebo prostoru momentálně nejsou žádné aplikace, zadejte `cf apps` znovu.

> [!NOTE] 
> Další informace o organizace a mezerách a jejich použití pro Cloud Foundry řízení přístupu na základě role (Cloud Foundry RBAC) najdete v [dokumentaci k Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Nasazení aplikace

Pojďme použít ukázkovou Cloud Foundry aplikaci nazvanou Hello Cloud, která je napsaná v jazyce Java a založená na [pružinové architektuře](https://spring.io) a [pružinovém spuštění](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klonovat cloudové úložiště Hello

Ukázková cloudová aplikace Hello pro Cloud je dostupná na GitHubu. Naklonujte ho do svého prostředí a přejděte do nového adresáře:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Sestavení aplikace

Sestavte aplikaci pomocí [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Nasazení aplikace pomocí CR push

Většinu aplikací můžete nasadit pro Cloud Foundry pomocí `push` příkazu:

```bash
cf push
```

Když nahrajete aplikaci, Cloud Foundry detekuje typ aplikace (v tomto případě aplikace Java) a identifikuje její závislosti (v tomto případě se *jedná o* architekturu pružiny). Pak zabalí vše potřebné ke spuštění kódu v samostatné imagi kontejneru, označované jako *droplet*. Nakonec Cloud Foundry naplánování aplikace na jednom z dostupných počítačů ve vašem prostředí a vytvoří adresu URL, na které se můžete dostat, která je k dispozici ve výstupu příkazu.

![Výstup z příkazu CR push][cf-push-output]

Pokud chcete zobrazit cloudovou aplikaci Hello-jaře, otevřete zadanou adresu URL v prohlížeči:

![Výchozí uživatelské rozhraní pro zdroj Hello pro Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Další informace o tom `cf push` , co se děje v průběhu, najdete v tématu [jak jsou aplikace připravené][cf-push-docs] v dokumentaci Cloud Foundry.

## <a name="view-application-logs"></a>Zobrazit protokoly aplikací

Pomocí rozhraní příkazového řádku Cloud Foundry můžete zobrazit protokoly aplikace podle jejího názvu:

```bash
cf logs hello-spring-cloud
```

Ve výchozím nastavení používá příkaz logs *konec*, který zobrazuje nové protokoly při jejich zápisu. Pokud se chcete podívat na nové protokoly, aktualizujte v prohlížeči aplikaci Hello-jaře-Cloud.

Chcete-li zobrazit protokoly, které již byly zapsány, přidejte `recent` přepínač:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Škálování aplikace

Ve výchozím nastavení `cf push` vytvoří pouze jednu instanci aplikace. Chcete-li zajistit vysokou dostupnost a povolit horizontální navýšení kapacity pro vyšší propustnost, je obecně vhodné spustit více než jednu instanci aplikace. Pomocí příkazu můžete snadno škálovat již nasazené aplikace `scale` :

```bash
cf scale -i 2 hello-spring-cloud
```

Spuštění `cf app` příkazu v aplikaci ukazuje, že Cloud Foundry vytváří další instanci aplikace. Po spuštění aplikace Cloud Foundry automaticky spustí vyrovnávání zatížení.


## <a name="next-steps"></a>Další kroky

- [Přečtěte si dokumentaci k Cloud Foundry][cloudfoundry-docs]
- [Nastavení modulu plug-in Azure DevOps Services pro Cloud Foundry][vsts-plugin]
- [Konfigurace Microsoft Log Analytics trysku pro Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: ../cloud-shell/overview.md
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png