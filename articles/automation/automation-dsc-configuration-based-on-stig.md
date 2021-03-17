---
title: Konfigurace dat na základě STIG pro konfiguraci stavu Azure Automation
description: V tomto článku se dozvíte, jak nakonfigurovat data na základě DoD STIG pro konfiguraci stavu Azure Automation.
keywords: DSC, PowerShell, konfigurace, instalace
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dab0cd7f7d660808b4ed7a91318baad55f80928c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015132"
---
# <a name="configure-data-based-on-security-technical-information-guide-stig"></a>Konfigurace dat na základě Průvodce technickými informacemi o zabezpečení (STIG)

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

Po vygenerování konfigurací můžete použít [konfigurační skripty DSC](/powershell/scripting/dsc/configurations/configurations) k vygenerování souborů MOF a [nahrání souborů MOF do Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Pak můžete své servery zaregistrovat z [místního](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) prostředí nebo [v Azure](./automation-dsc-onboarding.md#enable-azure-vms) a vyžádat si konfigurace.

Pokud si chcete vyzkoušet PowerSTIG, navštivte [Galerie prostředí PowerShell](https://www.powershellgallery.com) a Stáhněte řešení nebo klikněte na web projektu a zobrazte [dokumentaci](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Další kroky

- Informace o prostředí PowerShell DSC najdete v tématu [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Přečtěte si o prostředcích PowerShellu v [prostředcích DSC](/powershell/scripting/dsc/resources/resources).
- Podrobnosti o konfiguraci místních Configuration Manager najdete v tématu [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig).
