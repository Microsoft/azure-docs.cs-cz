---
title: Advanced Threat Protection – Azure Database for PostgreSQL
description: Ochrana před internetovými útoky detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/20/2018
ms.openlocfilehash: b7c1f873d47be0f2ec0125f769a47a399e492662
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536147"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql"></a>Advanced Threat Protection pro Azure Database for PostgreSQL

Služba Advanced Threat Protection pro Azure Database for PostgreSQL detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

Rozšířená ochrana před internetovými útoky je součástí nabídky Rozšířené zabezpečení dat, která je jednotný balíček pro pokročilé možnosti zabezpečení. Rozšířená ochrana před internetovými útoky jde přistupovat a spravovat přes [webu Azure portal](https://portal.azure.com) a je aktuálně ve verzi preview.

> [!NOTE]
> Funkce Advanced Threat Protection je **není** dostupná v oblastech suverénních cloudů a Azure government pro následující: (Gov) – Iowa, USA, Arizona, USA (gov) US Gov Texas, USA (gov) – Virginia, US DoD – východ, US DoD – střed, Německo – střed, Německo – sever, Čína – východ, Čína – východ 2. Navštivte prosím [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) pro obecnou dostupnost.
>

## <a name="set-up-threat-detection"></a>Nastavení detekce hrozeb
1. Spuštění webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com).
2. Přejděte na stránku konfigurace služby Azure Database for PostgreSQL, který chcete chránit. V nastavení zabezpečení, vyberte **Advanced Threat Protection (Preview)**.
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

Rozšířená ochrana před internetovými útoky integruje jeho výstrahy se službou [Azure Security Center](https://azure.microsoft.com/services/security-center/). 

Klikněte na tlačítko **výstrahy zabezpečení** pod **ochrana před internetovými útoky** spustit Azure Security Center oznámení stránce a získejte přehled o aktivní hrozby SQL v databázi nalezen.

  ![Asc protectoin před internetovými útoky](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>Další postup

* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Další informace o cenách najdete v tématu [– Azure Database for PostgreSQL ceník](https://azure.microsoft.com/pricing/details/postgresql/)  
