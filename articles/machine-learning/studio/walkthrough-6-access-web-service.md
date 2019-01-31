---
title: 'Krok 6: Přístup k webové službě – Azure Machine Learning Studio | Dokumentace Microsoftu'
description: 'Vývoj prediktivního řešení návod krok 6: Přístup k active webové službě Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: b84d3be06d1d560776a3fcc86668e20bf58d3b0b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467420"
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-studio-web-service"></a>Krok 6 Průvodce: Přístup k webové službě Azure Machine Learning Studio

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

