---
title: Připojení dat NXLog LinuxAudit k Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor NXLog LinuxAudit k vyžádání protokolů LinuxAudit do Azure Sentinel. Zobrazení dat LinuxAudit v sešitech, vytváření výstrah a zlepšení šetření.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 2010b21a3cdb81f2b2aa4180f87857862cd02bf5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700822"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>Připojení NXLog LinuxAudit k Azure Sentinel

> [!IMPORTANT]
> Konektor NXLog LinuxAudit je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Konektor [NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) umožňuje snadno exportovat události zabezpečení pro Linux do Azure Sentinel v reálném čase, což vám poskytne přehled o aktivitě názvového serveru domény v celé organizaci. Modul NXLog LinuxAudit podporuje vlastní pravidla auditu a shromažďuje protokoly bez auditování nebo jakéhokoli softwaru na uživatelském prostoru. IP adresy a ID skupin nebo uživatelů se přeloží na jejich příslušné názvy, které usnadňují analytikům zabezpečení protokoly [auditu pro Linux](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) . Integrace mezi NXLog LinuxAudit a službou Azure Sentinel je usnadněna prostřednictvím REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>Konfigurace a připojení NXLog LinuxAudit

NXLog je možné nakonfigurovat tak, aby odesílala události ve formátu JSON přímo do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte konektor **NXLog LinuxAudit** .

1. Vyberte **stránku otevřít konektor**.

1. Postupujte podle podrobných pokynů v tématu Integrace *uživatelské příručky NXLog* [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) ke konfiguraci předávání prostřednictvím REST API.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v části **protokoly** v části  **vlastní protokoly** v tabulce *LinuxAudit_CL* .

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat NXLog LinuxAudit k ingestování událostí zabezpečení pro Linux do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
