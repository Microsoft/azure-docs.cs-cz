---
title: Rozšířit místní vždy na skupiny dostupnosti do Azure | Microsoft Docs
description: Tento kurz používá prostředky, které jsou vytvořené pomocí modelu nasazení classic a popisuje, jak pomocí průvodce přidat repliky v serveru SQL Server Management Studio (SSMS) přidejte repliku vždy na skupiny dostupnosti v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: d3e56f1741a9cfd3f2d9f786c2ce22eb6a946ef2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29400473"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Rozšířit místní vždy na skupiny dostupnosti do Azure
Skupiny dostupnosti Always On zajištění vysoké dostupnosti pro skupiny databáze přidáním sekundární repliky. Povolit tyto repliky přebírání služeb při selhání databáze v případě selhání. Kromě toho může být používají k přesměrování zpracování úloh pro čtení nebo úlohy zálohování.

Microsoft Azure můžete rozšířit místní skupiny dostupnosti zřizování jeden nebo více virtuálních počítačů Azure se systémem SQL Server a potom je přidat jako repliky do skupin dostupnosti místně.

V tomto kurzu se předpokládá, že máte následující:

* Aktivní předplatné Azure. Můžete [zaregistrovat k bezplatné zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Existující vždy na skupinu dostupnosti místní. Další informace o skupinách dostupnosti najdete v tématu [skupin dostupnosti Always On](https://msdn.microsoft.com/library/hh510230.aspx).
* Připojení mezi místní sítí a virtuální sítě Azure. Další informace o vytváření této virtuální sítě najdete v tématu [vytvořit připojení Site-to-Site pomocí portálu Azure (klasický)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

## <a name="add-azure-replica-wizard"></a>Průvodce Azure repliky přidáním
V této části se dozvíte, jak používat **Průvodce přidáním Azure repliky** rozšířit řešení vždy na skupiny dostupnosti zahrnout Azure repliky.

> [!IMPORTANT]
> **Průvodce přidáním Azure repliky** podporuje pouze virtuální počítače vytvořené pomocí modelu nasazení Classic. Nové nasazení virtuálních počítačů by měl použít novější modelu Resource Manager. Pokud používáte virtuální počítače s Resource Managerem, je třeba ručně přidat sekundární replika Azure pomocí jazyka Transact-SQL commmands (není tady zobrazené). Tento průvodce nebudou fungovat ve scénáři Resource Manager.

1. Z v systému SQL Server Management Studio rozbalte **vždy na vysokou dostupnost** > **skupiny dostupnosti** > **[název skupiny dostupnosti]**.
2. Klikněte pravým tlačítkem na **replik dostupnosti**, pak klikněte na tlačítko **přidat repliky**.
3. Ve výchozím nastavení **přidat repliky dostupnosti skupiny průvodce** se zobrazí. Klikněte na **Další**.  Pokud jste vybrali **tuto stránku již příště nezobrazovat** možnost v dolní části stránky při předchozím spuštění tohoto průvodce, tato obrazovka se nezobrazí.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Bude nutné se připojit k všechny existující sekundární repliky. Kliknutím na **připojení...** vedle položky každou repliku nebo můžete kliknout na tlačítko **připojit všechny...** v dolní části obrazovky. Po ověření, klikněte na tlačítko **Další** přejdete na zobrazení další obrazovky.
5. Na **zadejte repliky** stránky, jsou uvedeny několik karet v horní části: **repliky**, **koncové body**, **zálohování Předvolby**, a **naslouchací proces**. Z **repliky** , klikněte na **přidat repliky Azure...** Spusťte Průvodce přidáním Azure repliky.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Vyberte certifikát pro správu Azure z místního úložiště certifikátů systému Windows, pokud jste nainstalovali jednu před. Vyberte nebo zadejte id předplatného Azure, pokud jste už použili před. Klikněte na tlačítko Stáhnout na stáhnout a nainstalovat certifikát pro správu Azure a stáhněte si seznam odběrů pomocí účtu Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Zaplníte každé pole na stránce s hodnotami, které se použijí k vytvoření Azure virtuálního počítače (VM), který bude hostitelem repliky.
   
   | Nastavení | Popis |
   | --- | --- |
   | **Bitové kopie** |Vyberte požadovanou kombinace operačního systému a systému SQL Server |
   | **Velikost virtuálního počítače** |Vyberte velikost virtuálního počítače, který nejlépe vyhovuje vašim obchodním potřebám |
   | **Název virtuálního počítače.** |Zadejte jedinečný název pro nový virtuální počítač. Název musí obsahovat 3 až 15 znaků, může obsahovat pouze písmena, číslice a pomlčky a musí začínat písmenem a končit písmenem nebo číslicí. |
   | **Uživatelské jméno virtuálních počítačů** |Zadejte uživatelské jméno, které bude účet správce ve virtuálním počítači |
   | **Heslo správce virtuálních počítačů** |Zadejte heslo pro nový účet |
   | **Potvrzení hesla** |Potvrďte heslo nového účtu |
   | **Virtual Network** |Zadejte virtuální síť Azure, nový virtuální počítač by měl použít. Další informace o virtuálních sítích najdete v tématu [Přehled virtuálních sítí](../../../virtual-network/virtual-networks-overview.md). |
   | **Podsíť virtuální sítě** |Zadejte podsíť virtuální sítě, které by měl používat nový virtuální počítač |
   | **Domain** |Potvrďte správnost předem vyplněná hodnota pro doménu |
   | **Uživatelské jméno domény** |Zadejte účet, který je ve skupině místních správců na uzlech místního clusteru |
   | **Heslo** |Zadejte heslo pro uživatelské jméno domény |
8. Klikněte na tlačítko **OK** se ověřit nastavení nasazení.
9. Zobrazí se další právní podmínky. Přečtěte si a klikněte na tlačítko **OK** Pokud souhlasíte s podmínkami.
10. **Zadejte repliky** znovu se zobrazí stránka. Ověřte nastavení pro nové Azure repliky na **repliky**, **koncové body**, a **zálohování Předvolby** karty. Upravte nastavení podle požadavků vaší firmy.  Další informace o parametrech obsažené v těchto karet najdete v tématu [určení stránky repliky (dostupnosti průvodce novou skupinou průvodce nebo přidat repliky)](https://msdn.microsoft.com/library/hh213088.aspx). Všimněte si, že naslouchací procesy nelze vytvořit pomocí kartě naslouchací proces pro skupiny dostupnosti, které obsahují Azure repliky. Kromě toho pokud již byl vytvořen naslouchací proces před spuštěním průvodce, obdržíte zprávu s upozorněním, že se nepodporuje v Azure. Podíváme se na tom, jak vytvořit naslouchací procesy v **vytvořit naslouchací proces skupiny dostupnosti** části.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Klikněte na **Další**.
12. Vyberte metodu synchronizace dat, kterou chcete použít na **vybrat počáteční synchronizaci dat** a klikněte na tlačítko **Další**. Pro většinu scénářů, vyberte **úplná synchronizace dat**. Další informace o metodách synchronizace dat najdete v tématu [vyberte počáteční synchronizace stránku dat (vždy na dostupnosti skupiny průvodci)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Prohlédněte si výsledky na **ověření** stránky. Opravte zbývající potíže a v případě potřeby znovu spusťte ověření. Klikněte na **Další**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Zkontrolujte nastavení na **Souhrn** stránky a pak klikněte na **Dokončit**.
15. Zahájí proces zřizování. Po úspěšném dokončení průvodce klikněte na tlačítko **Zavřít** Chcete-li ukončit průvodce.

> [!NOTE]
> Průvodce přidáním repliky Azure vytvoří soubor protokolu v Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Tento soubor protokolu můžete použít k vyřešení problémů nasazení se nezdařilo Azure repliky. Pokud průvodce selže, provádění všech akcí, budou se všechny předchozí operace vrátí zpátky, včetně odstraňování zřízeného virtuálního počítače.
> 
> 

## <a name="create-an-availability-group-listener"></a>Vytvořte naslouchací proces skupiny dostupnosti
Po vytvoření skupiny dostupnosti, měli byste vytvořit naslouchací proces pro klienty pro připojení k repliky. Naslouchací procesy směrují příchozí připojení k primární nebo sekundární repliku jen pro čtení. Další informace o naslouchací procesy najdete v tématu [nakonfigurovat modul pro naslouchání ILB pro skupiny dostupnosti Always On v Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Další postup
Kromě použití **Průvodce přidáním Azure repliky** rozšířit vaše vždy na skupiny dostupnosti do Azure, může také přesunete některé úlohy SQL serveru úplně do Azure. Abyste mohli začít, najdete v části [zřizování virtuálního počítače systému SQL Server na platformě Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Další témata související se systémem SQL Server ve virtuálních počítačích Azure, najdete v části [systému SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

