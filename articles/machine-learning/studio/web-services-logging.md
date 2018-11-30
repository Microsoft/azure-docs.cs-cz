---
title: Protokolování pro webové služby Machine Learning Studio | Dokumentace Microsoftu
description: Informace o povolení protokolování pro webové služby Machine Learning. Protokolování poskytuje další informace k řešení potíží se rozhraní API.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.openlocfilehash: 5ac8712b1f5dbea4ed3225e87b1a6f51312c3529
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310408"
---
# <a name="enable-logging-for-machine-learning-studio-web-services"></a>Povolení protokolování pro webové služby Machine Learning Studio
Tento dokument obsahuje informace o možnosti protokolování webových služeb Machine Learning. Protokolování poskytuje další informace, jenom číslo chyby a zprávy, které vám můžou pomoct vyřešit vaše volání rozhraní API pro Machine Learning.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Jak povolit protokolování pro webové služby

Povolení protokolování z [Azure Machine Learning Web Services](https://services.azureml.net) portálu. 

1. Přihlaste se k portálu Azure Machine Learning Web Services na adrese [ https://services.azureml.net ](https://services.azureml.net). Pro webové služby Classic, můžete také získáte k portálu kliknutím **nové webové služby prostředí** na stránce Machine Learning Web Services v nástroji Machine Learning Studio.

   ![Nový odkaz webové služby prostředí](./media/web-services-logging/new-web-services-experience-link.png)

2. V horní nabídce klikněte na **webových služeb** nové webové služby, nebo klikněte na tlačítko **klasické webové služby** klasické webové služby.

   ![Vyberte nový nebo klasické webové služby](./media/web-services-logging/select-web-service.png)

3. Nové webové služby klikněte na název webové služby. Klasické webové služby klikněte na název webové služby a na další stránce klikněte na příslušný koncový bod.

4. V horní nabídce klikněte na **konfigurovat**.

5. Nastavte **povolit protokolování** umožňuje *chyba* (chyb do protokolu jenom) nebo *všechny* (pro úplné protokolování).

   ![Vyberte úroveň protokolování](./media/web-services-logging/enable-logging.png)

6. Klikněte na **Uložit**.

7. U klasických webových služeb, vytvořte **ml-diagnostics** kontejneru.

   Všechny protokoly webové služby jsou uloženy v kontejneru objektů blob s názvem **ml-diagnostics** v účtu úložiště, které jsou přidružené k webové službě. Pro nové webové služby je tento kontejner vytvoří při prvním přístup k webové službě. U klasických webových služeb budete muset vytvořit kontejner, pokud ještě neexistuje. 

   1. V [webu Azure portal](https://portal.azure.com), přejděte na účet úložiště přidružený k webové službě.

   2. V části **Blob Service** klikněte na **Kontejnery**.

   3. Pokud kontejner **ml-diagnostics** neexistuje, klikněte na tlačítko **+ kontejner**, zadejte název "ml-diagnostics" kontejneru a pak vyberte **získat přístup k typu** jako "Blob". Klikněte na **OK**.

      ![Vyberte úroveň protokolování](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Pro webové služby Classic řídicím panelu webové služby v nástroji Machine Learning Studio obsahuje také přepnout na povolení protokolování. Ale protože protokolování je teď spravovaná prostřednictvím portálu webových služeb, je potřeba povolit protokolování na portálu, jak je popsáno v tomto článku. Pokud jste nepovolili protokolování v sadě Studio, na webovém portálu služby, volitelný zákaz protokolování a znovu ji povolte.


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

* dávka  
* skóre/požadavků  
* skóre/init  

