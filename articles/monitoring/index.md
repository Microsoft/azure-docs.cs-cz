---
title: Azure Management – monitorování | Microsoft Docs
description: Azure má několik služeb a nástroje, které společně poskytují kompletní správu pro nejen vaší aplikace, které jsou spuštěné v Azure, ale také v ostatních cloudů a místně.  Tento článek obsahuje nejvyšší úrovni popis různé oblasti správy a odkazy na obsah v Azure nástroje pro správu cloudových aplikací a prostředky.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: bwren
ms.openlocfilehash: b20283e1189e4f1a3555e2dd8d25972c9a677cd6
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="azure-management---monitoring"></a>Azure Management – monitorování

Sledování v Azure je jeden aspekt jejich správy Azure.  Tento článek stručně popisuje různé oblasti správy, které jsou nezbytné pro nasazení a údržbu vaše aplikace a prostředky v Azure s odkazy na dokumentaci, abyste mohli začít.

## <a name="management-in-azure"></a>Správa v Azure

Správa odkazuje na úlohy a procesy nezbytné zachování obchodních aplikací a prostředky, které je podporují.  Azure má několik služeb a nástroje, které společně poskytují kompletní správu pro nejen vaší aplikace, které jsou spuštěné v Azure, ale také v ostatních cloudů a místně.  Pochopení různých nástrojů, které jsou k dispozici a jak mohou být použity společně pro celou řadu scénářů správy je prvním krokem při navrhování kompletní správu prostředí.

Následující diagram znázorňuje různé oblasti správy, které mají povinnost zachovávat všechny aplikace nebo prostředku.  Tyto různé oblasti si lze představit z hlediska životní kterých každý se vyžaduje průběžné po sobě přes životnost prostředku.  Spustí se s jeho počáteční nasazení prostřednictvím nepřetržitý provoz, a nakonec po vyřazení.

![Možnosti správy](media/management-overview/management-capabilities.png)


Žádná jednoho Azure služba úplně doplní požadavky na správu konkrétní oblasti, ale místo toho každý realizován pomocí několika služeb společně.  Některé služby poskytovat cílové funkce, například Application Insights, která poskytuje monitorování pro webové aplikace.  Zadejte jiné běžné funkce jako je například analýzy protokolů, která ukládá data správy pro jiné služby, což umožňuje analyzovat data různých typů získané nástrojem různé služby.  

Následující části stručně popisují různé oblasti správy a poskytuje odkazy na podrobná obsah na hlavní služby Azure určený k jejich vyřešení.

## <a name="monitor"></a>Sledovat
Sledování je v rámci shromažďování a analýzy dat o výkonu, stavu a dostupnosti obchodní aplikace a prostředky, které závisí na. Účinné strategie sledování vám pomůže porozumět podrobné operaci různé součásti aplikace a zvýšit vaši provozu proaktivně vás upozorní na zásadních problémů, aby předtím, než začnou způsobovat problémy, můžete je vyřešit.  Můžete si přečíst Přehled monitorování v Azure, který identifikuje různé služby používané pro strategie monitorování v [monitorování Azure aplikacím a prostředkům](monitoring-overview.md).


## <a name="configure"></a>Konfigurovat
Konfigurace se vztahuje k počáteční nasazení a konfigurace aplikací a prostředků a jejich následné údržbě s oprav a aktualizací.  Automatizace tyto úlohy pomocí skriptů a zásad umožňuje vyloučit redundance, minimalizovat čas a úsilí a zvýšit přesnost a efektivitu.  [Služby Azure Automation](..\automation\automation-intro.md) poskytuje hromadné služeb pro automatizaci úloh konfigurace.  Kromě sady runbook pro automatizaci procesů poskytuje konfigurace a Správa aktualizací, které vám pomůže při správě konfigurace prostřednictvím zásad a na zjištění a nasazení aktualizací.

## <a name="govern"></a>Řídí
Zásady správného řízení poskytuje mechanismy a postupy, chcete-li zachovat kontrolu nad aplikacím a prostředkům v Azure.  Zahrnuje plánování vaší iniciativy a nastavování priorit strategické?.  Zásady správného řízení v Azure je primárně implementováno s dvě služby.  [Azure zásad](../azure-policy/azure-policy-introduction.md) vám umožní vytvořit, přiřadit a spravovat zásady definice, které vynucují různá pravidla a akce přes vaše prostředky tak, aby tyto prostředky zůstat kompatibility s firemními standardy a smlouvy o úrovni služeb. [Azure náklady na správu Cloudyn](../cost-management/overview.md) umožňuje sledovat využití cloudu a výdaje vašich prostředků Azure a jiných poskytovatelů cloudu, včetně AWS a Google.

## <a name="secure"></a>Zabezpečení
Správa zabezpečení vaší aplikace, prostředky a data zahrnuje kombinaci hodnocení hrozeb, shromažďování a analýza zabezpečení dat a zajištění, že vaše aplikace a prostředky jsou navržená tak a nakonfigurovat zabezpečené způsobem.  Analýza monitorování a ohrožení zabezpečení jsou poskytovány [Azure Security Center](../security-center/security-center-intro.md) což zahrnuje unified správu zabezpečení a ochrana před internetovými útoky pokročilé napříč hybridní Cloudová pracovní prostředí.  Měli byste taky vidět [Úvod do Azure zabezpečení](../security/azure-security.md) komplexní informace o zabezpečení ve službě Azure a pokyny k bezpečně konfigurace prostředků Azure.


## <a name="protect"></a>Ochrana
Ochrana se týká to umožňuje zajistit, že vaše aplikace a data jsou vždy k dispozici, i v případě výpadků nad rámec vlastního ovládacího prvku.  Dvě služby zajišťuje ochranu v Azure.  [Zálohování Azure](../backup/backup-introduction-to-azure-backup.md) zajišťuje zálohování a obnovení dat, buď v cloudu nebo místně.    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) zajistí vysokou dostupnost vaší aplikace poskytnutím kontinuity podnikových procesů a okamžitého obnovení v případě havárie.

## <a name="migrate"></a>Migrovat 
Migrace odkazovala na přechod úlohy aktuálně spuštěné místně do cloudu Azure.  [Azure migrací](../migrate/migrate-overview.md) je služba, která vám pomůže vyhodnocení vhodnosti migrace, včetně nastavení velikosti na základě výkonu a náklady odhady místního virtuálních počítačů do Azure.  Azure Site Recovery můžete provést migraci virtuálních počítačů [z místního](../site-recovery/migrate-tutorial-on-premises-azure.md) nebo [z Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  [Azure migrace databáze](../dms/dms-overview.md) vám pomůžou s více zdrojů databáze migrace pro platformy Azure Data.