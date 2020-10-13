---
title: Registrace důvěrné klientské aplikace v Azure AD – Azure API pro FHIR
description: Registrace důvěrné klientské aplikace v Azure Active Directory, která se ověřuje jménem uživatele a žádá o přístup k aplikacím prostředků
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8021fb3fa9f11ef895569f48a2ae21b3f7adcd36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826222"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrace důvěrné klientské aplikace v Azure Active Directory

V tomto kurzu se dozvíte, jak zaregistrovat důvěrnou klientskou aplikaci v Azure Active Directory. 

Registrace klientské aplikace je Azure Active Directory reprezentace aplikace, kterou lze použít k ověřování jménem uživatele a požadování přístupu k [aplikacím prostředků](register-resource-azure-ad-client-app.md). Důvěrná klientská aplikace je aplikace, která může být důvěryhodná pro uchování tajného klíče a při vyžádání přístupových tokenů prezentuje tajný klíč. Příklady důvěrných aplikací jsou aplikace na straně serveru.

Pokud chcete zaregistrovat novou důvěrnou aplikaci na portálu, postupujte podle těchto kroků.

## <a name="register-a-new-application"></a>Registrace nové aplikace

1. V [Azure Portal](https://portal.azure.com)přejděte na **Azure Active Directory**.

1. Vyberte **Registrace aplikací**.

    ![Azure Portal. Registrace nové aplikace](media/how-to-aad/portal-aad-new-app-registration.png)

1. Vyberte **Nová registrace**.

1. Zadejte zobrazovaný název aplikace.

1. Zadejte adresu URL odpovědi. Tyto podrobnosti lze později změnit, ale pokud znáte adresu URL odpovědi vaší aplikace, zadejte ji nyní.

    ![Nová registrace aplikace důvěrného klienta](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. Vyberte **Zaregistrovat**.

## <a name="api-permissions"></a>Oprávnění rozhraní API

Teď, když jste zaregistrovali aplikaci, musíte vybrat, která oprávnění API by tato aplikace měla být schopná považovat za uživatele:

1. Vyberte **oprávnění rozhraní API**.

    ![Důvěrný klient. API – oprávnění](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. Vyberte **Přidat oprávnění**.

    Pokud používáte rozhraní API Azure pro FHIR, přidáte oprávnění k rozhraním API pro zdravotní péče Azure pomocí hledání **rozhraní API pro zdravotní péče Azure** v části **rozhraní API, které používá moje organizace**. Tuto adresu budete moct najít, jenom když už máte [nasazené rozhraní API Azure pro FHIR](fhir-paas-powershell-quickstart.md).

    Pokud odkazujete na jinou aplikaci prostředků, vyberte svou [registraci aplikace prostředků rozhraní API FHIR](register-resource-azure-ad-client-app.md) , kterou jste vytvořili dříve v části **Moje rozhraní API**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Důvěrný klient. Moje organizační rozhraní API" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. Vyberte obory (oprávnění), na které by tajná aplikace měla být schopná požádat jménem uživatele:

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Důvěrný klient. Moje organizační rozhraní API":::

## <a name="application-secret"></a>Tajný kód aplikace

1. Vyberte **certifikáty & tajných**kódů.
1. Vyberte **Nový tajný klíč klienta**. 

    ![Důvěrný klient. Tajný klíč aplikace](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Zadejte popis a dobu trvání tajného kódu (buď 1 rok, 2 roky nebo nikdy).

3. Po vygenerování se na portálu zobrazí jenom jednou. Poznamenejte si ho a bezpečně ho uložte.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s postupem registrace důvěrné klientské aplikace v Azure Active Directory. V dalším kroku získáte přístup k serveru FHIR pomocí post.
 
>[!div class="nextstepaction"]
>[Přístup k rozhraní API Azure pro FHIR s využitím post](access-fhir-postman-tutorial.md)
