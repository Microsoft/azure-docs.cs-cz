---
title: Spravované identity ve službě Azure Signal
description: Zjistěte, jak spravované identity fungují ve službě Azure Signal Service, jak nakonfigurovat tak, aby používaly spravovanou identitu ve scénářích bez serveru.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: 8e122be74d623c7f1ea5576a5fe527dfa538d1d1
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988575"
---
# <a name="how-to-use-managed-identities-for-azure-signalr-service"></a>Jak používat spravované identity pro službu Azure Signal Service

V tomto tématu se dozvíte, jak vytvořit spravovanou identitu pro službu Azure Signal Service a jak ji používat ve scénářích bez serveru.

> [!Important] 
> Služba signalizace Azure může podporovat jenom jednu spravovanou identitu. To znamená, že můžete přidat buď identitu přiřazenou systémem, nebo jednu identitu přiřazenou uživatelem. 

## <a name="add-a-system-assigned-identity"></a>Přidat identitu přiřazenou systémem

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Pokud chcete na portálu nastavit spravovanou identitu, nejdřív vytvořte službu signalizace jako normální a pak tuto funkci povolte.

1. Vytvořte na portálu službu signalizace, která bude normálně. Přejděte na portál na portálu.

2. Vyberte **Identita**.

4. V rámci karty **přiřazené systémem** přepněte **stav** na **zapnuto**. Klikněte na **Uložit**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Přidání identity přiřazené systémem na portálu":::

## <a name="add-a-user-assigned-identity"></a>Přidání uživatelsky přiřazené identity

Vytvoření služby signalizace s uživatelem přiřazenou identitou vyžaduje, abyste vytvořili identitu a pak do své služby přidali svůj identifikátor prostředku.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Nejdřív budete muset vytvořit prostředek identity přiřazené uživatelem.

1. Podle [těchto pokynů](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)vytvořte prostředek spravované identity přiřazené uživatelem.

2. Vytvořte na portálu službu signalizace, která bude normálně. Přejděte na portál na portálu.

3. Vyberte **Identita**.

4. Na kartě **přiřazené uživatelem** klikněte na tlačítko **Přidat**.

5. Vyhledejte identitu, kterou jste vytvořili dříve, a vyberte ji. Klikněte na tlačítko **Add** (Přidat).

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Přidání identity uživatele-assigened na portálu":::

## <a name="use-managed-identity-in-serverless-scenarios"></a>Použití spravované identity ve scénářích bez serveru

Jako služba signalizace je plně spravovaná služba, takže nemůžete pomocí spravované identity získat tokeny ručně. Místo toho služba Signal používá spravovanou identitu, kterou jste nastavili k získání přístupového tokenu, a `Authorization` v nadřazeném požadavku ve scénářích bez serveru nastavte na hlavičku.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Povolit ověřování spravované identity v nastavení pro odesílání

1. Přidání identity přiřazené systémem nebo uživatelem přiřazené identity

2. Nakonfigurujte nastavení pro odesílání dat a použijte *ManagedIdentity* jako nastavení *ověřování* . Zjistěte, jak vytvořit nastavení pro odesílání pomocí ověřování v [nastaveních pro odesílání](concept-upstream.md#create-upstream-settings).

3. V nastavení spravovaného ověřování identity můžete zadat cílový *prostředek*. *Prostředek* se stane `aud` deklarací v načteném přístupovém tokenu, který se dá použít jako součást ověřování v koncových bodech pro odesílání dat. *Prostředkem* může být jedna z následujících možností:
    - Obsahovat
    - ID aplikace (klienta) instančního objektu
    - Identifikátor URI ID aplikace instančního objektu
    - ID prostředků služeb Azure najdete v tématu [ID prostředku služeb Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication) .

    > [!NOTE]
    > Pokud Váš přístup k tokenu ověřujete sami ve vaší službě, můžete si vybrat libovolný z těchto formátů *prostředků* . Pokud chcete zajistit konzistenci *prostředků* v nastaveních *ověřování* a ověřování. Používáte-li rovinu dat, je nutné použít *prostředek* , který požaduje poskytovatel služeb.

### <a name="validate-access-token"></a>Ověření přístupového tokenu

Token v `Authorization` hlavičce je [přístupový token platformy Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens).

K ověření přístupových tokenů by vaše aplikace měla taky ověřit cílovou skupinu a tokeny podepisování. Tyto hodnoty je nutné ověřit proti hodnotám v dokumentu zjišťování OpenID. Například verze dokumentu nezávislá na klientovi je umístěna na adrese [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) .

Middleware Azure AD obsahuje integrované funkce pro ověřování přístupových tokenů a můžete procházet pomocí našich [ukázek](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) a vyhledat je v jazyce podle vašeho výběru.

Poskytujeme knihovny a ukázky kódu, které ukazují, jak zpracovat ověření tokenu. Níže uvedené informace jsou k dispozici pro uživatele, kteří chtějí pochopit původní proces. K dispozici je také několik Open-Source knihoven třetích stran, které jsou k dispozici pro ověření JWT – k dispozici je alespoň jedna možnost pro skoro každou platformu a jazyk. Další informace o knihovnách ověřování Azure AD a ukázkách kódu najdete v tématu [knihovny ověřování v 2.0](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries).

## <a name="next-steps"></a>Další kroky

- [Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service](signalr-concept-serverless-development-config.md)
