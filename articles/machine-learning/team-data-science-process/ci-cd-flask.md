---
title: Vytvořit průběžnou integraci Azure kanál – vědecké zpracování týmových dat
description: 'DevOps pro aplikace s umělou inteligencí (AI): vytvoření kanálu průběžné integrace v Azure pomocí Dockeru a Kubernetes'
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 05/22/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: a3c570d3d22742afcbba86f35a9dd1f6a85c0b8f
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134280"
---
# <a name="creating-continous-integration-pipeline-on-azure-using-docker-kubernetes-and-python-flask-application"></a>Vytvoření kanálu průběžné integrace v Azure s využitím aplikace Python Flask, Docker a Kubernetes
Aplikace AI jsou často dvěma datovými proudy práce, datovým vědcům sestavování modelů strojového učení a vývojáře aplikací, vytváření aplikace, která bude vystavená koncovým uživatelům využívat. V tomto článku jsme ukazují, jak implementovat průběžné integrace (CI) / průběžné doručování (CD) kanálů pro aplikaci AI. Aplikace AI je kombinací kódu aplikace, které jsou vložené s modelem které je předem vytrénované machine learning (ML). Pro účely tohoto článku jsme pretrained modelu načítají z účtu úložiště objektů blob v Azure privátní, může to být účet AWS S3. Pro článek budeme používat webovou aplikaci flask python jednoduché.

> [!NOTE]
> Toto je jeden z několik způsobů, jimiž lze provést CI/CD. Neexistují nějaké alternativy nástrojů a jiných požadavcích uvedených níže. Když budeme vyvíjet další obsah, publikujeme ty.
>
>

## <a name="github-repository-with-document-and-code"></a>Úložiště GitHub s dokumentu a kódu
Můžete stáhnout zdrojový kód z [Githubu](https://github.com/Azure/DevOps-For-AI-Apps). A [podrobný kurz](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) je také k dispozici.

## <a name="pre-requisites"></a>Požadavky
Následují předpoklady pro postup dle kanálu CI/CD je popsáno níže:
* [Organizace Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Cluster Azure Container Service (AKS) s Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Účet služby Azure Container Registry (ACR)](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Instalace Kubectl pro spuštění příkazů pro cluster Kubernetes.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Můžeme to budete potřebovat k načtení konfigurace z clusteru ACS. 
* Vytvořte fork úložiště do účtu Githubu.

## <a name="description-of-the-cicd-pipeline"></a>Popis kanálu CI/CD
Zejména kopance kanálu vypnutí pro každé nové potvrzení, spustit testovací sadu, pokud úspěšných testů trvá nejnovější sestavení se balíčky v kontejneru Dockeru. Kontejner se pak nasadí pomocí Azure Container Service (ACS) a image jsou bezpečně uložené v Azure Container Registry (ACR). ACS je s Kubernetes pro správu clusteru služby container, ale můžete použít Docker Swarm nebo Mesos.

Aplikace bezpečně si vyžádá nejnovější model z účtu služby Azure Storage a balíčky, které jako součást aplikace. Nasazené aplikace má kód aplikace a modelu ML lze zabalit jako jeden kontejner. Odděluje obě části v aplikaci vývojáře a odborníky přes data, abyste měli jistotu, že jejich produkční aplikace běží vždy nejnovější kód s nejnovější modelu ML.

Architektura kanálu je uvedena níže. 

![Architektura](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Postup kanálu CI/CD
1. Vývojářské práce na sběrnici IDE podle svého výběru na kód aplikace.
2. Tito uživatelé potvrdí kód do správy zdrojového kódu podle vlastní volby (Azure DevOps má dobrou podporu pro různé ovládací prvky zdroje)
3. Samostatně odborník přes data pracovat na vývoji modelu.
4. Jakmile spokojení, že jejich model publikujte do modelu úložiště, v tomto případě používáme účet blob storage. 
5. Sestavení je vydáno v Azure DevOps, které jsou založené na potvrzení v Githubu.
6. Stáhne nejnovější model z kontejneru objektů Blob a vytváří kontejner Azure DevOps sestavení kanálu.
7. Azure DevOps odešle obrázek do úložiště privátních imagí ve službě Azure Container Registry
8. Podle nastaveného plánu (každou noc) je vydáno kanál pro vydávání verzí.
9. Nejnovější image ze služby ACR je načetli, nasazené v clusteru Kubernetes ACS.
10. Žádosti uživatelů pro aplikace prochází DNS server.
11. DNS server předává požadavek na nástroj pro vyrovnávání zatížení a odešle odpověď zpět uživateli.

## <a name="next-steps"></a>Další postup
* Odkazovat [kurzu](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) sledovat podrobnosti a implementovat vlastní kanál CI/CD pro vaši aplikaci.

## <a name="references"></a>Odkazy
* [Vědecké zpracování týmových dat (TDSP)](https://aka.ms/tdsp)
* [Azure Machine Learning (AML)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Azure DevOps](https://www.visualstudio.com/vso/)
* [Služby Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)
