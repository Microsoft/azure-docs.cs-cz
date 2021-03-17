---
title: Exportujte konfiguraci zřizování a vraťte se do známého funkčního stavu pro zotavení po havárii.
description: Naučte se exportovat vaši konfiguraci zřizování a vrátit se do známého stavu pro zotavení po havárii.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/19/2020
ms.author: kenwith
ms.openlocfilehash: c6af42c78bda66c4b397cbb99b26af7d6a5c7f07
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256369"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Postupy: Export konfigurace zřizování a návrat do známého funkčního stavu

V tomto článku se naučíte:

- Exportovat a importovat konfiguraci zřizování z Azure Portal
- Export a import konfigurace zřizování pomocí rozhraní Microsoft Graph API

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Exportovat a importovat konfiguraci zřizování z Azure Portal

### <a name="export-your-provisioning-configuration"></a>Exportujte konfiguraci zřizování.

Export konfigurace:

1. V [Azure Portal](https://portal.azure.com/)v levém navigačním panelu vyberte možnost **Azure Active Directory**.
1. V podokně **Azure Active Directory** vyberte možnost **podnikové aplikace** a zvolte aplikaci.
1. V levém navigačním podokně vyberte **zřizování**. Na stránce konfigurace zřizování klikněte na **mapování atributů** a pak na **Zobrazit pokročilé možnosti** a nakonec **Zkontrolujte schéma**. Tím přejdete do editoru schémat.
1. Kliknutím na stáhnout na panelu příkazů v horní části stránky Stáhněte své schéma.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Zotavení po havárii – vracení zpátky do známého funkčního stavu

Export a uložení konfigurace vám umožní vrátit se zpátky k předchozí verzi vaší konfigurace. Doporučujeme vám Exportovat konfiguraci zřizování a uložit ji pro pozdější použití, kdykoli uděláte změnu mapování atributů nebo filtrování rozsahu. Vše, co potřebujete udělat, je otevřít soubor JSON, který jste stáhli v předchozích krocích. Zkopírujte celý obsah souboru JSON, nahraďte celý obsah datové části JSON v editoru schémat a pak ho uložte. Pokud je aktivní cyklus zřizování, bude dokončen a další cyklus bude používat aktualizované schéma. Dalším cyklem bude také počáteční cyklus, který znovu vyhodnotí každého uživatele a skupinu na základě nové konfigurace. Při návratu do předchozí konfigurace Vezměte v úvahu následující:

- Uživatelé se znovu vyhodnotí a určí, jestli mají být v oboru. Pokud se změní rozsahy filtrů, uživatel není v oboru, protože už žádné další budou zakázané. I když se jedná o požadované chování ve většině případů, existují situace, kdy byste to měli chtít zabránit, a můžete použít funkci [Přeskočit z rozsahu odstranění](./skip-out-of-scope-deletions.md) . 
- Změna konfigurace zřizování restartuje službu a aktivuje [počáteční cyklus](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental).

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Export a import konfigurace zřizování pomocí rozhraní Microsoft Graph API

Pomocí rozhraní Microsoft Graph API a Průzkumníka Microsoft Graph můžete exportovat mapování atributů zřizování uživatelů a schéma do souboru JSON a naimportovat ho zpátky do Azure AD. Můžete také použít kroky zaznamenané tady a vytvořit zálohu konfigurace zřizování.

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Krok 1: načtení ID objektu zabezpečení zřizování App Service (ID objektu)

1. Spusťte [Azure Portal](https://portal.azure.com)a přejděte do části vlastnosti vaší aplikace zřizování. Pokud například chcete exportovat pracovní *den do mapování aplikace pro zřizování uživatelů služby AD* , přejděte do části vlastnosti této aplikace.
1. V části vlastnosti vaší aplikace pro zřizování Zkopírujte hodnotu identifikátoru GUID přidruženou k poli *ID objektu* . Tato hodnota se také označuje jako **ServicePrincipalId** vaší aplikace a bude se používat v operacích v Průzkumníkovi Microsoft Graph.

   ![ID objektu App Service Workday](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2: Přihlaste se k Průzkumníkovi Microsoft Graph

1. Spustit [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Klikněte na tlačítko Přihlásit se pomocí Microsoft a přihlaste se pomocí účtu globálního správce Azure AD nebo přihlašovacích údajů správce aplikací.

    ![Přihlášení Microsoft Graph](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Po úspěšném přihlášení se zobrazí podrobnosti o uživatelském účtu v levém podokně.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Krok 3: načtení ID zřizovací úlohy pro zřizovací aplikaci

V Průzkumníku Microsoft Graph spusťte následující příkaz GET Query, který nahradí [servicePrincipalId] **servicePrincipalId** extrahováným z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Dostanete odpověď, jak je znázorněno níže. Zkopírování atributu ID přítomného v odpovědi. Tato hodnota je **ProvisioningJobId** a bude použita k načtení základních metadat schématu.

   [![ID úlohy zřizování](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Krok 4: stažení schématu zřizování

V Průzkumníku Microsoft Graph spusťte následující příkaz GET Query, nahraďte [servicePrincipalId] a [ProvisioningJobId] ServicePrincipalId a ProvisioningJobId získanou v předchozích krocích.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Zkopírujte objekt JSON z odpovědi a uložte ho do souboru, aby se vytvořila záloha schématu.

### <a name="step-5-import-the-provisioning-schema"></a>Krok 5: import zřizovacího schématu

> [!CAUTION]
> Tento krok proveďte pouze v případě, že potřebujete upravit schéma pro konfiguraci, které nelze změnit pomocí Azure Portal nebo pokud potřebujete obnovit konfiguraci z dříve zálohovaného souboru s platným a pracovním schématem.

V Průzkumníku Microsoft Graph nakonfigurujte následující dotaz PUT, nahraďte [servicePrincipalId] a [ProvisioningJobId] ServicePrincipalId a ProvisioningJobId získanou v předchozích krocích.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na kartě tělo žádosti zkopírujte obsah souboru schématu JSON.

   [![Text požadavku](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Na kartě hlavičky žádosti přidejte atribut záhlaví Content-Type s hodnotou "Application/JSON".

   [![Hlavičky požadavku](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Vyberte **Spustit dotaz** pro import nového schématu.