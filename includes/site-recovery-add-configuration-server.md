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
ms.openlocfilehash: f699ffe6d5a91e8ce3ae90c7e12249bbad0fff3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500418"
---
1. Spusťte instalační soubor sjednocené instalace.
2. V části **než začnete**vyberte **nainstalovat konfigurační server a procesový Server**.

    ![Snímek obrazovky před začátkem obrazovky v sjednocené instalaci](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. Na stránce **Licence k softwaru jiného výrobce** vyberte **Souhlasím** pro stažení a instalaci MySQL.

    ![Snímek obrazovky Third Party Software licence v sjednocené instalaci](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. Na stránce **Registrace** vyberte registrační klíč, který jste si stáhli z trezoru.

    ![Obrazovka registrační obrazovky v sjednocené instalaci](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. Na stránce **Nastavení internetu** určete, jak se zprostředkovatel, který běží na konfiguračním serveru, připojí k Azure Site Recovery přes internet. Ujistěte se, že jste povolili požadované adresy URL.

    - Pokud se chcete připojit k proxy serveru, který je aktuálně nastavený na počítači, vyberte **připojit k Azure Site Recovery pomocí proxy server**.
    - Pokud chcete, aby se zprostředkovatel připojil přímo, vyberte **připojit přímo k Azure Site Recovery bez proxy server**.
    - Pokud existující proxy server vyžaduje ověření nebo pokud chcete pro připojení zprostředkovatele používat vlastní proxy server, vyberte **připojit se s vlastním nastavením proxy serveru**a zadejte adresu, port a přihlašovací údaje.
     ![Snímek obrazovky s nastavením Internetu v sjednocené instalaci](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. Na stránce **Kontrola předpokladů** instalační program provede kontrolu a ověří, že lze spustit instalaci. Pokud se zobrazí varování u položky **Kontrola synchronizace globálního času**, ověřte, že čas na systémových hodinách (nastavení **Datum a čas**) je stejný jako časové pásmo.

    ![Snímek obrazovky s kontrolou požadavků v sjednocené instalaci](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. Na stránce **Konfigurace MySQL** vytvořte přihlašovací údaje pro přihlašování k nainstalované instanci serveru MySQL.

    ![Snímek obrazovky s konfigurací MySQL v sjednocené instalaci](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. V **podrobnostech o prostředí**vyberte možnost Ne, pokud provádíte replikaci Azure Stack virtuálních počítačů nebo fyzických serverů. 
9. Na stránce **Umístění instalace** vyberte, kam chcete nainstalovat binární soubory a ukládat mezipaměť. Vybraná jednotka musí mít minimálně 5 GB dostupného místa na disku, ale pro mezipaměť doporučujeme jednotku alespoň s 600 GB volného místa.

    ![Snímek obrazovky s instalačním umístěním v sjednocené instalaci](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. V části **Výběr sítě**nejdřív Vyberte síťovou kartu, kterou integrovaný procesový Server používá pro zjišťování a nabízenou instalaci služby mobility na zdrojových počítačích, a pak vyberte síťovou kartu, kterou konfigurační server používá pro připojení k Azure. Výchozím portem pro odesílání a příjem přenosů replikace je port 9443, ale toto číslo portu můžete změnit podle potřeb vašeho prostředí. Kromě portu 9443 otevíráme také port 443, který používá webový server k orchestraci operací replikace. Pro odesílání a příjem provozu replikace nepoužívejte port 443.

    ![Snímek obrazovky s výběrem sítě v sjednocené instalaci](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. Na stránce **Souhrn** zkontrolujte informace a klikněte na **Nainstalovat**. Po dokončení instalace se vygeneruje heslo. Budete ho potřebovat k povolení replikace, proto si ho zkopírujte a uložte na bezpečném místě.

    ![Snímek obrazovky se souhrnem v sjednocené instalaci](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Po dokončení registrace se server zobrazí v okně **Nastavení**  >  **servery** v trezoru.
