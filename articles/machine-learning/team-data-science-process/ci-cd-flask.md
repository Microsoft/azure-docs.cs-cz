---
title: Vytvoření kanálu CI/CD s azure kanály – proces vědecké ho řízení týmových dat
description: Vytvořte průběžnou integraci a kanál průběžného doručování pro aplikace umělé inteligence (AI) pomocí Dockeru a Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721825"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Vytváření kanálů CI/CD pro aplikace AI pomocí Azure Pipelines, Dockeru a Kubernetes

Aplikace umělé inteligence (AI) je kód aplikace vložený s předem vyškoleným modelem strojového učení (ML). Pro aplikaci AI jsou vždy dva proudy práce: Datoví vědci vytvářejí model ML a vývojáři aplikací vytvářejí aplikaci a zveřejňují ji koncovým uživatelům, aby ji spotřebovávali. Tento článek popisuje, jak implementovat průběžné integrace a průběžné doručování (CI/CD) kanálu pro aplikaci AI, která vloží model ML do zdrojového kódu aplikace. Ukázkový kód a kurz použít webovou aplikaci Python Flask a načíst předem trénovaný model z účtu úložiště objektů blob privátní Azure. Můžete také použít účet úložiště AWS S3.

> [!NOTE]
> Následující proces je jedním z několika způsobů, jak provést CI/CD. Existují alternativy k tomuto nástroji a předpoklady.

## <a name="source-code-tutorial-and-prerequisites"></a>Zdrojový kód, kurz a předpoklady

Můžete si stáhnout [zdrojový kód](https://github.com/Azure/DevOps-For-AI-Apps) a [podrobný kurz](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) z GitHubu. Postupujte podle pokynů k implementaci kanálu CI/CD pro vlastní aplikaci.

Chcete-li použít stažený zdrojový kód a kurz, potřebujete následující předpoklady: 

- [Úložiště zdrojového kódu](https://github.com/Azure/DevOps-For-AI-Apps) se přetavělo na váš účet GitHub
- [Organizace Azure DevOps](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- Služba [Azure Container Service pro cluster Kubernetes (AKS)](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) pro spouštění příkazů a načítání konfigurace z clusteru AKS 
- [Účet registru kontejnerů Azure (ACR)](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>Souhrn kanálu CI/CD

Každá nová revize Gitu spustí kanál sestavení. Sestavení bezpečně vytáhne nejnovější model ML z účtu úložiště objektů blob a zabalí ho s kódem aplikace v jednom kontejneru. Toto oddělení vývoje aplikací a datových vědeckých toků zajišťuje, že produkční aplikace vždy používá nejnovější kód s nejnovějším modelem ML. Pokud aplikace projde testováním, kanál bezpečně uloží image sestavení v kontejneru Dockeru v ACR. Kanál vydání pak nasazuje kontejner pomocí AKS. 

## <a name="cicd-pipeline-steps"></a>Kroky kanálu CI/CD

Následující diagram a kroky popisují architekturu kanálu CI/CD:

![Architektura kanálu CI/CD](./media/ci-cd-flask/architecture.png)

1. Vývojáři pracují na kódu aplikace v rozhraní IDE podle svého výběru.
2. Vývojáři potvrdí kód azure úložiště, GitHub, nebo jiného poskytovatele správy zdrojového kódu Git. 
3. Samostatně pracují vědci zabývající se daty na vývoji svého modelu ML.
4. Datoví vědci publikují dokončený model do úložiště modelu, v tomto případě účet úložiště objektů blob. 
5. Azure Pipelines spustí sestavení na základě potvrzení Gitu.
6. Sestavení kanálu natáhne nejnovější model ML z úložiště objektů blob a vytvoří kontejner.
7. Kanál odešle image sestavení do úložiště soukromých bitových obrázků v ACR.
8. Kanál vydání se spustí na základě úspěšného sestavení.
9. Kanál natáhne nejnovější image z ACR a nasadí ji přes cluster Kubernetes na AKS.
10. Požadavky uživatelů na aplikaci procházejí serverem DNS.
11. Server DNS předá požadavky správci zatížení a odešle odpovědi zpět uživatelům.

## <a name="see-also"></a>Viz také

- [Proces vědecké ho spoje týmových dat (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure Machine Learning (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Služby Azure Kubernetes (AKS)](/azure/aks/intro-kubernetes)
