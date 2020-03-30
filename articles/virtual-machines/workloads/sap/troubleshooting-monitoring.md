---
title: Monitorování SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Monitorujte SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50a6b4f15a7de02533e3bb51e5659f7b4c078b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617295"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Jak sledovat SAP HANA (velké instance) v Azure

SAP HANA v Azure (velké instance) se nijak neliší od všech ostatních nasazení IaaS – je třeba sledovat, co operační systém a aplikace dělá a jak aplikace spotřebovávají následující prostředky:

- Procesor
- Memory (Paměť)
- Šířka pásma sítě
- Místo na disku

S virtuálními počítači Azure, musíte zjistit, zda výše uvedené třídy prostředků jsou dostatečné nebo se vyčerpají. Zde je více podrobností o každé z různých tříd:

**Spotřeba prostředků procesoru:** Poměr, který SAP definované pro určité zatížení proti HANA je vynuceno, aby se ujistil, že by měl být dostatek prostředků procesoru k dispozici pro práci prostřednictvím dat, která je uložena v paměti. Nicméně může být případy, kdy HANA spotřebovává mnoho procesorů provádění dotazů z důvodu chybějící indexy nebo podobné problémy. To znamená, že byste měli sledovat spotřebu prostředků procesoru jednotky velké instance HANA a také prostředky procesoru spotřebované konkrétními službami HANA.

**Spotřeba paměti:** Je důležité sledovat zevnitř HANA, stejně jako mimo HANA na jednotce. V rámci HANA sledovat, jak data spotřebovává HANA přidělené paměti, aby zůstali v rámci požadované zásady pro velikost i SAP. Chcete také sledovat spotřebu paměti na úrovni velké instance a ujistěte se, že další nainstalovaný software bez HANA nespotřebovává příliš mnoho paměti, a proto soutěžit s HANA pro paměť.

**Šířka pásma sítě:** Brána virtuální sítě Azure má omezenou šířku pásma dat, která se přesouvají do virtuální sítě Azure, takže je užitečné sledovat data přijatá všemi virtuálními počítači Azure v rámci virtuální sítě, abyste zjistili, jak blízko jste limitům skladové položky brány Azure, kterou jste vybrali. V jednotce velké instance HANA má smysl také sledovat příchozí a odchozí síťový provoz a sledovat svazky, které jsou zpracovávány v průběhu času.

**Místo na disku:** Spotřeba místa na disku se obvykle časem zvyšuje. Nejběžnější příčiny jsou: zvýšení objemu dat, spuštění záloh protokolu transakcí, ukládání trasovacích souborů a provádění snímků úložiště. Proto je důležité sledovat využití místa na disku a spravovat místo na disku přidružené k jednotce velké instance HANA.

Pro **objekty SKU typu II** velkých instancí HANA je server dodáván s předinstalovanými nástroji pro diagnostiku systému. Tyto diagnostické nástroje můžete použít k provedení kontroly stavu systému. Spusťte následující příkaz a vygenerujte soubor protokolu kontroly stavu na adrese /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Při práci s týmem podpory společnosti Microsoft k řešení problému, můžete být také vyzváni k poskytnutí souborů protokolu pomocí těchto diagnostických nástrojů. Soubor můžete zipovat pomocí následujícího příkazu.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Další kroky**

- Podívejte se [na how to monitor SAP HANA (large instances) on Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot).
