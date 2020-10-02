---
title: Ruční přidání rozhraní API pomocí portálu Azure Portal | Microsoft Docs
description: V tomto kurzu se dozvíte, jak ručně přidat rozhraní API pomocí API Managementu (APIM).
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 39a3b9d7dd9efbda93de0b5d7c5f9938922d0012
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631286"
---
# <a name="add-an-api-manually"></a>Ruční přidání rozhraní API

Kroky v tomto článku ukazují, jak pomocí portálu Azure Portal ručně přidat rozhraní API do instance API Managementu (APIM). Vytvořit prázdné rozhraní API a definovat ho ručně můžete třeba tehdy, když chcete napodobit rozhraní API. Podrobnosti o napodobení rozhraní API najdete v článku [Napodobení odpovědí API](mock-api-responses.md).

Pokud chcete importovat existující rozhraní API, přečtěte si články v části [Související témata](#related-topics).

V tomto článku vytvoříme prázdné rozhraní API a jako rozhraní API back-endu určíme [httpbin.org](https://httpbin.org) (veřejnou testovací službu).

## <a name="prerequisites"></a>Požadavky

Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Vytvoření rozhraní API

1. V Azure Portal přejděte do služby API Management a v nabídce vyberte **rozhraní API** .
2. V levé nabídce vyberte **+ Přidat rozhraní API**.
3. Vyberte ze seznamu **Prázdné rozhraní API**.  
    ![Prázdné rozhraní API](media/add-api-manually/blank-api.png)  
4. Zadejte nastavení pro rozhraní API. Nastavení jsou vysvětlena v kurzu [importu a publikování prvního rozhraní API](import-and-publish.md#import-and-publish-a-backend-api) .
5. Vyberte **Vytvořit**.

V tomto okamžiku nemáte v API Management žádné operace, které se mapují na operace v rozhraní API back-endu. Pokud voláte operaci, která je vystavena prostřednictvím back-endu, ale ne prostřednictvím API Management, dostanete **404**.

>[!NOTE] 
> Ve výchozím nastavení platí, že když přidáte rozhraní API, a to i v případě, že je připojený k některé back-endové službě, APIM nebude žádné operace vystavovat, dokud je nepovolíte. K povolení operace vaší back-endové služby vytvořte operaci APIM, která se mapuje na back-end operaci.

## <a name="add-and-test-an-operation"></a>Přidání a otestování operace

V této části se dozvíte, jak přidat operaci „/get“, aby se namapovala na back-endovou operaci „http://httpbin.org/get“.

### <a name="add-an-operation"></a>Přidání operace

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
2. Klikněte na **+ Přidat operaci**.
3. V **URL** vyberte **GET** a v prostředku zadejte „*/get*“.
4. Jako **Zobrazovaný název** zadejte „*FetchData*“.
5. Vyberte **Uložit**.

### <a name="test-an-operation"></a>Test operace

Otestujte operaci na portálu Azure Portal. Případně ji můžete otestovat na **Portálu pro vývojáře**.

1. Vyberte kartu **Test**.
2. Vyberte **FetchData**.
3. Stiskněte **Odeslat**.

Zobrazí se odpověď, že se generuje operace „http://httpbin.org/get“. Pokud své operace chcete transformovat, přečtěte si téma [Transformace a ochrana vašeho rozhraní API](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Přidání a otestování parametrizované operace

V této části se dozvíte, jak přidat operaci, která má parametr. V tomto případě namapujeme operaci na „http://httpbin.org/status/200“.

### <a name="add-the-operation"></a>Přidání operace

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
2. Klikněte na **+ Přidat operaci**.
3. V **URL** vyberte **GET** a v prostředku zadejte „*/status/{code}*“. Volitelně můžete zadat informace související s tímto parametrem. Jako **TYP** zadejte třeba „*Číslo*“ a jako **HODNOTY** zadejte„*200*“ (výchozí).
4. Jako **Zobrazovaný název** zadejte „GetStatus“.
5. Vyberte **Uložit**.

### <a name="test-the-operation"></a>Otestování operace 

Otestujte operaci na portálu Azure Portal.  Případně ji můžete otestovat na **Portálu pro vývojáře**.

1. Vyberte kartu **Test**.
2. Vyberte **GetStatus**. Ve výchozím nastavení je hodnota kódu nastavená na „*200*“. Můžete ji změnit k otestování dalších hodnot. Zadejte třeba „*418*“.
3. Stiskněte **Odeslat**.

    Zobrazí se odpověď, že se generuje operace „http://httpbin.org/status/200“. Pokud své operace chcete transformovat, přečtěte si téma [Transformace a ochrana vašeho rozhraní API](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)
