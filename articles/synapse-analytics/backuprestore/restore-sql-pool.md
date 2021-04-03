---
title: Obnovení existujícího vyhrazeného fondu SQL
description: Návod pro obnovení existujícího vyhrazeného fondu SQL.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94332063"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Obnovení existujícího vyhrazeného fondu SQL

V tomto článku se dozvíte, jak obnovit existující vyhrazený fond SQL ve službě Azure synapse Analytics pomocí Azure Portal a synapse studia. Tento článek se týká obnovení i geografického obnovení. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Obnovení existujícího vyhrazeného fondu SQL prostřednictvím synapse studia

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Přejděte do svého pracovního prostoru synapse. 
3. V části Začínáme-> otevřete synapse Studio, vyberte **otevřít**.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. V levém navigačním podokně vyberte **data**.
5. Vyberte **spravovat fondy**. 
6. Vyberte **+ Nová** a vytvořte nový vyhrazený fond SQL. 
7. Na kartě Další nastavení vyberte bod obnovení, ze kterého chcete obnovit. 

    Pokud chcete provést geografické obnovení, vyberte pracovní prostor a vyhrazený fond SQL, který chcete obnovit. 

8. Vyberte buď **body automatického obnovení** nebo **body obnovení definované uživatelem**. 

    ![Body obnovení](../media/sql-pools/restore-point.PNG)

    Pokud vyhrazený fond SQL neobsahuje žádné automatické body obnovení, počkejte několik hodin nebo před obnovením vytvořte uživatelem definovaný bod obnovení. U User-Defined bodů obnovení vyberte existující nebo vytvořte novou.

    Pokud obnovujete geografickou zálohu, stačí vybrat pracovní prostor umístěný ve zdrojové oblasti a vyhrazený fond SQL, který chcete obnovit. 

9. Vyberte **Zkontrolovat a vytvořit**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Obnovení existujícího vyhrazeného fondu SQL prostřednictvím Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Přejděte do vyhrazeného fondu SQL, ze kterého chcete obnovit.
3. V horní části okna Přehled vyberte **obnovit**.

    ![ Obnovení – přehled](../media/sql-pools/restore-sqlpool-01.png)

4. Vyberte buď **body automatického obnovení** nebo **body obnovení definované uživatelem**. 

    Pokud vyhrazený fond SQL neobsahuje žádné automatické body obnovení, počkejte několik hodin nebo před obnovením vytvořte bod obnovení definovaný uživatelem. 

    Pokud chcete provést geografické obnovení, vyberte pracovní prostor a vyhrazený fond SQL, který chcete obnovit. 

5. Vyberte **Zkontrolovat a vytvořit**.

## <a name="next-steps"></a>Další kroky

- [Vytvořit bod obnovení](sqlpool-create-restore-point.md)
