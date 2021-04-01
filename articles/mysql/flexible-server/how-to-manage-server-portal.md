---
title: Správa serveru-Azure Portal-Azure Database for MySQL flexibilního serveru
description: Naučte se spravovat Azure Database for MySQL flexibilní Server z Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7a01863b3a0c29e94550be67ca957655cff32660
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90935069"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Správa Azure Database for MySQLho flexibilního serveru (ve verzi Preview) pomocí Azure Portal


> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

V tomto článku se dozvíte, jak spravovat Azure Database for MySQL flexibilní servery (Preview). Úlohy správy zahrnují škálování na výpočetní výkon a úložiště, heslo správce serveru Rest a odstranění serveru.

## <a name="sign-in"></a>Přihlásit se
Přihlaste se na [Azure Portal](https://portal.azure.com). V Azure Portal přejít na prostředek flexibilního serveru.

## <a name="scale-compute-and-storage"></a>Škálování výpočetních prostředků a úložiště

Po vytvoření serveru můžete škálovat mezi různými [cenovými úrovněmi](https://azure.microsoft.com/pricing/details/mysql/) , jak se vaše potřeby mění. Zvýšením nebo snížením počtu virtuálních jader můžete také vertikálně navýšit nebo snížit kapacitu výpočetního výkonu a paměti.

1. V Azure Portal vyberte svůj server. Vyberte **COMPUTE + úložiště**, které najdete v části **Nastavení** .

2. Můžete změnit **výpočetní vrstvu**, **Vcore**, **úložiště** pro horizontální navýšení kapacity serveru pomocí vyšší výpočetní úrovně nebo navýšení kapacity v rámci stejné úrovně zvýšením úložiště nebo virtuální jádra na požadovanou hodnotu.

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
   > :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="resetování hesla pro flexibilní Server":::

3. Kliknutím na **Uložit** uložte nové heslo.

## <a name="delete-a-server"></a>Odstranění serveru

Server můžete odstranit, pokud ho už nepotřebujete.

1. V Azure Portal vyberte svůj server. V okně **Přehled** vyberte **Odstranit**.

2. Zadáním názvu serveru do vstupního pole potvrďte, že chcete odstranit server.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="odstranění flexibilního serveru":::

   > [!NOTE]
   > Odstranění serveru je nevratné.

3. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky
- [Informace o tom, jak spustit nebo zastavit server](how-to-stop-start-server-portal.md)
- [Informace o tom, jak obnovit server](how-to-restore-server-portal.md)
- [Řešení potíží s připojením](how-to-troubleshoot-common-connection-issues.md)

