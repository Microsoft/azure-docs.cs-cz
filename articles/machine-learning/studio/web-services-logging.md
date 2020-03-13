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
ms.openlocfilehash: 90e7692fe0e254074d8176d719d0ca9abad54a9b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217847"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Povolit protokolování pro webové služby Azure Machine Learning Studio (Classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Tento dokument poskytuje informace o schopnostech protokolování webových služeb Machine Learning Studio (Classic). Protokolování poskytuje další informace, mimo jiné číslo chyby a zprávu, která vám může pomoct řešit vaše volání rozhraní API pro Machine Learning Studio (Classic).  

## <a name="how-to-enable-logging-for-a-web-service"></a>Jak povolit protokolování pro webové služby

Protokolování se povoluje na portálu [Web Services pro Azure Machine Learning Studio (Classic)](https://services.azureml.net) . 

1. Přihlaste se k portálu Web Services Azure Machine Learning Studio (Classic) na [https://services.azureml.net](https://services.azureml.net). U klasických webových služeb se můžete na portál dostat taky tak, že kliknete na **nové prostředí webové služby** na stránce Machine Learning Studio (klasické) webové služby v nástroji Studio (Classic).

   ![Nový odkaz webové služby prostředí](./media/web-services-logging/new-web-services-experience-link.png)

2. V horním řádku nabídek klikněte na **webové služby** pro novou webovou službu nebo klikněte na **klasické webové** služby pro klasickou webovou službu.

   ![Vyberte nový nebo klasické webové služby](./media/web-services-logging/select-web-service.png)

3. Nové webové služby klikněte na název webové služby. Klasické webové služby klikněte na název webové služby a na další stránce klikněte na příslušný koncový bod.

4. V horním řádku nabídek klikněte na **Konfigurovat**.

5. Nastavte možnost **Povolit protokolování** na hodnotu *Chyba* (Pokud chcete protokolovat pouze chyby) nebo *vše* (pro úplné protokolování).

   ![Vyberte úroveň protokolování](./media/web-services-logging/enable-logging.png)

6. Klikněte na **Uložit**.

7. V případě klasických webových služeb vytvořte kontejner **ml-Diagnostics** .

   Všechny protokoly webových služeb se uchovávají v kontejneru objektů BLOB s názvem **ml-Diagnostics** v účtu úložiště přidruženém k webové službě. Pro nové webové služby je tento kontejner vytvoří při prvním přístup k webové službě. U klasických webových služeb budete muset vytvořit kontejner, pokud ještě neexistuje. 

   1. V [Azure Portal](https://portal.azure.com)přejdete do účtu úložiště přidruženého k webové službě.

   2. V části **Blob Service** klikněte na **Kontejnery**.

   3. Pokud kontejner **ml-Diagnostics** neexistuje, klikněte na **+ kontejner**, udělte kontejneru název "ml-Diagnostics" a vyberte **typ přístupu** jako "blob". Klikněte na tlačítko **OK**.

      ![Vytvořit nový kontejner pro uložení diagnostických protokolů](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> U klasických webových služeb má řídicí panel webové služby v Machine Learning Studio (Classic) také přepínač pro povolení protokolování. Ale protože protokolování je teď spravovaná prostřednictvím portálu webových služeb, je potřeba povolit protokolování na portálu, jak je popsáno v tomto článku. Pokud jste už povolili protokolování v nástroji Studio (Classic), pak na portálu Web Services zakažte protokolování a znovu ho povolte.


## <a name="the-effects-of-enabling-logging"></a>Účinky povolení protokolování
Když je povolené protokolování, diagnostika a chyby z koncového bodu webové služby se zaprotokolují do kontejneru objektů BLOB **ml diagnostiky** v účtu Azure Storage, který je propojený s pracovním prostorem uživatele. Tento kontejner obsahuje diagnostické informace pro všechny webové koncových bodů služby pro všechny pracovní prostory spojený s tímto účtem úložiště.

Protokoly můžete zobrazit některou z několika nástrojů dostupných k prozkoumání účtu služby Azure Storage. Nejjednodušší možná bude přejít na účet úložiště v Azure Portal, kliknout na **kontejnery**a pak kliknout na kontejner **ml-Diagnostika**.  

## <a name="log-blob-detail-information"></a>Podrobné informace o protokolu objektů blob
Každý objekt blob v kontejneru obsahuje diagnostické informace pro právě jeden z následujících akcí:

* Provedení spuštění dávky – metoda  
* Spuštění metody typu žádost-odpověď  
* Inicializace kontejneru typu žádost-odpověď

Název každý objekt blob má předponu v následujícím formátu: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


_Typ protokolu_ je jedna z následujících hodnot:  

* dávka  
* skóre/požadavků  
* skóre/init  

