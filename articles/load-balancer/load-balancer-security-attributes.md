---
title: Atributy zabezpečení pro Azure Load Balancer
description: Kontrolní seznam zabezpečení atributy za vaše rozhodnutí vyzkoušet nástroje pro vyrovnávání zatížení
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 1892b918ed35221bc73d5070d5a73ecc359aa4c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800078"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Atributy zabezpečení pro Azure Load Balancer

Tento článek popisuje běžné atributy zabezpečení integrované do Azure Load Balancer.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Není k dispozici | |
| Šifrování během přenosu (například šifrování ExpressRoute ve virtuální síti a šifrováním virtuálními sítěmi)| Není k dispozici | |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Není k dispozici | |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Šifrované volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manageru](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není k dispozici | |
| Vkládání podpory virtuálních sítí| Není k dispozici | . |
| Izolace sítě a Firewalling podpory| Není k dispozici |  |
| Vynucené tunelování podpory| Není k dispozici | |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Zobrazit [protokoly Azure monitoru pro veřejný Load balancer úrovně Basic](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Není k dispozici |  |
| Authorization| Není k dispozici |  |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Zobrazit [protokoly Azure monitoru pro veřejný Load balancer úrovně Basic](load-balancer-monitor-log.md). |
| Protokolování roviny dat a auditu | Není k dispozici |  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Není k dispozici |  | 
