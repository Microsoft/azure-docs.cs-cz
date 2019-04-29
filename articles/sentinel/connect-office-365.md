---
title: Připojení k Azure Preview Sentinelu dat Office 365 | Dokumentace Microsoftu
description: Informace o připojení k Azure ověřovacích dat Office 365.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 77587b0b7506ef0ccadbeb6d1f010f5b6a72d93e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714546"
---
# <a name="connect-data-from-office-365-logs"></a>Připojíte si data z Office 365 protokoly

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete Streamovat protokoly auditu z [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) do Azure Sentinelu jediným kliknutím. Protokoly auditu z více tenantů do jednoho pracovního prostoru v ověřovacích Azure můžete Streamovat. Konektor protokolů aktivit Office 365 poskytuje podrobné informace o probíhající uživatelských aktivit. Zobrazí se informace o různé uživatele, správce, systému a akce zásad a události z Office 365. Propojením protokolů Office 365 do ověřovacích Azure slouží tato data k zobrazení řídicích panelů, vytvářet vlastní výstrahy a zlepšení procesu šetření.


## <a name="prerequisites"></a>Požadavky

- Musíte být globálním správcem nebo správcem zabezpečení ve svém tenantovi
- V počítači, ze kterého jste přihlášeni do Azure Sentinelu k vytvoření připojení, ujistěte se, že port 4433 je otevřený pro webový provoz.

## <a name="connect-to-office-365"></a>Připojení k Office 365

1. V Azure Sentinelu, vyberte **datové konektory** a potom klikněte na tlačítko **Office 365** dlaždici.

2. Pokud jste ještě nepovolili, v části **připojení** použít **povolit** tlačítko povolíte řešení Office 365. Pokud je už povolená, budou označeny v okně připojení, které jsou už povolená.
1. Office 365 umožňuje streamování dat z více tenantů do Azure Sentinelu. Pro každého klienta, které se chcete připojit, přidání tenanta pod **připojení klientů k Azure Sentinelu**. 
1. Otevře se obrazovka Active Directory. Zobrazí se výzva k ověření uživatele globální správce na každého klienta, který chcete připojit k Azure Sentinelu a poskytnout oprávnění Azure ověřovací protokoly číst. 
5. V části protokoly aktivit Stream Office 365, klikněte na **vyberte** zvolit typy protokolů, které chcete Streamovat do ověřovacích Azure. V současné době Sentinelu Azure podporuje Exchange a SharePoint.

4. Klikněte na tlačítko **použít změny**.

3. Chcete-li použít příslušné schéma v Log Analytics pro protokoly služeb Office 365, vyhledejte **OfficeActivity**.


## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu Office 365. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

