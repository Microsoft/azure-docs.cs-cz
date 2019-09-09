---
title: Exportujte nebo importujte konfiguraci zřizování pomocí Graph API | Microsoft Docs
description: Naučte se exportovat a importovat konfiguraci zřizování pomocí Graph API.
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
ms.openlocfilehash: 1c3e92ee5ffd97174331703b703e811bd1ce5f43
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815818"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Exportujte nebo importujte konfiguraci zřizování pomocí Graph API

Pomocí Microsoft Graph API a Graph Exploreru můžete exportovat mapování atributů zřizování uživatelů a schéma do souboru JSON a importovat ho zpátky do Azure AD. Můžete také použít kroky zaznamenané tady a vytvořit zálohu konfigurace zřizování. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Krok 1: Načtení ID objektu zabezpečení zřizování App Service (ID objektu)

1. Spusťte [Azure Portal](https://portal.azure.com)a přejděte do části vlastnosti vaší aplikace zřizování. Například pokud chcete exportovat pracovní *postup do mapování aplikace pro zřizování uživatelů služby AD* , přejděte do části vlastnosti této aplikace. 
1. V části vlastnosti vaší aplikace pro zřizování Zkopírujte hodnotu identifikátoru GUID přidruženou k poli *ID objektu* . Tato hodnota se také označuje jako **ServicePrincipalId** vaší aplikace a bude se používat v rámci operací Graph Exploreru.

   ![ID objektu App Service Workday](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2: Přihlaste se k Průzkumníkovi Microsoft Graph

1. Spustit [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Klikněte na tlačítko Přihlásit se pomocí Microsoft a přihlaste se pomocí účtu globálního správce Azure AD nebo přihlašovacích údajů správce aplikací.

    ![Přihlášení do grafu](./media/export-import-provisioning-mappings/wd_export_02.png)

1. Po úspěšném přihlášení se zobrazí podrobnosti o uživatelském účtu v levém podokně.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Krok 3: Načtení ID zřizovací úlohy pro zřizovací aplikaci

V Průzkumníku Microsoft Graph spusťte následující příkaz GET Query, který nahradí [servicePrincipalId] **servicePrincipalId** extrahováným z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Dostanete odpověď, jak je znázorněno níže. Zkopírování atributu ID přítomného v odpovědi. Tato hodnota je **ProvisioningJobId** a bude použita k načtení základních metadat schématu.

   [![ID úlohy zřizování](./media/export-import-provisioning-mappings/wd_export_03.png)](./media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>Krok 4: Stažení schématu zřizování

V Průzkumníku Microsoft Graph spusťte následující příkaz GET Query, nahraďte [servicePrincipalId] a [ProvisioningJobId] ServicePrincipalId a ProvisioningJobId získanou v předchozích krocích.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Zkopírujte objekt JSON z odpovědi a uložte ho do souboru, aby se vytvořila záloha schématu.

## <a name="step-5-import-the-provisioning-schema"></a>Krok 5: Importovat schéma zřizování

> [!CAUTION]
> Tento krok proveďte pouze v případě, že potřebujete upravit schéma pro konfiguraci, které nelze změnit pomocí Azure Portal nebo pokud potřebujete obnovit konfiguraci z dříve zálohovaného souboru s platným a pracovním schématem.

V Průzkumníku Microsoft Graph nakonfigurujte následující dotaz PUT, nahraďte [servicePrincipalId] a [ProvisioningJobId] ServicePrincipalId a ProvisioningJobId získanou v předchozích krocích.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na kartě tělo žádosti zkopírujte obsah souboru schématu JSON.

   [![Text žádosti](./media/export-import-provisioning-mappings/wd_export_04.png)](./media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

Na kartě hlavičky žádosti přidejte atribut záhlaví Content-Type s hodnotou "Application/JSON".

   [![Hlavičky požadavku](./media/export-import-provisioning-mappings/wd_export_05.png)](./media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

Kliknutím na tlačítko spustit dotaz importujte nové schéma.
