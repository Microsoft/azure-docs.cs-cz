---
title: Transformace a ochrana rozhraní API ve službě Azure API Management | Microsoft Docs
description: Seznamte se s možnostmi ochrany rozhraní API pomocí zásad kvót a zásad omezování četnosti.
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
ms.openlocfilehash: 4aa4c69857bfd1ab99945cb0f5f748e60cff9978
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417326"
---
# <a name="transform-and-protect-your-api"></a>Transformace a ochrana vašeho rozhraní API 

Tento kurz ukazuje, jak vaše API transformovat, aby nezobrazovalo privátní back-endové informace. Například byste mohli chtít skrýt informace o technologii, která běží na back-endu. Dále byste také mohli chtít skrýt původní adresy URL, které se objevují v těle odpovědi protokolu HTTP rozhraní API, a přesměrovat je na bránu služby APIM.

Tento kurz vám také ukáže, jak snadno můžete pomocí služby Azure API Management přidat ochranu rozhraní API vašeho back-endu tím, že nakonfigurujete zásady omezování četnosti. Například byste mohli chtít omezit, kolikrát je možné rozhraní API volat, aby ho vývojáři nadměrně nevyužívali. Další informace najdete v tématu [Zásady služby API Management](api-management-policies.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Odebrat hlavičky odpovědi transformováním rozhraní API
> * Nahradit původní adresy URL v těle odpovědi rozhraní API adresami URL brány služby APIM
> * Ochránit rozhraní API přidáním zásady omezování četnosti (omezení využití sítě)
> * Testovat transformace

![Zásady](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Požadavky

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Seznamte se s [konceptem zásad ve službě Azure API Management](api-management-howto-policies.md).
+ Projděte si následující rychlý start: [Vytvoření instance Azure API Management](get-started-create-service-instance.md).
+ Také dokončení následujícího kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Odebrat hlavičky odpovědi transformováním rozhraní API

Tato část ukazuje, jak skrýt hlavičky protokolu HTTP, které by neměli vaši uživatelé vidět. V tomto příkladu se odstraní v odpovědi protokolu HTTP následující hlavičky:

* **X-Powered-By**
* **X-AspNet-Version**

### <a name="test-the-original-response"></a>Test původní odpovědi

Původní odpověď zobrazíte následovně:

1. V instanci služby APIM vyberte **Rozhraní API** (v části **API MANAGEMENT**).
2. V seznamu rozhraní API klikněte na **Demo Conference API**.
3. Nahoře na obrazovce klikněte na kartu **Test**.
4. Vyberte operaci **GetSpeakers**.
5. V dolní části obrazovky klikněte na tlačítko **Odeslat**.

Původní odpověď by měla vypadat takto:

![Zásady](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Nastavení zásady transformací

![Nastavení odchozí zásady](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png)

1. Vyberte **Demo Conference API**.
2. V horní části obrazovky vyberte kartu **Návrh**.
3. Vyberte **Všechny operace**.
4. V části **Zpracování odchozích požadavků** klikněte na ikonu **</>**.
5. Umístěte kurzor myši do elementu **&lt;outbound&gt;**.
6. V pravém okně v části **Zásady transformace** vložte dva fragmenty kódu zásady dvojitým kliknutím na **+ Set HTTP header** (Nastavit hlavičku protokolu HTTP).

    ![Zásady](./media/transform-api/transform-api.png)
7. Svůj kód **<outbound>** upravte následovně:

        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />

    ![Zásady](./media/transform-api/set-policy.png)

8. Klikněte na tlačítko **Uložit**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Nahradit původní adresy URL v těle odpovědi rozhraní API adresami URL brány služby APIM

Tato část ukazuje, jak skrýt původní adresy URL, které se objevují v těle odpovědi protokolu HTTP rozhraní API, a přesměrovat je na bránu služby APIM.

### <a name="test-the-original-response"></a>Test původní odpovědi

Původní odpověď zobrazíte následovně:

1. Vyberte **Demo Conference API**.
2. Nahoře na obrazovce klikněte na kartu **Test**.
3. Vyberte operaci **GetSpeakers**.
4. V dolní části obrazovky klikněte na tlačítko **Odeslat**. 

    Jak můžete vidět, původní odpověď vypadá takto:

    ![Zásady](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Nastavení zásady transformací

1. Vyberte **Demo Conference API**.
2. Vyberte **Všechny operace**.
3. V horní části obrazovky vyberte kartu **Návrh**.
4. V části **Zpracování odchozích požadavků** klikněte na ikonu **</>**.
5. Umístěte kurzor myši do elementu **&lt;outbound&gt;**.
6. V pravém okně v části **Zásady transformace** klikněte na **+ Find and replace string in body** (Najít a nahradit řetězec v těle textu).
7. Nahraďte adresu URL upravením kódu **find-and-replace** (v elementu **\<outbound\>**) tak, aby odpovídala bráně služby APIM. Příklad:

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Ochránit rozhraní API přidáním zásady omezování četnosti (omezení využití sítě)

Tato část ukazuje, jak přidat ochranu back-endovému rozhraní API nakonfigurováním omezování četnosti. Například byste mohli chtít omezit, kolikrát je možné rozhraní API volat, aby ho vývojáři nadměrně nevyužívali. V tomto příkladu je omezení nastaveno na 3 volání každých 15 sekund pro každé ID předplatného. Po 15 sekundách může vývojář zkusit volat rozhraní API znovu.

![Nastavení příchozí zásady](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png)

1. Vyberte **Demo Conference API**.
2. Vyberte **Všechny operace**.
3. V horní části obrazovky vyberte kartu **Návrh**.
4. V **zpracování příchozích** klikněte na tlačítko **</>** ikonu.
5. Umístěte kurzor myši do elementu **&lt;inbound&gt;**.
6. V pravém okně v části **Zásady omezení přístupu** klikněte na **+ Limit call rate per key** (Omezit četnost volání pro každý klíč).
7. Upravte kód **rate-limit-by-key** (v elementu **\<inbound\>**) následovně:

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>Testovat transformace
        
Když se v tomto okamžiku podíváte na kód v editoru, budou vaše zásady vypadat takto:

    <policies>
        <inbound>
            <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <set-header name="X-Powered-By" exists-action="delete" />
            <set-header name="X-AspNet-Version" exists-action="delete" />
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

Zbývající část článku testuje transformace zásad, které jste nastavili.

### <a name="test-the-stripped-response-headers"></a>Test odebraných hlaviček odpovědi

1. Vyberte **Demo Conference API**.
2. Vyberte kartu **Test**.
3. Klikněte na operaci **GetSpeakers**.
4. Stiskněte **Odeslat**.

    Jak vidíte, hlavičky byly odebrány:

    ![Zásady](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>Test nahrazené adresy URL

1. Vyberte **Demo Conference API**.
2. Vyberte kartu **Test**.
3. Klikněte na operaci **GetSpeakers**.
4. Stiskněte **Odeslat**.

    Jak vidíte, adresa URL byla nahrazena.

    ![Zásady](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>Test omezování četnosti (omezení využití sítě)

1. Vyberte **Demo Conference API**.
2. Vyberte kartu **Test**.
3. Klikněte na operaci **GetSpeakers**.
4. Třikrát po sobě stiskněte **Odeslat**.

    Po odeslání třech požadavků se objeví odpověď **429 Příliš mnoho požadavků**.

5. Počkejte asi 15 sekund a znovu stiskněte **Odeslat**. Tentokrát by se už měla objevit odpověď **200 OK**.

    ![Throttling](./media/transform-api/test-throttling.png)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Odebrat hlavičky odpovědi transformováním rozhraní API
> * Nahradit původní adresy URL v těle odpovědi rozhraní API adresami URL brány služby APIM
> * Ochránit rozhraní API přidáním zásady omezování četnosti (omezení využití sítě)
> * Testovat transformace

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Monitorování rozhraní API](api-management-howto-use-azure-monitor.md)
