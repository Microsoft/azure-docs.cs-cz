---
title: Správa aktualizací a oprav pro virtuální počítače v Azure Automation
description: V tomto článku se dozvíte, jak pomocí Update Management spravovat aktualizace a opravy pro virtuální počítače Azure a mimo Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: c86c9049bc0afc81f5dfd8553d2aa98cfd4b1a46
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915978"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Správa aktualizací a oprav pro virtuální počítače

Aktualizace softwaru v Azure Automation Update Management poskytují sadu nástrojů a prostředků, které vám pomůžou spravovat složité úlohy sledování a použití aktualizací softwaru na počítačích v Azure a hybridním cloudu. Účinný proces správy aktualizací softwaru je nutný k udržení provozní efektivity, překonání problémů se zabezpečením a omezení rizik zvýšeného o hrozbách zabezpečení na internetovém základě. Ale kvůli měnící se povaze technologie a neustálému výskytu nových hrozeb zabezpečení vyžaduje účinná správa aktualizací softwaru důslednou a nepřetržitou pozornost.

> [!NOTE]
> Update Management podporuje nasazení aktualizací od první strany a jejich předběžné stažení. Tato podpora vyžaduje změny v aktualizovaných systémech. Informace o tom, jak nakonfigurovat tato nastavení v systémech, najdete v tématu [konfigurace web Windows Updateho nastavení Azure Automation Update Management](configure-wuagent.md) .

Než se pokusíte spravovat aktualizace pro vaše virtuální počítače, ujistěte se, že jste povolili Update Management na těchto počítačích pomocí jedné z těchto metod:

* [Povolení Update Managementu z účtu Automation](enable-from-automation-account.md)
* [Povolení Update Management procházením Azure Portal](enable-from-portal.md)
* [Povolení Update Managementu z runbooku](enable-from-runbook.md)
* [Povolení Update Managementu z virtuálního počítače Azure](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Omezení rozsahu nasazení

Update Management používá v rámci pracovního prostoru konfiguraci oboru pro cílení na počítače, aby přijímaly aktualizace. Další informace najdete v tématu [omezení rozsahu nasazení Update Management](scope-configuration.md).

## <a name="compliance-assessment"></a>Posouzení dodržování předpisů

Před nasazením aktualizací softwaru do počítačů Zkontrolujte výsledky vyhodnocení dodržování předpisů aktualizací pro povolené počítače. Pro každou aktualizaci softwaru se zaznamená stav dodržování předpisů a po dokončení vyhodnocení se bude shromažďovat a předávány hromadně do Azure Monitor protokolů.

V počítači s Windows se kontrola kompatibility ve výchozím nastavení spouští každých 12 hodin a inicializuje se do 15 minut od Log Analytics agenta pro Windows se restartuje. Data posouzení se pak předají do pracovního prostoru a **aktualizuje tabulku Updates** . Před a po instalaci aktualizace se provede kontrola dodržování předpisů aktualizací k identifikaci chybějících aktualizací, ale výsledky se nepoužívají k aktualizaci dat posouzení v tabulce.

Je důležité si projít naše doporučení, jak [nakonfigurovat klienta web Windows Update](configure-wuagent.md) s Update Management, aby se předešlo jakýmkoli problémům, které brání jeho správné správě.

U počítače se systémem Linux se kontrola dodržování předpisů provádí ve výchozím nastavení každou hodinu. Pokud je restartován agent Log Analytics pro Linux, zahájí se kontrola kompatibility do 15 minut.

Výsledky dodržování předpisů jsou uvedeny v Update Management pro každý vyhodnocený počítač. Může trvat až 30 minut, než se na řídicím panelu zobrazí aktualizovaná data z nového počítače s povolenou správou.

Informace o tom, jak zobrazit výsledky dodržování předpisů, najdete v tématu [monitorování aktualizací softwaru](view-update-assessments.md) .

## <a name="deploy-updates"></a>Nasazení aktualizací

Po kontrole výsledků dodržování předpisů je fáze nasazení aktualizace softwaru proces nasazení aktualizací softwaru. Pokud chcete nainstalovat aktualizace, naplánujte nasazení, které se zarovnává s plánem vydávání verzí a oknem služby. Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

Přečtěte si téma [nasazení aktualizací softwaru](deploy-updates.md) , kde se dozvíte, jak naplánovat nasazení aktualizace.

## <a name="review-update-deployments"></a>Kontrola nasazení aktualizací

Po dokončení nasazení zkontrolujte proces a určete úspěšnost nasazení aktualizací podle počítače nebo cílové skupiny. Informace o tom, jak můžete monitorovat stav nasazení, najdete v tématu [Kontrola stavu nasazení](deploy-updates.md#check-deployment-status) .

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak vytvořit výstrahy, které vás upozorní na výsledky nasazení aktualizace, najdete v tématu [vytvoření výstrah pro Update Management](configure-alerts.md).

* Můžete zadat [dotaz na protokoly Azure monitor](query-logs.md) , abyste mohli analyzovat posouzení aktualizací, nasazení a další související úlohy správy. Obsahuje předem definované dotazy, které vám pomůžou začít.