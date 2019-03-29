---
title: Shromažďování dat Barracuda ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Zjistěte, jak shromažďovat data Barracuda v ověřovacích Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b4d1830e705e1813c8448787e587d720eaf5ddbd
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574714"
---
# <a name="connect-your-barracuda-appliance"></a>Připojit zařízení Barracuda 

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Barracuda Firewall webových aplikací (WAF) konektoru můžete snadno připojit vaše protokoly Barracuda s vaší Azure Sentinelu zobrazení řídicích panelů, vytvářet vlastní výstrahy a zlepší. To poskytuje lepší přehled o síti vaší organizace a zlepšuje schopnosti operace zabezpečení. Azure Sentinel využívá nativní integraci mezi službami **Barracuda** a Microsoft Azure OMS zajistit bezproblémovou integraci. 


> [!NOTE]
> 
> - Data se uloží v zeměpisné oblasti pracovního prostoru, na kterém je spuštěný Sentinelu Azure.

## <a name="configure-and-connect-barracuda-waf"></a>Konfigurace a připojení Barracuda WAF
Barracuda Web Application Firewall můžete integrovat a exportovat protokoly přímo do [ASI] prostřednictvím Azure OMS serveru.
1. Přejděte na [toku konfiguraci Barracuda WAF](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)a postupujte podle pokynů k nastavení připojení, s použitím těchto parametrů:
    - **ID pracovního prostoru**: Zkopírujte hodnotu ID vašeho pracovního prostoru na stránce Azure Sentinelu Barracuda konektoru.
    - **Primární klíč**: Zkopírujte hodnotu primárního klíče na stránce Azure Sentinelu Barracuda konektoru.
2. Na portálu Azure Sentinelu, přejděte do pracovního prostoru, na které jste nasadili Azure Sentinelu a vyberte tři tečky (...) na konci řádku a vyberte **upřesňující nastavení**. 
1. Vyberte **Data** a potom **Syslog**.
1. Ujistěte se, že existuje a nastavit závažnost a klikněte na zařízení, které jste nastavili v Barracuda **Uložit**.
6. Chcete-li použít příslušné schéma v Log Analytics pro události Barracuda, vyhledejte **CommonSecurityLog**.


## <a name="validate-connectivity"></a>Ověření připojení

Může trvat upwards of 20 minut, než vaše protokoly spuštění se zobrazí v Log Analytics. 



## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu zařízení Barracuda. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

