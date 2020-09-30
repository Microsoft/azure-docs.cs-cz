---
title: Přesunutí oblastí Azure – Azure Portal-Azure Database for MariaDB
description: Přesuňte server Azure Database for MariaDB z jedné oblasti Azure do jiné pomocí repliky pro čtení a Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: b2e4bc71a0883c6fef6f0115080a79a74ced92b0
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542423"
---
# <a name="move-an-azure-database-for-mariadb-server-to-another-region-by-using-the-azure-portal"></a>Přesunutí serveru Azure Database for MariaDB do jiné oblasti pomocí Azure Portal

Existují různé scénáře, jak přesunout existující Azure Database for MariaDB Server z jedné oblasti do druhé. Například můžete chtít přesunout provozní server do jiné oblasti v rámci plánování zotavení po havárii.

K dokončení přesunu do jiné oblasti můžete použít [repliku čtení Azure Database for MariaDB mezi jednotlivými oblastmi](concepts-read-replicas.md#cross-region-replication) . K tomu je třeba nejprve vytvořit repliku pro čtení v cílové oblasti. Potom zastavte replikaci na server repliky pro čtení, aby byl samostatný server, který přijímá přenosy čtení i zápisu. 

> [!NOTE]
> Tento článek se zaměřuje na přesun serveru do jiné oblasti. Pokud chcete přesunout server do jiné skupiny prostředků nebo předplatného, přečtěte si článek o [přesunutí](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) . 

## <a name="prerequisites"></a>Předpoklady

- Funkce replika čtení je k dispozici pouze pro Azure Database for MariaDB servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je zdrojový server v jedné z těchto cenových úrovní.

- Ujistěte se, že je zdrojový server Azure Database for MariaDB v oblasti Azure, ze které chcete přejít.

## <a name="prepare-to-move"></a>Příprava na přesunutí

Pokud chcete vytvořit v cílové oblasti server repliky pro různé oblasti pomocí Azure Portal, použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte existující server Azure Database for MariaDB, který chcete použít jako zdrojový server. Tato akce otevře stránku s **přehledem** .
1. V nabídce v části **Nastavení**vyberte **replikace** .
1. Vyberte **Přidat repliku**.
1. Zadejte název serveru repliky.
1. Vyberte umístění serveru repliky. Výchozí umístění je stejné jako na zdrojovém serveru. Ověřte, zda jste vybrali cílové umístění, do kterého chcete repliku nasadit.
1. Vyberte **OK** a potvrďte tak vytvoření repliky. Při vytváření repliky se data zkopírují ze zdrojového serveru do repliky. Čas vytvoření může trvat několik minut nebo i déle, a to v poměru k velikosti zdrojového serveru.

>[!NOTE]
> Když vytváříte repliku, nedědí koncové body služby virtuální sítě zdrojového serveru. Tato pravidla musí být pro repliku nastavena nezávisle.

## <a name="move"></a>Přesunout

> [!IMPORTANT]
> Samostatný server se nedá znovu vytvořit do repliky.
> Před zastavením replikace v replice pro čtení zajistěte, aby měla replika všechna data, která požadujete.

Zastavení replikace na server repliky způsobí, že se stane samostatným serverem. Pokud chcete zastavit replikaci do repliky z Azure Portal, použijte následující postup:

1. Po vytvoření repliky vyhledejte a vyberte zdrojový server Azure Database for MariaDB. 
1. V nabídce v části **Nastavení**vyberte **replikace** .
1. Vyberte server repliky.
1. Vyberte **zastavit replikaci**.
1. Kliknutím na **OK**potvrďte, že chcete replikaci zastavit.

## <a name="clean-up-source-server"></a>Vyčištění zdrojového serveru

Je možné, že budete chtít zdrojový Azure Database for MariaDB server odstranit. Můžete to udělat podle následujících kroků:

1. Po vytvoření repliky vyhledejte a vyberte zdrojový server Azure Database for MariaDB.
1. V okně **Přehled** vyberte **Odstranit**.
1. Zadejte název zdrojového serveru a potvrďte, že chcete odstranit.
1. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli Azure Database for MariaDB Server z jedné oblasti do druhé pomocí Azure Portal a pak vyčistili nepotřebné zdrojové prostředky. 

- Další informace o [replikách pro čtení](concepts-read-replicas.md)
- Další informace o [správě replik čtení v Azure Portal](howto-read-replicas-portal.md)
- Další informace o možnostech [kontinuity podnikových aplikací](concepts-business-continuity.md)
