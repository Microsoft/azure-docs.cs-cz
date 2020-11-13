---
title: Místní změna verze SQL Server
description: Naučte se, jak ve službě Azure změnit verzi SQL Serverho virtuálního počítače.
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: a0ecc36d78ffde002dac971a749889104ff10073
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556450"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Místní změna verze SQL Server na virtuálním počítači Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek popisuje, jak změnit verzi Microsoft SQL Server na virtuálním počítači s Windows (VM) v Microsoft Azure.

## <a name="prerequisites"></a>Požadavky

K provedení místního upgradu SQL Server platí následující podmínky:

- Je třeba zadat instalační médium požadované verze SQL Server. Zákazníci, kteří mají [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default), můžou instalační médium získat z centra [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Zákazníci, kteří nemají Software Assurance, mohou použít instalační médium z Azure Marketplace SQL Server image virtuálního počítače, která má novější verzi SQL Server (obvykle se nachází v C:\SQLServerFull).
- Upgrady edice by se měly řídit pomocí [cest upgradu podpory](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15).

## <a name="planning-for-version-change"></a>Plánování změny verze

Před změnou verze doporučujeme zkontrolovat následující položky:

1. Podívejte se, co je nového ve verzi, na kterou plánujete upgradovat:

   - Co je nového v [SQL 2019](/sql/sql-server/what-s-new-in-sql-server-ver15)
   - Co je nového v [SQL 2017](/sql/sql-server/what-s-new-in-sql-server-2017)
   - Co je nového v [SQL 2016](/sql/sql-server/what-s-new-in-sql-server-2016)


1. Doporučujeme, abyste kontrolovali [certifikaci kompatibility](/sql/database-engine/install-windows/compatibility-certification) pro verzi, na kterou se chystáte přejít, abyste mohli použít režimy kompatibility databáze k minimalizaci účinku upgradu.
1. Následující články vám pomohou zajistit úspěšný výsledek:

   - [Video: modernizaci SQL Server | Pam Lahoud & Pedro Lopes | 20 let PASS](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [Pomocník pro experimentování s databázemi pro testování AB](/sql/dea/database-experimentation-assistant-overview)
   - [Upgrade databází pomocí Pomocníka pro optimalizaci dotazů](/sql/relational-databases/performance/upgrade-dbcompat-using-qta)
   - [Změna úrovně kompatibility databáze a použití úložiště dotazů](/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store)

## <a name="upgrade-sql-version"></a>Upgradovat verzi SQL

> [!WARNING]
> Upgrade verze SQL Server restartuje službu, aby SQL Server kromě jakýchkoli přidružených služeb, jako jsou například služby Analysis Services a R.

Pokud chcete upgradovat verzi SQL Server, Získejte pro pozdější verzi instalační médium SQL Server, které by [podporovalo cestu upgradu](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15) SQL Server, a proveďte následující kroky:

1. Před zahájením procesu zálohujte databáze, včetně systému (s výjimkou databáze tempdb) a uživatelských databází. Zálohování na úrovni virtuálního počítače konzistentní s aplikacemi můžete vytvořit také pomocí služby Azure Backup Services.
1. Spusťte Setup.exe z instalačního média SQL Server.
1. Průvodce instalací spustí Centrum instalace SQL Server. Chcete-li upgradovat existující instanci SQL Server, v navigačním podokně vyberte možnost **instalace** a pak vyberte možnost **upgradovat ze starší verze nástroje SQL Server**.

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="Výběr pro upgrade verze SQL Server":::

1. Na stránce **kód Product Key** vyberte možnost, která určuje, jestli upgradujete na bezplatnou edici SQL Server nebo že máte k dispozici klíč PID pro produkční verzi produktu. Další informace najdete v tématu [edice a podporované funkce SQL Server 2019 (15. x)](/sql/sql-server/editions-and-components-of-sql-server-version-155) a [podporované verze a upgrady edice (SQL Server 2016)](/sql/database-engine/install-windows/supported-version-and-edition-upgrades).
1. Vyberte **Další** , dokud se nedostanete do stránky **připraveno k upgradu** , a pak vyberte **upgradovat**. Až se změna projeví, může okno nastavení přestat reagovat na několik minut. **Kompletní** stránka potvrdí, že se upgrade dokončil. Podrobný postup pro upgrade najdete v tématu [úplný postup](/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup#procedure).

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="Stránka dokončení":::

Pokud jste kromě změny verze změnili edici SQL Server, aktualizujte také edici, a pokud chcete změnit instanci virtuálního počítače SQL, přečtěte si část **ověření verze a edice v portálu** .

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="Změnit metadata verze":::

## <a name="downgrade-the-version-of-sql-server"></a>Downgrade verze SQL Server

Chcete-li snížit verzi SQL Server, je nutné zcela odinstalovat SQL Server a znovu ji znovu nainstalovat pomocí požadované verze. To je podobné jako při nové instalaci SQL Server, protože nebudete moci obnovit předchozí databázi z novější verze na nově nainstalovanou předchozí verzi. Databáze bude nutné znovu vytvořit úplně od začátku. Pokud jste zároveň změnili edici SQL Server během upgradu, změňte vlastnost **edice** SQL Server virtuálního počítače v Azure Portal na hodnotu nové edice. Tím se aktualizují metadata a fakturace, které jsou přidružené k tomuto virtuálnímu počítači.

> [!WARNING]
> Místní downgrade SQL Server není podporována.

Verzi SQL Server můžete downgradovat pomocí následujících kroků:

1. Ujistěte se, že nepoužíváte žádnou funkci, která je [k dispozici pouze v novější verzi](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx).
1. Zálohujte všechny databáze včetně systému (s výjimkou databáze tempdb) a uživatelských databází.
1. Exportujte všechny nezbytné objekty na úrovni serveru (například triggery serveru, role, přihlášení, propojené servery, úlohy, přihlašovací údaje a certifikáty).
1. Pokud nemáte skripty pro opětovné vytvoření vašich uživatelských databází v předchozí verzi, je nutné vytvořit skript všech objektů a exportovat všechna data pomocí BCP.exe, SSIS nebo DACPAC.

   Ujistěte se, že jste vybrali správné možnosti při skriptování takových položek jako cílové verze, závislých objektů a pokročilých možností.

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="Možnosti skriptování":::

1. Kompletně odinstalujte SQL Server a všechny přidružené služby.
1. Restartujte virtuální počítač.
1. Nainstalujte SQL Server pomocí média pro požadovanou verzi programu.
1. Nainstalujte nejnovější aktualizace Service Pack a kumulativní aktualizace.
1. Importujte všechny nezbytné objekty na úrovni serveru (které byly exportovány v kroku 3).
1. Znovu vytvořte všechny potřebné uživatelské databáze od nuly (pomocí vytvořených skriptů nebo souborů z kroku 4).

## <a name="verify-the-version-and-edition-in-the-portal"></a>Ověření verze a edice na portálu

Po změně verze SQL Server Zaregistrujte virtuální počítač SQL Server pomocí [rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md) , abyste mohli použít Azure Portal k zobrazení verze SQL Server. Uvedené číslo verze by nyní mělo odrážet nově upgradovanou verzi a edici instalace SQL Server.

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="Ověřit verzi":::

> [!NOTE]
> Pokud jste už zaregistrovali rozšíření agenta SQL IaaS, zrušte [registraci z RP](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) a pak znovu [Zaregistrujte prostředek SQL VM](sql-agent-extension-manually-register-single-vm.md#register-with-extension) , aby zjistil správnou verzi a edici SQL Server, která je na virtuálním počítači nainstalovaná. Tím se aktualizují metadata a fakturační informace, které jsou přidruženy k tomuto virtuálnímu počítači.

## <a name="remarks"></a>Poznámky

- Doporučujeme, abyste zahájili zálohování a aktualizace statistiky/znovu sestavovat indexy/provedli kontrolu konzistence po dokončení upgradu. Můžete také zkontrolovat úroveň kompatibility jednotlivých databází a ujistit se, že odpovídají vaší požadované úrovni.
- Po aktualizaci SQL Server na virtuálním počítači se ujistěte, že vlastnost **edice** SQL Server v Azure Portal odpovídá číslu instalované edice pro účely fakturace.
- Možnost [změnit edici](change-sql-server-edition.md#change-edition-in-portal) je funkcí rozšíření agenta SQL IaaS. Nasazení bitové kopie Azure Marketplace pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s příponou. Zákazníci, kteří si SQL Server vlastní instalaci, ale budou muset ručně [zaregistrovat SQL Server virtuální počítač](sql-agent-extension-manually-register-single-vm.md).
- Pokud vyřadíte prostředek virtuálního počítače SQL Server, obnoví se nastavení pevně nakódované verze image.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Přehled SQL Server na virtuálním počítači s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](frequently-asked-questions-faq.md)
- [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](pricing-guidance.md)
- [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](doc-changes-updates-release-notes.md)