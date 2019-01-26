---
title: Rozšířená ochrana před internetovými útoky – Azure Database for MySQL | Dokumentace Microsoftu
description: Ochrana před internetovými útoky detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 76f6c15fc1e186e254c4edbb53a2a0ccf7050b3e
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912744"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Advanced Threat Protection pro Azure Database for MySQL

Služba Advanced Threat Protection pro Azure Database for MySQL detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

Rozšířená ochrana před internetovými útoky je součástí nabídky Rozšířené zabezpečení dat, která je jednotný balíček pro pokročilé možnosti zabezpečení. Rozšířená ochrana před internetovými útoky jde přistupovat a spravovat přes [webu Azure portal](https://portal.azure.com) a je aktuálně ve verzi preview.

> [!NOTE]
> Funkce Advanced Threat Protection je **není** dostupná v oblastech suverénních cloudů a Azure government pro následující: (Gov) – Iowa, USA, Arizona, USA (gov) US Gov Texas, USA (gov) – Virginia, US DoD – východ, US DoD – střed, Německo – střed, Německo – sever, Čína – východ, Čína – východ 2. Navštivte prosím [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) pro obecnou dostupnost.
>

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, ve kterém je nasazená – Azure Database for MySQL pro servery pro obecné účely a optimalizovaný pro paměť.

## <a name="set-up-threat-detection"></a>Nastavení detekce hrozeb
1. Spuštění webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com).
2. Přejděte na stránku konfigurace serveru Azure Database for MySQL, kterou chcete chránit. V nastavení zabezpečení, vyberte **Advanced Threat Protection (Preview)**.
3. Na **Advanced Threat Protection (Preview)** stránka konfigurace:

   - Povolte rozšířenou ochranu před internetovými útoky na serveru.
   - V **Upřesnit nastavení ochrany před internetovými útoky**v **odeslat výstrahy Komu** text zadejte seznam e-mailů přijímat výstrahy zabezpečení po detekci neobvyklých databázových aktivit.
  
   ![Nastavení detekce hrozeb](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Prozkoumejte neobvyklé databázové aktivity

Obdržíte e-mailové oznámení po detekci neobvyklých databázových aktivit. E-mailu obsahuje informace o podezřelé události zabezpečení včetně povahy neobvyklých aktivit, název databáze, název serveru, název aplikace a čas události. Kromě toho e-mail obsahuje informace o možných příčinách a doporučených akcích pro šetření a zmírnění potenciálního ohrožení databáze.
 
1. Klikněte na tlačítko **zobrazené poslední výstrahy** odkaz v e-mailu můžete spustit na portálu Azure portal a zobrazení stránky s upozorněními Azure Security Center, která obsahuje základní informace o aktivní zjištěných hrozeb na SQL database.
    
    ![Sestava neobvyklých aktivit](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Zobrazit aktivní hrozby:

    ![Aktivní hrozby](./media/howto-database-threat-protection-portal/active-threats.png)

2. Kliknutím na konkrétní výstrahu zobrazíte další podrobnosti a akce pro zkoumání této hrozby a oprava budoucími hrozbami.
    
    ![Konkrétní výstrahy](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Prozkoumejte výstrahy detekce hrozeb

Detekce hrozeb služby SQL Database integruje výstrahy se jeho službou [Azure Security Center](https://azure.microsoft.com/services/security-center/). Živé dlaždice detekce hrozeb SQL v databázi a oken ochrany ATP v programu SQL na webu Azure Portal sleduje stav aktivní hrozby.

Klikněte na tlačítko **výstrahu o detekci hrozeb** spustit Azure Security Center oznámení stránce a získejte přehled o aktivní hrozby SQL v databázi nalezen.

   ![Výstrahy detekce hrozeb](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Další postup

* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Další informace o cenách najdete v tématu [– Azure Database for MySQL ceník](https://azure.microsoft.com/pricing/details/mysql/)  
