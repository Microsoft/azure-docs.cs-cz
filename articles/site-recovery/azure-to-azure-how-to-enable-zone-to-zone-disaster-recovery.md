---
title: Povolit zónu pro zotavení po havárii zóny pro Azure Virtual Machines
description: Tento článek popisuje, kdy a jak používat zónu k zotavení po havárii zóny pro virtuální počítače Azure.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 786d877328b1ab3d0f03a75604b7345dba14aa9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713403"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Povolení zotavení po havárii virtuálních počítačů Azure mezi zónami dostupnosti

Tento článek popisuje, jak replikovat, převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Azure z jedné zóny dostupnosti do druhé v rámci stejné oblasti Azure.

>[!NOTE]
>
>- Podpora zóny až po zotavení po havárii zóny je v současné době omezená na následující oblasti: jihovýchodní Asie, Japonsko – východ, Austrálie – východ, JIO Indie – západ, Velká Británie – jih, Západní Evropa, Severní Evropa, Střed USA, Východní USA, Východní USA 2 a Západní USA 2.  
>- Site Recovery nepřesouvá ani neukládají zákaznická data mimo oblast, ve které je nasazená, když zákazník používá k zotavení po havárii zóny. Zákazníci si můžou vybrat Recovery Services trezor z jiné oblasti, pokud si si je vyberou. Trezor Recovery Services obsahuje metadata, ale žádná skutečná zákaznická data.

Služba Site Recovery přispívá k strategii pro provozní kontinuitu a zotavení po havárii tím, že během plánovaných a neplánovaných výpadků udržuje podnikové aplikace v provozu. Tato možnost zotavení po havárii se doporučuje, aby vaše aplikace zůstaly v provozu a v případě regionálních výpadků.

Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každá zóna má jedno nebo více datových center. 

Pokud chcete přesunout virtuální počítače do zóny dostupnosti v jiné oblasti, [Přečtěte si tento článek](../resource-mover/move-region-availability-zone.md).

## <a name="using-availability-zones-for-disaster-recovery"></a>Použití Zóny dostupnosti pro zotavení po havárii 

Zóny dostupnosti se obvykle používají k nasazení virtuálních počítačů v konfiguraci s vysokou dostupností. Je možné, že jsou mezi sebou příliš blízko, aby sloužily jako řešení zotavení po havárii v případě přírodní havárie.

V některých scénářích se ale Zóny dostupnosti dá využít při zotavení po havárii:

- Mnoho zákazníků, kteří měli strategii zotavení po havárii Metro při hostování aplikací v místním prostředí, někdy vyhledává tuto strategii až po migraci aplikací do Azure. Tito zákazníci potvrzují skutečnost, že strategie zotavení po havárii Metro nemusí fungovat v případě rozsáhlé fyzické havárie a přijímá toto riziko. Pro takové zákazníky se dá zónu k zotavení po havárii zóny použít jako možnost zotavení po havárii.

- Mnoho dalších zákazníků má složitou síťovou infrastrukturu a nepřeje se ji znovu vytvořit v sekundární oblasti z důvodu přidružených nákladů a složitosti. Zotavení po havárii zóny do zóny snižuje složitost, protože využívá redundantní koncepty sítě v rámci Zóny dostupnosti významně jednodušších konfigurací. Tito zákazníci dávají přednost jednoduchosti a můžou Zóny dostupnosti použít taky k zotavení po havárii.

- V některých oblastech, které nemají spárované oblasti v rámci stejné právní pravomoci (například jihovýchodní Asie), může řešení pro zotavení po havárii zóny do zóny sloužit jako řešení zotavení po havárii, které pomáhá zajistit dodržování předpisů, protože vaše aplikace a data se nepřesunou mezi národní hranice. 

- Zóna až po zotavení po havárii zóny zahrnuje replikaci dat napříč kratšími vzdálenostmi v porovnání s Azure až Azure pro zotavení po havárii, a proto se může zobrazit nižší latence a následně nižší RPO.

I když se jedná o silné výhody, existuje možnost, že zóna pro zotavení po havárii zóny může v případě přírodní havárie v rámci oblasti mít za následek krátké požadavky na odolnost.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Síť pro zotavení po havárii zóny až po havárii

Jak je uvedeno výše, zóna pro zotavení po havárii zóny snižuje složitost, protože využívá redundantní koncepty sítě v rámci Zóny dostupnosti významně jednodušších konfigurací. Chování síťových součástí v zóně pro zotavení po havárii zóny je popsané níže: 

- Virtual Network: pro skutečné převzetí služeb při selhání můžete použít stejnou virtuální síť jako zdrojová síť. Pro testovací převzetí služeb při selhání použijte jinou virtuální síť ke zdrojové virtuální síti.

- Podsíť: převzetí služeb při selhání ve stejné podsíti je podporováno.

- Privátní IP adresa: Pokud používáte statické IP adresy, můžete použít stejné IP adresy v cílové zóně, pokud se rozhodnete je nakonfigurovat takovým způsobem.

- Akcelerované síťové služby: podobně jako v případě zotavení po havárii z Azure do Azure můžete povolit akcelerované síťové služby, pokud je skladová položka virtuálního počítače podporuje.

- Veřejná IP adresa: k cílovému virtuálnímu počítači můžete připojit dříve vytvořenou standardní veřejnou IP adresu ve stejné oblasti. Základní veřejné IP adresy nepodporují scénáře související s zónou dostupnosti.

- Load Balancer: standardní nástroj pro vyrovnávání zatížení je regionální prostředek, takže cílový virtuální počítač je možné připojit ke fondu back-endu stejného nástroje pro vyrovnávání zatížení. Není vyžadován nový nástroj pro vyrovnávání zatížení.

- Skupina zabezpečení sítě: ve zdrojovém virtuálním počítači můžete použít stejné skupiny zabezpečení sítě jako použité.

## <a name="pre-requisites"></a>Požadavky

Předtím, než nasadíte zónu do zóny zotavení po havárii pro vaše virtuální počítače, je důležité zajistit, aby byly na virtuálním počítači vzájemně ovladatelné další funkce s cílem zotavení po havárii zóny.

|Funkce  | Příkaz support  |
|---------|---------|
|Klasické virtuální počítače   |     Nepodporováno    |
|Virtuální počítače ARM    |    Podporováno    |
|Azure Disk Encryption V1 (duální průchod, s Azure Active Directory (Azure AD))     |     Podporováno   |
|Azure Disk Encryption v2 (Single Pass, bez Azure AD)    |    Podporováno    |
|Nespravované disky    |    Nepodporováno    |
|Spravované disky    |    Podporováno    |
|Klíče spravované zákazníkem    |    Podporováno    |
|Skupiny umístění bezkontaktní komunikace    |    Podporováno    |
|Interoperabilita zálohování    |    Podporuje se zálohování a obnovení na úrovni souborů. Zálohování a obnovení na úrovni disku a virtuálního počítače se nepodporuje.    |
|Připojit a přidat/odebrat    |    Disky je možné přidat po povolení replikace zóny do zóny. Odebrání disků po povolení replikace zóny do zóny se nepodporuje.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Nastavení Site Recovery zóny na zotavení po havárii zóny

### <a name="log-in"></a>Přihlášení

Přihlaste se k webu Azure Portal.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Povolení replikace pro virtuální počítač Azure v poli oblast

1. V nabídce Azure Portal vyberte virtuální počítače, nebo na libovolné stránce vyhledejte a vyberte virtuální počítače. Vyberte virtuální počítač, který chcete replikovat. Pro zónu až po zotavení po havárii zóny už tento virtuální počítač musí být v zóně dostupnosti.

2. V části Operace vyberte Zotavení po havárii.

3. Jak je znázorněno níže, na kartě základy vyberte možnost Ano pro zotavení po havárii mezi Zóny dostupnosti?

    ![Základní stránka nastavení](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Pokud souhlasíte se všemi výchozími hodnotami, klikněte na zkontrolovat a spustit replikaci a pak na spustit replikaci.

5. Pokud chcete provést změny nastavení replikace, klikněte na tlačítko Další: Upřesnit nastavení.

6. Kdykoli je to vhodné, změňte nastavení mimo výchozí nastavení. Pro uživatele z Azure do Azure zotavení po havárii se může tato stránka zdát dobře seznámit. Další podrobnosti o možnostech zobrazených v tomto [okně najdete tady](./azure-to-azure-tutorial-enable-replication.md) .

    ![Stránka Upřesnit nastavení](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Klikněte na tlačítko Další: zkontrolovat a spustit replikaci a pak na spustit replikaci.

## <a name="faqs"></a>Nejčastější dotazy

**1. jak má zóna k disdobu při zotavení po havárii zóny podle ceny?**
Ceny za zónu až po zotavení po havárii zóny jsou stejné jako ceny při zotavení po havárii z Azure do Azure. Další podrobnosti najdete [na stránce s](https://azure.microsoft.com/pricing/details/site-recovery/) cenami a [tady](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/). Všimněte si, že náklady na výstup, které byste viděli v zóně až po zotavení po havárii zóny, by byly nižší než oblast pro zotavení po havárii oblasti.

**2. co je smlouva SLA pro RTO a RPO?**
Smlouva SLA pro RTO je stejná jako u Site Recovery celkové. Proslibujte RTO po dobu až 2 hodin. Neexistuje žádná definovaná smlouva SLA pro RPO.

**3. má zaručenou kapacitu v sekundární zóně?**
Tým Site Recovery týmu a Azure Capacity Management plánuje dostatečnou kapacitu infrastruktury. Když spustíte převzetí služeb při selhání, týmy také pomůžou zajistit, aby se instance virtuálních počítačů, které jsou chráněné nástrojem Site Recovery, nasadily do cílové zóny.

**4. které operační systémy se podporují?**
Zotavení po havárii zóny do zóny podporuje stejné operační systémy jako Azure až Azure pro zotavení po havárii. Další informace najdete v [tématu](./azure-to-azure-support-matrix.md)věnovaném matici podpory.

**5. může se jednat o stejné zdrojové a cílové skupiny prostředků?**
Ne, je nutné převzít služby při selhání do jiné skupiny prostředků.

## <a name="next-steps"></a>Další kroky

Kroky, které je třeba dodržovat při spuštění postupu zotavení po havárii, převzetí služeb při selhání, opětovné ochrany a navrácení služeb po obnovení, jsou stejné jako kroky ve scénáři zotavení po havárii v Azure do Azure.

Pokud chcete provést postup zotavení po havárii, postupujte prosím podle kroků uvedených [tady](./azure-to-azure-tutorial-dr-drill.md).

Pokud chcete provést převzetí služeb při selhání a znovu nastavit ochranu virtuálních počítačů v sekundární zóně, postupujte podle kroků uvedených [tady](./azure-to-azure-tutorial-failover-failback.md).

Pokud chcete navrátit služby po obnovení primární zóně, postupujte podle kroků popsaných [tady](./azure-to-azure-tutorial-failback.md).
