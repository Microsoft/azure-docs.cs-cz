---
title: Úvod do rozhraní API Azure Cosmos DB etcd
description: Tento článek poskytuje přehled a klíčové výhody etcd API v Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: acd87fac5ec2edc40d27d98f073e13c0acae8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498595"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Úvod do rozhraní API Azure Cosmos DB etcd (preview)

Azure Cosmos DB je databázová služba Microsoftu s více modely pro klíčové aplikace použitelná v celosvětovém měřítku. Nabízí globální distribuci na klíč, elastické škálování propustnosti a úložiště, jednociferné milisekundové latence na 99 percentilu a zaručenou vysokou dostupnost, to vše s celosvětovou zárukou Smlouvy s la.

[Etcd](https://github.com/etcd-io/etcd) je distribuovaný klíč/hodnota úložiště. V [Kubernetes](https://kubernetes.io/)se etcd používá k ukládání stavu a konfigurace Kubernetesových klastrů. Zajištění dostupnosti, spolehlivosti a výkonu etcd je zásadní pro celkový stav clusteru, škálovatelnost, dostupnost elasticity a výkon clusteru Kubernetes. 

Rozhraní ETCD API ve službě Azure Cosmos DB umožňuje používat Azure Cosmos DB jako back-endové úložiště pro [Azure Kubernetes](../aks/index.yml). etcd API v Azure Cosmos DB je aktuálně ve verzi preview. Azure Cosmos DB implementuje protokol etcd wire. Díky rozhraní API etcd v Azure Cosmos DB vývojáři automaticky získají vysoce spolehlivé, [dostupné](high-availability.md) [globálně distribuované](distribute-data-globally.md) Kubernetes. Toto rozhraní API umožňuje vývojářům škálovat správu stavu Kubernetes na plně spravované cloudové nativní službě PaaS. 

> [!NOTE]
> Na rozdíl od jiných rozhraní API v Azure Cosmos DB, nelze zřídit účet etcd rozhraní API prostřednictvím portálu Azure, CLI nebo Sady SDK. Účet etcd API můžete zřídit pouze nasazením šablony Správce prostředků. Podrobné kroky najdete v tématu [Jak zřídit Azure Kubernetes s Azure Cosmos DB](bootstrap-kubernetes-cluster.md) článku. Azure Cosmos DB etcd rozhraní API je aktuálně v omezené verzi Preview. Do [náhledu se](https://aka.ms/cosmosetcdapi-signup)můžete zaregistrovat vyplněním registračního formuláře.

## <a name="wire-level-compatibility"></a>Kompatibilita na úrovni drátu

Azure Cosmos DB implementuje drátový protokol etcd verze 3 a umožňuje servery rozhraní API [hlavního uzlu](https://kubernetes.io/docs/concepts/overview/components/) používat Azure Cosmos DB stejně jako by to udělal v místně nainstalovaném prostředí etcd. Etcd API podporuje vzájemné ověřování TLS. 

Následující diagram znázorňuje součásti clusteru Kubernetes. V hlavním clusteru server ROZHRANÍ API používá rozhraní API Azure Cosmos DB etcd, namísto místně nainstalovaných etcd. 

![Azure Cosmos DB implementace drátového protokolu etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Klíčové výhody

### <a name="no-etcd-operations-management"></a>Řízení provozu bez etcd

Jako plně spravovaná nativní cloudová služba Azure Cosmos DB odstraňuje potřebu vývojářů Kubernetes nastavit a spravovat atd. Rozhraní API etcd v Azure Cosmos DB je škálovatelné, vysoce dostupné, odolné proti chybám a nabízí vysoký výkon. Režie nastavení replikace mezi více uzly, provádění rolling aktualizace, opravy zabezpečení a sledování stavu etcd jsou zpracovány Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Globální distribuční & vysokou dostupností 

Pomocí etcd ROZHRANÍ API, Azure Cosmos DB zaručuje 99,99 % dostupnost pro čtení dat a zápisy v jedné oblasti a 99,999 % dostupnost ve více oblastech. 

### <a name="elastic-scalability"></a>Elastická škálovatelnost

Azure Cosmos DB nabízí elastickou škálovatelnost pro požadavky na čtení a zápis v různých oblastech.
Jak cluster Kubernetes roste, účet etcd API v Azure Cosmos DB elasticky škáluje bez prostojů. Ukládání etcd data v Azure Cosmos DB, namísto hlavní uzly Kubernetes také umožňuje flexibilnější škálování hlavních uzlů. 

### <a name="security--enterprise-readiness"></a>Zabezpečení & připravenost podniku

Když jsou data etcd uložená v Db Služby Azure Cosmos, vývojáři Kubernetes automaticky získají [integrované šifrování v klidovém stavu](database-encryption-at-rest.md), [certifikace a dodržování předpisů](compliance.md)a [možnosti zálohování a obnovení](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) podporované službou Azure Cosmos DB. 

## <a name="next-steps"></a>Další kroky

* [Jak používat Azure Kubernetes s Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Klíčové výhody Azure Cosmos DB](introduction.md)
* [Průvodce rychlým startem motoru AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)