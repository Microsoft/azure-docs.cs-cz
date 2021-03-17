---
title: Registrace aplikace služby ve službě Azure AD – Azure API pro FHIR
description: Zjistěte, jak zaregistrovat aplikaci klienta služby v Azure Active Directory.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 6f7bf122b292ca144eac406957f19a13c7ba6662
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018190"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registrace klientské aplikace služby v Azure Active Directory

V tomto článku se dozvíte, jak zaregistrovat aplikaci klienta služby v Azure Active Directory. Registrace klientských aplikací Azure Active Directory reprezentace aplikací, které lze použít k ověřování a získání tokenů. Klient služby má být používán aplikací k získání přístupového tokenu bez interaktivního ověřování uživatele. Bude mít určitá oprávnění aplikace a při získání přístupových tokenů používat tajný klíč aplikace (heslo).

Pomocí těchto kroků vytvořte nového klienta služby.

## <a name="app-registrations-in-azure-portal"></a>Registrace aplikací v Azure Portal

1. V [Azure Portal](https://portal.azure.com)přejděte na **Azure Active Directory**.

2. Vyberte **Registrace aplikací**.

    ![Azure Portal. Registrace nové aplikace](media/how-to-aad/portal-aad-new-app-registration.png)

3. Vyberte **Nová registrace**.

4. Poskytněte klientovi služby zobrazované jméno. Klientské aplikace služby obvykle nepoužívají adresu URL odpovědi.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure Portal. Nová registrace klientské aplikace služby.":::

5. Vyberte **Zaregistrovat**.

## <a name="api-permissions"></a>Oprávnění rozhraní API

Teď, když jste zaregistrovali aplikaci, musíte vybrat, která oprávnění API by tato aplikace měla být schopná považovat za uživatele:

1. Vyberte **oprávnění rozhraní API**.
1. Vyberte **Přidat oprávnění**.

    Pokud používáte rozhraní API Azure pro FHIR, přidáte oprávnění k rozhraním API pro zdravotní péče Azure pomocí hledání **rozhraní API pro zdravotní péče Azure** v části **rozhraní API, které používá moje organizace**. 

    Pokud odkazujete na jinou aplikaci prostředků, vyberte svou [registraci aplikace prostředků rozhraní API FHIR](register-resource-azure-ad-client-app.md) , kterou jste vytvořili dříve v části **Moje rozhraní API**.

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="Důvěrný klient. Moje organizační rozhraní API" lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. Vyberte obory (oprávnění), na které by tajná aplikace měla být schopná požádat jménem uživatele:

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="Klient služby. Delegovaná oprávnění":::

1. Udělte aplikaci souhlas. Pokud nemáte požadovaná oprávnění, obraťte se na správce Azure Active Directory:

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="Klient služby. Udělit souhlas":::

## <a name="application-secret"></a>Tajný kód aplikace

Pro získání tokenu potřebuje klient služby tajný klíč (heslo).

1. Vyberte **certifikáty & tajných** kódů.
2. Vyberte **Nový tajný klíč klienta**.

    ![Azure Portal. Tajný kód klienta služby](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Zadejte popis a dobu trvání tajného kódu (buď 1 rok, 2 roky nebo nikdy).

4. Po vygenerování tajného klíče se na portálu zobrazí jenom jednou. Poznamenejte si ho a bezpečně ho uložte.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s postupem registrace klientské aplikace služby v Azure Active Directory. Potom otestujte přístup k serveru FHIR pomocí post.
 
>[!div class="nextstepaction"]
>[Přístup k rozhraní API Azure pro FHIR s využitím post](access-fhir-postman-tutorial.md)
