---
title: Jak chránit servery centra pro správu Windows pomocí Azure Security Center
description: Tento článek vysvětluje, jak integrovat Azure Security Center s centrem pro správu Windows
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: fc4207547a1f34fca1ef302626f1365572a236b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101303"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Ochrana prostředků centra pro správu Windows pomocí Security Center

Centrum pro správu systému Windows je nástroj pro správu serverů Windows. Pro správce systému je k dispozici jediné místo pro přístup ke většině nejčastěji používaných nástrojů pro správu. V centru pro správu Windows můžete své místní servery přímo připojit do Azure Security Center. Pak můžete zobrazit souhrn doporučení a výstrah zabezpečení přímo v prostředí centra pro správu systému Windows.

> [!NOTE]
> Aby bylo možné povolit integraci centra pro správu systému Windows, musí mít vaše předplatné Azure a přidružený Log Analytics pracovní prostor povolený program Azure Defender.
> Pokud jste ho předtím nepoužívali v předplatném a pracovním prostoru, je Azure Defender zdarma na prvních 30 dnů. Podrobnosti o cenách v měně zvolené a podle vaší oblasti najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).
>

Po úspěšné registraci serveru z centra pro správu Windows do Azure Security Center můžete:

* Zobrazení výstrah zabezpečení a doporučení v rámci rozšíření Security Center v centru pro správu Windows
* Prohlédněte si stav zabezpečení a získejte další podrobné informace o spravovaných serverech centra pro správu Windows v Security Center v rámci Azure Portal (nebo přes rozhraní API).

Kombinací těchto dvou nástrojů se Security Center, že se jedná o vaše samostatné podokno, kde můžete zobrazit všechny informace o zabezpečení, a to bez ohledu na to, jaký je prostředek: ochrana vašeho centra pro správu Windows spravovaná na místních serverech, virtuálních počítačů a dalších PaaS úloh.

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>Připojit spravované servery centra pro správu systému Windows do Security Center

1. V centru pro správu systému Windows vyberte jeden z vašich serverů a v podokně **nástroje** vyberte rozšíření Azure Security Center:

    ![Rozšíření Azure Security Center v centru pro správu Windows](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Pokud je server již připojen k Security Center, nezobrazí se okno nastavení.

1. Klikněte na **Přihlásit se k Azure a nastavte**.
    ![Připojování rozšíření centra pro správu Windows k Azure Security Center](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Podle pokynů připojte Server k Security Center. Po zadání potřebných podrobností a potvrzení Security Center provede potřebné změny konfigurace, aby se zajistilo splnění všech následujících podmínek:
    * Brána Azure je zaregistrovaná.
    * Server má pracovní prostor k hlášení a přidruženému předplatnému.
    * V pracovním prostoru je povolené řešení Log Analytics Security Center. Toto řešení poskytuje funkce v programu Azure Defender pro *všechny* servery a virtuální počítače, které vytvářejí zprávy do tohoto pracovního prostoru.
    * V předplatném je povolený Azure Defender pro servery.
    * Agent Log Analytics je nainstalovaný na serveru a nakonfigurovaný tak, aby nahlásil do vybraného pracovního prostoru. Pokud již Server hlásí do jiného pracovního prostoru, je nakonfigurován tak, aby nahlásil i nově vybraný pracovní prostor.

    > [!NOTE]
    > Zobrazení doporučení může nějakou dobu trvat. V závislosti na aktivitě serveru nemůžete obdržet *žádné* výstrahy. Chcete-li generovat výstrahy testu k otestování výstrah, které fungují správně, postupujte podle pokynů v [postupu ověření výstrahy](security-center-alert-validation.md).


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Zobrazení doporučení a upozornění zabezpečení v centru pro správu Windows

Po zprovoznění můžete výstrahy a doporučení zobrazit přímo v oblasti Azure Security Center v centru pro správu Windows. Kliknutím na doporučení nebo výstrahu je zobrazíte v Azure Portal. Tam najdete další informace a dozvíte se, jak problémy vyřešit.

[![Security Center doporučení a výstrah, jak se zobrazuje v centru pro správu Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Zobrazení doporučení a upozornění zabezpečení pro spravované servery centra pro správu Windows v Security Center
Z Azure Security Center:

* Chcete-li zobrazit doporučení zabezpečení pro všechny servery centra pro správu systému Windows, otevřete [inventář assetů](asset-inventory.md) a vyfiltrujte ho podle typu počítače, který chcete prozkoumat. Vyberte kartu **virtuální počítače a počítače** .

* Chcete-li zobrazit výstrahy zabezpečení pro všechny servery centra pro správu systému Windows, otevřete **výstrahy zabezpečení**. Klikněte na **Filtr** a ujistěte se, že je vybraná **jenom** možnost nepoužívá Azure:

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Filtrovat výstrahy zabezpečení pro spravované servery centra pro správu Windows" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::