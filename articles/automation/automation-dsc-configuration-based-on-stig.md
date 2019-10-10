---
title: Konfigurace založená na STIG, která se má použít v konfiguraci stavu – Azure Automation
description: Přečtěte si o konfiguracích založených na STIG pro konfiguraci stavu v Azure Automation.
keywords: DSC, PowerShell, konfigurace, instalace
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 685b6bda09026e64154590afd66bdfbec43b8b1e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243531"
---
# <a name="configuration-based-on-stig"></a>Konfigurace na základě STIG

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
Pak můžete své servery zaregistrovat z [místního](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) prostředí nebo [v Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a vyžádat si konfigurace.

Pokud si chcete vyzkoušet PowerSTIG, navštivte [Galerie prostředí PowerShell](http://www.powershellgallery.com) a Stáhněte řešení nebo klikněte na web projektu a zobrazte [dokumentaci](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Další kroky

- [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/dsc/overview/overview)
- [Prostředky DSC](/powershell/dsc/resources/resources)
- [Konfigurace místní Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
