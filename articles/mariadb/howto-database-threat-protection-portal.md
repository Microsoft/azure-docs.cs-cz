---
title: Rozšířená ochrana před internetovými útoky – Azure Portal-Azure Database for MariaDB
description: Ochrana před hrozbami pro Azure Database for MariaDB detekuje aktivity databáze neobvyklé, které indikují potenciální ohrožení zabezpečení databáze.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 1b5a16a014892764ee26b524489a82744fe6ca82
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425974"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Rozšířená ochrana před internetovými útoky pro Azure Database for MariaDB

Rozšířená ochrana před internetovými útoky pro Azure Database for MariaDB detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Rozšířená ochrana před internetovými útoky je součástí rozšířené nabídky zabezpečení dat, což je jednotný balíček pro pokročilé funkce zabezpečení. Rozšířená ochrana před internetovými útoky je dostupná a spravovaná prostřednictvím [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> Rozšířená ochrana před internetovými útoky je ve verzi Public Preview. Tato funkce je k dispozici ve všech oblastech Azure, kde Azure Database for MariaDB nasazeny pro Pro obecné účely a paměťově optimalizované servery.

> [!NOTE]
> Funkce rozšířené ochrany před internetovými útoky **není dostupná v** těchto oblastech cloudu Azure a v svrchovaném cloudu: US Gov – Texas, US Gov – Arizona, US gov – Iowa, US, gov) – virginia, US DoD – východ, US DoD – střed, Německo Central, Německo – sever, Čína – východ, Čína – východ 2. Pokud chcete získat obecnou dostupnost produktu, navštivte prosím [produkty dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) .

## <a name="set-up-threat-detection"></a>Nastavení detekce hrozeb
1. Spusťte Azure Portal v [https://portal.azure.com](https://portal.azure.com) .
2. Přejděte na stránku konfigurace serveru Azure Database for MariaDB, který chcete chránit. V nastavení zabezpečení vyberte **Rozšířená ochrana před internetovými útoky (Preview)**.
3. Na stránce konfigurace **rozšířené ochrany před internetovými útoky (Preview)** :

   - Povolit rozšířenou ochranu před internetovými útoky na serveru.
   - V části **Upřesnit nastavení ochrany před internetovými útoky**zadejte do textového pole **Odeslat výstrahy do** seznam e-mailů, které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.
  
   ![Nastavení detekce hrozeb](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Prozkoumejte aktivity databáze neobvyklé

Po detekci neobvykléch databázových aktivit obdržíte e-mailové oznámení. E-mail obsahuje informace o podezřelé události zabezpečení, včetně povahy aktivit neobvyklé, názvu databáze, názvu serveru, názvu aplikace a času události. Kromě toho e-mail poskytuje informace o možných příčinách a doporučených akcích k prošetření a zmírnění potenciální hrozby pro databázi.
 
1. Kliknutím na odkaz **Zobrazit nedávné výstrahy** v e-mailu spustíte Azure Portal a zobrazí se stránka Azure Security Center výstrahy, která poskytuje přehled aktivních hrozeb zjištěných v databázi SQL.
    
    ![Sestava aktivity neobvyklé](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Zobrazit aktivní hrozby:

    ![Aktivní hrozby](./media/howto-database-threat-protection-portal/active-threats.png)

2. Kliknutím na konkrétní výstrahu získáte další podrobnosti a akce pro šetření této hrozby a opravaí budoucích hrozeb.
    
    ![Konkrétní výstraha](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Prozkoumat výstrahy detekce hrozeb

SQL Database detekce hrozeb integruje své výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/). Živá dlaždice detekce hrozeb SQL v rámci databáze a oken ATP SQL v Azure Portal sledují stav aktivních hrozeb.

Kliknutím na **výstrahu detekce hrozeb** spustíte stránku Azure Security Center výstrahy a získáte přehled o aktivních hrozbách SQL zjištěných v databázi.

   ![Výstraha detekce hrozeb](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Security Center](../security-center/security-center-introduction.md)
* Další informace o cenách najdete na stránce s [cenami Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) .