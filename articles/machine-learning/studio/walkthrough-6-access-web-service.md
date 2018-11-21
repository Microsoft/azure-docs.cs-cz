---
title: 'Krok 6: Přístup ke službě Machine Learning Web | Dokumentace Microsoftu'
description: 'Krok 6 vývoj prediktivního řešení návod: přístup k aktivní službě Azure Machine Learning Web.'
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: b71f98c9ba4e21f2a33b62977ec54f6230443e1b
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264144"
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Krok 6 průvodce: Přístup k webové službě Azure Machine Learning

Toto je poslední krok tohoto průvodce, [vývoj řešení prediktivní analýzy v Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Vytvoření pracovního prostoru Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Nahrání existujících dat](walkthrough-2-upload-data.md)
3. [Vytvoření nového experimentu](walkthrough-3-create-new-experiment.md)
4. [Natrénování a vyhodnocení modelů](walkthrough-4-train-and-evaluate-models.md)
5. [Nasazení webové služby](walkthrough-5-publish-web-service.md)
6. **Přístup k webové službě**

- - -
V předchozím kroku v tomto názorném postupu jsme nasadili webovou službu, která používá naše úvěrové riziko prediktivního modelu. Uživatelé teď budou moct odesílat data a zobrazí výsledky. 

Webová služba je webová služba Azure, který dokáže přijmout a vrátit data pomocí rozhraní REST API v jednom ze dvou způsobů:  

* **Žádost/odpověď** – uživatel odesílá jeden nebo více řádků daty o úvěrovém ke službě s použitím protokolu HTTP a služba jako odpověď vrátí jednu nebo více sad výsledků.
* **Spuštění dávky** – uživatel uloží jednu nebo více řádků daty o úvěrovém v Azure blob a pak odešle umístění objektu blob do služby. Služba získává všechny řádky dat do vstupního objektu blob, jiný objekt blob uloží výsledky a vrátí adresu URL tohoto kontejneru.  

Nejrychlejší a nejjednodušší způsob pro přístup k webové služby Classic, je prostřednictvím [webová aplikace Azure ML Request Response Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) nebo [Azure ML Batch spuštění služby webové aplikace šablona](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Tyto šablony webové aplikace můžete vytvářet vlastní webové aplikace, který zná vstupních dat webové služby a budou vracet. Všechno, co je třeba provést je poskytnout přístup k vaší webové služby a data a udělá zbytek šablony.

Další informace o použití šablony webové aplikace najdete v tématu [využívání služby Azure Machine Learning Web pomocí šablony webové aplikace](consume-web-service-with-web-app-template.md).

Můžete také vyvíjet vlastní aplikace pro přístup k webové službě pomocí počáteční kód pro vás k dispozici v R, C#, a programovací jazyky Python.

Najdete úplné podrobnosti v [jak využívat Azure Machine Learning webové služby](consume-web-services.md).

