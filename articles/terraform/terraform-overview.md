---
title: Terraform pomocí Azure
description: Úvod do používání Terraform k vesion a nasazení infrastruktury Azure.
ms.service: virtual-machines-linux
keywords: terraform, devops, přehled, plánování, provedení, automatizovat
author: binderjoe
ms.author: jbinder
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 5d313bda6a1067e6d023f62fb26704f9aee5c7bf
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114468"
---
# <a name="terraform-with-azure"></a>Terraform s Azure

[Hashicorp Terraform](https://www.terraform.io/) je nástroj s otevřeným zdrojem pro zřizování a správa infrastruktury cloudu. Ho codifies infrastruktury v konfiguračních souborech, které popisují topologii cloudové prostředky, jako je například virtuální počítače, účty úložiště a síťové rozhraní. Na Terraform rozhraní příkazového řádku (CLI) poskytuje jednoduché mechanismus pro nasazení a verze konfigurační soubory do Azure nebo jiné podporované cloudu.

Tento článek popisuje výhody použití Terraform ke správě infrastruktury Azure.

## <a name="automate-infrastructure-management"></a>Automatizovat správu infrastruktury.

Na základě šablon konfigurační soubory na Terraform umožňují definovat, zřídíte a nakonfigurujete prostředky Azure způsobem opakovatelné a předvídatelné. Automatizace infrastruktury má několik výhod:

- Snižuje potenciální lidské chyby při nasazení a správa infrastruktury.
- Nasadí vícekrát stejné šablony vytvořit identické vývoj, testování a provozním prostředí.
- Snižuje náklady na vývoj a testování prostředí vytvořením na vyžádání.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Pochopení změny infrastruktury před jejich instalací 

Jako prostředek stane topologie komplexní, vysvětlení význam a dopad změny infrastruktury může být obtížné.

Terraform poskytuje rozhraní příkazového řádku (CLI), který umožňuje uživatelům ověření a zobrazení náhledu změn infrastruktury před jejich nasazením. Zobrazení náhledu změn infrastruktury v sejfu, produktivní způsobem má několik výhod:
- Členové týmu efektivněji spolupracovat a rychle porozuměním navrhované změny a jejich dopad.
- Nezamýšleným změny zachytit již v rané fázi v procesu vývoje


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Nasazení infrastruktury do víc cloudů

Terraform je Oblíbené nástroj volbou pro scénáře s více cloudu, kde je nasazená podobné infrastruktury Azure a poskytovatelé dodatečné cloudové nebo místní datacentra. Umožňuje vývojářům používat ke správě infrastruktury na několik poskytovatelů cloudu stejných nástrojů a konfigurační soubory.

## <a name="next-steps"></a>Další postup

Teď, když máte přehled Terraform a jeho výhody, zde jsou navrhované další kroky:

- Začínáme [Terraform instalaci a konfiguraci na používání Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
- [Vytvoření virtuálního počítače Azure pomocí Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm)
- Prozkoumejte [modulu Azure Resource Manager pro Terraform](https://www.terraform.io/docs/providers/azurerm/) 