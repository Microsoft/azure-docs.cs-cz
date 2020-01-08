---
title: Protokolování webové služby – Azure Machine Learning Studio (Classic) | Microsoft Docs
description: Naučte se, jak povolit protokolování pro webové služby Machine Learning Studio (Classic). Protokolování poskytuje další informace k řešení potíží se rozhraní API.
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
ms.openlocfilehash: 0b0dfeb6a19e2f6f24568de0b4712758d2b7ad4a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427401"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Povolit protokolování pro webové služby Azure Machine Learning Studio (Classic)
Tento dokument poskytuje informace o schopnostech protokolování webových služeb Machine Learning Studio (Classic). Protokolování poskytuje další informace, mimo jiné číslo chyby a zprávu, která vám může pomoct řešit vaše volání rozhraní API pro Machine Learning Studio (Classic).  

## <a name="how-to-enable-logging-for-a-web-service"></a>Jak povolit protokolování pro webové služby

Protokolování se povoluje na portálu [Web Services pro Azure Machine Learning Studio (Classic)](https://services.azureml.net) . 

1. Přihlaste se k portálu Web Services Azure Machine Learning Studio (Classic) na [https://services.azureml.net](https://services.azureml.net). U klasických webových služeb se můžete na portál dostat taky tak, že kliknete na **nové prostředí webové služby** na stránce Machine Learning Studio (klasické) webové služby v nástroji Studio (Classic).

   ![Nový odkaz webové služby prostředí](./media/web-services-logging/new-web-services-experience-link.png)

2. V horní nabídce klikněte na **webových služeb** nové webové služby, nebo klikněte na tlačítko **klasické webové služby** klasické webové služby.

   ![Vyberte nový nebo klasické webové služby](./media/web-services-logging/select-web-service.png)

3. Nové webové služby klikněte na název webové služby. Klasické webové služby klikněte na název webové služby a na další stránce klikněte na příslušný koncový bod.

4. V horní nabídce klikněte na **konfigurovat**.

5. Nastavte **povolit protokolování** umožňuje *chyba* (chyb do protokolu jenom) nebo *všechny* (pro úplné protokolování).

   ![Vyberte úroveň protokolování](./media/web-services-logging/enable-logging.png)

6. Klikněte na možnost **Uložit**.

7. U klasických webových služeb, vytvořte **ml-diagnostics** kontejneru.

   Všechny protokoly webové služby jsou uloženy v kontejneru objektů blob s názvem **ml-diagnostics** v účtu úložiště, které jsou přidružené k webové službě. Pro nové webové služby je tento kontejner vytvoří při prvním přístup k webové službě. U klasických webových služeb budete muset vytvořit kontejner, pokud ještě neexistuje. 

   1. V [webu Azure portal](https://portal.azure.com), přejděte na účet úložiště přidružený k webové službě.

   2. V části **Blob Service** klikněte na **Kontejnery**.

   3. Pokud kontejner **ml-diagnostics** neexistuje, klikněte na tlačítko **+ kontejner**, zadejte název "ml-diagnostics" kontejneru a pak vyberte **získat přístup k typu** jako "Blob". Klikněte na **OK**.

      ![Vytvořit nový kontejner pro uložení diagnostických protokolů](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> U klasických webových služeb má řídicí panel webové služby v Machine Learning Studio (Classic) také přepínač pro povolení protokolování. Ale protože protokolování je teď spravovaná prostřednictvím portálu webových služeb, je potřeba povolit protokolování na portálu, jak je popsáno v tomto článku. Pokud jste už povolili protokolování v nástroji Studio (Classic), pak na portálu Web Services zakažte protokolování a znovu ho povolte.


## <a name="the-effects-of-enabling-logging"></a>Účinky povolení protokolování
Pokud je povoleno protokolování diagnostiky a chyb z koncový bod webové služby jsou přihlášeni **ml-diagnostics** kontejneru objektů blob v účtu úložiště Azure propojené s pracovním prostorem uživatele. Tento kontejner obsahuje diagnostické informace pro všechny webové koncových bodů služby pro všechny pracovní prostory spojený s tímto účtem úložiště.

Protokoly můžete zobrazit některou z několika nástrojů dostupných k prozkoumání účtu služby Azure Storage. Nejjednodušší může být přejděte na účet úložiště na webu Azure Portal, klikněte na tlačítko **kontejnery**a potom klikněte na kontejner **ml-diagnostics**.  

## <a name="log-blob-detail-information"></a>Podrobné informace o protokolu objektů blob
Každý objekt blob v kontejneru obsahuje diagnostické informace pro právě jeden z následujících akcí:

* Provedení spuštění dávky – metoda  
* Spuštění metody typu žádost-odpověď  
* Inicializace kontejneru typu žádost-odpověď

Název každý objekt blob má předponu v následujícím formátu: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Kde _typ protokolu_ je jedním z následujících hodnot:  

* batch  
* skóre/požadavků  
* skóre/init  

