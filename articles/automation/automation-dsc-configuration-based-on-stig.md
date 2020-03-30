---
title: Konfigurace založená na stigu pro použití v konfiguraci stavu - Azure Automation
description: Další informace o konfiguracích založených na STIG pro konfiguraci stavu v Azure Automation.
keywords: dsc,powershell,konfigurace,nastavení
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028275"
---
# <a name="configuration-based-on-stig"></a>Konfigurace na základě STIG

> Platí pro: Windows PowerShell 5.1

Vytvoření obsahu konfigurace poprvé může být náročné.
V mnoha případech je cílem automatizovat konfiguraci serverů podle "základní linie", která se snad shoduje s doporučením odvětví.

> [!NOTE]
> Tento článek odkazuje na řešení, které je udržováno komunitou open source.
> Podpora je k dispozici pouze ve formě spolupráce githubu, ne od Microsoftu.

## <a name="community-project-powerstig"></a>Komunitní projekt: PowerSTIG

Komunitní projekt s názvem [PowerSTIG](https://github.com/microsoft/powerstig) si klade za cíl vyřešit tento problém generováním obsahu DSC na základě [veřejných informací poskytovaných](https://public.cyber.mil/stigs/) o STIG (Security Technical Implementation Guide),

Vypořádat se se základními liniemi je složitější, než se zdá.
Mnoho organizací musí [dokumentovat výjimky](https://github.com/microsoft/powerstig#powerstigdata) z pravidel a spravovat tato data ve velkém měřítku.
Program PowerSTIG řeší problém poskytnutím [složených prostředků](https://github.com/microsoft/powerstig#powerstigdsc) k řešení jednotlivých oblastí konfigurace, nikoli pokusem o řešení celého rozsahu nastavení v jednom velkém souboru.

Po vygenerování konfigurací můžete pomocí [skriptů Konfigurace DSC](/powershell/scripting/dsc/configurations/configurations) generovat soubory MOF a [nahrát soubory MOF do azure automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Pak zaregistrujte své servery z [místního](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) prostředí nebo [v Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) pro vyžádat konfigurace.

Chcete-li vyzkoušet powerstig, navštivte [Galerii prostředí PowerShell](https://www.powershellgallery.com) a stáhněte si řešení nebo klikněte na "Projektový web" a prohlédněte si [dokumentaci](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Další kroky

- [Windows PowerShell – přehled konfigurace požadovaného stavu](/powershell/scripting/dsc/overview/overview)
- [Zdroje dsc](/powershell/scripting/dsc/resources/resources)
- [Konfigurace správce místní konfigurace](/powershell/scripting/dsc/managing-nodes/metaconfig)
