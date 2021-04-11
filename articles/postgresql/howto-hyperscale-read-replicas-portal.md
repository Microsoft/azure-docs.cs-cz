---
title: Správa replik pro čtení-Azure Portal-Azure Database for PostgreSQL-Citus (škálování pro čtení)
description: Naučte se spravovat repliky pro čtení Azure Database for PostgreSQL-Citus () z Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 4d3a88692da6d1fc78e96a6261d42d3ca97b0dd0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023955"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Vytváření a Správa replik pro čtení v Azure Database for PostgreSQL-Citus) z Azure Portal

> [!IMPORTANT]
> Čtení replik v Citus) je aktuálně ve verzi Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
>
> Úplný seznam dalších nových funkcí najdete v části [funkce ve verzi Preview pro Citus (škálování)](hyperscale-preview-features.md).

V tomto článku se dozvíte, jak vytvářet a spravovat repliky pro čtení v Citus () z Azure Portal. Další informace o replikách pro čtení najdete v tématu [Přehled](concepts-hyperscale-read-replicas.md).


## <a name="prerequisites"></a>Požadavky

[Skupina serverů Citus (velká škála)](quickstart-create-hyperscale-portal.md) , která bude primární.

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

K vytvoření repliky pro čtení použijte následující postup:

1. Vyberte existující skupinu serverů Azure Database for PostgreSQL, kterou chcete použít jako primární. 

2. Na bočním panelu skupiny serverů v části **Správa skupin serverů** vyberte **replikace**.

3. Vyberte **Přidat repliku**.

4. Zadejte název repliky pro čtení. 

5. Kliknutím na **OK** potvrďte vytváření repliky.

Po vytvoření repliky pro čtení je možné ji zobrazit z okna **replikace** .

> [!IMPORTANT]
>
> Přečtěte si [část s informacemi v tématu Přehled repliky čtení](concepts-hyperscale-read-replicas.md#considerations).
>
> Než se nastavení primární skupiny serverů aktualizuje na novou hodnotu, aktualizujte nastavení repliky na hodnotu rovná se nebo větší. Tato akce pomůže replice uchovávat všechny změny provedené v hlavní větvi.

## <a name="delete-a-primary-server-group"></a>Odstraní primární skupinu serverů.

Pokud chcete odstranit primární skupinu serverů, použijte stejný postup jako při odstraňování samostatné skupiny serverů Citus (standalone Scale). 

> [!IMPORTANT]
>
> Když odstraníte primární skupinu serverů, replikace do všech replik čtení se zastaví. Repliky čtení se stanou samostatnými serverovými skupinami, které teď podporují čtení i zápis.

Pokud chcete odstranit skupinu serverů z Azure Portal, postupujte podle těchto kroků:

1. V Azure Portal vyberte svoji primární skupinu Azure Database for PostgreSQL serverů.

2. Otevřete stránku **Přehled** pro skupinu serverů. Vyberte **Odstranit**.
 
3. Zadejte název primární skupiny serverů, kterou chcete odstranit. Vyberte **Odstranit** a potvrďte odstranění primární skupiny serverů.
 

## <a name="delete-a-replica"></a>Odstranění repliky

Repliku čtení můžete odstranit podobně jako při odstraňování primární skupiny serverů.

- V Azure Portal otevřete stránku **Přehled** repliky pro čtení. Vyberte **Odstranit**.
 
Repliku pro čtení z okna **replikace** můžete také odstranit pomocí následujících kroků:

1. V Azure Portal vyberte svoji primární skupinu serverů Citus ().

2. V nabídce skupina serverů v části **Správa skupin serverů** vyberte **replikace**.

3. Vyberte repliku pro čtení, kterou chcete odstranit.
 
4. Vyberte **Odstranit repliku**.
 
5. Zadejte název repliky, která se má odstranit. Vyberte **Odstranit** a potvrďte odstranění repliky.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [replikách pro čtení v Azure Database for PostgreSQL – Citus (škálování)](concepts-hyperscale-read-replicas.md).
