---
title: Monitorování SAP HANA v Azure (velké instance) | Microsoft Docs
description: Monitorujte SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 246b9398824597ec337ee9e9ea3dc24267311f60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669596"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Jak monitorovat SAP HANA (velké instance) v Azure

SAP HANA v Azure (velké instance) se neliší od žádného jiného nasazení IaaS. potřebujete monitorovat, co operační systém a aplikace dělají, a jak aplikace spotřebovávají tyto prostředky:

- Procesor
- Memory (Paměť)
- Šířka pásma sítě
- Místo na disku

V případě Azure Virtual Machines je nutné zjistit, zda jsou třídy prostředků uvedené výše dostačující nebo jsou vyčerpány. Zde je více podrobností o každé z různých tříd:

**Spotřeba prostředků procesoru:** Poměr, který SAP definoval pro určité zatížení pro HANA, se vynutil, aby se zajistilo, že bude k dispozici dostatek prostředků procesoru pro práci prostřednictvím dat uložených v paměti. Nicméně mohou nastat případy, kdy HANA spotřebovává mnoho procesorů provádějících dotazy z důvodu chybějících indexů nebo podobných problémů. To znamená, že byste měli monitorovat spotřebu prostředků procesoru pro velkou instanci instance HANA a prostředky procesoru spotřebované konkrétními službami HANA.

**Spotřeba paměti:** Je důležité monitorovat z HANA i mimo HANA na jednotce. V rámci HANA monitorujte, jak data spotřebovávají paměť HANA, aby bylo možné zachovat požadované pokyny pro změnu velikosti v SAP. Také budete chtít monitorovat spotřebu paměti na úrovni velkých instancí, abyste se ujistili, že další nainstalovaný software jiného typu než HANA nespotřebovává příliš mnoho paměti, a proto s HANAm soutěží o paměť.

**Šířka pásma sítě:** Brána virtuální sítě Azure je omezená na šířku pásma dat přesouvaných do virtuální sítě Azure, takže je užitečné monitorovat data přijatá všemi virtuálními počítači Azure v rámci virtuální sítě, abyste zjistili, jak blízko máte omezení zvolené SKU brány Azure. U velké jednotky instance HANA má smysl monitorovat také příchozí a odchozí síťový provoz a sledovat svazky, které jsou zpracovávány v průběhu času.

**Místo na disku:** Využití místa na disku obvykle roste v čase. Mezi běžné příčiny patří: objem dat roste, provádění záloh protokolů transakcí, ukládání trasovacích souborů a provádění snímků úložiště. Proto je důležité monitorovat využití místa na disku a spravovat místo na disku spojené s jednotkou velké instance HANA.

U **SKU typu II** velkých instancí Hana Server obsahuje předem načtené systémové diagnostické nástroje. Tyto diagnostické nástroje můžete využít k provedení kontroly stavu systému. Spuštěním následujícího příkazu vygenerujete soubor protokolu kontroly stavu na adrese/var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Při práci s podpora Microsoftu týmem při řešení problému může být také požádáno o zadání souborů protokolu pomocí těchto diagnostických nástrojů. Soubor můžete ZIP pomocí následujícího příkazu.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Další kroky**

- Přečtěte si [, jak monitorovat SAP Hana (velké instance) v Azure](./hana-monitor-troubleshoot.md).
