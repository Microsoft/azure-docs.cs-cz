---
title: Postup migrace serverů s podporou ARC Azure v různých oblastech
description: Přečtěte si, jak migrovat server s podporou Azure ARC z jedné oblasti do druhé.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: d4e0f1e41e928ab489f7c2c167eea31785d9bc21
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417349"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>Postup migrace serverů s podporou ARC Azure v různých oblastech

Existují situace, kdy byste chtěli přesunout existující server s povoleným obloukem Azure z jedné oblasti do jiného. Například jste provedli registraci počítače v nesprávné oblasti, ke zlepšení spravovatelnosti nebo k přesunu z důvodů správy.

Pokud chcete migrovat server s podporou Azure ARC z jedné oblasti Azure na jiný, musíte odinstalovat rozšíření virtuálních počítačů, odstranit prostředek v Azure a znovu ho vytvořit v jiné oblasti. Před provedením těchto kroků byste měli auditovat počítač, aby se ověřilo, která rozšíření virtuálních počítačů jsou nainstalována.

> [!NOTE]
> I když jsou nainstalovaná rozšíření i nadále spuštěná a po dokončení tohoto postupu provádějí jejich normální činnost, nebudete je moct spravovat. Pokud se pokusíte znovu nasadit rozšíření na počítači, může docházet k nepředvídatelnému chování.

## <a name="move-machine-to-other-region"></a>Přesunout počítač do jiné oblasti

> [!NOTE]
> Během této operace dojde během migrace k výpadkům.

1. Pomocí rozhraní příkazového [řádku Azure](manage-vm-extensions-cli.md#remove-an-installed-extension)nebo pomocí [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension)odeberte rozšíření virtuálních počítačů nainstalovaná z [Azure Portal](manage-vm-extensions-portal.md#uninstall-extension).

2. Pomocí nástroje **azcmagent** s parametrem [odpojení](manage-agent.md#disconnect) odpojte počítač od Arc Azure a odstraňte prostředek počítače z Azure. Odpojení počítače od serverů s podporou ARC neodebere agenta připojeného počítače a nebudete muset agenta odebírat jako součást tohoto procesu. Tuto možnost můžete spustit ručně během interaktivního přihlášení nebo automatizovat pomocí stejného instančního objektu, který jste použili k připojení více agentů, nebo pomocí [přístupového tokenu](../../active-directory/develop/access-tokens.md)platformy Microsoft identity. Pokud jste nepoužívali instanční objekt k registraci počítače se servery s podporou ARC Azure, přečtěte si následující [článek](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) a vytvořte instanční objekt.

3. Znovu zaregistrujte agenta připojeného počítače se servery s podporou ARC v jiné oblasti. Spusťte `azcmagent` Nástroj s parametrem [Connect](manage-agent.md#connect) a dokončete tento krok.

4. Znovu nasaďte rozšíření virtuálních počítačů, které byly původně nasazeny do počítače ze serverů s podporou ARC. Pokud jste nasadili agenta Azure Monitor pro virtuální počítače (Insights) nebo agenta Log Analytics pomocí zásad Azure, agenti se znovu nasadí po dalším [zkušebním cyklu](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="next-steps"></a>Další kroky

* Informace o řešení potíží najdete v části [Poradce při potížích s agentem připojeného počítače](troubleshoot-agent-onboard.md).

* Naučte se, jak spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md), jako je [Konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověření, že se počítač hlásí k očekávanému log Analyticsmu pracovnímu prostoru, povolit monitorování pomocí [Azure monitor zásad virtuálních počítačů](../../azure-monitor/insights/vminsights-enable-policy.md) a spoustu dalších věcí.
