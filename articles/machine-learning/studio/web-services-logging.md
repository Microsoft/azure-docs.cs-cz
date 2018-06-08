---
title: Protokolování pro webové služby Machine Learning | Microsoft Docs
description: Informace o povolení protokolování pro webové služby Machine Learning. Protokolování poskytuje další informace k řešení rozhraní API.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
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
ms.openlocfilehash: 4e1545c8fd05795c683b24c029376a3d1e6d85b8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835827"
---
# <a name="enable-logging-for-machine-learning-web-services"></a>Povolení protokolování webových služeb Machine Learning
Tento dokument obsahuje informace o možnosti protokolování webových služeb Machine Learning. Protokolování poskytuje další informace, kromě právě číslo chyby a zprávu, která vám pomohou vyřešit váš volání rozhraní API Machine Learning.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Povolení protokolování pro webovou službu

Povolit protokolování z [webové služby Azure Machine Learning](https://services.azureml.net) portálu. 

1. Přihlaste se k portálu webové služby Azure Machine Learning na [ https://services.azureml.net ](https://services.azureml.net). Pro webovou službu Classic, také získáte na portál kliknutím **nové webové služby prostředí** na stránku webové služby Machine Learning v Machine Learning Studio.

   ![Nový odkaz webové služby prostředí](./media/web-services-logging/new-web-services-experience-link.png)

2. Na panelu horní nabídce klikněte na tlačítko **webové služby** pro nové webové služby, nebo klikněte na tlačítko **Classic webové služby** pro klasický webové služby.

   ![Vyberte nový nebo Classic webových služeb](./media/web-services-logging/select-web-service.png)

3. Pro novou webovou službu klikněte na název webové služby. Pro webovou službu Classic klikněte na název webové služby a potom na další stránce klikněte na příslušný koncový bod.

4. Na panelu horní nabídce klikněte na tlačítko **konfigurace**.

5. Nastavte **povolit protokolování** možnost k *chyba* (chyb do protokolu pouze) nebo *všechny* (pro úplné protokolování).

   ![Vyberte úroveň protokolování](./media/web-services-logging/enable-logging.png)

6. Klikněte na **Uložit**.

7. Webové služby Classic, vytvořte **ml diagnostiky** kontejneru.

   Všechny protokoly webové služby jsou uchovávány v kontejneru objektů blob s názvem **ml diagnostiky** v účtu úložiště přidruženého k webové službě. Pro nové webové služby je tento kontejner vytvoří při prvním přístup k webové službě. Webové služby Classic musíte vytvořit kontejner, pokud ještě neexistuje. 

   1. V [portál Azure](https://portal.azure.com), přejděte na účet úložiště, který je přidružený k webové službě.

   2. V části **služby objektů Blob**, klikněte na tlačítko **kontejnery**.

   3. Pokud kontejner **ml diagnostiky** neexistuje, klikněte na tlačítko **+ kontejner**, zadejte kontejner název "ml – Diagnostika" a vyberte **přistupovat typu** jako "Blob". Klikněte na **OK**.

      ![Vyberte úroveň protokolování](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Pro webovou službu Classic má také řídicího panelu webové služby v nástroji Machine Learning Studio přepínač povolení protokolování. Ale protože protokolování se teď spravují prostřednictvím portálu webové služby, musíte povolit protokolování prostřednictvím portálu, jak je popsáno v tomto článku. -Li již povoleno protokolování v sadě Studio služby na webovém portálu zakázání protokolování a znovu ji povolte.


## <a name="the-effects-of-enabling-logging"></a>Účinky povolení protokolování
Pokud je povoleno přihlašování, jsou přihlášení k chybám z koncový bod webové služby a Diagnostika **ml diagnostiky** kontejneru objektů blob v účtu úložiště Azure propojené s prostoru uživatele. Tento kontejner obsahuje všechny diagnostické informace pro všechny webové služby na koncové body pro všechny pracovní prostory spojené s tímto účtem úložiště.

Protokoly lze zobrazit pomocí kteréhokoli několik nástrojů, které jsou k dispozici a prozkoumejte účet úložiště Azure. Nejsnadnější může být přejděte na účet úložiště na portálu Azure, klikněte na **kontejnery**a potom klikněte na kontejner **ml diagnostiky**.  

## <a name="log-blob-detail-information"></a>Podrobné informace o protokolu objektů blob
Každý objekt blob v kontejneru obsahuje diagnostické informace pro právě jeden z následujících akcí:

* Spuštění metody Batch Execution  
* Spuštění metody požadavků a odpovědí  
* Inicializace kontejner požadavků a odpovědí

Název každý objekt blob může mít předponu v následujícím formátu: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Kde _protokolu zadejte_ je jedním z následujících hodnot:  

* dávka  
* skóre nebo požadavky  
* skóre/init  

