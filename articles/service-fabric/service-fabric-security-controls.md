---
title: Ovládací prvky zabezpečení pro Azure Service Fabric
description: Přečtěte si o ovládacích prvcích zabezpečení pro Azure Service Fabric. Obsahuje kontrolní seznam integrovaných ovládacích prvků zabezpečení.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645425"
---
# <a name="security-controls-for-azure-service-fabric"></a>Ovládací prvky zabezpečení pro Azure Service Fabric

Tento článek dokumentuje ovládací prvky zabezpečení integrované do Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vstřikování virtuální sítě| Ano |  |
| Podpora izolace sítě a brány firewall| Ano | Použití skupin zabezpečení sítě (NSG). |
| Podpora vynuceného tunelování| Ano | Azure networking poskytuje vynucené tunelové propojení. |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | Pomocí podpory monitorování Azure a podpory třetích stran. |
| Protokolování a audit roviny řízení a správy| Ano | Všechny operace roviny řízení procházejí procesy pro auditování a schvalování. |
| Protokolování a audit roviny dat| Není dostupné. | Zákazník vlastní cluster.  |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Ověřování probíhá prostřednictvím služby Azure Active Directory. |
| Autorizace| Ano | Správa identit a přístupu (IAM) pro volání přes SFRP. Volání přímo do koncového bodu clusteru podporuje dvě role: Uživatel a Správce. Zákazník může mapovat rozhraní API na obě role. |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Ano | Zákazník vlastní cluster a škálovací sadu virtuálních strojů, na které je cluster vytvořen. Šifrování disku Azure lze povolit na škálovací sadě virtuálních počítačů. |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ano | Zákazník vlastní cluster a škálovací sadu virtuálních strojů, na které je cluster vytvořen. Šifrování disku Azure lze povolit na škálovací sadě virtuálních počítačů. |
| Šifrování na úrovni sloupců (Azure Data Services)| Není dostupné. |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano |  |
| Zašifrovaná volání rozhraní API| Ano | Volání rozhraní API service fabric se uskutečují prostřednictvím Správce prostředků Azure. Je vyžadován platný webový token JSON (JWT). |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano | |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).