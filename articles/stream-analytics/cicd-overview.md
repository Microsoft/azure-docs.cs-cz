---
title: Průběžná integrace a nasazování pro Azure Stream Analytics
description: Tento článek obsahuje přehled kanálu průběžné integrace a nasazování (CI/CD) pro Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: ec8f27d0376f7187fd36b3feba556dbced0946e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935201"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Průběžná integrace a nasazování (CI/CD) pro Azure Stream Analytics

Úlohu Azure Stream Analytics můžete průběžně nasadit pomocí integrace správy zdrojového kódu. Integrace správy zdrojového kódu umožňuje pracovní postup, ve kterém aktualizace kódu spouští nasazení prostředků do Azure. Tento článek popisuje základní kroky pro vytvoření kanálu průběžné integrace a nasazování (CI/CD).

Pokud začínáte Azure Stream Analytics, začněte s [Azure Stream Analytics rychlý Start](stream-analytics-quick-create-portal.md).

## <a name="create-a-cicd-pipeline"></a>Vytvoření kanálu CI/CD

Při vytváření kanálu CI/CD pro Stream Analytics postupujte podle pokynů v tomto průvodci.

1. Vytvořte dotaz Azure Stream Analytics.

   K [vývoji a testování dotazů lokálně](develop-locally.md)použijte Azure Stream Analytics nástroje pro [Visual Studio Code](quick-create-vs-code.md) nebo [Visual Studio](stream-analytics-quick-create-vs.md) . [Existující úlohu](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) můžete také exportovat do místního projektu.

2. Potvrďte své Azure Stream Analytics projekty do systému správy zdrojového kódu, jako je úložiště Git.

3. Pomocí [Azure Stream Analytics nástrojů CI/CD](cicd-tools.md) můžete vytvářet projekty a generovat šablony pro správu prostředků Azure pro nasazení.

4. Spusťte [automatizované testy skriptu](cicd-tools.md#automated-test) pro regresi kvality.

5. [Nasaďte úlohu](cicd-tools.md#deploy-to-azure) automaticky do Azure.

## <a name="auto-build-test-and-deploy"></a>Automatické sestavení, testování a nasazení

K automatickému sestavování, testování a nasazování můžete použít příkazový řádek a [Azure Stream Analytics nástroje CI/CD](cicd-tools.md) . V [Azure Pipelines](set-up-cicd-pipeline.md)můžete také nastavit kanál CI/CD. Azure Pipelines k povolení pokročilejších funkcí, jako je například Správa kanálů, vizualizace a triggery.

## <a name="next-steps"></a>Další kroky

* [Automatizace sestavení, testování a nasazení Azure Stream Analytics úlohy pomocí nástrojů CI/CD](cicd-tools.md)
* [Nastavení kanálu CI/CD pro Stream Analytics úlohy pomocí Azure Pipelines](set-up-cicd-pipeline.md)
