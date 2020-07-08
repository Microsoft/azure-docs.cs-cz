---
title: Rozšiřování místních skupin dostupnosti Always On do Azure | Microsoft Docs
description: Tento kurz používá prostředky vytvořené pomocí modelu nasazení Classic a popisuje, jak pomocí Průvodce přidáním repliky v SQL Server Management Studio (SSMS) přidat repliku skupiny dostupnosti Always On v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 928162282ca973811315728a07b4fbe40100324e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84017575"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Rozšíření místních skupin dostupnosti Always On do Azure
Skupiny dostupnosti Always On poskytují vysokou dostupnost pro skupiny databáze přidáním sekundárních replik. Tyto repliky umožňují při selhání převzít služby při selhání databáze. Kromě toho je lze použít k přesměrování zatížení a úloh pro zálohování.

Místní skupiny dostupnosti můžete Microsoft Azure tím, že zřizujete jeden nebo více virtuálních počítačů Azure s SQL Server a pak je přidáte jako repliky do místních skupin dostupnosti.

V tomto kurzu se předpokládá, že máte následující:

* Aktivní předplatné Azure. Můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Existující skupina dostupnosti Always On v místním prostředí. Další informace o skupinách dostupnosti najdete v tématu [skupiny dostupnosti Always On](https://msdn.microsoft.com/library/hh510230.aspx).
* Připojení mezi místní sítí a virtuální sítí Azure. Další informace o vytváření této virtuální sítě najdete v tématu [vytvoření připojení typu Site-to-site pomocí Azure Portal (Classic)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

## <a name="add-azure-replica-wizard"></a>Průvodce přidáním repliky Azure
V této části se dozvíte, jak pomocí **Průvodce přidáním repliky Azure** zvětšit vaše řešení skupiny dostupnosti Always On, aby zahrnovalo repliky Azure.

> [!IMPORTANT]
> **Průvodce přidáním repliky Azure** podporuje jenom virtuální počítače vytvořené pomocí modelu nasazení Classic. Nová nasazení virtuálních počítačů by měla používat novější model Správce prostředků. Pokud používáte virtuální počítače s Správce prostředků, je nutné ručně přidat sekundární repliku Azure pomocí příkazů jazyka Transact-SQL (zde nejsou uvedeny). Tento průvodce nebude ve scénáři Správce prostředků fungovat.

1. V rámci SQL Server Management Studio rozbalte položku skupiny dostupnosti **Always On s vysokou dostupností**  >  **Availability Groups**  >  **[název vaší skupiny dostupnosti]**.
2. Klikněte pravým tlačítkem na **repliky dostupnosti**a pak klikněte na **Přidat repliku**.
3. Ve výchozím nastavení se zobrazí **Průvodce přidáním repliky do skupiny dostupnosti** . Klikněte na **Další**.  Pokud jste v dolní části stránky během předchozího spuštění tohoto průvodce vybrali možnost tuto **stránku již příště nezobrazovat** , tato obrazovka se nezobrazí.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Budete se muset připojit ke všem existujícím sekundárním replikám. Můžete kliknout na **připojit...** vedle každé repliky můžete kliknout na **Připojit vše...** v dolní části obrazovky. Po ověření kliknutím na tlačítko **Další** přejděte na další obrazovku.
5. Na stránce **zadat repliky** jsou v horní části uvedeny více karet: **repliky**, **koncové body**, **Předvolby zálohování**a **naslouchací proces**. Na kartě **repliky** klikněte na **Přidat repliku Azure...** pro spuštění Průvodce přidáním repliky Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Vyberte existující certifikát pro správu Azure z místního úložiště certifikátů Windows, pokud jste ho nainstalovali dřív. Vyberte nebo zadejte ID předplatného Azure, pokud jste ho použili dřív. Kliknutím na stáhnout si můžete stáhnout a nainstalovat certifikát pro správu Azure a stáhnout seznam předplatných pomocí účtu Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Každé pole na stránce se naplní hodnotami, které se použijí k vytvoření virtuálního počítače Azure, který bude hostovat repliku.
   
   | Nastavení | Description |
   | --- | --- |
   | **Image** |Vyberte požadovanou kombinaci OS a SQL Server |
   | **Velikost virtuálního počítače** |Vyberte velikost virtuálního počítače, který nejlépe vyhovuje vašim obchodním potřebám. |
   | **Název virtuálního počítače** |Zadejte jedinečný název nového virtuálního počítače. Název musí obsahovat 3 až 15 znaků, může obsahovat jenom písmena, číslice a spojovníky a musí začínat písmenem a končit písmenem nebo číslicí. |
   | **Uživatelské jméno virtuálního počítače** |Zadejte uživatelské jméno, které se stane účtem správce ve virtuálním počítači. |
   | **Heslo správce virtuálního počítače** |Zadejte heslo pro nový účet. |
   | **Potvrzení hesla** |Potvrďte heslo k novému účtu. |
   | **Virtual Network** |Zadejte virtuální síť Azure, kterou má nový virtuální počítač použít. Další informace o virtuálních sítích najdete v tématu [přehled Virtual Network](../../../virtual-network/virtual-networks-overview.md). |
   | **Virtual Network podsíť** |Zadejte podsíť virtuální sítě, kterou má nový virtuální počítač použít. |
   | **Domain (Doména)** |Potvrďte, že předem vyplněná hodnota pro doménu je správná. |
   | **Uživatelské jméno domény** |Zadejte účet, který je v místní skupině Administrators na místních uzlech clusteru. |
   | **Heslo** |Zadejte heslo pro uživatelské jméno domény. |
8. Kliknutím na **OK** ověřte nastavení nasazení.
9. Zobrazí se další právní výrazy. Pokud souhlasíte s těmito podmínkami, přečtěte si a klikněte na **OK** .
10. Znovu se zobrazí stránka **zadat repliky** . Ověřte nastavení pro novou repliku Azure na kartách **repliky**, **koncové body**a **Předvolby zálohování** . Upravte nastavení tak, aby splňovalo vaše obchodní požadavky.  Další informace o parametrech obsažených na těchto kartách najdete v tématu věnovaném [nastavení stránky repliky (Průvodce novou skupinou dostupnosti/průvodce přidáním repliky)](https://msdn.microsoft.com/library/hh213088.aspx). Naslouchací procesy nelze vytvořit pomocí karty naslouchacího procesu pro skupiny dostupnosti, které obsahují repliky Azure. Kromě toho, pokud již byl naslouchací proces vytvořen před spuštěním průvodce, obdržíte zprávu s oznámením, že není podporována v Azure. Podíváme se na to, jak vytvořit naslouchací procesy v části **Vytvoření naslouchacího procesu skupiny dostupnosti** .
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Klikněte na **Další**.
12. Na stránce **Vyberte počáteční synchronizaci dat** vyberte metodu synchronizace dat, kterou chcete použít, a klikněte na **Další**. U většiny scénářů vyberte **Úplná synchronizace dat**. Další informace o metodách synchronizace dat najdete na [stránce Výběr počáteční synchronizace dat (Průvodce skupinami dostupnosti Always On)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Zkontrolujte výsledky na stránce **ověřování** . Opravte nevyřízené problémy a v případě potřeby znovu spusťte ověřování. Klikněte na **Další**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Zkontrolujte nastavení na stránce **Souhrn** a pak klikněte na **Dokončit**.
15. Proces zřizování se zahájí. Po úspěšném dokončení průvodce kliknutím na **Zavřít** ukončete průvodce.

> [!NOTE]
> Průvodce přidáním repliky Azure vytvoří soubor protokolu v Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Tento soubor protokolu se dá použít k odstraňování potíží s neúspěšnými nasazeními repliky Azure. Pokud průvodce neprovede žádnou akci, vrátí se všechny předchozí operace zpátky, včetně odstranění zřízeného virtuálního počítače.
> 
> 

## <a name="create-an-availability-group-listener"></a>Vytvoření naslouchacího procesu skupiny dostupnosti
Po vytvoření skupiny dostupnosti byste měli vytvořit naslouchací proces pro klienty pro připojení k replikám. Naslouchací procesy směrují příchozí připojení buď k primární replice, nebo k sekundární replice jen pro čtení. Další informace o posluchačích najdete v tématu [Konfigurace naslouchacího procesu interního nástroje pro skupiny dostupnosti Always On v Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Další kroky
Kromě používání **Průvodce přidáním repliky Azure** k rozšíření skupiny dostupnosti Always On do Azure můžete také některé úlohy SQL Server do Azure přesunout úplně. Informace o tom, jak začít, najdete v tématu [zřízení virtuálního počítače s SQL Server v Azure](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

Další témata související se spouštěním SQL Server ve virtuálních počítačích Azure najdete v tématu [SQL Server v Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

