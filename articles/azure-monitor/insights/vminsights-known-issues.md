---
title: Azure Monitor pro virtuální počítače (preview) – známé problémy | Dokumentace Microsoftu
description: Tento článek se zabývá známými problémy s Azure Monitor pro virtuální počítače, řešením v Azure, které kombinuje stav, zjišťování závislosti aplikací a monitorování výkonu operačního systému virtuálního počítače Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/02/2019
ms.openlocfilehash: b59e2d1897557b47bcfeafbc17141f869e2f192e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450680"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Známé problémy s monitorováním Azure pro virtuální počítače (preview)

Tento článek se věnuje známým problémům s Azure Monitor pro virtuální počítače, řešením v Azure, které kombinuje stav, zjišťování součástí aplikace a monitorování výkonu operačního systému virtuálního počítače Azure. 

## <a name="health"></a>Stav akce 
Níže jsou uvedené známé problémy s aktuální verzí funkce Health:

- Pokud dojde k odebrání nebo odstranění virtuálního počítače Azure, zobrazí se v zobrazení seznamu virtuálních počítačů pro nějakou dobu. Kromě toho kliknutím na stav virtuálního počítače odstraněny nebo odstraněné otevře **stav diagnostiky** zobrazení a poté zahájí smyčku načítání. Výběrem názvu odstraněného virtuálního počítače se otevře podokno se zpráva, že virtuální počítač se odstranil.
- Změny konfigurace, jako je aktualizace prahové hodnoty, trvat až 30 minut i v případě, že na portálu nebo rozhraní API sledování pracovní vytížení může je aktualizovat okamžitě. 
- Prostředí pro diagnostiku stavu se aktualizuje rychleji než ostatní zobrazení. Tyto informace mohou být při přepínání mezi nimi zpožděny. 
- V případě virtuálních počítačů se systémem Linux má název stránky uvádějící kritéria stavu pro jedno zobrazení virtuálního počítače místo názvu virtuálního počítače definovaného uživatelem celý název tohoto virtuálního počítače. 
- Když zakážete monitorování pro virtuální počítač pomocí některé z podporovaných metod a zkusíte ho nasadit znovu, měli byste ho nasadit do stejného pracovního prostoru. Pokud zvolíte jiný pracovní prostor a pokusíte se zobrazit stav tohoto virtuálního počítače, může se zobrazit nekonzistentní chování.
- Po odebrání součástí řešení z pracovního prostoru můžete dál zobrazovat stav z vašich virtuálních počítačů Azure. Konkrétně data o výkonu a mapování dat při přechodu na libovolný pohled na portálu. Data se nakonec přestanou zobrazovat v zobrazení výkon a mapa po nějaké době; zobrazení stavu však bude i nadále zobrazovat stav virtuálních počítačů. Možnost **vyzkoušet nyní** bude k dispozici pro opětovné zprovoznění jenom z zobrazení výkon a mapa.

## <a name="next-steps"></a>Další kroky
Pokud chcete pochopit požadavky a metody pro povolení monitorování virtuálních počítačů, přečtěte si téma [povolení Azure monitor pro virtuální počítače](vminsights-enable-overview.md).
