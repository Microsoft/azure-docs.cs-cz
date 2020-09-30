---
title: Přesunout oblasti Azure – Azure Portal-Azure Database for PostgreSQL – jeden server
description: Přesuňte server Azure Database for PostgreSQL z jedné oblasti Azure do jiné pomocí repliky pro čtení a Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: c7c095aa710d97292afc7d2d8f633058c21fa4d0
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539227"
---
# <a name="move-an-azure-database-for-azure-database-for-postgresql---single-server-to-another-region-by-using-the-azure-portal"></a>Přesunout Azure Database for Azure Database for PostgreSQL – jeden server do jiné oblasti pomocí Azure Portal

Existují různé scénáře, jak přesunout existující Azure Database for PostgreSQL Server z jedné oblasti do druhé. Například můžete chtít přesunout provozní server do jiné oblasti v rámci plánování zotavení po havárii.

K dokončení přesunu do jiné oblasti můžete použít [repliku čtení Azure Database for PostgreSQL mezi jednotlivými oblastmi](concepts-read-replicas.md#cross-region-replication) . K tomu je třeba nejprve vytvořit repliku pro čtení v cílové oblasti. Potom zastavte replikaci na server repliky pro čtení, aby byl samostatný server, který přijímá přenosy čtení i zápisu. 

> [!NOTE]
> Tento článek se zaměřuje na přesun serveru do jiné oblasti. Pokud chcete přesunout server do jiné skupiny prostředků nebo předplatného, přečtěte si článek o [přesunutí](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) . 

## <a name="prerequisites"></a>Předpoklady

- Funkce repliky čtení mezi oblastmi je k dispozici pouze pro Azure Database for PostgreSQL jeden server v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je zdrojový server v jedné z těchto cenových úrovní.

- Ujistěte se, že je zdrojový server Azure Database for PostgreSQL v oblasti Azure, ze které chcete přejít.

## <a name="prepare-to-move"></a>Příprava na přesunutí

K přípravě zdrojového serveru pro replikaci pomocí Azure Portal použijte následující postup: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte existující server Azure Database for PostgreSQL, který chcete použít jako zdrojový server. Tato akce otevře stránku s **přehledem** .
1. V nabídce serveru vyberte **replikace**. Pokud je podpora replikace Azure nastavená na aspoň **repliku**, můžete vytvořit repliky pro čtení. 
1. Pokud není podpora replikace Azure nastavená na aspoň **repliku**, nastavte ji. Vyberte **Uložit**.
1. Restartujte server, aby se změna projevila, a to tak, že vyberete **Ano**.
1. Až se operace dokončí, zobrazí se vám dvě oznámení Azure Portal. Pro aktualizaci parametru serveru je k dispozici jedno oznámení. Pro okamžité restartování serveru je k dispozici jiné oznámení.
1. Aktualizujte stránku Azure Portal a aktualizujte tak panel nástrojů pro replikaci. Nyní můžete vytvořit repliky čtení pro tento server.

Pokud chcete vytvořit v cílové oblasti server repliky pro různé oblasti pomocí Azure Portal, použijte následující postup:

1. Vyberte existující server Azure Database for PostgreSQL, který chcete použít jako zdrojový server.
1. V nabídce v části **Nastavení**vyberte **replikace** .
1. Vyberte **Přidat repliku**.
1. Zadejte název serveru repliky.
1. Vyberte umístění serveru repliky. Výchozí umístění je stejné jako na primárním serveru. Ověřte, zda jste vybrali cílové umístění, do kterého chcete repliku nasadit.
1. Vyberte **OK** a potvrďte tak vytvoření repliky. Při vytváření repliky se data zkopírují ze zdrojového serveru do repliky. Čas vytvoření může trvat několik minut nebo i déle, a to v poměru k velikosti zdrojového serveru.

>[!NOTE]
> Když vytváříte repliku, nedědí pravidla firewallu a koncové body služby virtuální sítě primárního serveru. Tato pravidla musí být pro repliku nastavena nezávisle.

## <a name="move"></a>Přesunout

> [!IMPORTANT]
> Samostatný server se nedá znovu vytvořit do repliky.
> Před zastavením replikace v replice pro čtení zajistěte, aby měla replika všechna data, která požadujete.

Pokud chcete zastavit replikaci do repliky z Azure Portal, použijte následující postup:

1. Po vytvoření repliky vyhledejte a vyberte zdrojový server Azure Database for PostgreSQL. 
1. V nabídce v části **Nastavení**vyberte **replikace** .
1. Vyberte server repliky.
1. Vyberte **zastavit replikaci**.
1. Kliknutím na **OK**potvrďte, že chcete replikaci zastavit.

## <a name="clean-up-source-server"></a>Vyčištění zdrojového serveru

Je možné, že budete chtít zdrojový Azure Database for PostgreSQL server odstranit. Můžete to udělat podle následujících kroků:

1. Po vytvoření repliky vyhledejte a vyberte zdrojový server Azure Database for PostgreSQL.
1. V okně **Přehled** vyberte **Odstranit**.
1. Zadejte název zdrojového serveru a potvrďte, že chcete odstranit.
1. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli Azure Database for PostgreSQL Server z jedné oblasti do druhé pomocí Azure Portal a pak vyčistili nepotřebné zdrojové prostředky. 

- Další informace o [replikách pro čtení](concepts-read-replicas.md)
- Další informace o [správě replik čtení v Azure Portal](howto-read-replicas-portal.md)
- Další informace o možnostech [kontinuity podnikových aplikací](concepts-business-continuity.md)
