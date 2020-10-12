---
title: Spravované identity ve službě Azure Signal
description: Přečtěte si, jak spravované identity fungují ve službě Azure Signal Service a jak používat spravovanou identitu ve scénářích bez serveru.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: abe7503e7eb73d533ae901af21de001960173fb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85559413"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Spravované identity pro službu Azure Signal Service

V tomto článku se dozvíte, jak vytvořit spravovanou identitu pro službu Azure Signal Service a jak ji používat ve scénářích bez serveru.

> [!Important] 
> Služba signalizace Azure může podporovat jenom jednu spravovanou identitu. To znamená, že můžete přidat identitu přiřazenou systémem nebo identitu přiřazenou uživatelem. 

## <a name="add-a-system-assigned-identity"></a>Přidat identitu přiřazenou systémem

Pokud chcete v Azure Portal nastavit spravovanou identitu, nejdřív vytvořte instanci služby signalizace Azure a pak tuto funkci povolte.

1. Na portálu vytvořte instanci služby signalizace Azure, jako by to bylo normálně. Přejděte na portál na portálu.

2. Vyberte **Identita**.

4. Na kartě **přiřazeno systému** přepněte **stav** na **zapnuto**. Vyberte **Uložit**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Přidání identity přiřazené systémem na portálu":::

## <a name="add-a-user-assigned-identity"></a>Přidání uživatelsky přiřazené identity

Vytvoření instance služby signalizace Azure s uživatelem přiřazenou identitou vyžaduje, abyste vytvořili identitu a pak do své služby přidali svůj identifikátor prostředku.

1. Podle [těchto pokynů](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)vytvořte prostředek spravované identity přiřazené uživatelem.

2. Na portálu vytvořte instanci služby signalizace Azure, jako by to bylo normálně. Přejděte na portál na portálu.

3. Vyberte **Identita**.

4. Na kartě **přiřazený uživatel** vyberte **Přidat**.

5. Vyhledejte identitu, kterou jste vytvořili dříve, a vyberte ji. Vyberte **Přidat**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Přidání identity přiřazené systémem na portálu":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Použití spravované identity ve scénářích bez serveru

Služba signalizace Azure je plně spravovaná služba, takže nemůžete pomocí spravované identity získat tokeny ručně. Místo toho služba signalizace Azure používá spravovanou identitu, kterou jste nastavili k získání přístupového tokenu. Služba pak nastaví přístupový token na `Authorization` hlavičku v nadřazeném požadavku ve scénářích bez serveru.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Povolit ověřování spravované identity v nastavení pro odesílání

1. Přidejte identitu přiřazenou systémem nebo identitu přiřazenou uživatelem.

2. Nakonfigurujte nastavení pro odesílání dat a použijte **ManagedIdentity** jako nastavení **ověřování** . Informace o tom, jak vytvořit nastavení pro odesílání pomocí ověřování, najdete v tématu [nastavení pro odesílání dat](concept-upstream.md).

3. V nastavení spravované ověřování identity pro **prostředek**můžete zadat cílový prostředek. Prostředek se stane `aud` deklarací identity v načteném přístupovém tokenu, který se dá použít jako součást ověřování v koncových bodech pro odesílání dat. Prostředek může být jeden z následujících:
    - Obsahovat
    - ID aplikace (klienta) instančního objektu
    - Identifikátor URI ID aplikace instančního objektu
    - [ID prostředku služby Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Pokud váš přístupový token ověříte ve vaší službě sami, můžete si vybrat libovolný z formátů prostředků. Stačí se ujistit, že hodnota **prostředku** v nastavení **ověřování** a ověřování jsou konzistentní. Pokud pro rovinu dat používáte řízení přístupu na základě role (RBAC), musíte použít prostředek, který poskytovatel služeb požaduje.

### <a name="validate-access-tokens"></a>Ověřit přístupové tokeny

Token v `Authorization` hlavičce je [přístupový token platformy Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens).

K ověření přístupových tokenů by aplikace měla taky ověřit cílovou skupinu a tokeny podepisování. Tyto hodnoty je nutné ověřit proti hodnotám v dokumentu zjišťování OpenID. Podívejte se například na [verzi dokumentu nezávisle na klientovi](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Middleware Azure Active Directory (Azure AD) obsahuje integrované funkce pro ověřování přístupových tokenů. Můžete procházet naše [ukázky](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) a vyhledat je v jazyce podle vašeho výběru.

Poskytujeme knihovny a ukázky kódu, které ukazují, jak zpracovat ověření tokenu. K dispozici je také několik Open Source knihoven partnerů pro ověření JSON Web Token (JWT). K dispozici je alespoň jedna možnost pro skoro každou platformu a jazyk. Další informace o knihovnách ověřování a ukázkách kódu Azure AD najdete v tématu [knihovny ověřování Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries).

## <a name="next-steps"></a>Další kroky

- [Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service](signalr-concept-serverless-development-config.md)
