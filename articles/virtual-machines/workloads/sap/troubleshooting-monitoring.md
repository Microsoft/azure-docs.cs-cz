---
title: Monitorování systému SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Monitorování SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5a5d462be5555090d1dfced5fa07c9b748eb312
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345654"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Jak monitorovat SAP HANA (velké instance) v Azure

SAP HANA v Azure (velké instance) se nijak neliší od jakéhokoli jiného nasazení IaaS – je potřeba monitorovat, co operačního systému a aplikací dělají a jak využívat aplikace v následujících zdrojích:

- Procesor
- Memory (Paměť)
- Šířka pásma sítě
- Místo na disku

S Azure Virtual Machines, budete muset zjistit, jestli postačí třídy prostředků výše uvedený nebo získat vyčerpán. Tady jsou další podrobnosti o každé z různých tříd:

**Spotřeba procesoru prostředků:** poměr, který SAP definovaných pro určité zatížení proti HANA je vynuceno abyste měli jistotu, že by měla být k dispozici pro seznámení se základními data uložená v paměti dostatek prostředků procesoru. Můžou však nastat případy, kde HANA využívá mnoho procesorů spouštěním dotazů z důvodu chybějících indexů nebo podobné problémy. To znamená, že byste měli sledovat využití prostředků procesoru jednotky velká instance HANA, stejně jako používané konkrétní služby HANA prostředků procesoru.

**Spotřeba paměti:** je důležité monitorovat z v rámci HANA, a také mimo HANA na jednotce. V rámci HANA sledujte, jak data spotřebovává přidělené paměti, abyste mohli zůstat v rámci požadované velikosti pokyny pro SAP HANA. Chcete monitorovat využití paměti na úrovni velkých instancí, abyste měli jistotu, že další nainstalovaný bez-softwaru HANA není spotřebovat příliš mnoho paměti a proto soutěžit s HANA paměti.

**Šířka pásma sítě:** brány virtuální sítě Azure má zobrazení omezenou šířku pásma data přesouvaná do virtuální sítě Azure, tak je užitečné k monitorování dat přijatých všech virtuálních počítačů pro Azure v rámci virtuální sítě zjistit, jak blízko vás jsou limity Azure skladové položky brány výběru braný. Na jednotce velká Instance HANA se smysl monitorovat příchozí a odchozí síťový provoz i a mějte přehled o svazky, které jsou zpracovány v čase.

**Volné místo na disku:** využití místa na disku se obvykle zvyšuje v čase. Nejčastější příčiny patří: objem dat se zvyšuje, provedení zálohy transakčního protokolu, ukládání souborů trasování a provádění snímků úložiště. Proto je důležité monitorovat využití místa na disku a spravovat místo na disku ke velká Instance HANA jednotce je přidružená.

Pro **SKU typu II** z velkých instancích HANA přechodu serveru s předem systému diagnostické nástroje. Můžete využít tyto diagnostické nástroje k provádění kontroly stavu systému. Spusťte následující příkaz, který generuje soubor protokolu kontroly stavu /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Pokud pracujete s týmem Microsoft Support chcete vyřešit nějaký problém, vám může také požádáni o zadání soubory protokolů pomocí diagnostických nástrojů. Soubor pomocí následujícího příkazu můžete zip.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Další kroky**

- Přečtěte si [monitorování SAP HANA (velké instance) v Azure](troubleshooting-monitoring.md).