---
title: Azure Monitor známých problémů virtuálních počítačů | Dokumentace Microsoftu
description: Azure Monitor pro virtuální počítače je řešení v Azure, která kombinuje stavu a výkonu monitorování operačního systému virtuálního počítače Azure, stejně jako automatické zjišťování mezi součástmi aplikace a závislosti s jiným prostředkům a mapuje komunikace mezi je. Tento článek popisuje známé problémy.
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
ms.date: 10/03/2018
ms.author: magoedte
ms.openlocfilehash: 43000993c6a26ef8d44e941f5235ebad7aeee66f
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248053"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>Známé problémy s monitorováním Azure pro virtuální počítače

Následující seznam uvádí známé problémy s funkcí stavu služby Azure Monitor pro virtuální počítače:

- Pokud virtuální počítač Azure neexistuje žádné další, protože byl odebrán nebo odstraněn, se zobrazí v zobrazení seznamu virtuálních počítačů pro tři až sedm dní. Kromě toho by spusťte kliknutím na stav virtuálního počítače odstraněny nebo odstraněné **stav diagnostiky** , zobrazení, které pak přejde do smyčky načítání. Okno s výběrem názvu odstraněného virtuálního počítače spustí a zobrazí se zpráva s informacemi o tom, že virtuální počítač se odstranil.
- Časové období a četnost kritéria stavu nelze změnit v této vydané verzi. 
- Kritéria stavu nejde zakázat. 
- Po připojení, může trvat dobu, než se data zobrazí ve službě Azure Monitor -> Virtual Machines -> Stav nebo z okna prostředků virtuálního počítače -> Přehled
- Diagnostika stavu prostředí aktualizace rychleji než všech ostatních zobrazeních, takže informace zpoždění může dojít při přepínání mezi zobrazeními  
- Souhrn výstrah pro stav virtuálního počítače k dispozici ve službě Azure Monitor jsou určeny pouze pro upozornění aktivovaných pro problémů stavu zjištěných pomocí monitorovaných virtuálních počítačů Azure.
- **Seznamu výstrah** zobrazení stránky v jedné virtuální počítač a Azure Monitor zobrazí upozorní, jehož stav monitorování je nastavena na "aktivace" v posledních 30 dní.  Nejsou konfigurovatelné. Ale po kliknutí na souhrn, jednou **seznam výstrah** zobrazení stránky je spuštěn, můžete změnit hodnotu filtr rozsahu obou monitorování stavu a čas.
- Na **seznamu výstrah** zobrazení stránky, doporučujeme nebudete muset měnit **typ prostředku**, **prostředků**, a **Monitor Service** filtry, jakmile byly nakonfigurovány specifické pro řešení (Tento seznam ukazuje že některá další pole jako ve srovnání s Azure monitor -> zobrazení seznamu výstrah).    
- Ve virtuálních počítačích s Linuxem **stav diagnostiky** zobrazení má celá doména název virtuálního počítače namísto uživatelem definovaný název virtuálního počítače.
- Vypínání virtuálních počítačů bude aktualizovat některé jeho stav kritéria do kritického stavu a dalších do stavu v pořádku s net stav virtuálního počítače v kritickém stavu.
- Závažnost výstrahy stavu nelze změnit, můžete pouze měly být povoleno nebo zakázáno.  Kromě toho některé závažnosti aktualizují v závislosti na stavu kritéria.
- Úprava jakékoli nastavení stavu instance kritérium, povede k úpravy nastavení stejné napříč všemi instancemi kritéria stavu stejného typu na virtuálním počítači. Například pokud prahová hodnota disku volného místa stavu kritérium instance odpovídající logický disk se upraví C:, pak tato prahová hodnota bude platit pro všechny ostatní logické disky zjišťování a monitorování pro stejný virtuální počítač.   
- Prahové hodnoty pro následující stav kritéria cílení virtuálního počítače s Windows nejsou upravitelné, od jejich stav v pořádku už nastavené **systémem** nebo **dostupné**. Při dotazu z [úlohy monitorování API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), zobrazuje stav stavu *comparisonOperator* hodnotu **LessThan** nebo **GreaterThan**s *prahová hodnota* hodnotu **4** služby nebo entity pokud:
   - Stav služby Klient DNS – služba není spuštěná. 
   - Stav služby Klient DHCP – služba není spuštěná. 
   - Stav služby vzdáleného volání Procedur – služba není spuštěná. 
   - Stav služby Windows firewall – služba není spuštěná.
   - Stav služby protokolu událostí Windows – služba není spuštěná. 
   - Stav služby serveru – služba není spuštěná. 
   - Stav služby vzdálené správy Windows – služba není spuštěná. 
   - Chyba systému souborů nebo poškození – je logický Disk není k dispozici

- Prahové hodnoty pro následující stav kritéria Linux nejsou upravitelné, od jejich stavu jsou již nastaveny na **true**.  Zobrazuje stav stavu *comparisonOperator* s hodnotou **LessThan** a *prahová hodnota* hodnotu **1** při dotazu z pracovního vytížení Monitorování rozhraní API pro entitu v závislosti na jeho kontextu:
   - Stav logického disku – logický disk není online / k dispozici
   - Stav disku – Disk není online / k dispozici
   - Stav síťového adaptéru – síťový adaptér je zakázána.  

- **Celkové využití výkonu procesoru** kritéria stavu ve Windows zobrazí prahovou hodnotu **nerovná se 4** z portálu a posílat dotaz z rozhraní API monitorování úloh, když je větší než 95 % využití procesoru a délka fronty v systému větší než 15. V této verzi nelze upravit toto kritérium stavu.  
- Změny konfigurace, jako je aktualizace prahové hodnoty, trvá až 30 minut, než se projeví i v případě, že na portálu nebo rozhraní API sledování pracovní vytížení může aktualizovat okamžitě.  
- Jednotlivé procesoru a kritéria úrovně stavu logický procesor nejsou k dispozici ve Windows, pouze **celkové využití procesoru** je k dispozici pro virtuální počítače s Windows.  
- Pravidla výstrah, které jsou definovány pro každé kritérium stavu se nezobrazí na portálu Azure portal. Je možné konfigurovat pouze z [úlohy monitorování API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) povolit nebo zakázat pravidlo výstrahy pro kontrolu stavu.  
- Přiřazení [skupiny akcí Azure Monitor](../monitoring-and-diagnostics/monitoring-action-groups.md) stavu výstrah není možné z portálu Azure portal. Budete muset použít rozhraní API nastavení oznámení nakonfigurovat skupinu akcí, až se spustí pokaždé, když se aktivuje upozornění na stav. V současné době je možné přiřadit skupin akcí na virtuálním počítači, tak, aby všechny *výstrahy týkající se stavu* aktivuje proti virtuálním počítači aktivovat stejné skupiny akcí. Neexistuje koncept skupiny samostatnou akci pro každý stav pravidlo upozornění, třeba tradiční upozornění v Azure. Kromě toho podporuje pouze akce skupiny oznamovat zasláním e-mailem nebo SMS při aktivaci upozornění na stav. 

## <a name="next-steps"></a>Další postup
Kontrola [připojení Azure Monitor pro virtuální počítače](monitoring-vminsights-onboard.md) vám pomohou pochopit požadavky a metody, pokud chcete povolit monitorování virtuálních počítačů.