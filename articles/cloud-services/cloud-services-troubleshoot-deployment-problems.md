---
title: Řešení problémů s nasazením cloudové služby | Microsoft Docs
description: Při nasazení cloudové služby do Azure je možné, že narazíte na několik běžných problémů. Tento článek poskytuje řešení pro některé z nich.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 3b86ff205981cfe7bb00ecb3ca58804fba3e80b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460095"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Řešení problémů s nasazením cloudové služby
Když nasadíte balíček aplikace cloudové služby do Azure, můžete získat informace o nasazení z podokna **vlastnosti** v Azure Portal. Pomocí podrobností v tomto podokně můžete vyřešit problémy s cloudovou službou a při otevření nové žádosti o podporu můžete tyto informace poskytnout podpoře Azure.

Podokno **vlastnosti** můžete najít takto:

* V Azure Portal klikněte na nasazení cloudové služby, klikněte na **všechna nastavení**a potom klikněte na **vlastnosti**.

> [!NOTE]
> Obsah podokna **vlastnosti** můžete zkopírovat do schránky kliknutím na ikonu v pravém horním rohu podokna.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problém: Nemám přístup k webu, ale moje nasazení je spuštěné a všechny instance rolí jsou připravené.
Odkaz na adresu URL webu zobrazený na portálu nezahrnuje port. Výchozí port pro weby je 80. Pokud je vaše aplikace nakonfigurovaná tak, aby běžela na jiném portu, musíte při přístupu k webu přidat správné číslo portu k adrese URL.

1. V Azure Portal klikněte na nasazení cloudové služby.
2. V podokně **vlastnosti** Azure Portal ověřte porty pro instance rolí (v části **vstupní koncové body**).
3. Pokud port není 80, přidejte do adresy URL správnou hodnotu portu při přístupu k aplikaci. Pokud chcete zadat jiný než výchozí port, zadejte adresu URL následovaný dvojtečkou (:) následovaný číslem portu bez mezer.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problém: moje instance rolí se recykluje bez toho, aby mě prováděla cokoli
K automatickému výskytu služby dochází, když Azure detekuje uzly problémů, a proto přesune instance rolí na nové uzly. Pokud k tomu dojde, může se vaše instance role automaticky zobrazovat. Zjištění, zda došlo ke službě Service retušovací:

1. V Azure Portal klikněte na nasazení cloudové služby.
2. V podokně **vlastnosti** Azure Portal zkontrolujte informace a určete, zda během doby recyklace rolí došlo k této službě.

Role se v průběhu aktualizací pro operační systémy a hostované operační systémy recyklují také přibližně jednou za měsíc.  
Další informace najdete v tématu [restart instance role blogového příspěvku z důvodu upgradů operačního systému](https://docs.microsoft.com/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades) .

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problém: nejde provést swap VIP a získat chybu.
Swap VIP není povolený, pokud probíhá aktualizace nasazení. K aktualizacím nasazení může dojít automaticky v těchto případech:

* K dispozici je nový hostovaný operační systém, který jste nakonfigurovali pro automatické aktualizace.
* Dojde k chybě služby.

Zjistit, jestli vám automatické aktualizace znemožňuje provést prohození VIP:

1. V Azure Portal klikněte na nasazení cloudové služby.
2. V podokně **vlastnosti** Azure Portal se podívejte na hodnotu **stav**. Pokud je **připravená**, zkontrolujte **poslední operaci** , abyste viděli, jestli se nedávno nastalo, která by mohla zabránit prohození VIP.
3. Opakujte kroky 1 a 2 pro produkční nasazení.
4. Pokud probíhá automatická aktualizace, počkejte, než se dokončí, než se pokusíte provést prohození VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problém: instance role je mezi spuštěním, inicializací, zaneprázdněnou a zastavenou smyčkou.
Tato podmínka by mohla indikovat potíže s kódem aplikace, balíčkem nebo konfiguračním souborem. V takovém případě byste si měli být schopni zobrazit změny stavu každých několik minut a Azure Portal může například dojít k **recyklaci**, **zaneprázdnění**nebo **inicializaci**. To znamená, že u aplikace, která udržuje instanci role v běhu, dochází k nějakému problému.

Další informace o tom, jak tento problém vyřešit, najdete v blogovém příspěvku [Data diagnostiky Azure PaaS COMPUTE](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data) a [běžné problémy, které způsobují recyklaci rolí](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problém: aplikace zastavila práci
1. V Azure Portal klikněte na instanci role.
2. V podokně **vlastnosti** Azure Portal zvažte následující podmínky pro vyřešení vašeho problému:
   * Pokud se instance role v nedávné době zastavila (můžete zaškrtnout hodnotu **počet přerušení**), nasazení se může aktualizovat. Počkejte na zjištění, zda instance role pokračuje ve svém vlastním fungování.
   * Pokud je instance role **zaneprázdněná**, zkontrolujte kód aplikace a podívejte se, jestli se zpracovává událost [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) . Možná budete muset přidat nebo opravit nějaký kód, který zpracovává tuto událost.
   * Projděte si scénáře diagnostiky dat a řešení potíží v blogu post [Data diagnostiky Azure PaaS COMPUTE](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

> [!WARNING]
> Pokud recykluje svou cloudovou službu, resetujete vlastnosti nasazení a efektivně smazáni informace o původním problému.
>
>

## <a name="next-steps"></a>Další kroky
Podívejte se na další články týkající se [řešení potíží](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) pro Cloud Services.

Informace o řešení potíží s rolemi cloudové služby pomocí dat diagnostiky počítačů Azure PaaS najdete v tématu [série blogů v Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
