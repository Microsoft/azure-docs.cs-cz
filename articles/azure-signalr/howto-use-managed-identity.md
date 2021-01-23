---
title: Spravované identity ve službě Azure Signal
description: Přečtěte si, jak spravované identity fungují ve službě Azure Signal Service a jak používat spravovanou identitu ve scénářích bez serveru.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: dee15977318eda7bcd0b1950286bb33f621221dd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731580"
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

5. Vyhledejte identitu, kterou jste vytvořili dříve, a vybere ji. Vyberte **Přidat**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Přidání uživatelsky přiřazené identity na portálu":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Použití spravované identity ve scénářích bez serveru

Služba signalizace Azure je plně spravovaná služba, takže nemůžete pomocí spravované identity získat tokeny ručně. Místo toho služba signalizace Azure používá spravovanou identitu, kterou jste nastavili k získání přístupového tokenu. Služba pak nastaví přístupový token na `Authorization` hlavičku v nadřazeném požadavku ve scénářích bez serveru.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Povolit ověřování spravované identity v nastavení pro odesílání

1. Přidejte identitu přiřazenou systémem nebo identitu přiřazenou uživatelem.

2. Přidejte jedno nastavení pro odesílání a kliknutím na hvězdičku se dostanete na podrobnou stránku, jak je znázorněno níže.
    :::image type="content" source="media/signalr-howto-use-managed-identity/pre-msi-settings.png" alt-text="pre-MSI – nastavení":::
    
    :::image type="content" source="media/signalr-howto-use-managed-identity/msi-settings.png" alt-text="MSI – nastavení":::

3. V nastavení spravované ověřování identity pro **prostředek** můžete zadat cílový prostředek. Prostředek se stane `aud` deklarací identity v načteném přístupovém tokenu, který se dá použít jako součást ověřování v koncových bodech pro odesílání dat. Prostředek může být jeden z následujících:
    - Obsahovat
    - ID aplikace (klienta) instančního objektu
    - Identifikátor URI ID aplikace instančního objektu
    - [ID prostředku služby Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Pokud váš přístupový token ověříte ve vaší službě sami, můžete si vybrat libovolný z formátů prostředků. Stačí se ujistit, že hodnota **prostředku** v nastavení **ověřování** a ověřování jsou konzistentní. Pokud pro rovinu dat používáte řízení přístupu na základě role Azure (Azure RBAC), musíte použít prostředek, který poskytovatel služeb požaduje.

### <a name="validate-access-tokens"></a>Ověřit přístupové tokeny

Token v `Authorization` hlavičce je [přístupový token platformy Microsoft Identity](../active-directory/develop/access-tokens.md#validating-tokens).

K ověření přístupových tokenů by aplikace měla taky ověřit cílovou skupinu a tokeny podepisování. Tyto hodnoty je nutné ověřit proti hodnotám v dokumentu zjišťování OpenID. Podívejte se například na [verzi dokumentu nezávisle na klientovi](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Middleware Azure Active Directory (Azure AD) obsahuje integrované funkce pro ověřování přístupových tokenů. Můžete procházet naše [ukázky](../active-directory/develop/sample-v2-code.md) a vyhledat je v jazyce podle vašeho výběru.

Poskytujeme knihovny a ukázky kódu, které ukazují, jak zpracovat ověření tokenu. K dispozici je také několik Open Source knihoven partnerů pro ověření JSON Web Token (JWT). K dispozici je alespoň jedna možnost pro skoro každou platformu a jazyk. Další informace o knihovnách ověřování a ukázkách kódu Azure AD najdete v tématu [knihovny ověřování Microsoft Identity Platform](../active-directory/develop/reference-v2-libraries.md).

#### <a name="authentication-in-function-app"></a>Ověřování v Function App

Nastavení ověřování přístupového tokenu v Function App je snadné a efektivní bez fungování kódu.

1. Na stránce **ověřování/autorizace** přepněte **App Service ověřování** na **zapnuto**.

2. Vyberte možnost **Přihlásit se pomocí Azure Active Directory** v **akci, která se provede, když žádost není ověřená**.

3. V poskytovateli ověřování klikněte na možnost **Azure Active Directory**

4. Na nové stránce. Vyberte **expresní** a **vytvořte novou aplikaci AD** a potom klikněte na **OK** :::image type="content" source="media/signalr-howto-use-managed-identity/function-aad.png" alt-text="funkce AAD"::: .

5. Přejděte ke službě Signaler a postupujte podle [kroků](howto-use-managed-identity.md#add-a-system-assigned-identity) a přidejte identitu přiřazenou systémem nebo uživatelem přiřazenou identitu.

6. Přečtěte si **nastavení pro odesílání** do služby Signal Service a zvolte možnost **použít spravovanou identitu** a **Vyberte z existujících aplikací**. Vyberte aplikaci, kterou jste předtím vytvořili.

Po těchto nastavení Function App zamítne požadavky bez přístupového tokenu v hlavičce.

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Použití spravované identity pro Key Vault reference

Služba signalizace má přístup k Key Vault k získání tajného klíče pomocí spravované identity.

1. Přidejte identitu přiřazenou systémem nebo identitu uživatele pro službu Azure Signal.

2. Udělte oprávnění ke čtení pro spravovanou identitu v zásadách přístupu v Key Vault. Viz [přiřazení zásad Key Vault přístupu pomocí Azure Portal](../key-vault/general/assign-access-policy-portal.md)

V současné době se tato funkce dá použít v těchto scénářích:

- [Odkaz na tajný klíč ve vzoru adresy URL pro odesílání](./concept-upstream.md#key-vault-secret-reference-in-url-template-settings)


## <a name="next-steps"></a>Další kroky

- [Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service](signalr-concept-serverless-development-config.md)