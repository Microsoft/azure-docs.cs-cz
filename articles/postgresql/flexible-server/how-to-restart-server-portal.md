---
title: Restart-Azure Portal-Azure Database for PostgreSQL-flexibilní Server
description: Tento článek popisuje, jak provádět operace restartování v Azure Database for PostgreSQL prostřednictvím Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90934871"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Restartování Azure Database for PostgreSQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Tento článek poskytuje podrobný postup, jak provést restart flexibilního serveru. Tato operace je užitečná pro použití všech statických změn parametrů, které vyžadují restart databázového serveru. Postup je stejný pro servery nakonfigurované se redundantní vysokou dostupností v zóně. 

> [!IMPORTANT]
> Pokud je nakonfigurovaná s vysokou dostupností, primární i pohotovostní server se restartují ve stejnou dobu.

## <a name="pre-requisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

-   Musíte mít flexibilní Server.

## <a name="restart-your-flexible-server"></a>Restartování flexibilního serveru

Pomocí těchto kroků restartujete flexibilní Server.

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, který chcete restartovat.

2.  V levém panelu klikněte na **Přehled** a pak klikněte na **restartovat**.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="Restartovat výběr":::

3.  Zobrazí se automaticky otevíraná zpráva s potvrzením.

4.  Pokud chcete pokračovat, klikněte na **Ano** .
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="Potvrdit restartování":::
 
6.  Zobrazí se oznámení o zahájení operace restartování.

## <a name="next-steps"></a>Další kroky

-   Informace o [kontinuitě podnikových aplikací](./concepts-business-continuity.md)
-   Přečtěte si o [vysoké dostupnosti zóny jako redundantní](./concepts-high-availability.md) .
