---
title: Azure Monitor pro virtuální počítače (preview) – známé problémy | Dokumentace Microsoftu
description: Tento článek popisuje známé problémy s monitorováním Azure pro virtuální počítače, řešení v Azure, která kombinuje monitorování stavu a výkonu operačního systému virtuálního počítače Azure. Azure Monitor pro virtuální počítače také automaticky zjišťuje mezi součástmi aplikace a závislosti s jiným prostředkům a mapuje komunikace mezi nimi.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: d720a7401b9ed1188a01d3cc2cc9ec7b66b640ce
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091540"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Známé problémy s monitorováním Azure pro virtuální počítače (preview)

Tento článek popisuje známé problémy s monitorováním Azure pro virtuální počítače, řešení v Azure, která kombinuje monitorování stavu a výkonu operačního systému virtuálního počítače Azure. 

Následující seznam uvádí známé problémy s funkcí stavu:

- Stav funkce je povolená pro všechny virtuální počítače, které jsou připojené k pracovnímu prostoru Log Analytics. Takže i je při akci, která začíná a končí na jeden virtuální počítač.
- Poté co zakažte monitorování u virtuálního počítače pomocí podporované metody a zkuste ho znovu nasadíte, byste měli nasadit do stejného pracovního prostoru. Pokud používáte nový pracovní prostor a zkuste k zobrazení stavu pro tento virtuální počítač, může se zobrazit neobvyklé chování.
- Pokud virtuální počítač Azure je odebrání nebo odstranění, zobrazí se v zobrazení seznamu virtuálních počítačů pro tři až sedm dní. Kromě toho kliknutím na stav virtuálního počítače odstraněny nebo odstraněné otevře **stav diagnostiky** zobrazení a poté zahájí smyčku načítání. Výběrem názvu odstraněného virtuálního počítače se otevře podokno se zpráva, že virtuální počítač se odstranil.
- Časové období a četnost kritéria stavu nelze změnit v této vydané verzi. 
- Kritéria stavu nejde zakázat. 
- Po nasazení, může trvat dobu, než se data zobrazí v **Azure Monitor** > **virtuálních počítačů** > **stavu** podokně nebo  **Prostředek virtuálního počítače** > **Insights** podokně.
- Diagnostika stavu docházet rychleji než ostatní zobrazení aktualizace. Tyto informace můžou být zpožděné při přepínání mezi zobrazeními. 
- Souhrn výstrah, který je součástí Azure Monitor pro virtuální počítač se zobrazí jenom oznámení, že vzniknou problémy se stavem, které jsou zjištěny s monitorovat virtuální počítače Azure.
- **Seznamu výstrah** podokno v jednom virtuálním počítači a Azure Monitor se zobrazí oznámení, jejichž stav monitorování je nastavena na *aktivuje* za posledních 30 dní. Výstrahy nejsou konfigurovatelné. Ale po **seznam výstrah** se otevře podokno, můžete kliknout na souhrn, chcete-li změnit hodnotu filtr rozsahu obou monitorování stavu a čas.
- V **seznamu výstrah** podokno, doporučujeme místo abyste upravili **typ prostředku**, **prostředků**, a **Monitor Service** filtry. Jejich byl konfigurován specifické pro řešení. Tento seznam zobrazuje více polí než **Azure monitor** > **výstrahy** zobrazení seznamu nepodporuje.   
- Ve virtuálních počítačích s Linuxem **stav diagnostiky** zobrazení Celá doména název virtuálního počítače namísto uživatelem definovaný název virtuálního počítače.
- Vypínání virtuálních počítačů některé stavu kritérií, která aktualizuje *kritické* a ostatním uživatelům *v pořádku*. Zobrazí se jako čistý stav virtuálního počítače *kritické*.
- Závažnost výstrahy stavu se nedá upravit. Může být pouze povolené nebo zakázané. Kromě toho některé závažnosti se aktualizují na základě stavu kritéria.
- Při úpravě jakékoli nastavení instance kritérium stavu, jsou změněny všechny instance stejného typu na virtuálním počítači stav kritéria. Například pokud se změní prahovou hodnotu instance kritérium stavu volného místa disku, který odpovídá logický disk C:, tato prahová hodnota se vztahuje na všechny ostatní logické disky, které zjišťování a monitorování pro stejný virtuální počítač.  
- Prahové hodnoty pro kritéria, které se zaměřují virtuálního počítače s Windows nejsou upravitelné, protože jejich stavů jsou nastaveny na *systémem* nebo *dostupné*. Při dotazování stav z [úlohy monitorování API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), zobrazí *comparisonOperator* hodnotu **LessThan** nebo **GreaterThan** s *prahová hodnota* hodnotu **4** služby nebo entity pokud:
   - Stav služby Klient DNS – služba není spuštěná. 
   - Stav služby Klient DHCP – služba není spuštěná. 
   - Stav služby vzdáleného volání Procedur – služba není spuštěná. 
   - Stav služby Windows firewall – služba není spuštěná.
   - Stav služby protokolu událostí Windows – služba není spuštěná. 
   - Stav služby serveru – služba není spuštěná. 
   - Stav služby vzdálené správy Windows – služba není spuštěná. 
   - Chyba systému souborů nebo poškození – je logický Disk není k dispozici.

- Prahové hodnoty pro následující stav kritéria Linux nejsou upravitelné, protože jeho stav je již nastavena na *true*. Stav se zobrazí *comparisonOperator* s hodnotou **LessThan** a *prahová hodnota* hodnotu **1** při posílat dotaz z Úloha monitorování rozhraní API pro entitu, v závislosti na jeho kontextu:
   - Stav logického disku – logický disk není online / k dispozici
   - Stav disku – Disk není online / k dispozici
   - Stav síťového adaptéru – síťový adaptér je zakázána.  

- *Celkové využití výkonu procesoru* kritéria stavu ve Windows zobrazí prahovou hodnotu **nerovná se 4** v portál a API sledování pracovní vytížení. Když je dosaženo prahové hodnoty *celkové využití výkonu procesoru* je větší než 95 procent a systém délka fronty je větší než 15. V této verzi nelze upravit toto kritérium stavu. 
- Změny konfigurace, jako je aktualizace prahové hodnoty, trvat až 30 minut i v případě, že na portálu nebo rozhraní API sledování pracovní vytížení může je aktualizovat okamžitě. 
- Jednotlivé procesoru a kritéria úrovně stavu logický procesor nejsou dostupné ve Windows. Pouze celkové využití procesoru je k dispozici pro virtuální počítače s Windows. 
- Pravidla výstrah, které jsou definovány pro každé kritérium stavu se nezobrazují na portálu Azure portal. Můžete povolit nebo zakázat upozornění na stav pouze v pravidlo [úlohy monitorování rozhraní API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager). 
- Nelze přiřadit [skupiny akcí Azure Monitor](../../monitoring-and-diagnostics/monitoring-action-groups.md) pro výstrahy týkající se stavu na webu Azure Portal. Pouze rozhraní API nastavení oznámení můžete použít ke konfiguraci skupiny akcí až se spustí pokaždé, když se aktivuje upozornění na stav. V současné době můžete přiřadit skupiny akcí na virtuálním počítači tak, aby všechny *výstrahy týkající se stavu* aktivuje proti aktivační událost virtuální počítač stejné skupiny akcí. Na rozdíl od tradičních upozornění v Azure neexistuje koncept skupiny samostatnou akci pro každé pravidlo výstrahy stavu. Kromě toho pouze skupiny akcí, které jsou nakonfigurované na poskytování e-mailu nebo oznámení SMS jsou podporovány, když se aktivuje upozornění na stav. 

## <a name="next-steps"></a>Další postup
Pochopení požadavků a metody pro povolení monitorování virtuálních počítačů, najdete v tématu [nasazení Azure Monitor pro virtuální počítače](vminsights-onboard.md).
