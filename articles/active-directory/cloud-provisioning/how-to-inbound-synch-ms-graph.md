---
title: Příchozí synchronizace pro zřizování cloudu pomocí MS Graph API
description: Toto téma popisuje, jak povolit příchozí synchronizaci jenom pomocí Graph API
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c6ed106462d7dcac3f31a2ab157e5b634391006
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093447"
---
# <a name="inbound-synchronization-for-cloud-provisioning-using-ms-graph-api"></a>Příchozí synchronizace pro zřizování cloudu pomocí MS Graph API

Následující dokument popisuje, jak replikovat synchronizační profil od začátku pomocí pouze rozhraní MSGraph API.  
Struktura toho, jak se to provede, se skládá z následujících kroků.  Jsou to tyto:

- [Základní nastavení](#basic-setup)
- [Vytvoření instančních objektů](#create-service-principals)
- [Vytvořit úlohu synchronizace](#create-sync-job)
- [Aktualizovat cílovou doménu](#update-targeted-domain)
- [Spustit úlohu synchronizace](#start-sync-job)
- [Stav kontroly](#review-status)

Pomocí těchto příkazů [modul Microsoft Azure Active Directory pro Windows PowerShell](https://docs.microsoft.com/powershell/module/msonline/) můžete povolit synchronizaci pro produkčního tenanta, což je nezbytný předpoklad pro volání webové služby správy pro tohoto tenanta.

## <a name="basic-setup"></a>Základní nastavení

### <a name="enable-tenant-flags"></a>Povolit příznaky tenanta

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
První z těchto dvou příkazů vyžaduje Azure Active Directory přihlašovací údaje. Tyto rutin implicitně identifikují tenanta a povolují synchronizaci.

## <a name="create-service-principals"></a>Vytvoření instančních objektů
Dál je potřeba vytvořit [AD2AAD aplikaci nebo instanční objekt](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) .

Je nutné použít toto ID aplikace 1a4721b3-e57f-4451-ae87-ef078703ec94. DisplayName je adresa URL domény služby Active Directory, pokud se používá na portálu (například contoso.com), ale může mít název něco jiného.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Vytvořit úlohu synchronizace
Výstup výše uvedeného příkazu vrátí objectId objektu služby, který byl vytvořen. V tomto příkladu je identifikátor objectId 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Pomocí Microsoft Graph přidejte k objektu služby synchronizationJob.  

Dokumentaci k vytvoření úlohy synchronizace najdete [tady](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http).

Pokud jste výše uvedené ID nezaregistrovali, můžete instanční objekt najít spuštěním následujícího volání MS graphu. K provedení tohoto volání budete potřebovat Directory. Read. All oprávnění:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Pak ve výstupu vyhledejte název vaší aplikace.

Spuštěním následujících dvou příkazů vytvořte dvě úlohy: jednu pro zřizování uživatelů a skupin a jednu pro synchronizaci hodnot hash hesel. Je to stejný požadavek dvakrát, ale s různými ID šablon.


Zavolejte následující dva požadavky:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Pokud chcete vytvořit obojí, budete potřebovat dvě volání.

Příklad návratové hodnoty (pro zřizování):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Aktualizovat cílovou doménu
Identifikátor objektu a identifikátor aplikace instančního objektu pro tohoto tenanta je následující:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-C000-000000000000 DisplayName: testApp

Budeme muset aktualizovat doménu, na kterou je tato konfigurace cílena, aby se aktualizace tajných kódů pro tuto doménu aktualizovala.

Ujistěte se, že název domény, který používáte, je stejná adresa URL, kterou jste nastavili pro váš Prem řadič domény.

 ```
 PUT https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 Content-type: application/json
 {
  "value": [
    {
      "key": "Domain",
       {"value":[{"key":"Domain","value":"{\"domain\":\"[YOUR_DOMAIN_NAME]\"}"}]}
    }
  ]
 }
 ```

Očekávaná odpověď je... HTTP 204/žádný obsah

Tady je zvýrazněná hodnota "doména" je název místní domény služby Active Directory, ze které se mají položky zřídit Azure Active Directory.

## <a name="start-sync-job"></a>Spustit úlohu synchronizace
Úlohu lze znovu načíst pomocí následujícího příkazu:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

Dokumentaci k načítání úloh najdete [tady](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http). 
 
Chcete-li spustit úlohu, vydejte tento požadavek pomocí identifikátoru objektu služby vytvořeného v prvním kroku a identifikátor úlohy vrácený z požadavku, který úlohu vytvořil.

Dokumentaci, jak spustit úlohu, najdete [tady](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

Očekávaná odpověď je... HTTP 204/žádný obsah.

[Tady](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)jsou popsané další příkazy pro řízení úlohy.
 
Aby bylo možné úlohu restartovat, bude použita jedna z těchto...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Stav kontroly
Načíst stav úlohy prostřednictvím...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

V části stav návratového objektu vyhledejte relevantní podrobnosti.

## <a name="next-steps"></a>Další kroky 

- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
- [Transformace](how-to-transformation.md)
- [Rozhraní API pro synchronizaci Azure AD](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
