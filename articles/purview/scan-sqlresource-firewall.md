---
title: Kontrola služby Azure SQL DB za bránou firewall
description: Naučte se kontrolovat prostředky za bránou firewall pomocí portálu Azure dosah.
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552594"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Kontrola služby Azure SQL DB za bránou firewall v Azure dosah

V tomto článku se dozvíte, jak pomocí Azure dosah vyhledat prostředek za bránou firewall.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Váš vlastní [Azure Active Directory tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="set-up-sql-storage-behind-a-firewall"></a>Nastavení úložiště SQL za bránou firewall

Prvním krokem je přidání MSI katalogu do Azure SQL DB prostřednictvím [registrace a prohledání Azure SQL Database](register-scan-azure-sql-database.md).

## <a name="scan-sql-db-from-purview"></a>Prohledávání SQL DB z dosah

1. Přejděte na domovskou stránku dosah.

1. Na levém navigačním panelu vyberte **Centrum pro správu** .

1. Vyberte **zdroje dat** v části **zdroje a skenování**.

1. Pokud chcete přidat zdroj dat, vyberte **+ Nový** .

1. Vyberte **Azure SQL Database**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="Snímek obrazovky pro výběr SQL serveru":::

1. Zadejte název nového zdroje dat, vyberte možnost **z předplatného Azure** a z rozevíracího seznamu vyberte své předplatné a název serveru.

   Kliknutím na **Dokončit** dokončete registraci. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="Snímek obrazovky pro dokončení výběru":::

1. Vyberte **nastavit kontrolu** pro úložiště, které je za bránou firewall a otestujte připojení. Připojení indikuje, že je úspěšné. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="Snímek obrazovky výběru T0 nastavení kontroly":::

1. Nastavte četnost prohledávání a vyberte **pokračovat**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="Snímek obrazovky výběru, který spustí kontrolu SQL.":::

1.  Kontrola se dokončí a stav se aktualizuje v seznamu zdrojů dat.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="Snímek obrazovky dokončené kontroly zprávy":::
   
## <a name="next-steps"></a>Další kroky

V dalším kroku můžete nastavit sadu pravidel skenování [vytvořením sady pravidel skenování](create-a-scan-rule-set.md) , která bude seskupovat kontroly.
