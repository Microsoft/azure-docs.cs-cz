---
title: Azure Monitor pro virtuální počítače (preview) – známé problémy | Dokumentace Microsoftu
description: Tento článek popisuje známé problémy s monitorováním Azure pro virtuální počítače, řešení v Azure, která sloučí, aplikace závislost zjišťování, monitorování stavu a výkonu operačního systému virtuálního počítače Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2019
ms.author: magoedte
ms.openlocfilehash: 677fec21b7491398da5e4958441e5405e0c10e0e
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745669"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Známé problémy s monitorováním Azure pro virtuální počítače (preview)

Tento článek popisuje známé problémy s monitorováním Azure pro virtuální počítače, řešení v Azure, která kombinuje stavu, zjišťování součásti aplikace a sledování výkonu operačního systému virtuálního počítače Azure. 

## <a name="health"></a>Stav 
Následující seznam uvádí známé problémy v aktuální verzi funkce stavu:

- Panel vlastností virtuálního počítače zobrazí operační systém Windows Server 2019 jako Windows Server 2016. Tato chyba bude opravena v nadcházející verzi.
- Pokud virtuální počítač Azure je odebrání nebo odstranění, zobrazí se v zobrazení seznamu virtuálních počítačů pro nějakou dobu. Kromě toho kliknutím na stav virtuálního počítače odstraněny nebo odstraněné otevře **stav diagnostiky** zobrazení a poté zahájí smyčku načítání. Výběrem názvu odstraněného virtuálního počítače se otevře podokno se zpráva, že virtuální počítač se odstranil.
- Změny konfigurace, jako je aktualizace prahové hodnoty, trvat až 30 minut i v případě, že na portálu nebo rozhraní API sledování pracovní vytížení může je aktualizovat okamžitě. 
- Diagnostika stavu aktualizace docházet rychleji než ostatní zobrazení. Tyto informace můžou být zpožděné při přepínání mezi nimi. 
- Vypínání virtuálních počítačů některé stavu kritérií, která aktualizuje *kritické* a ostatním uživatelům *v pořádku*. Zobrazí se jako čistý stav virtuálního počítače *kritické*.
- Název stránku s výpisem stavu kritéria pro jedno zobrazení virtuálního počítače pro virtuální počítače s Linuxem, má celá doména název virtuálního počítače namísto uživatelem definovaný název virtuálního počítače. 
- Poté, co zakážete monitorování virtuálního počítače pomocí jedné z podporovaných metod a zkuste ho znovu nasadíte, měli byste nasadit do stejného pracovního prostoru. Pokud zvolíte jiný pracovní prostor a pokuste se zobrazit stav pro tento virtuální počítač, může zobrazovat nekonzistentní chování.
- Zobrazí celkové využití výkonu procesoru stavu kritéria pro Windows prahovou hodnotu *není rovno* **4**, tj. využití procesoru je větší než 95 % a délka fronty v systému je větší než 15. Toto kritérium stavu se nedá konfigurovat v této verzi preview.  
- Po odebrání součástí řešení z pracovního prostoru, může nadále zobrazovat stav z vašich virtuálních počítačů Azure; Konkrétně mapy data o výkonu a přejdete na některém zobrazení na portálu. Data se nakonec zastaví povolí, v zobrazení výkon a mapy za nějakou dobu; Zobrazení stavu budou ale nadále zobrazovat stav pro virtuální počítače. **Vyzkoušet** možnost je k dispozici na znovu připojit z výkon a mapy zobrazení.

## <a name="next-steps"></a>Další postup
Pochopení požadavků a metody pro povolení monitorování virtuálních počítačů, najdete v tématu [nasazení Azure Monitor pro virtuální počítače](vminsights-onboard.md).
