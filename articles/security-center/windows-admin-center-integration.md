---
title: Jak integrovat Centrum pro správu Windows s Azure Security Center | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak integrovat Azure Security Center s Centrem pro správu Windows
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5467794bf246fab4ff7ded9c445dbeee0c4093b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139619"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Integrace Centra zabezpečení Azure s Centrem pro správu Windows

Centrum pro správu systému Windows je nástroj pro správu serverů se systémem Windows. Je to jediné místo pro správce systému pro přístup k většině nejčastěji používaných nástrojů pro správu. V Centru pro správu Windows můžete přímo založit servery na premu do Azure Security Center. Shrnutí doporučení a výstrah zabezpečení a výstrah můžete zobrazit přímo v centru pro správu Windows.

> [!NOTE]
> Vaše předplatné Azure a přidružený pracovní prostor Log Analytics musí mít povolenou úroveň zabezpečení Centra zabezpečení, aby byla povolena integrace Centra pro správu Windows.
> Standardní úroveň je zdarma pro prvních 30 dní, pokud jste dříve nepoužívali v předplatném a pracovním prostoru. Další informace naleznete [na stránce s informacemi o cenách](security-center-pricing.md).
>

Až úspěšně založíte server z Centra pro správu Windows do Centra zabezpečení Azure, můžete:

* Zobrazení výstrah a doporučení zabezpečení v rozšíření Centrum zabezpečení v Centru pro správu Windows
* Zobrazení stavu zabezpečení a načtení dalších podrobných informací o spravovaných serverech Centra pro správu Windows v Centru zabezpečení na webu Azure Portal (nebo prostřednictvím rozhraní API).

Kombinací těchto dvou nástrojů se Centrum zabezpečení stane vaším jediným skleněným podoknem, které zobrazí všechny vaše informace o zabezpečení bez ohledu na prostředek: ochrana serverů správy windows admin-prem, virtuálních počítačů a dalších úloh PaaS.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Registrace spravovaných serverů Centra pro správu systému Windows do Centra zabezpečení

1. V Centru pro správu Windows vyberte jeden ze serverů a v podokně **Nástroje** vyberte rozšíření Azure Security Center:

    ![Rozšíření Azure Security Center v Centru pro správu Windows](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Pokud je server již zasazený do Centra zabezpečení, okno nastavení se nezobrazí.

1. Klikněte na **Přihlásit se do Azure a nastavte .**
    ![Registrace rozšíření Centra pro správu Windows do Centra zabezpečení Azure](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Podle pokynů připojte server k Centru zabezpečení. Po zadání potřebných podrobností a potvrzení provede Centrum zabezpečení potřebné změny konfigurace, aby bylo zajištěno, že budou splněny všechny následující skutečnosti:
    * Je registrovaná brána Azure.
    * Server má pracovní prostor, který má být vykazován, a přidružené předplatné.
    * Centrum zabezpečení je standardní úroveň Log Analytics řešení je povoleno v pracovním prostoru. Toto řešení poskytuje funkce úrovně Standard centra zabezpečení pro *všechny* servery a virtuální počítače, které se hlásí do tohoto pracovního prostoru.
    * Standard security center je úroveň ceny pro virtuální počítač je povolena na předplatné.
    * Agent monitorování společnosti Microsoft (MMA) je nainstalován na serveru a nakonfigurován tak, aby se hlásil do vybraného pracovního prostoru. Pokud server již hlásí do jiného pracovního prostoru, je nakonfigurován tak, aby sestavy do nově vybraného pracovního prostoru také.

    > [!NOTE]
    > Může chvíli trvat, než se doporučení zobrazí. Ve skutečnosti, v závislosti na vaší činnosti serveru nemusí přijímat *žádná* upozornění. Chcete-li generovat testovací výstrahy pro testování výstrah, které fungují správně, postupujte podle pokynů v [postupu ověření výstrahy](security-center-alert-validation.md).


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Zobrazení doporučení zabezpečení a výstrah v Centru pro správu Windows

Po naložit, můžete zobrazit výstrahy a doporučení přímo v oblasti Azure Security Center centra Windows Admin Center. Kliknutím na doporučení nebo upozornění je zobrazíte na webu Azure Portal. Tam získáte další informace a dozvíte se, jak problémy napravit.

[![Doporučení a výstrahy Centra zabezpečení, jak je vidět v Centru pro správu Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Zobrazení doporučení zabezpečení a výstrah pro spravované servery Centra pro správu Systému Windows v Centru zabezpečení
Z Azure Security Center:

* Pokud chcete zobrazit doporučení zabezpečení pro všechny servery Centra pro správu Windows, otevřete **Výpočetní & aplikace** a klikněte na kartu Virtuální počítače a **počítače.**

    [![Zobrazení doporučení zabezpečení pro spravované servery Centra pro správu Windows](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Chcete-li zobrazit výstrahy zabezpečení pro všechny servery Centra pro správu systému Windows, otevřete **schod ové výstrahy zabezpečení**. Klikněte na **Filtr** a ujistěte se, že je **vybrána jenom** "Non-Azure":

    ![Filtrování výstrah zabezpečení pro spravované servery Centra pro správu Windows](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Zobrazení výstrah zabezpečení pro spravované servery Centra pro správu Windows](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)