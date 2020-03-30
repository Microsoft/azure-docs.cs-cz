---
title: Protokolování webové služby – Azure Machine Learning Studio (klasické) | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit protokolování pro webové služby Machine Learning Studio (klasické). Protokolování obsahuje další informace, které vám pomohou řešit řešení potíží s řešením potíží s řešením potíží s řešením potíží s řešením potíží.
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
ms.openlocfilehash: 90e7692fe0e254074d8176d719d0ca9abad54a9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217847"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Povolení protokolování pro webové služby Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Tento dokument obsahuje informace o možnosti protokolování machine learningových studio (klasické) webové služby. Protokolování poskytuje další informace, kromě pouze číslo chyby a zprávy, které vám pomohou vyřešit vaše volání machine learning studio (klasické) rozhraní API.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Povolení protokolování pro webovou službu

Povolíte protokolování z portálu [webových služeb Azure Machine Learning Studio (klasické).](https://services.azureml.net) 

1. Přihlaste se k portálu Azure Machine [https://services.azureml.net](https://services.azureml.net)Learning Studio (klasické) webové služby na adrese . U klasické webové služby se můžete také dostat na portál kliknutím na **nové prostředí webových služeb** na stránce Machine Learning Studio (klasické) webové služby ve studiu (klasické).

   ![Odkaz Nové prostředí webových služeb](./media/web-services-logging/new-web-services-experience-link.png)

2. Na horním řádku nabídek klikněte na **webové služby** pro novou webovou službu nebo na **položku Klasické webové služby** pro klasickou webovou službu.

   ![Vybrat nové nebo klasické webové služby](./media/web-services-logging/select-web-service.png)

3. V případě nové webové služby klepněte na název webové služby. U klasické webové služby klikněte na název webové služby a na další stránce klikněte na příslušný koncový bod.

4. Na horním řádku nabídek klepněte na **tlačítko Konfigurovat**.

5. Nastavte možnost **Povolit protokolování** na *Chyba* (chcete-li protokolovat pouze chyby) nebo *Vše* (pro úplné protokolování).

   ![Vybrat úroveň protokolování](./media/web-services-logging/enable-logging.png)

6. Klikněte na **Uložit**.

7. Pro klasické webové služby vytvořte kontejner **ml-diagnostics.**

   Všechny protokoly webové služby jsou uloženy v kontejneru objektů blob s názvem **ml diagnostiky** v účtu úložiště přidružené k webové službě. Pro nové webové služby je tento kontejner vytvořen při prvním přístupu k webové službě. Pro klasické webové služby je třeba vytvořit kontejner, pokud ještě neexistuje. 

   1. Na [webu Azure Portal](https://portal.azure.com)přejděte na účet úložiště přidružený k webové službě.

   2. V části **Blob Service** klikněte na **Kontejnery**.

   3. Pokud **diagnostika ml** kontejneru neexistuje, klepněte na tlačítko **+Kontejner**, přiřazujte kontejneru název "ml-diagnostics" a vyberte **typ aplikace Access** jako "Blob". Klikněte na tlačítko **OK**.

      ![Vytvoření nového kontejneru pro uložení diagnostických protokolů](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Pro klasickou webovou službu má řídicí panel webových služeb v machine learningovém studiu (klasické) také přepínač umožňující protokolování. Protože je však protokolování nyní spravováno prostřednictvím portálu webových služeb, je třeba povolit protokolování prostřednictvím portálu, jak je popsáno v tomto článku. Pokud jste již povolili protokolování v aplikaci Studio (klasické), zakažte protokolování a znovu jej povolte.


## <a name="the-effects-of-enabling-logging"></a>Účinky povolení protokolování
Pokud je protokolování povoleno, diagnostika a chyby z koncového bodu webové služby jsou zaznamenány v kontejneru objektů blob **diagnostiky ml** v účtu úložiště Azure propojeného s pracovním prostorem uživatele. Tento kontejner obsahuje všechny diagnostické informace pro všechny koncové body webové služby pro všechny pracovní prostory přidružené k tomuto účtu úložiště.

Protokoly lze zobrazit pomocí některého z několika nástrojů, které jsou k dispozici prozkoumat účet úložiště Azure. Nejjednodušší může být přejít na účet úložiště na webu Azure Portal, kliknout na **kontejnery**a potom kliknout na **diagnostiku ml kontejneru**.  

## <a name="log-blob-detail-information"></a>Podrobné informace o objektu blob protokolu
Každý objekt blob v kontejneru obsahuje diagnostické informace přesně pro jednu z následujících akcí:

* Spuštění metody dávkového spuštění  
* Spuštění metody Požadavek odpověď  
* Inicializace kontejneru požadavku a odpovědi

Název každého objektu blob má předponu následujícího formuláře: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Kde _Log typ_ je jedna z následujících hodnot:  

* dávka  
* skóre/žádosti  
* skóre/init  

