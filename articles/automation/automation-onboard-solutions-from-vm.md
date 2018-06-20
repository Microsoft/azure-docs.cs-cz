---
title: Zařadit řešení pro správu aktualizací, sledování změn a inventáře z virtuálního počítače Azure
description: Zjistěte, jak chcete připojit virtuální počítač Azure s řešeními správy aktualizací, sledování změn a inventáře které jsou součástí Azure Automation.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: f270b2ccea51e83bc6475051b8667bf73d7fd717
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221508"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Zařadit řešení pro správu aktualizací, sledování změn a inventáře z virtuálního počítače Azure

Azure Automation nabízí řešení, která vám pomohou spravovat aktualizace zabezpečení operačního systému, sledování změn a inventáře nainstalovaných v počítačích. Zařadit počítačům několika způsoby. Můžete připojit řešení z virtuálního počítače, [z vašeho účtu Automation](automation-onboard-solutions-from-automation-account.md), [z procházení více počítačů](automation-onboard-solutions-from-browse.md), nebo pomocí [runbook](automation-onboard-solutions.md). Tento článek se zabývá registrace těchto řešení z virtuálního počítače Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-the-solutions"></a>Povolit řešení

Přejděte na existující virtuální počítač. V části **operace**, vyberte **Správa aktualizací**, **inventáře**, nebo **sledování změn**.

Pokud chcete povolit řešení pro virtuální počítač pouze, ujistěte se, že **povolit pro tento virtuální počítač** je vybrána. Chcete-li připojit více počítačů k řešení, vyberte **povolit pro virtuální počítače v tomto předplatném**a potom vyberte **kliknutím vyberte počítače, které chcete povolit**. Další informace, jak chcete zaváděním více počítačů najednou, přečtěte si téma [zařadit správy aktualizací, sledování změn a inventáře řešení](automation-onboard-solutions-from-automation-account.md).

Vyberte pracovní prostor analýzy protokolů Azure a účet Automation a pak vyberte **povolit** povolit řešení. Povolení řešení trvá přibližně 15 minut.

![Zařadit do řešení pro správu aktualizací](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Přejděte do jiných řešení a potom vyberte **povolit**. Rozevírací seznamy účet analýzy protokolů a automatizace jsou zakázané, protože tato řešení použít stejné prostoru a účet Automation jako dříve povoleno řešení.

> [!NOTE]
> **Sledování změn** a **inventáře** použít stejné řešení. Když jedno z těchto řešení je povoleno, povolí se také dalších.

## <a name="scope-configuration"></a>Konfigurace oboru

Každé řešení používá konfigurace oboru v pracovním prostoru pro cílové počítače, které získání řešení. Konfigurace oboru je skupina jednoho nebo více uložená hledání, které se používají k omezení oboru řešení, aby konkrétní počítače. Přístup v části Konfigurace oboru, v účtu Automation **související prostředky**, vyberte **prostoru**. V pracovním prostoru v části **zdroje dat pracovního prostoru**, vyberte **konfigurace oboru**.

Pokud vybraný pracovní prostor již nemá řešení správy aktualizací nebo sledování změn, budou vytvořeny následující konfigurace oboru:

* **Vlastnost MicrosoftDefaultScopeConfig ChangeTracking**

* **MicrosoftDefaultScopeConfig aktualizace**

Pokud je vybraný pracovní prostor již řešení, není znovu nasadit řešení a konfigurace oboru není přidáno.

Vyberte na symbol tří teček (**...** ) na žádném z konfigurace a pak vyberte **upravit**. V **konfiguraci úpravy rozsahu** podokně, vyberte **vyberte skupiny počítačů**. **Skupiny počítačů** podokně se zobrazují uložená hledání, které se používají k vytvoření konfigurace oboru.

## <a name="saved-searches"></a>Uložené výsledky hledání

Když počítač přidán do správy aktualizací, sledování změn nebo inventáře řešení, je počítač přidán do jednoho z dvou uložená hledání v pracovním prostoru. Uložená hledání jsou dotazy, které obsahují počítače, které jsou cílem těchto řešení.

Přejděte do svého pracovního prostoru. V části **Obecné**, vyberte **uložená hledání**. V následující tabulce jsou uvedeny dva uložená hledání, které jsou používány těchto řešení:

|Název     |Kategorie  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Vlastnost ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualizace        | Updates__MicrosoftDefaultComputerGroup         |

Vyberte buď uložená hledání k zobrazení dotazu, který se používá k naplnění skupiny. Následující obrázek ukazuje dotaz a jeho výsledky:

![Uložené výsledky hledání](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>Další postup

Nadále kurzy pro řešení se dozvíte, jak používat:

* [Kurz – Správa aktualizací pro virtuální počítač](automation-tutorial-update-management.md)
* [Kurz – identifikaci softwaru na virtuálním počítači](automation-tutorial-installed-software.md)
* [Kurz – řešení potíží s změny na virtuálním počítači](automation-tutorial-troubleshoot-changes.md)
