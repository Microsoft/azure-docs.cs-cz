---
title: Přesunutí oblastí Azure – Azure Portal-Azure Database for MySQL
description: Přesuňte server Azure Database for MySQL z jedné oblasti Azure do jiné pomocí repliky pro čtení a Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.openlocfilehash: 279526d31adf25d1f4cd5a6d9b15519679bd88b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540225"
---
# <a name="move-an-azure-database-for-mysql-server-to-another-region-by-using-the-azure-portal"></a>Přesunutí serveru Azure Database for MySQL do jiné oblasti pomocí Azure Portal

Existují různé scénáře, jak přesunout existující Azure Database for MySQL server z jedné oblasti do druhé. Například můžete chtít přesunout provozní server do jiné oblasti v rámci plánování zotavení po havárii.

K dokončení přesunu do jiné oblasti můžete použít [repliku čtení Azure Database for MySQL mezi jednotlivými oblastmi](concepts-read-replicas.md#cross-region-replication) . K tomu je třeba nejprve vytvořit repliku pro čtení v cílové oblasti. Potom zastavte replikaci na server repliky pro čtení, aby byl samostatný server, který přijímá přenosy čtení i zápisu. 

> [!NOTE]
> Tento článek se zaměřuje na přesun serveru do jiné oblasti. Pokud chcete přesunout server do jiné skupiny prostředků nebo předplatného, přečtěte si článek o [přesunutí](../azure-resource-manager/management/move-resource-group-and-subscription.md) . 

## <a name="prerequisites"></a>Požadavky

- Funkce replika čtení je k dispozici pouze pro Azure Database for MySQL servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je zdrojový server v jedné z těchto cenových úrovní.

- Ujistěte se, že je zdrojový server Azure Database for MySQL v oblasti Azure, ze které chcete přejít.

## <a name="prepare-to-move"></a>Příprava na přesunutí

Pokud chcete vytvořit v cílové oblasti server repliky pro různé oblasti pomocí Azure Portal, použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte existující server Azure Database for MySQL, který chcete použít jako zdrojový server. Tato akce otevře stránku s **přehledem** .
1. V nabídce v části **Nastavení** vyberte **replikace** .
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

1. Po vytvoření repliky vyhledejte a vyberte zdrojový server Azure Database for MySQL. 
1. V nabídce v části **Nastavení** vyberte **replikace** .
1. Vyberte server repliky.
1. Vyberte **zastavit replikaci**.
1. Kliknutím na **OK** potvrďte, že chcete replikaci zastavit.

## <a name="clean-up-source-server"></a>Vyčištění zdrojového serveru

Je možné, že budete chtít zdrojový Azure Database for MySQL server odstranit. Můžete to udělat podle následujících kroků:

1. Po vytvoření repliky vyhledejte a vyberte zdrojový server Azure Database for MySQL.
1. V okně **Přehled** vyberte **Odstranit**.
1. Zadejte název zdrojového serveru a potvrďte, že chcete odstranit.
1. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli Azure Database for MySQL server z jedné oblasti do druhé pomocí Azure Portal a pak vyčistili nepotřebné zdrojové prostředky. 

- Další informace o [replikách pro čtení](concepts-read-replicas.md)
- Další informace o [správě replik čtení v Azure Portal](howto-read-replicas-portal.md)
- Další informace o možnostech [kontinuity podnikových aplikací](concepts-business-continuity.md)