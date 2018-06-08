---
title: 'DevOps pro aplikace umělé Intelligence (AI): vytvoření kanálu nepřetržité integrace v Azure pomocí Docker, Kubernetes & Python Flask aplikace'
description: 'DevOps pro aplikace umělé Intelligence (AI): vytvoření kanálu nepřetržité integrace v Azure pomocí Docker a Kubernetes'
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
ms.openlocfilehash: 233da393bb9e030d885ce588f4841dc1c707c1cb
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836262"
---
# <a name="devops-for-artificial-intelligence-ai-applications-creating-continuous-integration-pipeline-on-azure-using-docker-and-kubernetes"></a>DevOps pro aplikace umělé Intelligence (AI): vytvoření kanálu průběžnou integraci v Azure pomocí Docker a Kubernetes
Pro aplikaci AI jsou často dvě datové proudy práce, datových vědců vytváření modelů machine learning a vývojáři aplikací vytváření aplikace, která bude vystavená koncovým uživatelům využívat. V tomto článku jsme ukazují, jak implementovat nepřetržité integrace (CI) / nepřetržité doručení (CD) kanálu pro aplikaci AI. AI aplikace je kombinace kódu aplikace vložených s modelem pretrained machine learning (ML). V tomto článku jsme jsou načítání pretrained modelu z účtu úložiště Azure blob privátní, může to být účet AWS S3. Pro článek budeme používat jednoduché python flask webové aplikace.

> [!NOTE]
> Toto je jedna z několik způsobů, jimiž lze provést CI/CD. Neexistují nějaké alternativy nástroje a další požadavky uvedené níže. Když jsme vyvíjet další obsah, budeme publikovat do těch.
>
>

## <a name="github-repository-with-document-and-code"></a>Úložiště GitHub se dokument a kódem
Si můžete stáhnout zdrojového kódu z [Githubu](https://github.com/Azure/DevOps-For-AI-Apps). A [podrobný kurz](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) je také k dispozici.

## <a name="pre-requisites"></a>Požadavky
Tady jsou požadavky na následující CI/CD kanálu popsané dál:
* [Visual Studio Team Services účtu](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student)
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure Container Service (AKS) clusteru se systémem Kubernetes](https://docs.microsoft.com/en-us/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Účet Azure kontejneru registru (ACR)](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal)
* [Nainstalujte Kubectl ke spuštění příkazů oproti Kubernetes clusteru.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Jsme budete potřebovat k načtení konfigurace z clusteru služby ACS. 
* Větev úložiště k účtu Githubu.

## <a name="description-of-the-cicd-pipeline"></a>Popis kanálu CI/CD
Zejména kopance kanálu vypnutí pro každý nový potvrzení spustit testovací sadu, pokud předává testovací trvá na nejnovější verzi balíčků ho v kontejner Docker. Kontejner se pak nasazuje pomocí Azure container service (ACS) a image jsou bezpečně uložené v registru kontejner Azure (ACR). ACS běží Kubernetes pro správu clusteru kontejneru, ale můžete vybrat Docker Swarm nebo Mesos.

Aplikace bezpečně vrátí nejnovější model z účtu Azure Storage a balíčky, které jako součást aplikace. Nasazené aplikace má kód aplikace a zabalené jako jediný kontejner modelu ML. To oddělí v aplikaci vývojáři a data vědců, abyste měli jistotu, že jejich produkční aplikace bude vždy spuštěn nejnovější kód s nejnovější modelu ML.

Architektura kanálu je uveden níže. 

![Architektura](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Postup konfigurace disku CD kanálu
1. Vývojáře fungovat na sběrnici IDE, které si sami vyberou v kódu aplikace.
2. Jejich Potvrdit kód do správy zdrojového kódu, které si sami vyberou (služby VSTS má dobrou podpora pro ovládací prvky různé zdroje)
3. Samostatně fungovat vědecký pracovník data týkající se vývoje jejich modelu.
4. Jakmile radostí, že publikují modelu do modelu úložiště, v takovém případě se používá účet úložiště blob. To může snadno vyměnit službou management modelu Azure ML Workbench pomocí příslušných rozhraní API REST.
5. Sestavení je spuštěna v závislosti na potvrzení v Githubu služby VSTS.
6. Služby VSTS sestavení kanálu vrátí poslední model z kontejneru objektů Blob a vytvoří kontejner.
7. Služby VSTS sami bitovou kopii do úložiště privátní bitové kopie v registru kontejner Azure
8. Podle nastaveného plánu (noční) je spuštěna verze kanálu.
9. Nejnovější bitovou kopii z ACR je vyžádat a nasadit v clusteru Kubernetes na služby ACS.
10. Žádosti uživatelů pro aplikace projde DNS server.
11. DNS server předává požadavek na službu Vyrovnávání zatížení a odešle odpověď zpět na uživatele.

## <a name="next-steps"></a>Další postup
* Odkazovat [kurzu]((https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)) postupujte podle podrobnosti a implementovat vlastní kanál CI nebo CD pro vaši aplikaci.

## <a name="references"></a>Odkazy
* [Proces vědecké účely dat Team (TDSP)](https://aka.ms/tdsp)
* [Azure Machine Learning (AML)](https://docs.microsoft.com/en-us/azure/machine-learning/service/)
* [Visual Studio Team Services (služby VSTS)](https://www.visualstudio.com/vso/)
* [Služby Azure Kubernetes (AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes)