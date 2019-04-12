---
title: Připojení dat společnosti Symantec ICDX Sentinelu ve verzi Preview Azure | Dokumentace Microsoftu
description: Informace o připojení k Azure Sentinelu Symantec ICDX data.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 1a6843fb1668307aa442011233999c648901d404
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502080"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Připojit zařízení Symantec ICDX 

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Symantec ICDX konektoru můžete snadno připojit všechny protokoly řešení Symantec zabezpečení s vaší Azure Sentinelu zobrazení řídicích panelů, vytvářet vlastní výstrahy a zlepší. To poskytuje lepší přehled o síti vaší organizace a zlepšuje schopnosti operace zabezpečení. Integrace mezi Symantec ICDX a Sentinelu Azure využívá rozhraní REST API.


> [!NOTE]
> Data se uloží v zeměpisné oblasti pracovního prostoru, na kterém je spuštěný Sentinelu Azure.

## <a name="configure-and-connect-symantec-icdx"></a>Konfigurace a připojení Symantec ICDX 

Integrace a exportovat protokoly přímo do Azure Sentinelu Symantec ICDX.

1. Otevřete konzolu pro správu ICDX.
2. V levé navigační nabídce vyberte **konfigurace** a pak **předávání** kartu.
3. V řádku Microsoft Azure Log Analytics, klikněte na tlačítko **Další**následovaný **upravit**. 
4. V **předávání Log Analytics v Microsoft Azure** okno, nastavte následující:
    - Ponechte výchozí SymantecICDX název vlastního protokolu.
    - ID pracovního prostoru zkopírujte a vložte ji **identifikátor zákazníka** pole. Kopírovat **primární klíč** a vložte ho do pole sdíleného klíče. Tyto hodnoty můžete zkopírovat z portálu Azure Sentinelu tak, že vyberete **datové konektory** a potom **Symantec ICDX**.
6. Chcete-li použít příslušné schéma v Log Analytics pro události Symantec ICDX, vyhledejte **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Ověření připojení

Může trvat upwards of 20 minut, než vaše protokoly spuštění se zobrazí v Log Analytics. 



## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu Symantec ICDX. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

