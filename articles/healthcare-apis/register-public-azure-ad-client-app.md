---
title: Registrace veřejné klientské aplikace v Azure AD – Azure API pro FHIR
description: Tento článek vysvětluje, jak zaregistrovat veřejnou klientskou aplikaci v Azure Active Directory v tématu Příprava na nasazení rozhraní FHIR API v Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 6671b8aa60690bc1915e297bc31b19299be2b1da
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629076"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Registrace veřejné klientské aplikace v Azure Active Directory

V tomto článku se dozvíte, jak zaregistrovat veřejnou aplikaci v Azure Active Directory.  

Registrace klientských aplikací Azure Active Directory reprezentace aplikací, které se můžou ověřit a požádat o oprávnění rozhraní API jménem uživatele. Veřejné klienty jsou aplikace, jako například mobilní aplikace a aplikace JavaScriptu na jedné stránce, které nemůžou udržovat důvěrné tajné kódy. Postup je podobný jako při [registraci důvěrného klienta](register-confidential-azure-ad-client-app.md), ale vzhledem k tomu, že veřejné klienty nemůžou držet tajný klíč aplikace, není potřeba ho přidávat.

## <a name="app-registrations-in-azure-portal"></a>Registrace aplikací v Azure Portal

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na levém navigačním panelu na **Azure Active Directory**.

2. V okně **Azure Active Directory** klikněte na **Registrace aplikací**:

    ![Azure Portal. Registrace nové aplikace](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klikněte na **novou registraci**.

## <a name="application-registration-overview"></a>Přehled registrace aplikace

1. Zadejte zobrazovaný název aplikace.

2. Zadejte adresu URL odpovědi. Adresa URL odpovědi je místo, kde se vrátí ověřovací kódy do klientské aplikace. Můžete přidat další adresy URL odpovědi a později upravit stávající.

    ![Azure Portal. Registrace nové veřejné aplikace](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>Oprávnění rozhraní API

Podobně jako u [důvěrných klientských aplikací](register-confidential-azure-ad-client-app.md)budete muset vybrat, jaká oprávnění API by tato aplikace měla být schopná považovat za uživatele:

1. Otevřete **oprávnění rozhraní API**.

    Pokud používáte rozhraní API Azure pro FHIR, přidáte oprávnění k rozhraním API pro zdravotní péče Azure pomocí hledání rozhraní API pro zdravotní péče Azure v části **rozhraní API, které používá moje organizace**. Tuto adresu budete moct najít, jenom když už máte [nasazené rozhraní API Azure pro FHIR](fhir-paas-powershell-quickstart.md).

    
    Pokud odkazujete na jinou aplikaci prostředků, vyberte svou [registraci aplikace prostředků rozhraní API FHIR](register-resource-azure-ad-client-app.md) , kterou jste vytvořili dříve v části **Moje rozhraní API**:

    ![Azure Portal. Nová oprávnění veřejného rozhraní API – Azure API pro výchozí FHIR](media/public-client-app/api-permissions.png)


2. Vyberte oprávnění, která chcete, aby aplikace mohla požadovat: ![ Azure Portal. Oprávnění aplikace](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>Ověřit autoritu serveru FHIR
Pokud je aplikace, kterou jste zaregistrovali v tomto článku, a váš server FHIR ve stejném tenantovi služby Azure AD, můžete přejít k dalším krokům.

Pokud nakonfigurujete klientskou aplikaci v jiném tenantovi Azure AD ze serveru FHIR, budete muset **autoritu**aktualizovat. V Azure API pro FHIR nastavíte autoritu v části nastavení--> ověřování. Nastavte svou autoritu na **https://login.microsoftonline.com/\<TENANT-ID>** .

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s postupem registrace veřejné klientské aplikace v Azure Active Directory. Potom otestujte přístup k serveru FHIR pomocí post.
 
>[!div class="nextstepaction"]
>[Přístup k rozhraní API Azure pro FHIR s využitím post](access-fhir-postman-tutorial.md)
