---
title: Nasazení vaší první aplikace do cloudu Foundry v Microsoft Azure | Microsoft Docs
description: Nasazení aplikace do cloudu Foundry v Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 5e7b321c9fc8f8568cd8109cea0ae877048d3663
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841414"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Nasazení vaší první aplikace do cloudu Foundry v Microsoft Azure

[Cloud Foundry](http://cloudfoundry.org) je platforma oblíbených open-source aplikace k dispozici na Microsoft Azure. V tomto článku jsme ukazují, jak nasadit a spravovat aplikaci v cloudu Foundry v prostředí Azure.

## <a name="create-a-cloud-foundry-environment"></a>Vytvořte prostředí cloudu Foundry

Existuje několik možností pro vytvoření Foundry cloudové prostředí v Azure:

- Použití [nabídka hrají cloudu Foundry] [ pcf-azuremarketplace] v Azure Marketplace vytvořit standardní prostředí, které obsahuje PCF Ops Manager a službu Azure Service Broker. Můžete najít [úplné pokyny] [ pcf-azuremarketplace-pivotaldocs] pro nasazení webu marketplace nabízí v hrají dokumentaci.
- Vytvořit vlastní prostředí pomocí [ručního nasazení hrají cloudu Foundry][pcf-custom].
- [Nasazení balíčků cloudu Foundry open-source přímo] [ oss-cf-bosh] nastavením [BOSH](http://bosh.io) ředitel, virtuální počítač, který koordinuje nasazení Foundry cloudové prostředí.

> [!IMPORTANT] 
> Pokud nasazujete PCF z Azure Marketplace, poznamenejte si SYSTEMDOMAINURL a přihlašovací údaje správce potřebné pro přístup správce hrají aplikace, které jsou popsané v Průvodci nasazením aplikace marketplace. Že jsou nutné k dokončení tohoto kurzu. Pro nasazení webu marketplace SYSTEMDOMAINURL je ve formátu https://system. *IP adresa*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Připojení k řadiči cloudu

Kontroleru cloudu je primárním vstupním bodem do cloudu Foundry prostředí pro nasazení a správu aplikací. Základní rozhraní API řadiče v cloudu (CCAPI) je rozhraní REST API, ale je přístupný prostřednictvím různých nástrojů. V takovém případě budeme pracovat s ním prostřednictvím [cloudu Foundry rozhraní příkazového řádku][cf-cli]. Rozhraní příkazového řádku můžete nainstalovat na systému Linux, systému MacOS nebo Windows, ale pokud chcete raději nechcete instalovat ho vůbec, je k dispozici předinstalován v [prostředí cloudu Azure][cloudshell-docs].

K přihlášení, předřadit `api` k SYSTEMDOMAINURL, který jste získali z marketplace nasazení. Vzhledem k tomu, že výchozí nasazení používá certifikát podepsaný svým držitelem, musí rovněž zahrnovat `skip-ssl-validation` přepínače.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Zobrazí se výzva k přihlášení do Kontroleru cloudu. Použijte přihlašovací údaje účtu správce, které jste získali z kroků nasazení marketplace.

Poskytuje cloudu Foundry *orgs* a *prostory* jako obory názvů izolovat týmy a prostředí v rámci sdílené nasazení. Nasazení webu marketplace PCF zahrnuje výchozí *systému* organizace a sadu prostorů vytvořit tak, aby obsahovala základní součásti, jako třeba službu automatické škálování a zprostředkovatele služby Azure. Nyní, vyberte *systému* místa.


## <a name="create-an-org-and-space"></a>Vytvoření organizace a místa

Pokud zadáte `cf apps`, najdete v části sadu systému aplikací, které jsou nasazené v prostoru systému v rámci org. systému 

Byste měli mít *systému* org vyhrazena pro aplikace, systému, tak vytvoření organizace a místa pro uložení naše ukázková aplikace.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Použijte příkaz cíl přepnout do nové organizace a místa:

```bash
cf target -o testorg -s dev
```

Teď když nasadíte aplikaci, je vytvořeno automaticky v nové organizace a místa. Chcete-li potvrdit, že aktuálně neexistují žádné aplikace v nové organizace/prostor, zadejte `cf apps` znovu.

> [!NOTE] 
> Další informace o orgs a prostory a jak mohou být použity pro řízení přístupu na základě role (RBAC) najdete v tématu [dokumentace cloudu Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Nasazení aplikace

Můžeme použít ukázkové aplikace cloudu Foundry názvem Hello pružiny cloudu, což je napsanou v jazyce Java a na základě [pružiny Framework](http://spring.io) a [pružiny spouštěcí](http://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klonovat úložiště v cloudu pružiny Hello

Ukázková aplikace Hello pružiny cloudu je k dispozici na Githubu. Klonování pro vaše prostředí a změňte do nového adresáře:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Sestavení aplikace

Sestavení aplikace pomocí [Apache Maven](http://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Nasazení aplikace pomocí nabízených CR

Většina aplikací pomocí Foundry cloudu můžete nasadit `push` příkaz:

```bash
cf push
```

Když jste *nabízená* aplikace cloudu Foundry zjistí typ aplikace (v tomto případě aplikace v jazyce Java) a identifikuje jeho závislosti (v tomto případě pružiny framework). Potom balíčky vše potřebné ke spuštění kódu do kontejneru bitovou kopii samostatné říká *droplet*. Nakonec cloudu Foundry plány aplikace na jednu z dostupných počítačů ve vašem prostředí a vytvoří adresa URL, kde můžete dosáhnout, která je k dispozici ve výstupu příkazu.

![Výstup z příkazu nabízené CR][cf-push-output]

Pokud chcete zobrazit aplikace hello pružiny cloud, otevřete zadané adresy URL v prohlížeči:

![Výchozí nastavení uživatelského rozhraní pro cloudové pružiny Hello][hello-spring-cloud-basic]

> [!NOTE] 
> Další informace o tom co se stane při `cf push`, najdete v části [jak jsou připraveny aplikace] [ cf-push-docs] v dokumentaci k Foundry cloudu.

## <a name="view-application-logs"></a>Zobrazit protokoly aplikací

Pokud chcete zobrazit protokoly pro aplikaci, jeho název můžete použít rozhraní příkazového řádku Foundry cloudu:

```bash
cf logs hello-spring-cloud
```

Ve výchozím nastavení, v protokolech příkaz používá *tail*, který zobrazuje nové protokoly, jako jsou zapsané. Pokud chcete zobrazit nové protokoly se zobrazí, aktualizujte hello pružiny cloudové aplikace v prohlížeči.

Chcete-li zobrazit protokoly, které již byla zapsána, přidejte `recent` přepínače:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Škálování aplikace

Ve výchozím nastavení `cf push` pouze vytvoří jednu instanci aplikace. K zajištění vysoké dostupnosti a povolit škálování pro vyšší propustnost, chcete obecně spustit více než jednu instanci aplikace. Můžete snadno škálovat již nasazené aplikace pomocí `scale` příkaz:

```bash
cf scale -i 2 hello-spring-cloud
```

Spuštění `cf app` příkaz u aplikace zobrazí, že cloudové Foundry vytváří jiná instance aplikace. Po spuštění aplikace Foundry cloudu se automaticky spustí přenosů do ní služby Vyrovnávání zatížení.


## <a name="next-steps"></a>Další postup

- [Přečtěte si dokumentaci cloudu Foundry][cloudfoundry-docs]
- [Nastavení modulu plug-in Visual Studio Team Services pro Cloud Foundry][vsts-plugin]
- [Konfigurace trysek Analýza protokolů Microsoft pro Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: http://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png