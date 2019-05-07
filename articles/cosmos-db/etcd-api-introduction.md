---
title: Úvod do služby Azure Cosmos DB etcd rozhraní API
description: Tento článek obsahuje přehled a klíč výhody etcd rozhraní API ve službě Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 30d18e1e23767ce3a179221714e001574f8e9778
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075487"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Úvod do služby Azure Cosmos DB etcd rozhraní API (preview)

Azure Cosmos DB je globálně distribuovaná a vícemodelová databázová služba od Microsoftu pro klíčové aplikace. Nabízí globální distribuce na klíč, elastické škálování propustnosti a úložiště, latence v řádu milisekund na 99. percentilu a záruka vysoké dostupnosti, vše zajištěné špičkové smlouvy SLA.

[Etcd](https://github.com/etcd-io/etcd) je úložiště dvojic klíč/hodnota distribuované. V [Kubernetes](https://kubernetes.io/), etcd se používá k ukládání stavu a konfigurace clusterů Kubernetes. Zajištění dostupnosti, spolehlivosti a výkonu etcd je zásadní význam pro celkový stav clusteru, škálovatelnost, pružnost dostupnost a výkon Kubernetes cluster. 

Etcd rozhraní API ve službě Azure Cosmos DB umožňuje používat službu Azure Cosmos DB jako back-endu úložiště pro [Kubernetes se službou Azure](../aks/index.yml). etcd rozhraní API ve službě Azure Cosmos DB je aktuálně ve verzi preview. Azure Cosmos DB implementuje přenosový protokol etcd. S etcd rozhraní API ve službě Azure Cosmos DB, vývojáře, automaticky získá vysoce spolehlivé [dostupné](high-availability.md), [globálně distribuované](distribute-data-globally.md) Kubernetes. Toto rozhraní API umožňuje vývojářům umožní škálování správy stavu Kubernetes na plně spravovanou cloudovou nativní služby PaaS. 

> [!NOTE]
> Na rozdíl od jiných rozhraní API ve službě Azure Cosmos DB nemůžete zřídit účet etcd rozhraní API na webu Azure portal, sady SDK nebo rozhraní příkazového řádku. Můžete zřídit účet rozhraní API etcd nasazením šablony Resource Manageru. podrobné pokyny najdete v článku [jak zřídit Kubernetes v Azure pomocí služby Azure Cosmos DB](bootstrap-kubernetes-cluster.md) článku.  

## <a name="wire-level-compatibility"></a>Úroveň kompatibility při přenosu

Azure Cosmos DB implementuje přenosový protokol etcd verze 3 a umožňuje [hlavní uzel](https://kubernetes.io/docs/concepts/overview/components/) rozhraní API serverů k používání služby Azure Cosmos DB, stejně jako byste udělali v prostředí s místně nainstalovanými etcd. Etcd rozhraní API podporuje vzájemného ověřování TLS. 

Následující diagram znázorňuje komponenty Kubernetes cluster. V hlavní databázi clusteru používá API Server etcd rozhraní API služby Azure Cosmos DB, namísto etcd lokálně nainstalované. 

![Azure Cosmos DB implementace etcd při přenosu protokolů](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Klíčové výhody

### <a name="no-etcd-operations-management"></a>Žádné etcd operace správy

Jako plně spravovanou nativní cloudovou službu službu Azure Cosmos DB odstraňuje nutnost Kubernetes vývojářům vytvářet a spravovat etcd. Etcd rozhraní API ve službě Azure Cosmos DB je škálovatelná, vysoce dostupné, odolné proti chybám a nabízí vysoký výkon. Nároky na nastavení replikace napříč několika uzly, provádí se zajištěním provozu aktualizací, oprav zabezpečení a monitorování stavu etcd provádí služba Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Globální distribuci a vysokou dostupnost 

Pomocí rozhraní API etcd a Azure Cosmos DB zaručuje 99,99 % dostupnost pro čtení dat a zapíše napříč více regiony v jedné oblasti a 99,999 % dostupnost. 

### <a name="elastic-scalability"></a>Elastická škálovatelnost

Azure Cosmos DB nabízí elastickou škálovatelnost pro čtení a zápis požadavků mezi různými oblastmi.
S růstem clusteru Kubernetes, účet etcd rozhraní API ve službě Azure Cosmos DB se Elasticky škáluje bez jakýchkoli prostojů. Ukládání etcd dat ve službě Azure Cosmos DB, namísto hlavních uzlů Kubernetes umožňuje také více flexibilní škálování hlavního uzlu. 

### <a name="security--enterprise-readiness"></a>Zabezpečení a podnikové připravenosti

Když etcd data uložená ve službě Azure Cosmos DB, vývojáři Kubernetes, automaticky získá [integrované šifrování v klidovém stavu](database-encryption-at-rest.md), [certifikací a dodržování předpisů](compliance.md), a [zálohování a obnovení možnosti](online-backup-and-restore.md) podporovány službou Azure Cosmos DB. 

## <a name="next-steps"></a>Další postup

* [Jak používat Kubernetes v Azure pomocí služby Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Mezi klíčové výhody služby Azure Cosmos DB](introduction.md)
* [Příručky rychlý start modul AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)