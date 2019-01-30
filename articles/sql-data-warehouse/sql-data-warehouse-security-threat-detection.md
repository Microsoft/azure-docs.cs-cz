---
title: Detekce hrozeb – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Konfigurace zjišťování hrozeb a prozkoumat podezřelé události ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 112d093946099e623d1edfa4555eda681069892e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243381"
---
# <a name="threat-detection-in-azure-sql-data-warehouse"></a>Detekce hrozeb ve službě Azure SQL Data Warehouse
Konfigurace zjišťování hrozeb a prozkoumat podezřelé události ve službě Azure SQL Data Warehouse.

## <a name="what-is-threat-detection"></a>Co je detekce hrozeb
Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze. 

Detekce hrozeb poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje díky poskytování výstrah zabezpečení na neobvyklé aktivity detekovat a reagovat na potenciální hrozby, když k nim dojde. Uživatelé můžou prozkoumat podezřelé události pomocí [auditování služby Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md) k určení, jestli jsou důsledkem pokus o přístup, narušení nebo zneužití dat v datovém skladu.
Detekce hrozeb usnadňuje řešení potenciálních ohrožení datový sklad, aniž byste museli být odborné zabezpečení nebo spravovat pokročilé systémy monitorování zabezpečení.

Detekce hrozeb například detekuje určité neobvyklé databázové aktivity značící potenciální pokusy o útok prostřednictvím injektáže SQL. Injektáž SQL představuje jeden z nejběžnějších problémů zabezpečení webových aplikací na internetu a používá se k útokům na aplikace založené na datech. Útočníci využívají ohrožení zabezpečení aplikací k injektáži škodlivých příkazů jazyka SQL do vstupních polí aplikace za účelem porušení zabezpečení nebo úpravy dat v databázi.

## <a name="set-up-threat-detection-for-your-database"></a>Nastavení detekce hrozeb pro vaši databázi
1. Spuštění webu Azure Portal na [ https://portal.azure.com ](https://portal.azure.com).
2. Přejděte do okna konfigurace služby SQL Data Warehouse, kterou chcete monitorovat. V okně Nastavení vyberte **Auditování a detekce hrozeb**.
   
    ![Navigační podokno](media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png)
3. V **auditování a detekce hrozeb** zapnout okno Konfigurace **ON** auditování, které se zobrazí nastavení detekce hrozeb.
   
    ![Navigační podokno](media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png)
4. Zapnout **ON** detekce hrozeb.
5. Nakonfigurujte seznam emailů, které budou přijímat výstrahy zabezpečení po detekci neobvyklých datového skladu aktivit.
6. Klikněte na tlačítko **Uložit** v **auditování a detekce hrozeb** okno Konfigurace k uložení nových nebo aktualizovaných auditování a zásad detekce hrozeb.
   
    ![Navigační podokno](media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png)

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Prozkoumejte neobvyklého datového skladu aktivity při zjištění podezřelé události
1. Zobrazí se oznámení o e-mailu po detekci neobvyklých databázových aktivit. <br/>
   E-mail bude obsahovat informace o podezřelé události zabezpečení, včetně povahy neobvyklých aktivit, názvu databáze, názvu serveru a času události. Kromě toho bude obsahovat informace o možných příčinách a doporučených akcích pro šetření a zmírnění potenciálního ohrožení databáze.<br/>
   
    ![Navigační podokno](media/sql-data-warehouse-security-threat-detection/4_td_email.png)
2. V e-mailu, klikněte na **protokol auditování SQL Azure** odkaz, který otevře Azure portal, který se zobrazí relevantní záznamy auditování v době výskytu podezřelé události.
   
    ![Navigační podokno](media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png)
3. Klikněte na záznamy auditu zobrazíte další podrobnosti o podezřelých databázových aktivitách, jako je příkaz jazyka SQL, selhání z důvodu a IP adresa klienta.
   
    ![Navigační podokno](media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png)
4. V okně Záznamy auditování klikněte na **Otevřít v aplikaci Excel** a otevřete předkonfigurovanou šablonu aplikace Excel umožňující import a spuštění hlubší analýzy protokolu auditu v době výskytu podezřelé události.<br/>
   **Poznámka:** V aplikaci Excel 2010 nebo novější Power Query a **rychle zkombinovat** nastavení je povinné
   
    ![Navigační podokno](media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png)
5. Konfigurace nastavení **Rychle zkombinovat** – Na záložce pásu karet **POWER QUERY** vyberte **Možnosti** a zobrazte dialogové okno Možnosti. Vyberte část Osobní údaje a zvolte druhou možnost – Ignorovat úrovně ochrany osobních údajů a potenciálně tak vylepšit výkon:
   
    ![Navigační podokno](media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png)
6. Načíst protokoly auditu SQL, zajistěte, aby parametrů v nastavení kartě jsou správně nastavena a poté vyberte pásu karet, Data a klikněte na tlačítko 'aktualizovat vše.
   
    ![Navigační podokno](media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png)
7. Výsledky se zobrazí na listu **Protokoly auditu SQL**, na kterém můžete spustit hlubší analýzu detekovaných neobvyklých aktivit a zmírnit dopad události zabezpečení na vaši aplikaci.

## <a name="next-steps"></a>Další postup
Další informace o zabezpečení, najdete v části [zabezpečení datového skladu](sql-data-warehouse-overview-manage-security.md).
