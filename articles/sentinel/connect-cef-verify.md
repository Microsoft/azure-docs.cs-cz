---
title: Ověřit připojení ke službě Azure Sentinel | Microsoft Docs
description: Ověřte připojení k řešení zabezpečení, abyste měli jistotu, že se zprávy CEF předávají do Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: e192f5db212faae9a8a93fbf3991f05b0adef0f8
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615343"
---
# <a name="step-3-validate-connectivity"></a>Krok 3: ověření připojení



Po nasazení agenta a konfiguraci řešení zabezpečení pro přeposílání zpráv CEF použijte tento článek k pochopení, jak ověřit připojení mezi službou Azure Sentinel a řešením zabezpečení. 

## <a name="how-to-validate-connectivity"></a>Jak ověřit připojení

1. Otevřete Log Analytics a ujistěte se, že jsou protokoly přijaté pomocí schématu CommonSecurityLog.<br> Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

1. Před spuštěním skriptu doporučujeme, abyste odeslali zprávy z řešení zabezpečení, abyste se ujistili, že jsou předávány na počítač proxy syslog, který jste nakonfigurovali. 
1. Na počítači musíte mít zvýšená oprávnění (sudo). Ujistěte se, že máte v počítači Python, a to pomocí následujícího příkazu: `python –version`
1. Spuštěním následujícího skriptu zkontrolujete konektivitu mezi agentem, službou Azure Sentinel a vaším řešením zabezpečení. Kontroluje, zda je přesměrování démona správně nakonfigurováno, naslouchá na správných portech a zda nic neblokuje komunikaci mezi démonem a agentem Log Analytics. Skript také pošle příznakové zprávy "TestCommonEventFormat", aby zkontrolovala koncové připojení. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit zařízení CEF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

