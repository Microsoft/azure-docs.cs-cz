---
title: Nasazení první aplikace pro Cloud Foundry v Microsoft Azure | Dokumentace Microsoftu
description: Nasazení aplikace do Cloud Foundry v Azure
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
ms.openlocfilehash: 6e2fa77273ef35fae6c3b232cb36fa913faf879d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299045"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Nasazení první aplikace pro Cloud Foundry v Microsoft Azure

[Cloud Foundry](http://cloudfoundry.org) oblíbených open source aplikační platformy je k dispozici na Microsoft Azure. V tomto článku vám ukážeme, jak nasadit a spravovat aplikace na Cloud Foundry v prostředí Azure.

## <a name="create-a-cloud-foundry-environment"></a>Vytvoření prostředí Cloud Foundry

Existuje několik možností pro vytvoření prostředí Cloud Foundry v Azure:

- Použití [Pivotal Cloud Foundry nabídka] [ pcf-azuremarketplace] na webu Azure Marketplace k vytvoření standardního prostředí, která zahrnuje PCF Ops Manageru a Azure Service Broker. Můžete najít [úplné pokyny] [ pcf-azuremarketplace-pivotaldocs] pro nasazení na webu marketplace nabízí v Pivotal dokumentaci.
- Vytvořte přizpůsobené prostředí podle [ručního nasazení Pivotal Cloud Foundry][pcf-custom].
- [Nasazení Cloud Foundry balíčků open source přímo] [ oss-cf-bosh] nastavením [BOSH](http://bosh.io) ředitel, virtuální počítač, který koordinuje nasazení prostředí Cloud Foundry.

> [!IMPORTANT] 
> Pokud provádíte nasazení PCF z Azure Marketplace, poznamenejte si SYSTEMDOMAINURL a přihlašovací údaje správce vyžadovaných pro přístup k Pivotal Správce aplikací, které jsou popsané v příručce pro nasazení webu marketplace. Tyto jsou potřeba k dokončení tohoto kurzu. Pro nasazení webu marketplace, SYSTEMDOMAINURL je ve formě https://system. *IP adresa*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Připojení k řadiči cloudu

Řadič cloudu je primárním vstupním bodem do prostředí Cloud Foundry pro nasazení a správu aplikací. Základní cloudové Kontroleru rozhraní API (CCAPI) je rozhraní REST API, ale je přístupná prostřednictvím různých nástrojů. V takovém případě budeme pracovat s nimi prostřednictvím [Cloud Foundry CLI][cf-cli]. Rozhraní příkazového řádku můžete nainstalovat Linux, MacOS nebo Windows, ale pokud chcete raději nechcete instalovat vůbec, je k dispozici předem nainstalována v [Azure Cloud Shell][cloudshell-docs].

K přihlášení, předřaďte `api` k SYSTEMDOMAINURL, který jste získali z webu marketplace nasazení. Protože výchozím nasazení používá certifikát podepsaný svým držitelem, měli byste taky zahrnout `skip-ssl-validation` přepnout.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Zobrazí se výzva k přihlášení k řadiči cloudu. Pomocí přihlašovacích údajů účtu správce, které jste získali z webu marketplace kroky nasazení.

Poskytuje cloud Foundry *organizace* a *prostory* jako obory názvů izolovat týmy a prostředí v rámci sdílené nasazení. Nasazení PCF marketplace obsahuje výchozí *systému* organizace a sadu prostory vytvořené tak, aby obsahovala základní komponenty, jako je automatické škálování služby a služby Azure service broker. Tentokrát klikněte *systému* místa.


## <a name="create-an-org-and-space"></a>Vytvoření organizace a místa

Pokud zadáte `cf apps`, zobrazí se řada aplikací systému, které jsou nasazené v systému mezeru v rámci organizace. systém 

Byste měli mít *systému* organizace, které jsou vyhrazené pro aplikace systému, tak vytvoření organizace a místa pro uložení naší ukázkovou aplikací.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Použít cílový příkaz pro přepnutí na novou organizaci a místa:

```bash
cf target -o testorg -s dev
```

Nyní když nasadíte aplikaci, je automaticky vytvořen ve novou organizaci a místo. Pokud chcete potvrdit, že nejsou momentálně žádné aplikace v nové organizaci/prostor, zadejte `cf apps` znovu.

> [!NOTE] 
> Další informace o organizace a mezery a jak můžete využít k řízení přístupu na základě role (RBAC), najdete v článku [dokumentace ke Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Nasazení aplikace

Použijeme ukázkovou aplikaci Cloud Foundry názvem Hello Spring cloudem, který je napsaný v jazyce Java a na základě [Spring Framework](http://spring.io) a [Spring Boot](http://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Naklonujte úložiště Hello Spring cloudem

Ukázkové aplikace Hello Spring cloudem je k dispozici na Githubu. Si ho naklonovat do vašeho prostředí a přejděte do nového adresáře:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Sestavení aplikace

Sestavení aplikace pomocí [Apache Maven](http://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Nasazení aplikace pomocí nasdílení změn cf

Většina aplikací pomocí Cloud Foundry můžete nasadit `push` příkaz:

```bash
cf push
```

Pokud jste *nabízených* aplikace Cloud Foundry detekuje typu aplikace (v tomto případě aplikace v Javě) a identifikuje jeho závislosti (v tomto případě Spring framework). Potom balíčky všechno, co je potřeba ke spouštění kódu do samostatné image kontejneru, označované jako *droplet*. A konečně Cloud Foundry plánuje aplikaci na některém z dostupných počítačů ve vašem prostředí a vytvoří adresu URL, kde je k němu přistoupit, která je k dispozici ve výstupu příkazu.

![Výstup z příkazu push cf][cf-push-output]

Pokud chcete zobrazit aplikaci hello-spring cloudem, otevřete v prohlížeči adresy URL:

![Výchozí uživatelské rozhraní pro Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Další informace o tom, co se stane během `cf push`, naleznete v tématu [jak, aplikace připravené] [ cf-push-docs] v dokumentaci k Cloud Foundry.

## <a name="view-application-logs"></a>Zobrazit protokoly aplikací

Cloud Foundry rozhraní příkazového řádku můžete použít k zobrazení protokolů pro aplikaci pomocí jeho názvu:

```bash
cf logs hello-spring-cloud
```

Ve výchozím nastavení, protokoly příkaz používá *tail*, který zobrazuje nové protokoly, jako jsou zapsány. Pokud chcete zobrazit nové protokoly nezobrazí, aktualizujte aplikaci hello-spring cloud v prohlížeči.

Chcete-li zobrazit protokoly, které již byly napsány, přidejte `recent` přepínače:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Škálování aplikace

Ve výchozím nastavení `cf push` vytvoří pouze jedna instance vaší aplikace. K zajištění vysoké dostupnosti a povolit horizontální navýšení kapacity pro vyšší propustnost, obvykle chcete spustit více než jednu instanci aplikace. Je možné snadno škálovat na již nasazených aplikací pomocí `scale` příkaz:

```bash
cf scale -i 2 hello-spring-cloud
```

Spuštění `cf app` příkaz aplikace zobrazí, že Cloud Foundry je vytvořením další instance okna aplikace. Po zahájení aplikace Cloud Foundry automaticky spustí vyrovnáváním zatížení provozu na ni.


## <a name="next-steps"></a>Další postup

- [Přečtěte si dokumentaci Cloud Foundry][cloudfoundry-docs]
- [Nastaví modul plug-in Azure DevOps služby pro Cloud Foundry][vsts-plugin]
- [Konfigurace Microsoft Log Analytics Nozzle pro Cloud Foundry][loganalytics-nozzle]

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
