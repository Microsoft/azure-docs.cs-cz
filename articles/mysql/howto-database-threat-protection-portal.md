---
title: Rozšířená ochrana před internetovými útoky – Azure Portal-Azure Database for MySQL
description: Přečtěte si, jak nakonfigurovat rozšířenou ochranu před internetovými útoky pro detekci neobvykléch databázových aktivit, které indikují potenciální ohrožení zabezpečení databáze.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 43b85ba58ed7163a896054ab9e77ddf82e3461a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902798"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Rozšířená ochrana před internetovými útoky pro Azure Database for MySQL

Služba Advanced Threat Protection pro Azure Database for MySQL detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

Rozšířená ochrana před internetovými útoky je součástí rozšířené nabídky zabezpečení dat, což je jednotný balíček pro pokročilé funkce zabezpečení. Rozšířená ochrana před internetovými útoky je dostupná a spravovaná prostřednictvím [Azure Portal](https://portal.azure.com) a je aktuálně ve verzi Preview.

> [!NOTE]
> Funkce rozšířené ochrany před internetovými útoky **není dostupná v** těchto oblastech cloudu Azure a v svrchovaném cloudu: US Gov – Texas, US Gov – Arizona, US gov – Iowa, US, gov) – virginia, US DoD – východ, US DoD – střed, Německo Central, Německo – sever, Čína – východ, Čína – východ 2. Pokud chcete získat obecnou dostupnost produktu, navštivte prosím [produkty dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) .
>

> [!NOTE]
> Tato funkce je k dispozici ve všech oblastech Azure, kde Azure Database for MySQL nasazeny pro Pro obecné účely a paměťově optimalizované servery.

## <a name="set-up-threat-detection"></a>Nastavení detekce hrozeb
1. Spusťte Azure Portal v [https://portal.azure.com](https://portal.azure.com) .
2. Přejděte na stránku konfigurace serveru Azure Database for MySQL, který chcete chránit. V nastavení zabezpečení vyberte **Rozšířená ochrana před internetovými útoky (Preview)**.
3. Na stránce konfigurace **rozšířené ochrany před internetovými útoky (Preview)** :

   - Povolit rozšířenou ochranu před internetovými útoky na serveru.
   - V části **Upřesnit nastavení ochrany před internetovými útoky**zadejte do textového pole **Odeslat výstrahy do** seznam e-mailů, které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.
  
   :::image type="content" source="./media/howto-database-threat-protection-portal/set-up-threat-protection.png" alt-text="Nastavení detekce hrozeb":::

## <a name="explore-anomalous-database-activities"></a>Prozkoumejte aktivity databáze neobvyklé

Po detekci neobvykléch databázových aktivit obdržíte e-mailové oznámení. E-mail obsahuje informace o podezřelé události zabezpečení, včetně povahy aktivit neobvyklé, názvu databáze, názvu serveru, názvu aplikace a času události. Kromě toho e-mail poskytuje informace o možných příčinách a doporučených akcích k prošetření a zmírnění potenciální hrozby pro databázi.
 
1. Kliknutím na odkaz **Zobrazit nedávné výstrahy** v e-mailu spustíte Azure Portal a zobrazí se stránka Azure Security Center výstrahy, která poskytuje přehled aktivních hrozeb zjištěných v databázi SQL.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/anomalous-activity-report.png" alt-text="Nastavení detekce hrozeb":::

    Zobrazit aktivní hrozby:

    :::image type="content" source="./media/howto-database-threat-protection-portal/active-threats.png" alt-text="Nastavení detekce hrozeb":::

2. Kliknutím na konkrétní výstrahu získáte další podrobnosti a akce pro šetření této hrozby a opravaí budoucích hrozeb.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/specific-alert.png" alt-text="Nastavení detekce hrozeb":::

## <a name="explore-threat-detection-alerts"></a>Prozkoumat výstrahy detekce hrozeb

SQL Database detekce hrozeb integruje své výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/). Živá dlaždice detekce hrozeb SQL sleduje stav aktivních hrozeb na stránkách databáze a ATP SQL v Azure Portal.

Kliknutím na **výstrahu detekce hrozeb** spustíte stránku Azure Security Center výstrahy a získáte přehled o aktivních hrozbách SQL zjištěných v databázi.

   :::image type="content" source="./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png" alt-text="Nastavení detekce hrozeb":::
   

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Další informace o cenách najdete na stránce s [cenami Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/) .  
