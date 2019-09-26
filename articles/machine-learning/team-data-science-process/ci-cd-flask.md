---
title: Vytvoření kanálu CI/CD pomocí procesu Azure Pipelines – tým pro datové vědy
description: Vytvořte kanál průběžné integrace a průběžného doručování pro aplikace Umělal Intelligence (AI) pomocí Docker a Kubernetes.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/06/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: f07ce8e8834a2804b6a5b7668718c8e6bff00fa6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260667"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Vytváření kanálů CI/CD pro aplikace AI pomocí Azure Pipelines, Docker a Kubernetes

Aplikace Umělal Intelligence (AI) je kód aplikace vložený s předvýukým modelem strojového učení (ML). Pro aplikaci AI jsou vždy dva proudy práce: Vědečtí data sestaví model ML a vývojáři aplikací sestaví aplikaci a zpřístupňují ji koncovým uživatelům, aby mohli využívat. Tento článek popisuje, jak implementovat kanál průběžné integrace a průběžného doručování (CI/CD) pro aplikaci AI, která vkládá model ML do zdrojového kódu aplikace. Vzorový kód a kurz použití jednoduché webové aplikace v Pythonu a načtení předvýukového modelu z privátního účtu úložiště Azure Blob. Můžete použít také účet úložiště AWS S3.

> [!NOTE]
> Následující postup je jedním z několika způsobů, jak provádět CI/CD. Existují alternativy těchto nástrojů a požadavků.

## <a name="source-code-tutorial-and-prerequisites"></a>Zdrojový kód, kurz a požadavky

[Zdrojový kód](https://github.com/Azure/DevOps-For-AI-Apps) a [podrobný kurz](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) si můžete stáhnout z GitHubu. Podle pokynů v kurzu implementujte kanál CI/CD pro vaši vlastní aplikaci.

Chcete-li použít stažený zdrojový kód a kurz, potřebujete následující požadavky: 

- [Zdrojové úložiště zdrojového kódu](https://github.com/Azure/DevOps-For-AI-Apps) rozvětvené do vašeho účtu GitHubu
- [Organizace Azure DevOps](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Cluster Azure Container Service pro Kubernetes (AKS)](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) spuštění příkazů a načtení konfigurace z clusteru AKS 
- [Účet Azure Container Registry (ACR)](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>Shrnutí kanálu CI/CD

Každé nové potvrzení Git se zahájí v kanálu sestavení. Sestavení bezpečně načte nejnovější model ML z účtu služby Blob Storage a zabalí ho do kódu aplikace v jednom kontejneru. Toto oddělení vývoje aplikací a data vědy workstreams zajišťuje, aby produkční aplikace vždy běžela nejnovější kód s nejnovějším modelem ML. Pokud aplikace projde testováním, kanál bezpečně uloží image sestavení do kontejneru Docker v ACR. Kanál verze pak nasadí kontejner pomocí AKS. 

## <a name="cicd-pipeline-steps"></a>Postup CI/CD kanálu

Následující diagram a kroky popisují architekturu kanálu CI/CD:

![Architektura kanálu CI/CD](./media/ci-cd-flask/architecture.png)

1. Vývojáři pracují na kódu aplikace v integrovaném vývojovém prostředí (IDE) podle svého výběru.
2. Vývojáři tento kód potvrdí do Azure Repos, GitHubu nebo jiného poskytovatele správy zdrojů Git. 
3. Pracovníci s daty pracují samostatně na vývoji jejich modelu ML.
4. Vědečtí data publikují dokončený model do úložiště modelu, v tomto případě účet úložiště BLOB. 
5. Azure Pipelines vypíná sestavení na základě potvrzení Git.
6. Kanál sestavení vyžádá nejnovější model ML z úložiště objektů BLOB a vytvoří kontejner.
7. Kanál sestaví image sestavení do úložiště privátních imagí v ACR.
8. Kanál verze se odstartuje na základě úspěšného sestavení.
9. Kanál si vyžádá poslední obrázek z ACR a nasadí ho napříč clusterem Kubernetes v AKS.
10. Žádosti uživatelů o aplikaci procházejí serverem DNS.
11. Server DNS předává požadavky do nástroje pro vyrovnávání zatížení a odesílá odpovědi zpět uživatelům.

## <a name="see-also"></a>Viz také:

- [Vědecké zpracování týmových dat (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure Machine Learning (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Služby Azure Kubernetes (AKS)](/azure/aks/intro-kubernetes)
