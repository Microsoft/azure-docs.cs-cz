---
title: Protokolování webové služby – Azure Machine Learning Studio (Classic) | Microsoft Docs
description: Naučte se, jak povolit protokolování pro webové služby Machine Learning Studio (Classic). Protokolování poskytuje další informace, které vám pomůžou při řešení potíží s rozhraními API.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 90ee036275e13b209d9f6c37aba6d16ed564cf5d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466962"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Povolit protokolování pro webové služby Azure Machine Learning Studio (Classic)
Tento dokument poskytuje informace o schopnostech protokolování webových služeb Machine Learning Studio (Classic). Protokolování poskytuje další informace, mimo jiné číslo chyby a zprávu, která vám může pomoct řešit vaše volání rozhraní API pro Machine Learning Studio (Classic).  

## <a name="how-to-enable-logging-for-a-web-service"></a>Postup povolení protokolování pro webovou službu

Protokolování se povoluje na portálu [Web Services pro Azure Machine Learning Studio (Classic)](https://services.azureml.net) . 

1. Přihlaste se k portálu Web Services Azure Machine Learning Studio (Classic) na [https://services.azureml.net](https://services.azureml.net). U klasických webových služeb se můžete na portál dostat taky tak, že kliknete na **nové webové služby** na stránce Machine Learning Studio (klasické) webové služby v klasické verzi Machine Learning Studio.

   ![Odkaz na nový Web Services – odkaz](./media/web-services-logging/new-web-services-experience-link.png)

2. V horním řádku nabídek klikněte na **webové služby** pro novou webovou službu nebo klikněte na **klasické webové** služby pro klasickou webovou službu.

   ![Výběr nových nebo klasických webových služeb](./media/web-services-logging/select-web-service.png)

3. Novou webovou službu získáte tak, že kliknete na název webové služby. U klasických webových služeb klikněte na název webové služby a potom na další stránce klikněte na příslušný koncový bod.

4. V horním řádku nabídek klikněte na **Konfigurovat**.

5. Nastavte možnost **Povolit protokolování** na hodnotu *Chyba* (Pokud chcete protokolovat pouze chyby) nebo *vše* (pro úplné protokolování).

   ![Vybrat úroveň protokolování](./media/web-services-logging/enable-logging.png)

6. Klikněte na **Uložit**.

7. V případě klasických webových služeb vytvořte kontejner **ml-Diagnostics** .

   Všechny protokoly webových služeb se uchovávají v kontejneru objektů BLOB s názvem **ml-Diagnostics** v účtu úložiště přidruženém k webové službě. Pro nové webové služby se tento kontejner vytvoří při prvním přístupu k webové službě. U klasických webových služeb je potřeba kontejner vytvořit, pokud ještě neexistuje. 

   1. V [Azure Portal](https://portal.azure.com)přejdete do účtu úložiště přidruženého k webové službě.

   2. V části **Blob Service** klikněte na **Kontejnery**.

   3. Pokud kontejner **ml-Diagnostics** neexistuje, klikněte na **+ kontejner**, udělte kontejneru název "ml-Diagnostics" a vyberte **typ přístupu** jako "blob". Klikněte na **OK**.

      ![Vytvořit nový kontejner pro uložení diagnostických protokolů](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> U klasických webových služeb má řídicí panel webové služby v klasické verzi Machine Learning Studio také přepínač pro povolení protokolování. Protože je ale protokolování teď spravované prostřednictvím portálu Web Services, musíte povolit protokolování přes portál, jak je popsáno v tomto článku. Pokud jste už povolili protokolování v nástroji Studio (Classic), pak na portálu Web Services zakažte protokolování a znovu ho povolte.


## <a name="the-effects-of-enabling-logging"></a>Účinky povolení protokolování
Když je povolené protokolování, diagnostika a chyby z koncového bodu webové služby se zaprotokolují do kontejneru objektů BLOB **ml diagnostiky** v účtu Azure Storage, který je propojený s pracovním prostorem uživatele. Tento kontejner obsahuje všechny diagnostické informace pro všechny koncové body webové služby pro všechny pracovní prostory, které jsou přidružené k tomuto účtu úložiště.

Protokoly lze zobrazit pomocí kteréhokoli z několika nástrojů, které jsou k dispozici pro prozkoumání Azure Storageho účtu. Nejjednodušší možná bude přejít na účet úložiště v Azure Portal, kliknout na **kontejnery**a pak kliknout na kontejner **ml-Diagnostika**.  

## <a name="log-blob-detail-information"></a>Podrobné informace o objektu BLOB protokolu
Každý objekt BLOB v kontejneru obsahuje diagnostické informace pro přesně jednu z následujících akcí:

* Provedení metody batch-execution  
* Provedení metody požadavek-odpověď  
* Inicializace kontejneru požadavek-odpověď

Název každého objektu BLOB má předponu následujícího tvaru: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


_Typ protokolu_ je jedna z následujících hodnot:  

* batch  
* skóre a požadavky  
* skóre/inicializace  

