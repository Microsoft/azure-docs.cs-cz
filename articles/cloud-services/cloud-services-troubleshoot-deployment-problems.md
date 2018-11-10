---
title: Řešení problémů s nasazením cloudové služby | Dokumentace Microsoftu
description: Existuje několik běžných problémů, které můžete narazit při nasazení cloudové služby v Azure. Tento článek nabízí řešení pro některé z nich.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 348321592fc32f7dd77447c7217c7142f7ad9bd4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51226936"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Řešení problémů s nasazením cloudové služby
Když nasazujete balíček aplikace cloudové služby do Azure, můžete získat informace o nasazení z **vlastnosti** podokně webu Azure Portal. Podrobné informace v tomto podokně můžete použít k vyřešení problémů s cloudovou službou a zadání těchto informací k podpoře Azure při otevírání novou žádost o podporu.

Můžete najít **vlastnosti** podokně následujícím způsobem:

* Na webu Azure Portal, klikněte na možnost nasazení cloudové služby, klikněte na tlačítko **všechna nastavení**a potom klikněte na tlačítko **vlastnosti**.

> [!NOTE]
> Můžete zkopírovat obsah **vlastnosti** podokno do schránky. Kliknutím na ikonu v pravém horním rohu podokna.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problém: Nelze získat přístup k webu, ale Moje nasazení je spuštěna a všechny instance rolí jsou připravené
Odkaz na adresu URL webu na portálu nezobrazuje neobsahuje port. Výchozí port pro websites je 80. Pokud vaše aplikace je nakonfigurovaná pro spuštění v jiném portu, musíte přidat se správným číslem portu k adrese URL při přístupu k webu.

1. Na webu Azure Portal klikněte na nasazení cloudové služby.
2. V **vlastnosti** podokně webu Azure portal, zkontrolujte porty pro instance rolí (v části **vstupní koncové body**).
3. Nejsou-li port 80, přidejte hodnotu správného portu k adrese URL při přístupu k aplikaci. Chcete-li zadat jiný než výchozí port, zadejte adresu URL, za nímž následuje dvojtečka (:), za nímž následuje číslo portu, bez mezer.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problém: Moje instance role recyklovat bez mi teď zrovna nic nedělá
Služba opravy probíhá automaticky, když Azure zjistí problém uzly a instance rolí proto přesune do nové uzly. Pokud k tomu dojde, může se zobrazit vaše instance rolí recyklace automaticky. Chcete-li zjistit, pokud opravou služeb došlo k chybě:

1. Na webu Azure Portal klikněte na nasazení cloudové služby.
2. V **vlastnosti** podokně webu Azure portal, projděte si informace a určit, zda je během doby, který jste zaznamenali recyklování rolí došlo k opravou služeb.

Role se taky recyklaci přibližně jednou za měsíc během hostitelským operačním systémem a aktualizací hostovaného operačního systému.  
Další informace naleznete v příspěvku blogu [Role Instance se restartuje kvůli upgrady operačního systému](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problém: nelze provést prohození virtuálních IP adres a zobrazí chyba
Prohození virtuálních IP adres není povolená, pokud probíhá nasazení aktualizace. Aktualizace nasazení může dojít automaticky po:

* Nový operační systém hosta je k dispozici a jsou nakonfigurované pro automatické aktualizace.
* Obnova služby vyvolá.

Chcete-li zjistit, jestli automatické aktualizace vám brání to prohození virtuálních IP adres:

1. Na webu Azure Portal klikněte na nasazení cloudové služby.
2. V **vlastnosti** podokně webu Azure Portal, podívejte se na hodnotu **stav**. Pokud je **připravené**, zkontrolujte **poslední operace** zobrazíte, pokud jeden nedávno se stalo, které by mohly bránit prohození virtuálních IP adres.
3. Opakujte kroky 1 a 2 pro nasazení do produkčního prostředí.
4. Pokud je v procesu automatické aktualizace, počkejte na dokončení a zkuste provést prohození virtuálních IP adres.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problém: Role instance je opakování ve smyčce mezi spuštěno, inicializuje, zaneprázdněno a zastaveno
Tato podmínka by mohla indikovat potíže s kódem aplikace, balíčkem nebo konfiguračním souborem. V takovém případě byste měli vidět stav změna každých několik minut a na webu Azure portal může Řekněme, že něco jako **recyklace**, **zaneprázdněn**, nebo **inicializace**. To znamená, že je něco není v pořádku s aplikací, která je udržovat role instance spuštění.

Další informace o způsobu řešení tohoto problému naleznete v příspěvku blogu [Azure PaaS Compute diagnostická Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) a [běžné problémy, které můžou způsobit recyklaci rolí](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problém: Moje aplikace přestala pracovat
1. Na webu Azure Portal klikněte na instanci role.
2. V **vlastnosti** podokně webu Azure portal, vezměte v úvahu následující podmínky, které mají problém vyřešit:
   * Pokud se nedávno zastavil instanci role (můžete zkontrolovat hodnotu **přerušit počítání**), může být aktualizace nasazení. Počkejte, pokud se role instance obnoví fungování sama o sobě.
   * Pokud je role instance **zaneprázdněn**, zkontrolujte kód vaší aplikace a zjistěte, jestli [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) událost zpracovává. Můžete potřebovat k přidání nebo oprava kódu, který zpracovává tuto událost.
   * Projděte si diagnostických dat a řešení potíží se scénáři v blogovém příspěvku [Azure PaaS Compute diagnostická Data](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Pokud jste recyklovat cloudové služby, obnovíte vlastnosti pro nasazení, efektivně vymazat z nich informace pro původní problém.
>
>

## <a name="next-steps"></a>Další postup
Zobrazit další [článků o řešení potíží](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) pro cloudové služby.

Zjistěte, jak řešit problémy s cloudovou službou role pomocí Azure PaaS počítače diagnostická data, najdete v článku [Kevin Williamson blogovou sérii](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
