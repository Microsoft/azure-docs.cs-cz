---
title: Ruční přidání rozhraní API pomocí portálu Azure Portal | Microsoft Docs
description: V tomto kurzu se dozvíte, jak ručně přidat rozhraní API pomocí API Managementu (APIM).
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: ef7cfa0f30eaaa426c312b21ce0a73aa4409d2ec
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="add-an-api-manually"></a>Ruční přidání rozhraní API 

Kroky v tomto článku ukazují, jak pomocí portálu Azure Portal ručně přidat rozhraní API do instance API Managementu (APIM). Vytvořit prázdné rozhraní API a definovat ho ručně můžete třeba tehdy, když chcete napodobit rozhraní API. Podrobnosti o napodobení rozhraní API najdete v článku [Napodobení odpovědí API](mock-api-responses.md).

Pokud chcete importovat existující rozhraní API, přečtěte si články v části [Související témata](#related-topics).

V tomto článku vytvoříme prázdné rozhraní API a jako rozhraní API back-endu určíme [httpbin.org](http://httpbin.org) (veřejnou testovací službu).

## <a name="prerequisites"></a>Požadavky

Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Vytvoření rozhraní API

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. V levé nabídce vyberte **+ Přidat rozhraní API**.
3. Vyberte ze seznamu **Prázdné rozhraní API**.

    ![Prázdné rozhraní API](media/add-api-manually/blank-api.png)
4. Zadejte nastavení pro rozhraní API.

    ![Nastavení](media/add-api-manually/settings.png)

    |**Název**|**Hodnota**|**Popis**|
    |---|---|---|
    |**Zobrazovaný název**|„*Prázdné rozhraní API*“ |Tento název se zobrazí na portálu pro vývojáře.|
    |**Adresa URL webové služby** (volitelné)| „*http://httpbin.org*“| Pokud chcete rozhraní API napodobit, nemusíte zadávat nic. <br/>V tomto případě zadáme [http://httpbin.org](http://httpbin.org). Je to veřejná testovací služba. <br/>Pokud chcete automaticky importovat rozhraní API, které je namapované na back-end, přečtěte si některé z témat v části [Související témata](#related-topics).|
    |**Schéma URL**|„*HTTPS*“|V tomto případě, i když má back-end nezabezpečený přístup HTTP, určíme k back-endu zabezpečený přístup HTTPS APIM. <br/>Tento druh scénáře (HTTPS na HTTP) se nazývá ukončení protokolu HTTPS. Můžete to udělat, pokud vaše rozhraní API existuje ve virtuální síti (kde víte, že přístup je bezpečný, i když se nepoužívá HTTPS). <br/>„Ukončení protokolu HTTPS“ můžete využít k úspoře některých cyklů procesoru.|
    |**Přípona adresy URL**|„*hbin*“| Přípona je název, který identifikuje toto konkrétní rozhraní API v této instanci APIM. Musí být v této instanci APIM jedinečná.|
    |**Produkty**|*Unlimited* |Publikujte rozhraní API jeho přidružením k produktu. Pokud chcete, aby bylo rozhraní API publikované a k dispozici pro vývojáře, přidejte ho k produktu. Můžete to udělat při vytváření rozhraní API nebo nastavit později.<br/><br/>Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. <br/>Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci služby APIM, již jste správcem, takže jste ve výchozím nastavení přihlášeni k odběru všech produktů.<br/><br/> Ve výchozím nastavení každá instance služby API Management obsahuje dva ukázkové produkty: **Starter** a **Unlimited**.| 
5. Vyberte **Vytvořit**.

V tuto chvíli nemáte v APIM žádné operace, které se mapují na operace v rozhraní API back-endu. Pokud budete volat operaci, která je prezentovaná prostřednictvím back-endu, ale ne prostřednictvím APIM, může nastat chyba **404**. 

>[!NOTE] 
> Ve výchozím nastavení při přidání rozhraní API platí, že i když je připojené k některé back-endové službě, APIM nebude prezentovat žádné operace, dokud je nepřidáte do seznamu povolených. Operaci back-endové služby přidáte do seznamu povolených tak, že vytvoříte operaci APIM, která se namapuje na back-endovou operaci.
>

## <a name="add-and-test-an-operation"></a>Přidání a otestování operace

V této části se dozvíte, jak přidat operaci „/get“, aby se namapovala na back-endovou operaci „http://httpbin.org/get“.

### <a name="add-the-operation"></a>Přidání operace

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
2. Klikněte na **+ Přidat operaci**.
3. V **URL** vyberte **GET** a v prostředku zadejte „*/get*“.
4. Jako **Zobrazovaný název** zadejte „*FetchData*“.
5. Vyberte **Uložit**.

### <a name="test-the-operation"></a>Otestování operace

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
