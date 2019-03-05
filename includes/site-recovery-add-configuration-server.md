---
title: zahrnout soubor
description: zahrnout soubor
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: 7c682105113dac7c1d457489cf926210ead77993
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57343550"
---
1. Spusťte instalační soubor sjednocené instalace.
2. V **před zahájením**vyberte **nainstalovat konfigurační server a procesový server**.

    ![Než začnete](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. Na stránce **Licence k softwaru jiného výrobce** vyberte **Souhlasím** pro stažení a instalaci MySQL.

    ![Software jiných výrobců](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. Na stránce **Registrace** vyberte registrační klíč, který jste si stáhli z trezoru.

    ![Registrace](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. Na stránce **Nastavení internetu** určete, jak se zprostředkovatel, který běží na konfiguračním serveru, připojí k Azure Site Recovery přes internet. Ujistěte se, že jste povolili požadované adresy URL.

    - Pokud chcete pro připojení s proxy serverem, který je aktuálně nastavený na počítači, vyberte **připojit k Azure Site Recovery pomocí proxy serveru**.
    - Pokud chcete, aby zprostředkovatel připojil přímo, vyberte **připojit přímo k Azure Site Recovery bez proxy serveru**.
    - Pokud stávající proxy server vyžaduje ověření nebo pokud chcete používat vlastní proxy server pro připojení zprostředkovatele vyberte **Connect s vlastním nastavením proxy serveru**a zadejte adresu, port a přihlašovací údaje.
     ![Brána firewall](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. Na stránce **Kontrola předpokladů** instalační program provede kontrolu a ověří, že lze spustit instalaci. Pokud se zobrazí varování u položky **Kontrola synchronizace globálního času**, ověřte, že čas na systémových hodinách (nastavení **Datum a čas**) je stejný jako časové pásmo.

    ![Požadavky](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. Na stránce **Konfigurace MySQL** vytvořte přihlašovací údaje pro přihlašování k nainstalované instanci serveru MySQL.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. V **podrobnosti o prostředí**, vyberte Ne, pokud provádíte replikaci virtuálních počítačů Azure Stack nebo fyzické servery. 
9. Na stránce **Umístění instalace** vyberte, kam chcete nainstalovat binární soubory a ukládat mezipaměť. Vybraná jednotka musí mít minimálně 5 GB dostupného místa na disku, ale pro mezipaměť doporučujeme jednotku alespoň s 600 GB volného místa.

    ![Umístění instalace](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. V **výběr sítě**, nejprve vyberte síťový adaptér, který používá integrovaný procesový server pro zjišťování a vynucená instalace služby mobility na zdrojový počítače a potom vyberte síťový adaptér, který používá konfigurační Server pro připojení s Azure. Výchozím portem pro odesílání a příjem přenosů replikace je port 9443, ale toto číslo portu můžete změnit podle potřeb vašeho prostředí. Kromě portu 9443 otevíráme také port 443, který používá webový server k orchestraci operací replikace. Nepoužívejte port 443 pro odesílání nebo příjem provozu replikace.

    ![Výběr sítě](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. Na stránce **Souhrn** zkontrolujte informace a klikněte na **Nainstalovat**. Po dokončení instalace se vygeneruje heslo. Budete ho potřebovat k povolení replikace, proto si ho zkopírujte a uložte na bezpečném místě.

    ![Souhrn](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Po dokončení registrace se server zobrazí v okně **Nastavení** > **Servery** v trezoru.
