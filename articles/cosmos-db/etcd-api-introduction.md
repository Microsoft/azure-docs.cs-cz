---
title: Úvod k rozhraní Azure Cosmos DB etcd API
description: Tento článek poskytuje přehled a klíčové výhody rozhraní etcd API v Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 208f0d73b4c8bb11adc9c73796ebc6a622f5ed50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85118164"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Úvod do rozhraní API pro Azure Cosmos DB etcd (Preview)

Azure Cosmos DB je databázová služba Microsoftu s více modely pro klíčové aplikace použitelná v celosvětovém měřítku. Nabízí globální distribuci klíč, elastické škálování propustnosti a úložiště, latence v řádu milisekund na 99 percentilu a zaručenou vysokou dostupnost, která je zajištěná v oboru špičkové smlouvy SLA.

[Etcd](https://github.com/etcd-io/etcd) je distribuované úložiště klíčů a hodnot. V [Kubernetes](https://kubernetes.io/)se k uložení stavu a konfigurace clusterů Kubernetes používá etcd. Zajištění dostupnosti, spolehlivosti a výkonu etcd je klíčové pro celkový stav clusteru, škálovatelnost, dostupnost pružnosti a výkon clusteru Kubernetes.

Rozhraní etcd API v Azure Cosmos DB umožňuje používat Azure Cosmos DB jako úložiště back-endu pro Azure Kubernetes. rozhraní etcd API v Azure Cosmos DB je aktuálně ve verzi Preview. Azure Cosmos DB implementuje etcd přenosový protokol. Díky rozhraní etcd API v Azure Cosmos DB budou vývojáři automaticky získávat vysoce spolehlivá, [dostupná](high-availability.md), [globálně distribuovaná](distribute-data-globally.md) Kubernetes. Toto rozhraní API umožňuje vývojářům škálovat správu stavu Kubernetes na plně spravovanou cloudovou nativní službu PaaS. 

> [!NOTE]
> Na rozdíl od jiných rozhraní API v Azure Cosmos DB nemůžete zřídit účet rozhraní API etcd prostřednictvím Azure Portal, CLI nebo sad SDK. Účet rozhraní etcd API můžete zřídit jenom nasazením šablony Správce prostředků. Podrobný postup najdete v článku [jak zřídit Azure Kubernetes s Azure Cosmos DB](bootstrap-kubernetes-cluster.md) článkem. Rozhraní API pro Azure Cosmos DB etcd je aktuálně ve verzi omezené verze Preview. Můžete se [zaregistrovat ve verzi Preview](https://aka.ms/cosmosetcdapi-signup)tak, že vyplníte formulář pro registraci.

## <a name="wire-level-compatibility"></a>Kompatibilita na úrovni drátu

Azure Cosmos DB implementuje protokol etcd verze 3 a umožňuje serverům rozhraní API [hlavního uzlu](https://kubernetes.io/docs/concepts/overview/components/) používat Azure Cosmos DB stejně, jako by to vedlo místně nainstalované prostředí etcd. Rozhraní etcd API podporuje vzájemné ověřování TLS. 

Následující diagram znázorňuje komponenty clusteru Kubernetes. Server API na hlavním serveru používá Azure Cosmos DB rozhraní API etcd namísto lokálně nainstalovaných etcd. 

:::image type="content" source="./media/etcd-api-introduction/etcd-api-wire-protocol.png" alt-text="Azure Cosmos DB implementace etcd drátového protokolu" border="false":::

## <a name="key-benefits"></a>Klíčové výhody

### <a name="no-etcd-operations-management"></a>Žádná Správa operací etcd

Jako plně spravovaná nativní cloudová služba Azure Cosmos DB nepotřebují vývojáři Kubernetes vytvářet a spravovat etcd. Rozhraní etcd API v Azure Cosmos DB je škálovatelné, vysoce dostupné, odolné proti chybám a nabízí vysoký výkon. Režie při nastavení replikace mezi více uzly, provádění kumulativních aktualizací, oprav zabezpečení a monitorování stavu etcd jsou zpracovávány Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Globální distribuce & vysoké dostupnosti 

Pomocí rozhraní API etcd Azure Cosmos DB garantuje dostupnost 99,99% pro čtení a zápis dat v jedné oblasti a 99,999% dostupnost napříč několika oblastmi. 

### <a name="elastic-scalability"></a>Elastická škálovatelnost

Azure Cosmos DB nabízí elastickou škálovatelnost pro požadavky na čtení a zápis napříč různými oblastmi.
Jak cluster Kubernetes roste, účet rozhraní etcd API v Azure Cosmos DB elasticky škálovat bez výpadků. Ukládání etcd dat v Azure Cosmos DB místo hlavních uzlů Kubernetes také umožňuje pružnější škálování hlavního uzlu. 

### <a name="security--enterprise-readiness"></a>Security & Readiness Enterprise

Když jsou etcd data uložená v Azure Cosmos DB, vývojáři Kubernetes automaticky získají [integrované šifrování při klidování](database-encryption-at-rest.md),  [certifikaci a dodržování předpisů](compliance.md)a [Možnosti zálohování a obnovení](online-backup-and-restore.md) , které Azure Cosmos DB podporuje. 

## <a name="next-steps"></a>Další kroky

* [Jak používat Azure Kubernetes s Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Klíčové výhody Azure Cosmos DB](introduction.md)
* [Příručka rychlý Start pro modul AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)