---
title: Rozšíření místních skupin dostupnosti Always On do Azure | Dokumentace Microsoftu
description: Tento kurz používá prostředky vytvořené pomocí modelu nasazení classic a popisuje, jak pomocí průvodce přidat repliky v SQL Server Management Studio (SSMS) přidání repliky skupiny dostupnosti Always On v Azure.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61481579"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Rozšíření místních skupin dostupnosti Always On do Azure
Skupiny dostupnosti AlwaysOn pro zajištění vysoké dostupnosti pro skupiny databáze tak, že přidáte sekundárních replik. Povolit tyto repliky databáze v případě selhání selhání. Kromě toho jsou slouží k přesměrování zpracování úlohami pro čtení nebo úlohy zálohování.

Skupiny dostupnosti v místním do Microsoft Azure můžete rozšířit zřizování jeden nebo více virtuálních počítačů Azure s SQL serverem a jejich přidání jako repliky skupiny dostupnosti v místním.

Tento kurz předpokládá, že máte následující:

* Aktivní předplatné Azure. Je možné [zaregistrujte si bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Existující skupiny dostupnosti Always On na místní. Další informace o skupinách dostupnosti najdete v tématu [skupin dostupnosti Always On](https://msdn.microsoft.com/library/hh510230.aspx).
* Připojení mezi místní sítí a virtuální sítí Azure. Další informace o vytváření této virtuální síti, najdete v části [vytvoření připojení typu Site-to-Site pomocí webu Azure portal (classic)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

## <a name="add-azure-replica-wizard"></a>Průvodce přidáním repliky Azure
V této části se dozvíte, jak používat **Průvodce přidáním repliky Azure** rozšířit vaše řešení skupiny dostupnosti Always On zahrnout Azure repliky.

> [!IMPORTANT]
> **Průvodce přidáním repliky Azure** podporuje pouze virtuální počítače vytvořené pomocí modelu nasazení Classic. Nové nasazení virtuálního počítače by měla používat novější modelu Resource Manager. Pokud používáte virtuální počítače s využitím Resource Manageru, je třeba ručně přidat sekundární repliky Azure pomocí jazyka Transact-SQL commmands (není tady zobrazené). Tento průvodce nebudou fungovat ve scénáři Resource Manageru.

1. Z v rámci SQL Server Management Studio rozbalte **vysoká dostupnost AlwaysOn** > **skupin dostupnosti** >  **[název vaší skupiny dostupnosti]** .
2. Klikněte pravým tlačítkem na **replik dostupnosti**, pak klikněte na tlačítko **přidat repliky**.
3. Ve výchozím nastavení **přidat repliky Průvodce vytvořením skupiny dostupnosti** se zobrazí. Klikněte na **Další**.  Pokud jste vybrali **tuto stránku již příště nezobrazovat** možnost v dolní části stránky při předchozím spuštění tohoto průvodce se tato obrazovka se nezobrazí.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Budete muset připojit na všechny stávající sekundární repliky. Můžete kliknout na **připojit...** vedle každé repliky nebo je můžete kliknout na tlačítko **připojit všechny...** v dolní části obrazovky. Po ověření klikněte na tlačítko **Další** k přechodu na další obrazovce.
5. Na **zadejte repliky** stránce několik karet jsou uvedeny v horní části: **Repliky**, **koncové body**, **předvolby zálohování**, a **naslouchací proces**. Z **repliky** klikněte na tlačítko **přidat repliky Azure...** Chcete-li spustit Průvodce přidáním repliky Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Pokud jste nainstalovali, než bude moct, vyberte certifikát pro správu Azure z místního úložiště certifikátů Windows. Vyberte nebo zadejte id předplatného Azure, pokud jste už použili, než bude moct. Klikněte na možnost stažení, a stáhněte si a nainstalujte certifikát pro správu Azure a stáhněte si seznam předplatných, pomocí účtu Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Každé pole na stránce s hodnotami, které se použijí k vytvoření Azure virtuální počítač (VM), který bude hostovat repliku naplníte.
   
   | Nastavení | Popis |
   | --- | --- |
   | **Image** |Vyberte požadovanou kombinaci operačního systému a serveru SQL Server |
   | **Velikost virtuálního počítače** |Vyberte velikost virtuálního počítače, který nejlépe vyhovuje vašim obchodním potřebám |
   | **Název virtuálního počítače** |Zadejte jedinečný název pro nový virtuální počítač. Název musí obsahovat 3 až 15 znaků, může obsahovat jenom písmena, číslice a spojovníky, musí začínat písmenem a končit písmenem nebo číslicí. |
   | **Uživatelské jméno virtuálního počítače** |Zadejte uživatelské jméno, které se stanou účet správce na virtuálním počítači |
   | **Heslo správce virtuálního počítače** |Zadejte heslo pro nový účet |
   | **Potvrzení hesla** |Potvrďte heslo pro nový účet |
   | **Virtual Network** |Zadejte virtuální síť Azure, který má používat nový virtuální počítač. Další informace o virtuálních sítích najdete v tématu [Přehled služby Virtual Network](../../../virtual-network/virtual-networks-overview.md). |
   | **Virtual Network Subnet** |Zadejte podsíť virtuální sítě, který má používat nový virtuální počítač |
   | **Domény** |Potvrďte správnost předem vyplněné hodnoty pro doménu |
   | **Doména uživatelské jméno** |Zadejte účet, který je ve skupině místních správců v uzlech místního clusteru |
   | **Heslo** |Zadejte heslo pro uživatelské jméno domény |
8. Klikněte na tlačítko **OK** chcete ověřit nastavení nasazení.
9. Zobrazí se další právní podmínky. Čtení a klikněte na tlačítko **OK** Pokud souhlasíte s těmito podmínkami.
10. **Zadejte repliky** stránky se zobrazí znovu. Ověřte nastavení pro nový Azure repliky na **repliky**, **koncové body**, a **předvolby zálohování** karty. Upravte nastavení podle požadavků vaší firmy.  Další informace o parametrech obsažená na těchto kartách najdete v tématu [zadat stránku repliky (nový průvodce nebo přidání repliky Průvodce vytvořením skupiny dostupnosti)](https://msdn.microsoft.com/library/hh213088.aspx). Poznámka: Nelze vytvořit naslouchacích procesů pomocí karty naslouchacího procesu skupiny dostupnosti, které obsahují Azure repliky. Kromě toho pokud naslouchací proces již byl vytvořen před spuštěním průvodce, obdržíte zprávu s oznámením, že se nepodporuje v Azure. Podíváme se na to, jak pro vytvoření naslouchacích procesů v **vytvořit naslouchací proces skupiny dostupnosti** oddílu.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Klikněte na **Další**.
12. Vyberte metodu synchronizace dat, kterou chcete použít na **vybrat počáteční synchronizaci dat** stránky a klikněte na tlačítko **Další**. Pro většinu scénářů, vyberte **úplnou synchronizaci dat**. Další informace o metodách synchronizace dat najdete v tématu [vyberte počáteční synchronizace stránka dat (vždy na dostupnost skupiny průvodci)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Zkontrolujte výsledky na **ověření** stránky. Opravte nevyřízených problémů a v případě potřeby znovu spusťte ověření. Klikněte na **Další**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Zkontrolujte nastavení na **Souhrn** stránce a potom klikněte na **Dokončit**.
15. Zahájí proces zřizování. Po úspěšném dokončení průvodce klikněte na tlačítko **Zavřít** Chcete-li ukončit průvodce.

> [!NOTE]
> Průvodce přidáním repliky Azure vytvoří soubor protokolu v Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Tento protokolový soubor lze použít k vyřešení problémů nasazení se nezdařilo Azure repliky. Pokud průvodce selže, provádění jakékoli akce, budou se všechny předchozí operace vrátit zpět, včetně odstranění zřízených virtuálních počítačů.
> 
> 

## <a name="create-an-availability-group-listener"></a>Vytvořte naslouchací proces skupiny dostupnosti
Po vytvoření skupiny dostupnosti, měli byste vytvořit naslouchací proces pro připojení klientů k replik. Posluchači přímý příchozí připojení k primární nebo sekundární repliky jen pro čtení. Další informace o naslouchacích procesů, naleznete v tématu [konfigurace naslouchacího procesu ILB pro skupiny dostupnosti Always On v Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Další postup
Kromě použití **Průvodce přidáním repliky Azure** pro rozšíření vaší skupiny dostupnosti Always On do Azure, může také přesunutí některých úloh SQL serveru zcela do Azure. Abyste mohli začít, najdete v článku [zřízení virtuálního počítače SQL serveru v Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Další témata související s SQL serverem na virtuálních počítačích Azure, najdete v části [systému SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

