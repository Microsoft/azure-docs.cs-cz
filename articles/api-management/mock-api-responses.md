---
title: Napodobení odpovědí rozhraní API pomocí webu Azure Portal | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí služby API Management (APIM) nastavit zásadu pro rozhraní API tak, aby vracelo imitaci odpovědi. Tato metoda umožňuje vývojářům pokračovat v implementaci a testování instance služby API Management i v případě, že není k dispozici back-end, který by odesílal skutečné odpovědi.
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
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4383ce3788f6fade5299d69ef99b80221c58d9e7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="mock-api-responses"></a>Napodobení odpovědí API

Back-endová rozhraní API je možné importovat do rozhraní API služby APIM nebo vytvářet a spravovat ručně. Postup v tomto kurzu ukazuje, jak pomocí služby APIM vytvořit prázdné rozhraní API a ručně ho spravovat. V tomto kurzu se dozvíte, jak nastavit zásadu pro rozhraní API tak, aby vracelo imitaci odpovědi. Tato metoda umožňuje vývojářům pokračovat v implementaci a testování instance služby APIM i v případě, že není k dispozici back-end, který by odesílal skutečné odpovědi. Možnost napodobovat odpovědi může být užitečná v řadě scénářů:

+ Když se jako první navrhuje fasáda rozhraní API a back-endová implementace proběhne později. Případně vývoj back-endu probíhá paralelně.
+ Když back-end dočasně není v provozu nebo neumožňuje škálování.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření testovacího rozhraní API 
> * Přidání operace do testovacího rozhraní API
> * Povolení napodobování odpovědí
> * Test imitace rozhraní API

![Napodobení odpovědi operace](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Požadavky

Projděte si následující rychlý start: [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Vytvoření testovacího rozhraní API 

Postup v této části ukazuje, jak vytvořit prázdné rozhraní API bez jakéhokoli back-endu. Také ukazuje, jak do rozhraní API přidat operaci. Zavolání operace po dokončení kroků v této části způsobí chybu. Po dokončení postupu v části Povolení napodobování odpovědí už se chyby zobrazovat nebudou.

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. V levé nabídce vyberte **+ Přidat rozhraní API**.
3. Vyberte ze seznamu **Prázdné rozhraní API**.
4. Jako **Zobrazovaný název** zadejte *Testovací rozhraní API*.
5. V části **Produkty** zadejte *Neomezeno*.
6. Vyberte **Vytvořit**.

## <a name="add-an-operation-to-the-test-api"></a>Přidání operace do testovacího rozhraní API

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
2. Klikněte na **+ Přidat operaci**.

    ![Napodobení odpovědi operace](./media/mock-api-responses/mock-api-responses02.png)

    |Nastavení|Hodnota|Popis|
    |---|---|---|
    |**Adresa URL** (příkaz HTTP)|GET|Můžete zvolit některý z předdefinovaných příkazů HTTP.|
    |**Adresa URL** |*/test*|Cesta URL k rozhraní API. |
    |**Zobrazovaný název**|*Test call*|Název, který se zobrazí na **portálu pro vývojáře**.|
    |**Popis**||Zadejte popis operace, který se na **portálu pro vývojáře** použije jako dokumentace pro vývojáře používající toto rozhraní API.|
    |Karta **Dotaz**||Můžete přidat parametry dotazu. Kromě zadání názvu a popisu můžete do tohoto parametru zadat hodnoty, které je možné přiřadit. Jedna z hodnot může být označená jako výchozí (volitelné).|
    |Karta **Požadavek**||Můžete definovat typy obsahu, příklady a schémata požadavku. |
    |Karta **Odpověď**|Viz kroky uvedené pod touto tabulkou.|Definujte stavový kód, typy obsahu, příklady a schémata odpovědi.|

3. Vyberte kartu **Odpověď**, která se nachází pod poli Adresa URL, Zobrazovaný název a Popis.
4. Klikněte na **+ Přidat odpověď**.
5. Vyberte ze seznamu **200 OK**.
6. Pod záhlavím **Reprezentace** na pravé straně vyberte **+ Přidat reprezentaci**.
7. Do vyhledávacího pole zadejte *application/json* a vyberte typ obsahu **application/json**.
8. Do textového pole **Ukázka** zadejte *{ 'sampleField' : 'test' }*.
9. Vyberte **Uložit**.

## <a name="enable-response-mocking"></a>Povolení napodobování odpovědí

1. Vyberte rozhraní API, které jste vytvořili v kroku Vytvoření testovacího rozhraní API.
2. Vyberte testovací operaci, kterou jste přidali.
2. V pravém okně klikněte na kartu **Návrh**.
3. V okně **Vstupní zpracování** klikněte na ikonu tužky.
4. Na kartě **Napodobování** v části **Chování při napodobování** vyberte **Statické odpovědi**.
5. Do textového pole **API Management vrátí následující odpověď:** zadejte **200 OK, application/json**. Tento výběr určuje, že by vaše rozhraní API mělo vracet ukázku odpovědi, kterou jste definovali v předchozí části.
6. Vyberte **Uložit**.

## <a name="test-the-mocked-api"></a>Test imitace rozhraní API

1. Vyberte rozhraní API, které jste vytvořili v kroku Vytvoření testovacího rozhraní API.
2. Otevřete kartu **Test**.
3. Ujistěte se, že je vybrané rozhraní API **Test call**.

    > [!TIP]
    > Žlutý pruh s textem **Napodobování je povoleno** značí, že odpovědi vracené ze služby API Management vrací zásady napodobování, a nejedná se tedy o skutečné odpovědi back-endu.

3. Vyberte **Odeslat** a proveďte testovací volání.
4. V **odpovědi HTTP** se zobrazí JSON zadaný jako ukázka v první části tohoto kurzu.

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]
> 
> 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření testovacího rozhraní API
> * Přidání operace do testovacího rozhraní API
> * Povolení napodobování odpovědí
> * Test imitace rozhraní API

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)
