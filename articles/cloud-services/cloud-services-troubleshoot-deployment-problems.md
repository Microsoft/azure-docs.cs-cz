---
title: Poradce při potížích s nasazením cloudové služby | Dokumenty společnosti Microsoft
description: Existuje několik běžných problémů, které můžete narazit při nasazování cloudové služby do Azure. Tento článek obsahuje řešení pro některé z nich.
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
ms.openlocfilehash: ccb08f853ae0f941dd5f9c0eca8c77f0f650905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122752"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Poradce při potížích s nasazením cloudové služby
Když nasadíte balíček aplikací cloudové služby do Azure, můžete získat informace o nasazení z **podokna Vlastnosti** na webu Azure Portal. Podrobnosti v tomto podokně můžete použít k řešení problémů s cloudovou službou a tyto informace můžete poskytnout podpoře Azure při otevírání nové žádosti o podporu.

Podokno **Vlastnosti** najdete takto:

* Na webu Azure Portal klikněte na nasazení cloudové služby, klikněte na **Všechna nastavení**a potom klikněte na **Vlastnosti**.

> [!NOTE]
> Obsah podokna **Vlastnosti** můžete zkopírovat do schránky klepnutím na ikonu v pravém horním rohu podokna.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problém: Nemám přístup k webu, ale moje nasazení je spuštěno a všechny instance rolí jsou připraveny
Odkaz URL webu zobrazený na portálu neobsahuje port. Výchozí port pro weby je 80. Pokud je aplikace nakonfigurována tak, aby se spouštěla v jiném portu, je nutné při přístupu na web přidat správné číslo portu k adrese URL.

1. Na webu Azure Portal klikněte na nasazení cloudové služby.
2. V podokně **Vlastnosti** na portálu Azure zkontrolujte porty pro instance rolí (v části **Vstupní koncové body).**
3. Pokud port není 80, přidejte správnou hodnotu portu do adresy URL při přístupu k aplikaci. Chcete-li zadat nevýchozí port, zadejte adresu URL následovanou dvojtečkou (:), následovanou číslem portu bez mezer.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problém: Moje role instance recyklovány, aniž bych něco dělat
Service retušování dochází automaticky, když Azure zjistí problém uzly a proto přesune instance role do nových uzlů. V takovém případě se může zobrazit vaše role instance recyklace automaticky. Chcete-li zjistit, zda došlo k ozdravení služby:

1. Na webu Azure Portal klikněte na nasazení cloudové služby.
2. V podokně **Vlastnosti** na webu Azure Portal zkontrolujte informace a zjistěte, zda došlo k ozdravení služeb během doby, kdy jste sledovali recyklaci rolí.

Role budou také recyklovat zhruba jednou za měsíc během aktualizace host-OS a host-OS.  
Další informace naleznete v příspěvku [blogu, že se instance role restartuje kvůli upgradům operačního systému.](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problém: Nemohu udělat VIP swap a obdrží chybu
Probíhá výměna virtuálních ip informací, pokud probíhá aktualizace nasazení. K aktualizacím nasazení může dojít automaticky, když:

* K dispozici je nový hostovaný operační systém, který je konfigurován pro automatické aktualizace.
* Dojde k hojení služby.

Chcete-li zjistit, zda vám automatická aktualizace brání v provedení výměny VIP:

1. Na webu Azure Portal klikněte na nasazení cloudové služby.
2. V podokně **Vlastnosti** na portálu Azure se podívejte na hodnotu **Stav**. Pokud je **připraven**, zkontrolujte **poslední operaci,** zda došlo k jedné nedávno, která by mohla zabránit výměně VIP.
3. Opakujte kroky 1 a 2 pro produkční nasazení.
4. Pokud probíhá automatická aktualizace, počkejte na její dokončení, než se pokusíte provést výměnu programu VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problém: Instance role je opakování mezi Spuštěno, Inicializace, Zaneprázdněn a Zastaveno
Tato podmínka by mohla indikovat potíže s kódem aplikace, balíčkem nebo konfiguračním souborem. V takovém případě byste měli být schopni vidět stav mění každých několik minut a portál Azure může říct něco jako **recyklace**, **Zaneprázdněn**nebo **Inicializace**. To znamená, že je něco v nepořádku s aplikací, která brání spuštění instance role.

Další informace o tom, jak tento problém vyřešit, najdete v příspěvku blogu [Azure PaaS Compute Diagnostics Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) a [běžné problémy, které způsobují recyklovací role](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problém: Aplikace přestala fungovat
1. Na webu Azure Portal klikněte na instanci role.
2. V podokně **Vlastnosti** na webu Azure Portal zvažte následující podmínky, jak problém vyřešit:
   * Pokud byla instance role nedávno zastavena (můžete zkontrolovat hodnotu **počtu přerušení),** může být nasazení aktualizováno. Počkejte, pokud instance role pokračuje v práci samostatně.
   * Pokud je instance role **Zaneprázdněná**, zkontrolujte kód aplikace a zjistěte, zda je zpracována událost [StatusCheck.](/previous-versions/azure/reference/ee758135(v=azure.100)) Možná budete muset přidat nebo opravit nějaký kód, který zpracovává tuto událost.
   * Projděte si diagnostická data a scénáře řešení potíží v příspěvku blogu [Azure PaaS Compute Diagnostics Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Pokud recyklujete cloudovou službu, resetujete vlastnosti pro nasazení a efektivně vymažete informace o původním problému.
>
>

## <a name="next-steps"></a>Další kroky
Zobrazit další [články o řešení potíží](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) pro cloudové služby.

Informace o řešení problémů s rolí cloudové služby pomocí dat diagnostiky počítače Azure PaaS najdete v [tématu Kevin Williamson blog series](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
