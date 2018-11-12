---
title: Ladění rozhraní API s využitím trasování požadavků ve službě Azure API Management | Microsoft Docs
description: Pomocí kroků v tomto kurzu se naučíte zkoumat kroky zpracování ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: ff3dde8ac95b678866ba6f5216ba23357b067765
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415878"
---
# <a name="debug-your-apis-using-request-tracing"></a>Ladění rozhraní API s využitím trasování požadavků

Tento kurz popisuje postup zkoumání zpracování požadavků, který vám pomůže s laděním a řešením potíží s rozhraním API. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Trasování volání

![Nástroj pro inspekci rozhraní API](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Požadavky

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Projděte si následující rychlý start: [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="trace-a-call"></a>Trasování volání

![Trasování rozhraní API](media/api-management-howto-api-inspector/06-DebugYourAPIs-01-TraceCall.png)

1. Vyberte **Rozhraní API**.
2. V seznamu rozhraní API klikněte na **Demo Conference API**.
3. Přepněte na kartu **Test**.
4. Vyberte operaci **GetSpeakers**.
5. Nezapomeňte zahrnout hlavičku HTTP **Ocp-Apim-Trace** s hodnotou nastavenou na **true**.

    > [!NOTE]
    > Pokud nedojde k automatickému doplnění hodnoty Ocp-Apim-Subscription-Key, můžete ji získat tak, že přejdete na portál pro vývojáře a zveřejníte klíče na stránce profilu.

6. Kliknutím na **Odeslat** proveďte volání rozhraní API. 
7. Počkejte na dokončení volání. 
8. V **konzole rozhraní API** přejděte na kartu **Trasování**. Kliknutím na některý z následujících odkazů můžete přejít k podrobným informacím o trasování: **příchozí**, **back-end**, **odchozí**.

    V části **příchozí** se zobrazí původní požadavek, který služba API Management přijala od volajícího, a všechny zásady aplikované na požadavek, včetně zásad omezení četnosti a nastavení hlaviček, které jsme přidali v kroku 2:

    V části **back-end** se zobrazí požadavky, které služba API Management odeslala do back-endu rozhraní API, a přijaté odpovědi.

    V části **odchozí** se zobrazí všechny zásady aplikované na odpověď před jejím odesláním volajícímu.

    > [!TIP]
    > Každý krok také ukazuje uplynulý čas od přijetí požadavku službou API Management.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Trasování volání

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Použití revizí](api-management-get-started-revise-api.md)
