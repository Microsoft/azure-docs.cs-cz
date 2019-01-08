---
title: Správa služby Azure Key Vault pomocí Azure Automation – Azure Key Vault | Dokumentace Microsoftu
description: Další informace o používání služby Azure Automation ke správě služby Azure Key Vault.
services: Key-Vault, automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: magoedte
ms.openlocfilehash: 85a0c05719ba374ed213212db6f094d75e433183
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073427"
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Správa služby Azure Key Vault pomocí Azure Automation.

Tato příručka vás seznámí s služby Azure Automation a jak ji můžete použít k zjednodušení správy klíčů a tajných klíčů ve službě Azure Key Vault.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?

[Azure Automation](../automation/automation-intro.md) je služba Azure pro zjednodušení správu cloudu díky automatizaci procesů a konfigurace požadovaného stavu. Pomocí Azure Automation, ruční, opakované, časově náročné a náchylné úlohy je možné automatizovat zvýšit spolehlivost, efektivitu a času na hodnotu pro vaši organizaci.

Azure Automation poskytuje s vysoce spolehlivé a vysoce dostupného prováděcího modulu, která se škáluje podle vašich potřeb. Ve službě Azure Automation procesy můžete má zahájit ručně, 3. stran systémy, nebo v naplánovaných intervalech, aby úlohy stát přesně v případě potřeby.

Snižte operační režii a uvolnit tak IT a týmu DevOps umožňovaly práci, která vytváří přidanou hodnotu díky přesunu úkoly správy cloudu ke spuštění automaticky službou Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Jak Azure Automation pomáhá spravovat služby Azure Key Vault?

Key Vault můžete spravovat ve službě Azure Automation s použitím [rutin AzureRM Key Vault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) a [rutiny pro Azure Classic Key Vault](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Modul Azure pro správu klasické služby Key Vault je k dispozici automaticky ve službě Azure Automation, a můžete importovat [modulu AzureRM KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) do Azure Automation, tak, že můžete provádět mnoho úkolů správy služby Key Vault v rámci služby. Můžete také spárovat tyto rutiny ve službě Azure Automation s rutinami pro dalšími službami Azure, automatizují komplexní úlohy napříč službami Azure a systémech 3. stran.

Pomocí rutiny Azure Key Vault můžete provádět tyto úlohy mimo jiné: 

* Vytvoření a konfigurace služby key vault
* Vytvoření nebo import klíče
* Vytvořit nebo aktualizovat tajný kód.
* Aktualizovat atributy klíče
* Získat klíče nebo tajného klíče
* Odstranění klíče nebo tajného klíče

Tady je několik příkladů použití Powershellu ke správě služby Key Vault:  

* [Azure Key Vault – krok za krokem](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Nastavení a konfiguraci služby Azure Key Vault](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili základy služby Azure Automation a jak ho lze použít ke správě služby Azure Key Vault, použijte tyto odkazy na další informace o službě Azure Automation.

* Azure Automation najdete v článku [úvodní kurz](../automation/automation-first-runbook-graphical.md).
* Zobrazit [skriptů Azure Key Vault prostředí PowerShell](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

