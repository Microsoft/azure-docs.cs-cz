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
ms.openlocfilehash: 90c1132091f543b84fc764522dfa95672f2e2aef
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89277489"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Ochrana prostředků centra pro správu Windows pomocí Security Center

Centrum pro správu systému Windows je nástroj pro správu serverů Windows. Pro správce systému je k dispozici jediné místo pro přístup ke většině nejčastěji používaných nástrojů pro správu. V centru pro správu Windows můžete přímo připojit servery na Prem do Azure Security Center. Pak můžete zobrazit souhrn doporučení a výstrah zabezpečení přímo v prostředí centra pro správu systému Windows.

> [!NOTE]
> Aby bylo možné povolit integraci centra pro správu systému Windows, musí mít vaše předplatné Azure a přidružený pracovní prostor Log Analytics mít povolenou úroveň Security Center.
> Úroveň Standard je zadarmo na prvních 30 dnů, pokud jste ji předtím nepoužívali v předplatném a pracovním prostoru. Další informace najdete [na stránce s informacemi o cenách](security-center-pricing.md).
>

Po úspěšné registraci serveru z centra pro správu Windows do Azure Security Center můžete:

* Zobrazení výstrah zabezpečení a doporučení v rámci rozšíření Security Center v centru pro správu Windows
* Prohlédněte si stav zabezpečení a získejte další podrobné informace o spravovaných serverech centra pro správu Windows v Security Center v rámci Azure Portal (nebo přes rozhraní API).

Kombinací těchto dvou nástrojů se Security Center, že se jedná o vaše samostatné podokno, kde můžete zobrazit všechny informace o zabezpečení, a to bez ohledu na prostředek: ochrana centra pro správu Windows spravovaného na serverech Prem, virtuálních počítačů a dalších PaaS úloh.

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
    * V pracovním prostoru je povolené řešení Log Analytics úrovně Standard Security Center. Toto řešení poskytuje funkce úrovně Standard Security Center pro *všechny* servery a virtuální počítače, které vytvářejí zprávy do tohoto pracovního prostoru.
    * Pro virtuální počítač je v předplatném povolené ceny na úrovni Standard Security Center.
    * Agent Log Analytics je nainstalovaný na serveru a nakonfigurovaný tak, aby nahlásil do vybraného pracovního prostoru. Pokud již Server hlásí do jiného pracovního prostoru, je nakonfigurován tak, aby nahlásil i nově vybraný pracovní prostor.

    > [!NOTE]
    > Zobrazení doporučení může nějakou dobu trvat. V závislosti na aktivitě serveru nemůžete obdržet *žádné* výstrahy. Chcete-li generovat výstrahy testu k otestování výstrah, které fungují správně, postupujte podle pokynů v [postupu ověření výstrahy](security-center-alert-validation.md).


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Zobrazení doporučení a upozornění zabezpečení v centru pro správu Windows

Po zprovoznění můžete výstrahy a doporučení zobrazit přímo v oblasti Azure Security Center v centru pro správu Windows. Kliknutím na doporučení nebo výstrahu je zobrazíte v Azure Portal. Tam najdete další informace a dozvíte se, jak problémy vyřešit.

[![Security Center doporučení a výstrah, jak se zobrazuje v centru pro správu Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Zobrazení doporučení a upozornění zabezpečení pro spravované servery centra pro správu Windows v Security Center
Z Azure Security Center:

* Chcete-li zobrazit doporučení zabezpečení pro všechny servery centra pro správu systému Windows, otevřete **výpočetní & aplikace** a klikněte na kartu **virtuální počítače a počítače** . Vyfiltrujte seznam podle prostředku "Server", jak je znázorněno zde:

    [![Zobrazit doporučení zabezpečení pro spravované servery centra pro správu Windows](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Chcete-li zobrazit výstrahy zabezpečení pro všechny servery centra pro správu systému Windows, otevřete **výstrahy zabezpečení**. Klikněte na **Filtr** a ujistěte se, že je vybraná **jenom** možnost nepoužívá Azure:

    ![Filtrovat výstrahy zabezpečení pro spravované servery centra pro správu Windows](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Zobrazit výstrahy zabezpečení pro spravované servery centra pro správu Windows](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)