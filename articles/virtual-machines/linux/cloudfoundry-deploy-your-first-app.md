---
title: Nasazení první aplikace do Cloud Foundry v Microsoft Azure
description: Nasazení aplikace do Cloud Foundry v Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 793a8f291be4fcca6fad19d486849253dddc089f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294795"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Nasazení první aplikace do Cloud Foundry v Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) je oblíbená open source aplikační platforma dostupná v Microsoft Azure. V tomto článku ukážeme, jak nasadit a spravovat aplikaci na Cloud Foundry v prostředí Azure.

## <a name="create-a-cloud-foundry-environment"></a>Vytvoření prostředí Cloud Foundry

Existuje několik možností pro vytvoření prostředí Cloud Foundry v Azure:

- Využijte [nabídku Pivotal Cloud Foundry][pcf-azuremarketplace] na Azure Marketplace k vytvoření standardního prostředí, které zahrnuje PCF Ops Manager a Azure Service Broker. [Úplné pokyny][pcf-azuremarketplace-pivotaldocs] pro nasazení nabídky marketplace najdete v klíčové dokumentaci.
- Vytvořte přizpůsobené prostředí [ručním nasazením pivotal cloud foundry][pcf-custom].
- [Nasazení balíčků cloudové slévárny přímo][oss-cf-bosh] nastavením ředitele [BOSH,](https://bosh.io) virtuálního počítače, který koordinuje nasazení prostředí Cloud Foundry.

> [!IMPORTANT] 
> Pokud nasazujete PCF z Azure Marketplace, poznamenejte si adresu SYSTEMDOMAINURL a přihlašovací údaje správce potřebné pro přístup ke Správci klíčových aplikací, které jsou popsány v průvodci nasazením marketplace. Jsou potřebné k dokončení tohoto kurzu. Pro nasazení tržiště systemdomainurl je `https://system.*ip-address*.cf.pcfazure.com`ve formě .

## <a name="connect-to-the-cloud-controller"></a>Připojení ke cloudovému řadiči

Cloud controller je primární vstupní bod do prostředí Cloud Foundry pro nasazení a správu aplikací. Základní rozhraní API cloudového řadiče (CCAPI) je rozhraní REST API, ale je přístupné prostřednictvím různých nástrojů. V tomto případě s ním interagujeme prostřednictvím [cli cloudové slévárny][cf-cli]. Rozhraní PŘÍKAZOVÉHO PŘÍKAZU můžete nainstalovat do Linuxu, macOS nebo Windows, ale pokud nechcete, aby se vůbec neinstalovalo, je k dispozici předinstalované v [prostředí Azure Cloud Shell][cloudshell-docs].

Chcete-li se `api` přihlásit, předchlazený do SYSTEMDOMAINURL, které jste získali z nasazení tržiště. Vzhledem k tomu, že výchozí nasazení používá `skip-ssl-validation` certifikát podepsaný svým držitelem, měli byste také zahrnout přepínač.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Budete vyzváni k přihlášení do cloudového řadiče. Použijte přihlašovací údaje účtu správce, které jste získali z kroků nasazení marketplace.

Cloud Foundry poskytuje *orgs* a *prostory* jako obory názvů izolovat týmy a prostředí v rámci sdíleného nasazení. Nasazení na trhu PCF zahrnuje výchozí *systémové* organizace a sadu prostorů vytvořených tak, aby obsahovaly základní součásti, jako je služba automatického škálování a zprostředkovatel služeb Azure. Prozatím zvolte *systémový* prostor.


## <a name="create-an-org-and-space"></a>Vytvoření organizace a prostoru

Pokud zadáte `cf apps`, zobrazí se sada systémových aplikací, které byly nasazeny v systémovém prostoru v rámci systémové organizace. 

Měli byste zachovat *systémové* organizace vyhrazené pro systémové aplikace, tak vytvořit org a prostor pro dům naše ukázkové aplikace.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Pomocí příkazu target přepněte na novou organizaci a prostor:

```bash
cf target -o testorg -s dev
```

Nyní při nasazení aplikace, je automaticky vytvořen v nové organizace a prostoru. Chcete-li potvrdit, že v nové organizaci/prostoru `cf apps` momentálně nejsou žádné aplikace, zadejte znovu.

> [!NOTE] 
> Další informace o orgs a prostory a jak mohou být použity pro řízení přístupu na základě rolí (RBAC), naleznete v [dokumentaci Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Nasazení aplikace

Použijeme ukázkovou aplikaci Cloud Foundry s názvem Hello Spring Cloud, která je napsána v Javě a je založena na [jarním frameworku](https://spring.io) a [jarním startu](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klonujte úložiště Hello Spring Cloud

Ukázková aplikace Hello Spring Cloud je k dispozici na GitHubu. Naklonujte jej do vašeho prostředí a přeměňte jej do nového adresáře:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Sestavení aplikace

Vytvořte aplikaci pomocí [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Nasazení aplikace pomocí cf push

Většinu aplikací můžete nasadit do `push` Cloud Foundry pomocí příkazu:

```bash
cf push
```

Při *nabízení* aplikace Cloud Foundry zjistí typ aplikace (v tomto případě java aplikace) a identifikuje její závislosti (v tomto případě rámec jaro). Potom zabalí vše potřebné ke spuštění kódu do samostatné image kontejneru, známé jako *kapicí*. Nakonec Cloud Foundry naplánuje aplikaci na jednom z dostupných počítačů ve vašem prostředí a vytvoří adresu URL, kde ji můžete dosáhnout, která je k dispozici ve výstupu příkazu.

![Výstup z příkazu cf push][cf-push-output]

Chcete-li zobrazit aplikaci hello-spring-cloud, otevřete v prohlížeči uvedenou adresu URL:

![Výchozí uI pro Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Další informace o tom, co se děje během `cf push`, najdete v tématu Jak jsou aplikace [připraveny][cf-push-docs] v dokumentaci ke slévárně cloudu.

## <a name="view-application-logs"></a>Zobrazit protokoly aplikací

Cli cloudové slévárny můžete použít k zobrazení protokolů pro aplikaci podle jejího názvu:

```bash
cf logs hello-spring-cloud
```

Ve výchozím nastavení příkaz protokoly používá *ocas*, který zobrazuje nové protokoly, jak jsou zapsány. Chcete-li zobrazit nové protokoly, aktualizujte aplikaci hello-spring-cloud v prohlížeči.

Chcete-li zobrazit protokoly, které `recent` již byly zapsány, přidejte přepínač:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Škálování aplikace

Ve výchozím `cf push` nastavení vytvoří pouze jednu instanci aplikace. Chcete-li zajistit vysokou dostupnost a povolit horizontální navýšení kapacity pro vyšší propustnost, obecně chcete spustit více než jednu instanci vašich aplikací. Pomocí příkazu `scale` můžete snadno škálovat již nasazené aplikace:

```bash
cf scale -i 2 hello-spring-cloud
```

Spuštění `cf app` příkazu v aplikaci ukazuje, že Cloud Foundry vytváří jinou instanci aplikace. Po spuštění aplikace Cloud Foundry automaticky spustí provoz vyrovnávání zatížení.


## <a name="next-steps"></a>Další kroky

- [Přečtěte si dokumentaci ke slétovce cloudu][cloudfoundry-docs]
- [Nastavení pluginu Azure DevOps Services pro slévárnu cloudu][vsts-plugin]
- [Konfigurace trysky Microsoft Log Analytics pro slévárnu cloud][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
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
