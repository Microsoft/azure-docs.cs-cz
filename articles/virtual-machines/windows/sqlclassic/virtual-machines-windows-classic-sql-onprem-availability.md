---
title: Rozšíření místních skupin dostupnosti always on do Azure | Dokumenty společnosti Microsoft
description: Tento kurz používá prostředky vytvořené pomocí klasického modelu nasazení a popisuje, jak pomocí průvodce Přidat repliku v SQL Server Management Studio (SSMS) přidat vždy na dostupnost skupiny repliky v Azure.
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
ms.openlocfilehash: 4521c2c112c93e83144cfc84d600208817b2ccac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978048"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Rozšíření místních skupin dostupnosti Always On do Azure
Vždy na dostupnost skupiny poskytují vysokou dostupnost pro skupiny databáze přidáním sekundární repliky. Tyto repliky umožňují převzetí služby při selhání databází v případě selhání. Kromě toho je lze použít k přetácení úloh čtení nebo úloh zálohování.

Místní skupiny dostupnosti můžete rozšířit do Microsoft Azure zřízením jednoho nebo více virtuálních počítačích Azure pomocí SQL Serveru a jejich přidáním jako repliky do místních skupin dostupnosti.

Tento kurz předpokládá, že máte následující:

* Aktivní předplatné Azure. Můžete [se zaregistrovat k bezplatné zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Existující skupina dostupnosti vždy v místním prostředí. Další informace o skupinách dostupnosti naleznete v [tématu Vždy na skupinách dostupnosti](https://msdn.microsoft.com/library/hh510230.aspx).
* Připojení mezi místní sítí a virtuální sítí Azure. Další informace o vytváření této virtuální sítě najdete [v tématu Vytvoření připojení site-to-site pomocí portálu Azure (klasické)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

## <a name="add-azure-replica-wizard"></a>Průvodce přidáním repliky Azure
Tato část ukazuje, jak pomocí **Průvodce přidáním repliky Azure** rozšířit vaše vždy na dostupnost skupiny řešení zahrnout repliky Azure.

> [!IMPORTANT]
> **Průvodce přidáním repliky Azure** podporuje pouze virtuální počítače vytvořené pomocí klasického modelu nasazení. Nová nasazení virtuálních počítače by měla používat novější model Správce prostředků. Pokud používáte virtuální počítače se Správcem prostředků, musíte ručně přidat sekundární repliku Azure pomocí příkazů Transact-SQL (není zobrazeno zde). Tento průvodce nebude fungovat ve scénáři Správce prostředků.

1. V rámci aplikace SQL Server Management Studio rozbalte **položku Vždy na skupinách** > **Availability Groups** > dostupnosti s vysokou dostupností **[Název skupiny dostupnosti]**.
2. Klepněte pravým tlačítkem myši na **položku Repliky dostupnosti**a potom klepněte na příkaz **Přidat repliku**.
3. Ve výchozím nastavení se zobrazí **Průvodce přidáním repliky do skupiny dostupnosti.** Klikněte na **Další**.  Pokud jste vybrali možnost **Nezobrazovat tuto stránku znovu** v dolní části stránky během předchozího spuštění tohoto průvodce, tato obrazovka se nezobrazí.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Budete muset připojit ke všem existujícím sekundárním replikám. Můžete kliknout na **Připojit...** vedle každé repliky nebo můžete kliknout **na připojit vše...** v dolní části obrazovky. Po ověření klikněte na **Další** a přesuňte na další obrazovku.
5. Na stránce **Zadat repliky** je v horní části uvedeno více karet: **repliky**, **koncové body**, **předvolby zálohování**a **naslouchací proces**. Na kartě **Repliky** klikněte na **Přidat repliku Azure...** spusťte Průvodce přidáním repliky Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Vyberte existující certifikát azure managementu z místního úložiště certifikátů Windows, pokud jste ho již dříve nainstalovali. Vyberte nebo zadejte id předplatného Azure, pokud jste ho už dříve používali. Kliknutím na Stáhnout si můžete stáhnout a nainstalovat certifikát Azure Management a stáhnout seznam předplatných pomocí účtu Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Každé pole na stránce naplníte hodnotami, které se použijí k vytvoření virtuálního počítače Azure (VM), který bude hostitelem repliky.
   
   | Nastavení | Popis |
   | --- | --- |
   | **Obrázek** |Vyberte požadovanou kombinaci operačního systému a serveru SQL Server |
   | **Velikost virtuálního počítače** |Vyberte velikost virtuálního počítače, která nejlépe vyhovuje vašim obchodním potřebám |
   | **Název virtuálního_km** |Zadejte jedinečný název nového virtuálního soudu. Název musí obsahovat 3 až 15 znaků, může obsahovat pouze písmena, číslice a pomlčky a musí začínat písmenem a končit písmenem nebo číslem. |
   | **Uživatelské jméno virtuálního uživatele** |Zadejte uživatelské jméno, které se stane účtem správce na virtuálním počítači |
   | **Heslo správce virtuálního počítače** |Zadání hesla pro nový účet |
   | **Potvrdit heslo** |Potvrzení hesla nového účtu |
   | **Virtuální síť** |Zadejte virtuální síť Azure, kterou by měl nový virtuální počítač použít. Další informace o virtuálních sítích naleznete v [tématu Přehled virtuálních sítí](../../../virtual-network/virtual-networks-overview.md). |
   | **Podsíť virtuální sítě** |Určení podsítě virtuální sítě, kterou by měl nový virtuální počítač používat |
   | **Domény** |Potvrzení správné předvyplněné hodnoty domény |
   | **Uživatelské jméno domény** |Zadejte účet, který je ve skupině Místní správci v uzlech místního clusteru |
   | **Heslo** |Zadání hesla pro uživatelské jméno domény |
8. Klepnutím na **tlačítko OK** ověřte nastavení nasazení.
9. Dále jsou zobrazeny právní podmínky. Přečtěte si a klikněte na **OK,** pokud souhlasíte s těmito podmínkami.
10. Stránka **Zadat repliky** se znovu zobrazí. Ověřte nastavení nové repliky Azure na kartách **Repliky**, **Koncové body**a **Předvolby zálohování.** Upravte nastavení tak, aby vyhovovalo vašim obchodním požadavkům.  Další informace o parametrech obsažených na těchto kartách naleznete v [tématu Určení stránky replik (Průvodce skupinou dostupnosti/Průvodce přidáním repliky).](https://msdn.microsoft.com/library/hh213088.aspx) Všimněte si, že naslouchací procesy nelze vytvořit pomocí naslouchací proces kartu pro skupiny dostupnosti, které obsahují repliky Azure. Kromě toho pokud naslouchací proces již byla vytvořena před spuštěním průvodce, zobrazí se zpráva oznamující, že není podporovánv Azure. Podíváme se na to, jak vytvořit posluchače v části **Vytvořit skupinu dostupnosti.**
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Klikněte na **Další**.
12. Vyberte metodu synchronizace dat, kterou chcete použít na stránce **Vybrat počáteční synchronizaci dat,** a klepněte na tlačítko **Další**. U většiny scénářů vyberte **možnost Úplná synchronizace dat**. Další informace o metodách synchronizace dat naleznete v [tématu Výběr stránky počáteční synchronizace dat (Průvodci skupinou dostupnosti vždy zapnuto).](https://msdn.microsoft.com/library/hh231021.aspx)
13. Prohlédněte si výsledky na stránce **Ověření.** Opravte nevyřešené problémy a v případě potřeby znovu spusťte ověření. Klikněte na **Další**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Zkontrolujte nastavení na stránce **Souhrn** a klepněte na tlačítko **Dokončit**.
15. Proces zřizování začíná. Po úspěšném dokončení průvodce klepnutím na tlačítko **Zavřít** ukončete průvodce.

> [!NOTE]
> Průvodce přidáním repliky Azure vytvoří soubor protokolu v poli Uživatelé\Uživatelské jméno\AppData\Local\SQL Server\AddReplicaWizard. Tento soubor protokolu lze použít k řešení potíží s neúspěšnými nasazeními replik Azure. Pokud průvodce selže při provádění jakékoli akce, všechny předchozí operace jsou vráceny zpět, včetně odstranění zřízeného virtuálního soudu.
> 
> 

## <a name="create-an-availability-group-listener"></a>Vytvoření naslouchací proces skupiny dostupnosti
Po vytvoření skupiny dostupnosti byste měli vytvořit naslouchací proces pro klienty pro připojení k replikám. Naslouchací procesy přímé příchozí připojení k primární nebo jen pro čtení sekundární repliky. Další informace o naslouchacích procesech najdete [v tématu Konfigurace naslouchací proces ILB pro skupiny dostupnosti always on v Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Další kroky
Kromě použití **Průvodce přidáním repliky Azure** k rozšíření skupiny dostupnosti always on do Azure můžete také zcela přesunout některé úlohy SQL Serveru do Azure. Další informace najdete [v tématu Zřizování virtuálního počítače SQL Server v Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Další témata související se spuštěním SQL Serveru ve virtuálních počítačích Azure najdete v článku [SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

