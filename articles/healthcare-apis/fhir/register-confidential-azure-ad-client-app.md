---
title: Registrace důvěrné klientské aplikace v Azure AD – Azure API pro FHIR
description: Registrace důvěrné klientské aplikace v Azure Active Directory, která se ověřuje jménem uživatele a žádá o přístup k aplikacím prostředků
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: matjazl
ms.openlocfilehash: c10b27d375e2bfb8c64130eceb416a633241cf68
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284425"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrace důvěrné klientské aplikace v Azure Active Directory

V tomto kurzu se dozvíte, jak zaregistrovat důvěrnou klientskou aplikaci v Azure Active Directory (Azure AD).  

Registrace klientské aplikace je reprezentace aplikace v Azure AD, kterou je možné použít k ověřování jménem uživatele a požadování přístupu k [aplikacím prostředků](register-resource-azure-ad-client-app.md). Důvěrná klientská aplikace je aplikace, která může být důvěryhodná pro uchování tajného klíče a při vyžádání přístupových tokenů prezentuje tajný klíč. Příklady důvěrných aplikací jsou aplikace na straně serveru. 

Pokud chcete zaregistrovat novou důvěrnou klientskou aplikaci, podívejte se na následující postup. 

## <a name="register-a-new-application"></a>Registrace nové aplikace

1. Na portálu [Azure Portal](https://portal.azure.com) vyberte **Azure Active Directory**.

1. Vyberte **Registrace aplikací**. 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="Azure Portal. Registrace nové aplikace":::

1. Vyberte **Nová registrace**.

1. Poskytněte aplikaci zobrazované uživatelské jméno.

1. U **podporovaných typů účtů** vyberte, kdo může aplikaci používat, nebo přístup k tomuto rozhraní API.

1. Volitelné Zadejte **identifikátor URI pro přesměrování**. Tyto podrobnosti lze později změnit, ale pokud znáte adresu URL odpovědi vaší aplikace, zadejte ji nyní.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="Nová registrace aplikace důvěrného klienta":::

1. Vyberte **Zaregistrovat**.

## <a name="api-permissions"></a>Oprávnění rozhraní API

Teď, když jste zaregistrovali aplikaci, musíte vybrat, která oprávnění API by tato aplikace měla požádat jménem uživatelů.

1. Vyberte **oprávnění rozhraní API**.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="Důvěrný klient. Oprávnění rozhraní API.":::

1. Vyberte **Přidat oprávnění**.

    Pokud používáte rozhraní API Azure pro FHIR, přidáte do rozhraní API pro **zdravotní péči Azure oprávnění v části** rozhraní API, které **používá moje organizace**. Výsledek hledání pro rozhraní API zdravotní péče Azure se vrátí jenom v případě, že už máte [nasazené rozhraní API Azure pro FHIR](fhir-paas-powershell-quickstart.md).

    Pokud odkazujete na jinou aplikaci prostředků, vyberte svou [registraci aplikace prostředků rozhraní API FHIR](register-resource-azure-ad-client-app.md) , kterou jste vytvořili dříve v části **Moje rozhraní API**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Důvěrný klient. Moje organizační rozhraní API" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. Vyberte obory (oprávnění), které důvěrná klientská aplikace vyzve jménem uživatele. Vyberte **user_impersonation** a pak vyberte **Přidat oprávnění**.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Důvěrný klient. Delegovaná oprávnění":::


## <a name="application-secret"></a>Tajný kód aplikace

1. Vyberte **certifikáty & tajných** kódů a pak vyberte **nový tajný klíč klienta**. 

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="Důvěrný klient. Tajný klíč aplikace":::

1. Zadejte **Popis** tajného klíče klienta. Vyberte rozevírací nabídku pro **vypršení platnosti** a zvolte časový rámec vypršení platnosti a pak klikněte na **Přidat**.

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="Přidejte tajný klíč klienta.":::

1. Po vytvoření tajného řetězce klienta zkopírujte jeho **hodnotu** a **ID** a uložte je do zabezpečeného umístění podle vašeho výběru.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="Řetězec tajného klíče klienta."::: 

> [!NOTE]
>Řetězec tajného klíče klienta je v Azure Portal viditelný jenom jednou. Když opustíte webové stránky certifikáty & tajných kódů a pak se vrátíte zpět, řetězec hodnoty se zamaskuje. Je důležité vytvořit kopii tajného řetězce klienta hned po jeho vygenerování. Pokud nemáte záložní kopii tajného klíče klienta, je nutné zopakovat výše uvedené kroky a znovu je vygenerovat.
 
## <a name="next-steps"></a>Další kroky

V tomto článku jste provedli kroky k registraci důvěrné klientské aplikace v Azure AD. Provedli jste také postup přidání oprávnění rozhraní API k rozhraní API zdravotní péče Azure. Nakonec jste si ukázali, jak vytvořit tajný klíč aplikace. Kromě toho se můžete dozvědět, jak získat přístup k serveru FHIR pomocí post.
 
>[!div class="nextstepaction"]
>[Přístup k rozhraní API Azure pro FHIR s využitím post](access-fhir-postman-tutorial.md)
