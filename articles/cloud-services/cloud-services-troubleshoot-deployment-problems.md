---
title: Cloudové služby řešení problémů s nasazením | Microsoft Docs
description: Existuje několik běžných problémů, které může nastat při nasazení cloudové služby Azure. Tento článek poskytuje řešení na některé z nich.
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
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 3c56a5750c9f8a6c59ea07c01c101f358331174b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2017
ms.locfileid: "26408175"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Cloudové služby řešení problémů s nasazením
Při nasazení balíčku aplikace cloudové služby Azure, můžete získat informace o nasazení z **vlastnosti** podokně na portálu Azure. Podrobné informace v tomto podokně můžete použít k vyřešení problémů s cloudovou službou a může poskytnout tyto informace k podpoře Azure, při otevření nové žádosti o podporu.

Můžete najít **vlastnosti** podokně následujícím způsobem:

* Na portálu Azure klikněte nasazení cloudové služby, klikněte na **všechna nastavení**a potom klikněte na **vlastnosti**.

> [!NOTE]
> Můžete kopírovat obsah **vlastnosti** podokně do schránky. Kliknutím na ikonu v pravém horním rohu podokna.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problém: Nelze získat přístup k webu, ale my nasazení je spuštěna a všechny instance rolí jsou připravené
Odkazu adresu URL webu, který je zobrazen v portálu nezahrnuje port. Výchozí port pro weby je 80. Pokud vaše aplikace je nakonfigurována pro spuštění v jiný port, musíte přidat správným číslem portu k adrese URL při přístupu k webu.

1. Na portálu Azure klikněte na tlačítko nasazení cloudové služby.
2. V **vlastnosti** podokně portálu Azure, zkontrolujte porty pro instance rolí (v části **vstupní koncové body**).
3. Pokud není port 80, přidejte hodnotu správný port na adresu URL při přístupu k aplikaci. Pokud chcete zadat jiný než výchozí port, zadejte adresu URL, následovaným dvojtečkou (:), za nímž následuje číslo portu, bez mezer.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problém: Instance Moje rolí recyklované bez mi jakékoli akce
Služba opravy probíhá automaticky, když zjistí problém uzlů Azure a proto přesune instance role na nové uzly. Když k tomu dojde, může se zobrazit instance role recyklace automaticky. Chcete-li zjistit, pokud služba opravy došlo k chybě:

1. Na portálu Azure klikněte na tlačítko nasazení cloudové služby.
2. V **vlastnosti** podokně portálu Azure, zkontrolujte informace a určit, zda službou opravy došlo v době, zjištěnými role recyklace.

Role se také recyklujte přibližně jednou za měsíc během hostitelským operačním systémem a aktualizací hostovaného operačního systému.  
Další informace naleznete v příspěvku blogu [Role Instance restartuje kvůli upgrady operačního systému](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problém: nelze provést prohození virtuálních IP adres a zobrazí chybová zpráva
Prohození virtuálních IP adres není povolená, pokud probíhá nasazení aktualizace. Aktualizace nasazení může dojít automaticky při:

* Nové hostovaný operační systém je k dispozici a jsou nakonfigurovány pro automatické aktualizace.
* Služba opravy nastane.

Chcete-li zjistit, pokud jste automatické aktualizace brání provádění prohození virtuálních IP adres:

1. Na portálu Azure klikněte na tlačítko nasazení cloudové služby.
2. V **vlastnosti** podokně portálu Azure, podívejte se na hodnotu **stav**. Pokud je **připraven**, zkontrolujte **poslední operace** chcete zobrazit, pokud jeden nedávno se stalo, by mohly bránit prohození virtuálních IP adres.
3. Opakujte kroky 1 a 2 pro produkční nasazení.
4. Pokud je v procesu automatické aktualizace, počkejte na její dokončení, než se pokusíte provést prohození virtuálních IP adres.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problém: Instanci role je opakování ve smyčce mezi spuštěno, Probíhá inicializace, zaneprázdněn a zastaveno
Tato podmínka by mohla indikovat potíže s kódem aplikace, balíčkem nebo konfiguračním souborem. V takovém případě byste měli vidět stav změna každých několik minut a portálu Azure může být uvedeno něco podobného jako **recyklace**, **zaneprázdněn**, nebo **Probíhá inicializace**. To znamená, že je něco chybně v aplikaci, která je uchovávání role instance spuštění.

Další informace o způsobu řešení tohoto problému naleznete v příspěvku blogu [Azure PaaS výpočetní diagnostická Data](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) a [běžné problémy, které způsobit recyklaci rolí](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problém: Moje aplikace přestala pracovat
1. Na portálu Azure klikněte na instanci role.
2. V **vlastnosti** podokně portálu Azure, zvažte následující podmínky, chcete-li problém vyřešit:
   * Pokud se nedávno zastavil instanci role (můžete zkontrolovat hodnotu **Abort počet**), může být nasazení aktualizace. Počkejte, pokud role instance obnoví funguje na svůj vlastní.
   * Pokud je instance role **zaneprázdněn**, zkontrolujte kódu aplikace a zjistěte, zda [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) událost je zpracovávána. Možná budete muset přidejte nebo odstraňte některé kód, který zpracovává této události.
   * Projít diagnostických dat a řešení potíží s scénáře v příspěvku na blogu [Azure PaaS výpočetní diagnostická Data](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Pokud jste recyklovat cloudové služby, resetujete vlastnosti pro nasazení, efektivně mazání informace pro původní problém.
>
>

## <a name="next-steps"></a>Další kroky
Zobrazení [řešení potíží s články](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) pro cloudové služby.

Informace o řešení problémů role služby cloudu pomocí Azure PaaS dat diagnostiky počítače, v tématu [řady blogu kevina Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
