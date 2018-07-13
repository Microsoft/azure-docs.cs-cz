---
title: 'DevOps pro aplikace s umělou inteligencí (AI): vytvoření kanálu průběžné integrace v Azure pomocí Docker, Kubernetes a Python Flask aplikace'
description: 'DevOps pro aplikace s umělou inteligencí (AI): vytvoření kanálu průběžné integrace v Azure pomocí Dockeru a Kubernetes'
services: machine-learning, team-data-science-process
documentationcenter: ''
author: jainr
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jainr
ms.openlocfilehash: 4d95fc25ed6f2f2efec8313e5b208b3cccbb619f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968787"
---
# <a name="devops-for-artificial-intelligence-ai-applications-creating-continuous-integration-pipeline-on-azure-using-docker-and-kubernetes"></a>DevOps pro aplikace s umělou inteligencí (AI): vytvoření kanálu průběžné integrace v Azure pomocí Dockeru a Kubernetes
Aplikace AI jsou často dvěma datovými proudy práce, datovým vědcům sestavování modelů strojového učení a vývojáře aplikací, vytváření aplikace, která bude vystavená koncovým uživatelům využívat. V tomto článku jsme ukazují, jak implementovat průběžné integrace (CI) / průběžné doručování (CD) kanálů pro aplikaci AI. Aplikace AI je kombinací kódu aplikace, které jsou vložené s modelem které je předem vytrénované machine learning (ML). Pro účely tohoto článku jsme pretrained modelu načítají z účtu úložiště objektů blob v Azure privátní, může to být účet AWS S3. Pro článek budeme používat webovou aplikaci flask python jednoduché.

> [!NOTE]
> Toto je jeden z několik způsobů, jimiž lze provést CI/CD. Neexistují nějaké alternativy nástrojů a jiných požadavcích uvedených níže. Když budeme vyvíjet další obsah, publikujeme ty.
>
>

## <a name="github-repository-with-document-and-code"></a>Úložiště GitHub s dokumentu a kódu
Můžete stáhnout zdrojový kód z [Githubu](https://github.com/Azure/DevOps-For-AI-Apps). A [podrobný kurz](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) je také k dispozici.

## <a name="pre-requisites"></a>Požadavky
Následují předpoklady pro postup dle kanálu CI/CD je popsáno níže:
* [Účet služby Visual Studio Team Services](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Cluster Azure Container Service (AKS) s Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Účet Azure registru kontejneru (ACR)](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Instalace Kubectl pro spuštění příkazů pro cluster Kubernetes.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Můžeme to budete potřebovat k načtení konfigurace z clusteru ACS. 
* Vytvořte fork úložiště do účtu Githubu.

## <a name="description-of-the-cicd-pipeline"></a>Popis kanálu CI/CD
Zejména kopance kanálu vypnutí pro každé nové potvrzení, spustit testovací sadu, pokud úspěšných testů trvá nejnovější sestavení se balíčky v kontejneru Dockeru. Kontejner se pak nasadí pomocí služby Azure container service (ACS) a image jsou bezpečně uložené ve službě Azure container registry (ACR). ACS je s Kubernetes pro správu clusteru služby container, ale můžete použít Docker Swarm nebo Mesos.

Aplikace bezpečně si vyžádá nejnovější model z účtu služby Azure Storage a balíčky, které jako součást aplikace. Nasazené aplikace má kód aplikace a modelu ML lze zabalit jako jeden kontejner. Odděluje obě části v aplikaci vývojáře a odborníky přes data, abyste měli jistotu, že jejich produkční aplikace běží vždy nejnovější kód s nejnovější modelu ML.

Architektura kanálu je uvedena níže. 

![Architektura](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Postup kanálu CI/CD
1. Vývojářské práce na sběrnici IDE podle svého výběru na kód aplikace.
2. Tito uživatelé potvrdí kód do správy zdrojového kódu podle vlastní volby (VSTS má dobrou podporu pro různé ovládací prvky zdroje)
3. Samostatně odborník přes data pracovat na vývoji modelu.
4. Jakmile spokojení, že jejich model publikujte do modelu úložiště, v tomto případě používáme účet blob storage. To může snadno nahradit aplikaci Azure ML Workbench služba Správa modelů prostřednictvím svých rozhraní REST API.
5. Sestavení je vydáno ve VSTS založené na potvrzení v Githubu.
6. Kanál sestavení VSTS získává nejnovější model z kontejneru objektů Blob a vytvoří kontejner.
7. VSTS odešle obrázek do úložiště privátních imagí ve službě Azure Container Registry
8. Podle nastaveného plánu (každou noc) je vydáno kanál pro vydávání verzí.
9. Nejnovější image ze služby ACR je načetli, nasazené v clusteru Kubernetes ACS.
10. Žádosti uživatelů pro aplikace prochází DNS server.
11. DNS server předává požadavek na nástroj pro vyrovnávání zatížení a odešle odpověď zpět uživateli.

## <a name="next-steps"></a>Další postup
* Odkazovat [kurzu]((https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)) sledovat podrobnosti a implementovat vlastní kanál CI/CD pro vaši aplikaci.

## <a name="references"></a>Odkazy
* [Vědecké zpracování týmových dat (TDSP)](https://aka.ms/tdsp)
* [Azure Machine Learning (AML)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/vso/)
* [Služby Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)