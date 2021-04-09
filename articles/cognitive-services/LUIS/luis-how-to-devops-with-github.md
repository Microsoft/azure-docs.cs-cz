---
title: Jak použít DevOps s GitHubem-LUIS
titleSuffix: Azure Cognitive Services
description: Použijte DevOps s využitím Language Understanding (LUIS) a GitHubu.
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: 448b3d93ed58e4cfc73da576f0c5871600400ac6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98019835"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>Použití DevOps pro vývoj aplikací LUIS pomocí akcí GitHubu

Pro úplné řešení, které implementuje DevOps a osvědčené postupy pro LUIS, použijte [úložiště šablon Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) . Pomocí tohoto úložiště šablon můžete vytvořit vlastní úložiště s integrovanou podporou pro pracovní postupy CI/CD a postupy, které umožňují správu [zdrojového kódu](luis-concept-devops-sourcecontrol.md), [automatizované](luis-concept-devops-automation.md)sestavování, [testování](luis-concept-devops-testing.md)a [správu](luis-concept-devops-automation.md#release-management) vydaných verzí s Luis pro váš vlastní projekt.

## <a name="the-luis-devops-template-repo"></a>Úložiště šablon DevOps LUIS

[Úložiště šablon Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) vás provede těmito postupy:

* **Naklonujte úložiště šablon** – Zkopírujte šablonu do vlastního úložiště GitHub.
* **Konfigurace prostředků Luis** – vytvořte [Luis pro vytváření obsahu a předpověď prostředků v Azure](./luis-how-to-azure-subscription.md) , které budou využívat pracovní postupy průběžné integrace.
* **Nakonfigurujte pracovní postupy CI/CD** – nakonfigurujte parametry pro pracovní postupy CI/CD a uložte je do [tajných kódů GitHubu](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).
* **Provede vás ["vývojovým vnitřním smyčkou"](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)** – vývojář provede aktualizace ukázkové aplikace Luis při práci ve vývojové větvi, otestuje aktualizace a potom vyvolá žádost o přijetí změn, aby navrhla změny a hledal schválení kontroly.
* **Spouštění pracovních postupů CI/CD** – spuštění [pracovních postupů průběžné integrace pro sestavení a otestování aplikace Luis](luis-concept-devops-automation.md) pomocí akcí GitHubu.
* **Automatizované testování** – proveďte [automatizované dávkové testování pro aplikaci Luis](luis-concept-devops-testing.md) , která vám umožní vyhodnotit kvalitu aplikace.
* **Nasazení aplikace pro Luis** – spuštění [úlohy průběžného doručování (CD)](luis-concept-devops-automation.md#continuous-delivery-cd) pro publikování aplikace Luis
* **Použijte úložiště s vlastním projektem** – vysvětluje, jak používat úložiště s vlastní aplikací Luis.

## <a name="next-steps"></a>Další kroky

* Použijte [úložiště šablon Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) pro použití DevOps s vlastním projektem.
* [Správa zdrojového kódu a strategie větvení pro LUIS](luis-concept-devops-sourcecontrol.md)
* [Testování pro LUIS DevOps](luis-concept-devops-testing.md)
* [Pracovní postupy automatizace pro LUIS DevOps](luis-concept-devops-automation.md)
