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
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 819c3e74355cf80c7a998abb8b02b10c9e077059
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062764"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>Známé problémy s monitorováním Azure pro virtuální počítače

Následující seznam uvádí známé problémy s funkcí stavu služby Azure Monitor pro virtuální počítače:

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
- Prahové hodnoty pro některá kritéria stav Windows jako stav klienta služby DNS nejsou upravitelné, protože jejich stav v pořádku je již uzamčena na **systémem**, **dostupné** stavu služby nebo entity v závislosti na kontextu.  Místo toho hodnota představuje číslo 4, převede se na skutečné zobrazovaný řetězec v budoucí verzi.  
- Prahové hodnoty pro stav kritérií Linux nejsou upravitelné, jako je například stav logického disku, protože jsou již nastaveny pro aktivaci stav není v pořádku.  Tyto zprávy značí, zda něco online i offline, nebo zapnout nebo vypnout a jsou reprezentovány a stejné tím, že zobrazuje hodnotu 1 nebo 0.
- Aktualizuje filtr skupin prostředků v libovolné skupině prostředků se při používání ve velkém měřítku Azure monitorování -> Virtual Machines -> Stav -> všechny zobrazení seznamu s Zkontrolujte předem vybrané předplatné a skupinu prostředků, způsobí, že zobrazení seznamu zobrazíte **žádnývýsledek**.  Přejděte zpět do Azure monitoru -> Virtual Machines -> kartu Ochrana a vyberte požadované předplatné a skupinu prostředků a pak přejděte do zobrazení seznamu.

## <a name="next-steps"></a>Další postup
Kontrola [připojení Azure Monitor pro virtuální počítače](monitoring-vminsights-onboard.md) vám pomohou pochopit požadavky a metody, pokud chcete povolit monitorování virtuálních počítačů.