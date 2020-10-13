---
title: Koncepty serveru – Azure Database for MySQL flexibilního serveru
description: V tomto tématu najdete pokyny a pokyny pro práci s Azure Database for MySQL flexibilním serverem.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7217817e9add6214c2da8362a2769cad0c2cf330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936165"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Koncepty serveru v Azure Database for MySQL flexibilním serveru (Preview)

> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Tento článek popisuje informace a pokyny pro práci s Azure Database for MySQL flexibilními servery.

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>Co je Azure Database for MySQL flexibilní Server?

Azure Database for MySQL flexibilní Server je plně spravovaná databázová služba, ve které je spuštěná komunita verze MySQL. Obecně je služba navržena tak, aby poskytovala flexibilitu a přizpůsobení konfigurace podle požadavků uživatelů. Je to stejná konstrukce serveru MySQL, kterou můžete znát v místním světě. Konkrétně se spravuje flexibilní Server, který poskytuje výkon v krabicích, lepší správu a řízení serveru a zpřístupňuje přístup a funkce na úrovni serveru.

Azure Database for MySQL flexibilní Server:

- Vytvoří se v rámci předplatného Azure.
- Je nadřazeným prostředkem pro databáze.
- Umožňuje zpřístupnění konfigurace MySQL prostřednictvím parametrů serveru (odkaz na koncepty parametrů serveru).
- Provádí automatizované zálohování a podporuje obnovení bodu v čase.
- Poskytuje obor názvů pro databáze.
- Je kontejner se sémantikou silné životnosti – odstraní Server a odstraní databáze s omezením.
- Uspořádává prostředky v oblasti.
- Podpora plánu údržby serveru poskytovaného zákazníkem
- Možnost nasazovat flexibilní servery v zóně redundantním nastavením pro lepší vysokou dostupnost
- Poskytuje integraci virtuální sítě pro přístup k databázovému serveru.
- Poskytuje způsob, jak ušetřit náklady tím, že se pozastaví flexibilní Server, když se nepoužívá.
- Poskytuje obor pro zásady správy, které se vztahují na jeho databáze: přihlašovací jméno, brána firewall, uživatelé, role, konfigurace atd.
- Se aktuálně podporuje s verzí MySQL 5,7. Další informace najdete v tématu [podporované verze modulu Azure Database for MySQL](./concepts-supported-versions.md).

V rámci Azure Database for MySQL flexibilního serveru můžete vytvořit jednu nebo více databází. Můžete se rozhodnout, že vytvoříte izolovanou databázi na jeden server, abyste mohli použít všechny prostředky, nebo vytvořit více databází pro sdílení prostředků. Ceny jsou strukturované na server, a to na základě konfigurace výpočetní vrstvy, virtuální jádra a úložiště (GB). Další informace najdete v tématu [COMPUTE a Storage](./concepts-compute-storage.md).

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>Zastavení nebo spuštění Azure Database for MySQL flexibilního serveru

Azure Database for MySQL flexibilní server vám dává možnost **zastavit** Server, pokud se nepoužívá, a **Spustit** Server při obnovení aktivity. To je v podstatě hotové, aby se ušetřily náklady na databázové servery a platily jenom za prostředek, který se používá. To je ještě důležitější pro úlohy vývoje a testování, a pokud server používáte jenom během dne. Po zastavení serveru budou všechna aktivní připojení vyhozena. Později, pokud chcete převést Server zpátky do režimu online, můžete použít [Azure Portal](how-to-stop-start-server-portal.md) nebo CLI.

Pokud je server v **zastaveném** stavu, výpočetní služba se neúčtuje. Úložiště se ale nadále bude účtovat, protože úložiště serveru zůstává, aby se zajistilo, že datové soubory budou k dispozici, až se server znovu spustí.

> [!IMPORTANT]
> Když **zastavíte** Server, zůstane v tomto stavu po dobu dalších 7 dnů roztažení. Pokud ji během této doby **nespustíte** ručně, server bude automaticky spuštěn na konci 7 dnů. Pokud nepoužíváte server, můžete ho znovu **zastavit** .

Během tohoto časového serveru nelze na serveru provádět žádné operace správy. Aby bylo možné změnit nastavení konfigurace na serveru, bude nutné [spustit server](how-to-stop-start-server-portal.md). Přečtěte si o [omezeních Stop/Start](./concepts-limitations.md#stopstart-operation).

## <a name="how-do-i-manage-a-server"></a>Návody spravovat Server?

Azure Database for MySQL flexibilní Server můžete spravovat pomocí [Azure Portal](./quickstart-create-server-portal.md) nebo rozhraní příkazového [řádku Azure](./quickstart-create-server-cli.md).

## <a name="next-steps"></a>Další kroky

-   Informace o [vytvoření serveru](./quickstart-create-server-portal.md)
-   Informace o [monitorování a výstrahách](./how-to-alert-on-metric.md)

