---
title: Konfigurace dat na základě STIG pro konfiguraci stavu Azure Automation
description: V tomto článku se dozvíte, jak nakonfigurovat data na základě STIG pro konfiguraci stavu Azure Automation.
keywords: DSC, PowerShell, konfigurace, instalace
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e17ae51addce2bb156b6f1c54e29fd777699516f
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711376"
---
# <a name="configure-data-based-on-stig-for-azure-automation-state-configuration"></a>Konfigurace dat na základě STIG pro konfiguraci stavu Azure Automation

> Platí pro: Windows PowerShell 5,1

První vytvoření obsahu konfigurace může být náročné.
Cílem je v mnoha případech automatizace konfigurace serverů za "základní" hodnotou, která snad se zarovnává s doporučeními pro obor.

> [!NOTE]
> Tento článek popisuje řešení, které spravuje komunita open source.
> Podpora je dostupná jenom ve formě spolupráce na GitHubu, ne od Microsoftu.

## <a name="community-project-powerstig"></a>Projekt komunity: PowerSTIG

Projekt komunity s názvem [PowerSTIG](https://github.com/microsoft/powerstig) se zaměřuje na vyřešení tohoto problému vygenerováním obsahu DSC na základě [veřejných informací](https://public.cyber.mil/stigs/) , které jsou k dispozici na Stig (Příručka pro technickou implementaci zabezpečení),

Zvládnutí standardních hodnot je složitější než zvuk.
Mnoho organizací potřebuje k těmto pravidlům [zdokumentovat výjimky](https://github.com/microsoft/powerstig#powerstigdata) a spravovat tato data ve velkém měřítku.
PowerSTIG tento problém řeší tím, že poskytuje [složené prostředky](https://github.com/microsoft/powerstig#powerstigdsc) k adresování každé oblasti konfigurace místo toho, aby se pokoušela adresovat celý rozsah nastavení v jednom velkém souboru.

Po vygenerování konfigurací můžete použít [konfigurační skripty DSC](/powershell/scripting/dsc/configurations/configurations) k vygenerování souborů MOF a [nahrání souborů MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Pak můžete své servery zaregistrovat z [místního](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) prostředí nebo [v Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) a vyžádat si konfigurace.

Pokud si chcete vyzkoušet PowerSTIG, navštivte [Galerie prostředí PowerShell](https://www.powershellgallery.com) a Stáhněte řešení nebo klikněte na web projektu a zobrazte [dokumentaci](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Další kroky

- [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Prostředky DSC](/powershell/scripting/dsc/resources/resources)
- [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
