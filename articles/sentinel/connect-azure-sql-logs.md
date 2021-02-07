---
title: Připojení protokolů diagnostiky a auditu služby Azure SQL Database do Azure Sentinel
description: Naučte se připojit protokoly diagnostiky služby Azure SQL Database a protokoly auditování zabezpečení do Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: a3a09ceffc75e2d396d7bd7aeedd97b7f2b6ec2b
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807729"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Připojení protokolů diagnostiky a auditu služby Azure SQL Database

Azure SQL je plně spravovaný databázový stroj PaaS (platforma jako služba), který zpracovává většinu funkcí správy databází, jako je upgrade, opravy, zálohování a monitorování, bez zásahu uživatele. 

Konektor služby Azure SQL Database umožňuje streamovat auditování vašich databází a diagnostické protokoly do Azure Sentinel, což vám umožní průběžně monitorovat aktivity ve všech vašich instancích.

- Připojení diagnostických protokolů umožňuje odeslat protokoly diagnostiky databáze různých datových typů do pracovního prostoru Sentinel Azure.

- Připojení protokolů auditování umožňuje streamovat protokoly auditu zabezpečení ze všech databází SQL Azure na úrovni serveru.

Přečtěte si další informace o [monitorování databází SQL Azure](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

## <a name="prerequisites"></a>Požadavky

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Pokud chcete připojit protokoly auditování, musíte mít oprávnění ke čtení a zápisu pro nastavení auditování v Azure SQL Server.

## <a name="connect-to-azure-sql-database"></a>Připojení k databázi Azure SQL
    
1. V navigační nabídce Azure Sentinel vyberte **datové konektory**.

1. V galerii datových konektorů vyberte **Azure SQL Database** a potom v podokně náhledu vyberte **Stránka otevřít konektor**  .

1. V části **Konfigurace** stránky konektor si všimněte dvou kategorií protokolů, které se můžete připojit.

### <a name="connect-diagnostics-logs"></a>Připojit diagnostické protokoly

1. V části **diagnostické protokoly** rozbalte **možnost Povolit diagnostické protokoly v každé databázi SQL Azure ručně**.

1. Kliknutím na odkaz **otevřít Azure sql >** otevřete okno prostředky **SQL Azure** .

1. **(Volitelné)** Pokud chcete najít prostředek databáze snadno, vyberte **Přidat filtr** na panelu filtry v horní části.
    1. V rozevíracím seznamu **Filtr** vyberte **typ prostředku**.
    1. V rozevíracím seznamu **hodnota** zrušte výběr **Vybrat vše** a pak vyberte **SQL Database**.
    1. Klikněte na **Použít**.
    
1. Vyberte databázový prostředek, jehož diagnostické protokoly chcete odeslat do Azure Sentinel.

    > [!NOTE]
    > U každého databázového prostředku, jehož protokoly chcete shromažďovat, je nutné tento postup zopakovat, počínaje tímto krokem.

1. Na stránce prostředek v databázi, kterou jste vybrali, v části **sledování** v navigační nabídce vyberte **nastavení diagnostiky**.

    1. V dolní části tabulky vyberte odkaz **+ Přidat nastavení diagnostiky** .

    1. Na obrazovce **nastavení diagnostiky** zadejte název do pole  **název nastavení diagnostiky** .
    
    1. Ve sloupci **Podrobnosti cíle** označte zaškrtávací políčko **Odeslat do Log Analytics pracovní prostor** . Pod ní se zobrazí dvě nová pole. Vyberte relevantní **předplatné** a **Log Analytics pracovní prostor** (kde se nachází Azure Sentinel).

    1. Ve sloupci **Podrobnosti kategorie** označte zaškrtávací políčka u typů protokolů a metrik, které chcete ingestovat. V obou **protokolech** i v **metrikě** doporučujeme vybrat všechny dostupné typy.

    1. V horní části obrazovky vyberte **Save (Uložit** ).

- Alternativně můžete k připojení diagnostických protokolů použít dodaný **skript prostředí PowerShell** .
    1. V části **diagnostické protokoly** rozbalte **Povolit skript PowerShellu**.

    1. Zkopírujte blok kódu a vložte ho do PowerShellu.

### <a name="connect-audit-logs"></a>Připojit protokoly auditu

1. V části **protokoly auditování (Preview)** rozbalte **možnost povolit protokoly auditování ve všech databázích SQL Azure (na úrovni serveru)**.

1. Výběrem odkazu **otevřít Azure SQL >** otevřete okno prostředků **SQL serveru** .

1. Vyberte SQL Server, jehož protokoly auditování chcete odeslat do Azure Sentinel.

    > [!NOTE]
    > Pro každý prostředek serveru, jehož protokoly chcete shromáždit, je nutné tento postup zopakovat, počínaje tímto krokem.

1. Na stránce prostředek vybraného serveru vyberte v části **zabezpečení** v navigační nabídce možnost **auditování**.

    1. Přesuňte přepínač **Povolit AUDITOVÁNÍ SQL Azure** na **zapnuto**.

    1. V části **cíl protokolu auditu** vyberte **Log Analytics (Preview)**.
    
    1. V seznamu pracovních prostorů, které se zobrazí, vyberte svůj pracovní prostor (kde se nachází Azure Sentinel).

    1. V horní části obrazovky vyberte **Save (Uložit** ).

- Alternativně můžete k připojení diagnostických protokolů použít dodaný **skript prostředí PowerShell** .
    1. V části **protokoly auditování** rozbalte **možnost Povolit skriptem prostředí PowerShell**.

    1. Zkopírujte blok kódu a vložte ho do PowerShellu.


> [!NOTE]
>
> U tohoto konkrétního datového konektoru se indikátory stavu připojení (barevný pruh v galerii datových konektorů a ikony připojení vedle názvů datových typů) budou zobrazovat jako *připojené* (zeleně) pouze v případě, že data byla v určitém bodě za poslední dva týdny. Když dva týdny prošly bez příjmu dat, konektor se zobrazí jako odpojený. Okamžik, kdy se data dostanou, se vrátí do stavu *připojeno* .

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit protokoly diagnostiky a auditu služby Azure SQL Database do služby Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).