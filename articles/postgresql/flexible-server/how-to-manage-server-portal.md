---
title: Správa serveru Azure Portal-Azure Database for PostgreSQL-flexibilní Server
description: Naučte se spravovat Azure Database for PostgreSQL flexibilní Server z Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961404"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Správa Azure Database for PostgreSQL flexibilního serveru pomocí Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

V tomto článku se dozvíte, jak spravovat Azure Database for PostgreSQL flexibilní Server. Úlohy správy zahrnují výpočetní výkon a škálování úložiště, resetování hesla správce a zobrazení podrobností serveru.

## <a name="sign-in"></a>Přihlásit se

Přihlaste se k [portálu Azure Portal](https://portal.azure.com). V Azure Portal přejít na prostředek flexibilního serveru.

## <a name="scale-compute-and-storage"></a>Škálování výpočetních prostředků a úložiště

Po vytvoření serveru můžete škálovat mezi různými [cenovými úrovněmi](https://azure.microsoft.com/pricing/details/postgresql/) , jak se vaše potřeby mění. Můžete také snížit nebo snížit kapacitu výpočetních prostředků a paměti tím, že zvýšíte nebo zmenšíte virtuální jádra.

> [!NOTE]
> Velikost úložiště nejde škálovat dolů na nižší hodnotu.

1. V Azure Portal vyberte svůj server. Vyberte **COMPUTE + úložiště**, které najdete v části **Nastavení** .
2. Můžete změnit **výpočetní vrstvu** , **Vcore**, **úložiště** pro horizontální navýšení kapacity serveru pomocí vyšší výpočetní úrovně nebo navýšení kapacity v rámci stejné úrovně zvýšením úložiště nebo virtuální jádra na požadovanou hodnotu.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="škálování úložiště flexibilního serveru":::

> [!Important]
> - Velikost úložiště nejde škálovat.
> - Škálování virtuální jádra způsobí restartování serveru.

3. Kliknutím na **tlačítko OK** uložte změny.

## <a name="reset-admin-password"></a>Resetovat heslo správce

Můžete změnit heslo role správce pomocí Azure Portal.

1. V Azure Portal vyberte svůj server. V okně **Přehled** vyberte **resetovat heslo**.
2. Zadejte nové heslo a potvrďte ho. Textové pole vás vyzve k zadání požadavků na složitost hesla.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="škálování úložiště flexibilního serveru":::

3. Kliknutím na **Uložit** uložte nové heslo.

## <a name="delete-a-server"></a>Odstranění serveru

Server můžete odstranit, pokud ho už nepotřebujete.

1. V Azure Portal vyberte svůj server. V okně **Přehled** vyberte **Odstranit**.
2. Zadáním názvu serveru do vstupního pole potvrďte, že chcete odstranit server.

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="škálování úložiště flexibilního serveru":::

   > [!IMPORTANT]
   > Odstranění serveru je nevratné.

  > [!div class="mx-imgBorder"]
  > ![odstranění flexibilního serveru](./media/howto-manage-server-portal/delete-server.png)  

3. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

- [Vysvětlení konceptů zálohování a obnovení](concepts-backup-restore.md)
- [Vyladění a sledování serveru](concepts-monitoring.md)
