---
title: Exportujte konfiguraci zřizování a vraťte se do známého funkčního stavu pro zotavení po havárii.| Dokumenty společnosti Microsoft
description: Zjistěte, jak exportovat konfiguraci zřizování a vrátit se zpět do známého dobrého stavu pro zotavení po havárii.
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
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051316"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Export konfigurace zřizování a návrat zpět do známého dobrého stavu

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Export a import konfigurace zřizování z webu Azure Portal

### <a name="how-can-i-export-my-provisioning-configuration"></a>Jak můžu exportovat konfiguraci zřizování?
Export konfigurace:
1. Na [webu Azure Portal](https://portal.azure.com/)na levém navigačním panelu vyberte **Azure Active Directory**.
2. V podokně **Azure Active Directory** vyberte podnikové **aplikace** a zvolte aplikaci.
3. V levém navigačním podokně vyberte **zřizování**. Na stránce konfigurace zřizování klikněte na **mapování atributů**, **potom zobrazte rozšířené možnosti**a nakonec **zkontrolujte schéma**. Tím se dostanete do editoru schématu. 
5. Klikněte na stažení v panelu příkazů v horní části stránky a stáhněte si schéma.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Zotavení po havárii - návrat do známého dobrého stavu
Export a uložení konfigurace umožňuje vrátit se k předchozí verzi konfigurace. Doporučujeme exportovat konfiguraci zřizování a uložit ji pro pozdější použití vždy, když provedete změnu mapování atributů nebo filtrů oborů. Vše, co musíte udělat, je otevřít soubor JSON, který jste stáhli ve výše uvedených krocích, zkopírujte celý obsah souboru JSON, nahraďte celý obsah datové části JSON v editoru schématu a pak uložte. Pokud je aktivní zřizování cyklu, bude dokončena a další cyklus bude používat aktualizované schéma. Dalším cyklem bude také počáteční cyklus, který přehodnotí každého uživatele a skupinu na základě nové konfigurace. Při vrácení zpět k předchozí konfiguraci zvažte následující:
* Uživatelé budou vyhodnoceny znovu k určení, zda by měly být v oboru. Pokud filtry oborů změnily uživatel není v oboru žádné další budou zakázány. I když se jedná o požadované chování ve většině případů, existují časy, kdy můžete chtít zabránit a můžete použít [funkci přeskočit z oboru odstranění.](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions) 
* Změna konfigurace zřizování restartuje službu a aktivuje [počáteční cyklus](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental).


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Export a import konfigurace zřizování pomocí rozhraní Microsoft Graph API
Rozhraní Microsoft Graph API a Microsoft Graph Explorer můžete exportovat mapování atributů zřizování uživatelů a schéma do souboru JSON a importovat je zpět do Azure AD. Můžete také použít kroky zachycené zde k vytvoření zálohy konfigurace zřizování. 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Krok 1: Načtení ID instancí service service zřizování (ID objektu)

1. Spusťte [portál Azure](https://portal.azure.com)a přejděte do části Vlastnosti vaší zřizovací aplikace. Například pokud chcete exportovat *workday do ad user provisioning mapování aplikací* přejděte do části Vlastnosti této aplikace. 
1. V části Vlastnosti zřizovací aplikace zkopírujte hodnotu GUID přidruženou k poli *ID objektu.* Tato hodnota se také nazývá **ServicePrincipalId** vaší aplikace a bude použita v operacích aplikace Microsoft Graph Explorer.

   ![ID hlavního povinného spuštění aplikace pracovního dne](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2: Přihlášení do Aplikace Microsoft Graph Explorer

1. Spuštění [aplikace Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Klikněte na tlačítko "Přihlásit se s Microsoftem" a přihlásit se pomocí přihlašovacích údajů Azure AD Global Admin nebo App Admin.

    ![Přihlášení k Microsoft Graphu](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Po úspěšném přihlášení se podrobnosti o uživatelském účtu zobrazí v levém podokně.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Krok 3: Načtení ID zřizovací úlohy zřizovací aplikace

V aplikaci Microsoft Graph Explorer spusťte následující dotaz GET, který nahradí [servicePrincipalId] **id ServicePrincipalId** extrahovaným z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Obdržíte odpověď, jak je uvedeno níže. Zkopírujte atribut "id" přítomný v odpovědi. Tato hodnota je **ProvisioningJobId** a bude použita k načtení metadat základního schématu.

   [![ID úlohy zřizování](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Krok 4: Stažení schématu zřizování

V aplikaci Microsoft Graph Explorer spusťte následující dotaz GET, který nahradí [servicePrincipalId] a [ProvisioningJobId] id ServicePrincipalId a ProvisioningJobId načtené v předchozích krocích.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Zkopírujte objekt JSON z odpovědi a uložte jej do souboru, abyste vytvořili zálohu schématu.

### <a name="step-5-import-the-provisioning-schema"></a>Krok 5: Import schématu zřizování

> [!CAUTION]
> Tento krok proveďte jenom v případě, že potřebujete upravit schéma pro konfiguraci, kterou nelze změnit pomocí portálu Azure nebo pokud potřebujete obnovit konfiguraci z dříve zálohovaného souboru s platným a funkčním schématem.

V aplikaci Microsoft Graph Explorer nakonfigurujte následující dotaz PUT a nahrazujte [servicePrincipalId] a [ProvisioningJobId] id ServicePrincipalId a ProvisioningJobId načtené v předchozích krocích.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na kartě Text požadavku zkopírujte obsah souboru schématu JSON.

   [![Tělo požadavku](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Na kartě "Záhlaví požadavků" přidejte atribut hlavičky Typu obsahu s hodnotou "application/json"

   [![Hlavičky požadavků](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Kliknutím na tlačítko Spustit dotaz importujte nové schéma.
