---
title: Exportujte nebo importujte konfiguraci zřizování pomocí rozhraní Microsoft Graph API | Microsoft Docs
description: Naučte se exportovat a importovat konfiguraci zřizování pomocí rozhraní Microsoft Graph API.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2fa80726875c82cfa4b5d4cf6a14f4e0dae1871
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367797"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Export nebo import konfigurace zřizování pomocí rozhraní Microsoft Graph API

Pomocí rozhraní Microsoft Graph API a Průzkumníka Microsoft Graph můžete exportovat mapování atributů zřizování uživatelů a schéma do souboru JSON a naimportovat ho zpátky do Azure AD. Můžete také použít kroky zaznamenané tady a vytvořit zálohu konfigurace zřizování. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Krok 1: načtení ID objektu zabezpečení zřizování App Service (ID objektu)

1. Spusťte [Azure Portal](https://portal.azure.com)a přejděte do části vlastnosti vaší aplikace zřizování. Například pokud chcete exportovat pracovní *postup do mapování aplikace pro zřizování uživatelů služby AD* , přejděte do části vlastnosti této aplikace. 
1. V části vlastnosti vaší aplikace pro zřizování Zkopírujte hodnotu identifikátoru GUID přidruženou k poli *ID objektu* . Tato hodnota se také označuje jako **ServicePrincipalId** vaší aplikace a bude se používat v operacích v Průzkumníkovi Microsoft Graph.

   ![ID objektu App Service Workday](./media/export-import-provisioning-configuration/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2: Přihlaste se k Průzkumníkovi Microsoft Graph

1. Spustit [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Klikněte na tlačítko Přihlásit se pomocí Microsoft a přihlaste se pomocí účtu globálního správce Azure AD nebo přihlašovacích údajů správce aplikací.

    ![Přihlášení Microsoft Graph](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Po úspěšném přihlášení se zobrazí podrobnosti o uživatelském účtu v levém podokně.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Krok 3: načtení ID zřizovací úlohy pro zřizovací aplikaci

V Průzkumníku Microsoft Graph spusťte následující příkaz GET Query, který nahradí [servicePrincipalId] **servicePrincipalId** extrahováným z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Dostanete odpověď, jak je znázorněno níže. Zkopírování atributu ID přítomného v odpovědi. Tato hodnota je **ProvisioningJobId** a bude použita k načtení základních metadat schématu.

   [ID úlohy zřizování ![](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>Krok 4: stažení schématu zřizování

V Průzkumníku Microsoft Graph spusťte následující příkaz GET Query, nahraďte [servicePrincipalId] a [ProvisioningJobId] ServicePrincipalId a ProvisioningJobId získanou v předchozích krocích.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Zkopírujte objekt JSON z odpovědi a uložte ho do souboru, aby se vytvořila záloha schématu.

## <a name="step-5-import-the-provisioning-schema"></a>Krok 5: import zřizovacího schématu

> [!CAUTION]
> Tento krok proveďte pouze v případě, že potřebujete upravit schéma pro konfiguraci, které nelze změnit pomocí Azure Portal nebo pokud potřebujete obnovit konfiguraci z dříve zálohovaného souboru s platným a pracovním schématem.

V Průzkumníku Microsoft Graph nakonfigurujte následující dotaz PUT, nahraďte [servicePrincipalId] a [ProvisioningJobId] ServicePrincipalId a ProvisioningJobId získanou v předchozích krocích.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na kartě tělo žádosti zkopírujte obsah souboru schématu JSON.

   [Text žádosti o ![](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Na kartě hlavičky žádosti přidejte atribut záhlaví Content-Type s hodnotou "Application/JSON".

   [![hlavičky žádosti](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Kliknutím na tlačítko spustit dotaz importujte nové schéma.
