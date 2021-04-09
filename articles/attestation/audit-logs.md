---
title: Protokoly auditu Azure Attestation
description: Popisuje úplné protokoly auditu, které jsou shromažďovány pro Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95758974"
---
# <a name="audit-logs-for-azure-attestation"></a>Protokoly auditu pro Azure Attestation

Protokoly auditu jsou zabezpečené, neměnné záznamy s časovým razítkem diskrétních událostí, ke kterým došlo v průběhu času. Tyto protokoly zaznamenávají důležité události, které mohou ovlivnit funkčnost vaší instance ověření identity.

Azure Attestation spravuje instance ověření identity a k nim přidružené zásady. Akce související se správou instancí a změnami zásad jsou auditovány a protokolovány.

Tento článek obsahuje informace o protokolovaných událostech, shromažďovaných informacích a umístění těchto protokolů.

## <a name="about-audit-logs"></a>O protokolech auditu

Azure Attestation používá kód k vytváření protokolů auditu pro události ovlivňující způsob, jakým je prováděno ověřování identity. Obvykle se přeloží na to, jak nebo kdy se v instanci ověření provedou změny zásad, a také na některé akce správce.

### <a name="auditable-events"></a>Auditované události
Tady jsou některé protokoly auditu, které shromažďujeme:

|     Událost/rozhraní API                              |     Popis události                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     Vytvořit instanci                        |     Vytvoří novou instanci služby ověření identity. |
|     Zničit instanci                       |     Odstraní instanci služby ověření identity. |
|     Přidat certifikát zásad                 |     Přidání certifikátu do aktuální sady certifikátů správy zásad. |
|     Odebrat certifikát zásad              |     Odebere certifikát z aktuální sady certifikátů správy zásad. |
|     Nastavit aktuální zásadu                     |     Nastaví zásady ověření identity pro daný typ TEE. |
|     Resetovat zásady ověření identity               |     Obnoví zásadu ověření identity pro daný typ TEE. |
|     Příprava na aktualizaci zásad               |     Příprava na aktualizaci zásad ověření identity pro daný typ TEE. |
|     Dehydratované klienty po havárii       |     Přepečetí všechna tenanta ověření identity v této instanci služby ověření identity. Dá se to udělat jenom správci služby ověření identity. |

### <a name="collected--information"></a>Shromážděné informace
Pro každou z těchto událostí shromažďuje Azure Attestation tyto informace:

- Název operace
- Operace byla úspěšná.
- Volající operace, což může být kterýkoli z následujících:
    - Hlavní název uživatele (UPN) Azure AD
    - ID objektu
    - Certifikát
    - ID klienta Azure AD
- Cíl operace, který může být kterýkoli z následujících:
    - Prostředí
    - Oblast služby
    - Role služby
    - Instance role služby
    - ID prostředku
    - Oblast prostředků

### <a name="sample-audit-log"></a>Ukázkový protokol auditu

Protokoly auditu jsou k dispozici ve formátu JSON. Tady je příklad toho, jak může vypadat protokol auditu.

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>Přístup k protokolům auditu

Tyto protokoly se ukládají v Azure a nedají se získat přímo. Pokud potřebujete získat přístup k těmto protokolům, zasouborte lístek podpory. Další informace najdete v tématu [kontakt podpora Microsoftu](https://azure.microsoft.com/support/options/). 

Po podání lístku podpory se Microsoft stáhne a poskytne vám přístup k těmto protokolům.
