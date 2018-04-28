---
title: Migrace místní úlohy do Azure s Azure DMS a Site Recovery | Microsoft Docs
description: Zjistěte, jak připravit Azure pro migraci místního počítače pomocí služby Azure Service migrace databáze a služba Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: d6fc1af3c5a587ab62b86dcd4189165d96e6e3bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="scenario-2-lift-and-shift-migration-to-azure"></a>Scénář 2: Migrace navýšení a shift do Azure

Společnost Contoso zvažují migrace do Azure. Pokud chcete vyzkoušet tento limit, chtějí vyhodnocování a migrace malé místní cesta aplikace do Azure. Je cesta dvouvrstvé aplikace, pomocí webové aplikace na jeden virtuální počítač a databázi SQL serveru na druhý virtuální počítač spuštěna. Aplikace je nasazená ve VMware a správu prostředí zajišťuje vCenter Server. 

V [scénář 1: posouzení migrace do Azure](migrate-scenarios-assessment.md), používají Data migrace Assistant (DMA) pro vyhodnocení databázi systému SQL Server pro aplikaci. Kromě toho používají službu Azure migraci k vyhodnocení aplikace virtuálních počítačů. Nyní v tomto scénáři po úspěšném dokončení hodnocení, chtějí migrovat databázi na instanci spravované SQL Azure pomocí Azure databáze migrace služby (DMS) a místní počítače k virtuálním počítačům Azure pomocí služby Azure Site Recovery.

Pokud chcete vyzkoušet [scénáři 1](migrate-scenarios-assessment.md), a pak tento scénář použití této aplikace ilustrativní cesta, můžete ji stáhnout z [githubu](https://github.com/Microsoft/SmartHotel360).



**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Služba správy databáze](https://docs.microsoft.com/azure/dms/dms-overview) | Služba DMS umožňuje bezproblémové migrace z více zdrojů databáze do platformy dat Azure s minimálními výpadky. | Tato služba je aktuálně ve verzi public preview (duben 2018) a můžete použít bezplatné zdarma ve verzi Preview.<br/><br/> Všimněte si, že pro verzi public preview je omezená na DMS [počet oblastí](https://docs.microsoft.com/azure/dms/dms-overview).
[Instance spravované SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Spravované Instance je služba spravované databáze, která představuje plně spravované Instance systému SQL Server v cloudu Azure. Ji sdílí stejný kód s nejnovější verzi databázového stroje SQL Server a má nejnovější funkce, vylepšení výkonu a opravy zabezpečení. | Spuštění v Azure pomocí Azure SQL Database spravované instancí platit poplatky stanovené podle kapacity. [Další informace](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Služba orchestruje a spravuje migrace a zotavení po havárii pro virtuální počítače Azure a místní virtuální počítače a fyzické servery.  | Při replikaci do Azure se vám neúčtují poplatky za úložiště Azure.  Virtuální počítače Azure se vytvářejí a platit poplatky, když dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) o poplatky a ceny.

V tomto scénáři nastavíme site-to-site VPN, aby mohli připojit k místní databázi, vytvoření spravované Instance Azure SQL v Azure a migraci databáze DMS. Site Recovery jsme budete Příprava místního prostředí VMware, nastavení replikace a migrace virtuálních počítačů do Azure.  


> [!IMPORTANT]
> Budete muset být zaregistrovaná v SQL spravované Instance omezené verzi Public Preview. Potřebujete předplatné Azure za účelem [zaregistrovat](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Registrace může trvat několik dní na provedení, ujistěte se, že je to provést před zahájením nasazení tohoto scénáře.

## <a name="architecture"></a>Architektura

### <a name="site-recovery"></a>Site Recovery

![Site Recovery](media/migrate-scenarios-lift-and-shift/asr-architecture.png)  

### <a name="dms"></a>SLUŽBA DMS
![SLUŽBA DMS](media/migrate-scenarios-lift-and-shift/dms-architecture.png)  

V tomto scénáři:

- Contoso je fictious název představující typické podnikové organizace. Contoso chce vyhodnocování a migrovat své aplikace dvouvrstvá místní cesta.
- Contoso má místní datacenter (contoso-datacenter), s řadič domény místní (**contosodc1**).
- Interní cesta vrstvené mezi dva virtuální počítače, WEBVM a SQLVM a aplikace umístěný na hostiteli VMware ESXi (**contosohost1.contoso.com**).
- Správu prostředí VMware zajišťuje vCenter Server (**vcenter.contoso.com**) spuštěný na virtuálním počítači.

## <a name="prerequisites"></a>Požadavky

Pokud chcete spustit tento scénář, zde je co byste měli mít.

Požadavky | Podrobnosti
--- | ---
**Předplatné Azure** | Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud používáte stávající předplatné a nejste správce, budete muset ve spolupráci s správcem můžete přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Obnovení lokality (místní)** | Server vCenter místní verzí 5.5, 6.0 nebo 6.5<br/><br/> Hostiteli ESXi verzí 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware na hostiteli ESXi spuštěna.<br/><br/> Virtuální počítače, musí splňovat [požadavky pro Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Podporované [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfigurace.
**SLUŽBA DMS** | Služba DMS musíte [kompatibilní místní zařízení VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Musí umět nakonfigurovat místního zařízení VPN. Musí mít zvenčí veřejnou IPv4 adresu a adresu nesmí být umístěné za zařízením NAT.<br/><br/> Ujistěte se, že máte přístup k místní databázi systému SQL Server.<br/><br/> Brána Windows Firewall byste měli mít přístup k zdrojového databázového stroje. [Další informace](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Pokud je brána firewall před počítači pro databáze, přidejte pravidla povolit přístup k databázi a soubory přes SMB port 445.<br/><br/> Pověření použitá k připojení k systému SQL Server zdrojové a cílové spravované Instance musí být členy role serveru sysadmin.<br/><br/> Budete potřebovat síť sdílet v místní databázi, DMS můžete použít k zálohování zdrojové databáze.<br/><br/> Ujistěte se, zda má účet služby spuštění instance systému SQL Server zdrojové oprávnění k zápisu ve sdílené síťové složce.<br/><br/> Poznamenejte si uživatel systému Windows (a heslo), který má oprávnění Úplné řízení na sdílené síťové složce. Služba Azure databáze migrace zosobňuje tyto přihlašovací údaje uživatele pro nahrání záložní soubory do kontejneru úložiště Azure.<br/><br/> Proces instalace systému SQL Server Express nastaví protokolu TCP/IP na **zakázané** ve výchozím nastavení. Ujistěte se, že je povolena.


# <a name="scenario-overview"></a>Přehled scénáře

Tady je přehled toho, co uděláme:

> [!div class="checklist"]
> * **Krok 1: Nastavení připojení site-to-site VPN**: DMS připojí k místní databázi prostřednictvím připojení site-to-site VPN. Nemůžeme vytvořit virtuální síť pro připojení k síti VPN a novější, můžeme opakovaně používat tuto síť Site Recovery. Síť, kterou vytvoříte, se musí nacházet v oblasti, ve kterém vytvoříte trezor služeb zotavení.
> * **Krok 2: Nastavení spravované Instance SQL Azure**: budete potřebovat předem vytvořené spravované instance, do kterého bude migrovat místní databázi systému SQL Server.
> * **Krok 3: Nastavení SA URI DMS**: sdílený přístupový podpis (SAS) identifikátor URI (Uniform Resource) poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště, takže můžete udělit omezená oprávnění k objektům úložiště. Nastavte identifikátor URI SAS, aby DMS přístup kontejneru účtu úložiště, do které služba odesílá soubory zálohování systému SQL Server.
> * **Krok 4: Příprava DMS**: Zaregistrujte poskytovatele migrace databáze, vytvoření instance a pak vytvořte projekt DMS.
> * **Krok 5: Příprava Azure Site Recovery**: vytvoření účtu úložiště Azure pro uložení replikovaná data.
> * **Krok 6: Příprava místní VMware Site Recovery**: Příprava účty pro virtuální počítač zjišťování a instalaci agenta a příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání. 
> * **Krok 7: Replikace virtuálních počítačů**: Konfigurace Site Recovery zdrojové a cílové prostředí, nastavte zásady replikace a zahájení replikace virtuálních počítačů do úložiště Azure.
> * **Krok 8: Migrace databáze s DMS**: spuštění migrace databáze.
> * **Krok 9: Migrace virtuálních počítačů pomocí Site Recovery**: spustit převzetí služeb při selhání k migraci virtuálních počítačů do Azure.


## <a name="step-1-set-up-site-to-site-vpn-connection"></a>Krok 1: Nastavení připojení site-to-site VPN

Příprava pro připojení site-to-site VPN, nastavení virtuální sítě a podsítě, vytvořit bránu sítě VPN pro virtuální síť a nastavit bránu místní sítě, aby věděl, že Azure může jak se připojit k vaší místní síti VPN. Pak vytvořte a ověřit připojení site-to-site.

### <a name="set-up-a-virtual-network"></a>Nastavení virtuální sítě

Vytvoření virtuální sítě Azure, umožníte DMS připojení site-to-site k místní SQL Server.


1. V [portál Azure](https://portal.azure.com), klikněte na tlačítko **vytvořit prostředek**. Vyberte **sítě** > **virtuální síť**.
2. V **virtuální sítě** > **vybrat model nasazení**, vyberte **Resource Manager** > **vytvořit**
3. V **vytvořit virtuální síť** > **název**, zadejte název sítě jedinečné. Pro náš scénář **ContosoVNET**.
4. V **adresní prostor**, přidat rozsah IP adres. Rozsah se nesmí překrývat s váš místní adresní prostor.
5. V **skupiny prostředků**, vytvořte novou skupinu, nebo vyberte nějaký existující. V tomto scénáři používáme **ContosoRG**.
6. V **umístění**, vyberte oblast, ve které chcete vytvořit virtuální síť. Používáme **východní USA 2**.
7. V **podsíť**přidejte první název podsítě a rozsah adres. Vytvořili jsme **aplikace** podsítě.
8. Zakážete koncové body služby.

    ![Vytvoření virtuální sítě](media/migrate-scenarios-lift-and-shift/vnet-create-network.png)

9. Vyberte **připnout na řídicí panel** , aby mohli snadno najít síti v budoucnosti a pak klikněte na tlačítko **vytvořit**.
10. Vytvoření virtuální sítě trvá několik sekund. Po vytvoření, ověřte na řídicím panelu portálu Azure.
11. Ujistěte se, že jsou povoleny tyto porty komunikace ve virtuální síti: 443, 53, 9354, 445, 12000.


### <a name="set-up-subnets"></a>Nastavení podsítě

V našem síť Azure jsme bude mít čtyři podsítě:

![Seznam podsítí](media/migrate-scenarios-lift-and-shift/vnet-subnet-list.png)

Při vytváření sítě nastavíte první podsíť (aplikace). Teď vytvořte zbytek podsítě. Podsíť brány se používá připojení brány VPN Azure k posílání provozu přes připojení VPN k místní lokalitě.

1. Ve virtuální síti v části **nastavení**, klikněte na tlačítko **podsíť** > **+ podsítě**.
2. Nastavit podsíť **Datamigration**s tímto rozsah adres a pak klikněte na tlačítko **OK**.

    ![Data podsítě](media/migrate-scenarios-lift-and-shift/vnet-subnet-data.png)  


3. V **+ podsíť**, nastavit podsíť **Identity**s tímto rozsah adres a pak klikněte na tlačítko **OK**.
    ![Podsíť identity](media/migrate-scenarios-lift-and-shift/vnet-subnet-identity.png)

4. V **podsíť**, klikněte na tlačítko **podsíť brány**a potom klikněte na **OK**.
    - Tato podsíť obsahuje IP adresy, které budou používat bránu VPN pro připojení k síti VPN.
    - Název této podsítě se nastaví automaticky, tak, že Azure poznáte.
    - Upravte rozsah adres automaticky vyplněné tak, aby odpovídaly vaší místní podsítě. 

    ![Podsíť brány](media/migrate-scenarios-lift-and-shift/vnet-subnet-gateway.png)
    
### <a name="set-up-a-virtual-network-gateway"></a>Nastavení brány virtuální sítě

1. Na levé straně stránky portálu klikněte na **+** a do vyhledávacího pole zadejte text „Brána virtuální sítě“. V **výsledky**, klikněte na tlačítko **Brána virtuální sítě**.
2. V dolní části stránky Brána virtuální sítě klikněte na **Vytvořit**.
3. V **vytvořit bránu virtuální sítě** >  **název**, zadejte název objektu brány.
4. V **typ brány**, vyberte **VPN**.
5. V **typ sítě VPN**, vyberte **založené na trasách**.
6. V **SKU**, vyberte z rozevíracího seznamu SKU brány. SKU uvedené v rozevíracím seznamu závisí na typu VPN, který jste vybrali. Nemáte, povolte režim aktivní aktivní. [Další informace](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) o SKU.
7. V **virtuální síť** > **vyberte virtuální síť**, přidejte síť, do které jste vytvořili. Brána bude přidán k této síti.
8. V **konfiguraci první IP**, zadejte veřejnou IP adresu.  Brána sítě VPN, musí mít veřejnou IP adresu. Tato adresa se dynamicky přiřazuje prostředek při vytvoření brány VPN. Aktuálně jsou podporovány pouze dynamické adresy, ale po je přiřazen není změnit IP adresu.
    - Klikněte na tlačítko **konfigurace IP brány vytvořit**. V **zvolte veřejnou IP adresu**, klikněte na tlačítko **+ vytvořit nový**.
    - V **vytvoření veřejné IP adresy**, zadejte název pro veřejnou IP adresu (Contoso-Gateway). Nechte SKU jako **základní**a klikněte na tlačítko **OK** a uložte změny.
        
    ![Brána virtuální sítě](media/migrate-scenarios-lift-and-shift/vnet-create-vpn-gateway2.png) 

9. Kliknutím na **Vytvořit** zahajte proces vytváření brány VPN. Nastavení se ověří a na řídicím panelu se zobrazí "Nasazení brány virtuální sítě".

    ![Ověření brány virtuální sítě](media/migrate-scenarios-lift-and-shift/vnet-vpn-gateway-validate.png)
    
Vytváření brány může trvat až 45 minut. Možná budete muset obnovit stránku portálu k zobrazení stavu dokončení.

### <a name="set-up-a-local-network-gateway"></a>Nastavit bránu místní sítě

Nastavte bránu místní sítě v Azure, představují místního webu.

1. Na portálu klikněte na **+ Vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **Brána místní sítě** a pak spusťte hledání stisknutím klávesy **Enter**. Ve výsledcích, klikněte na tlačítko **brány místní sítě** > **vytvořit**.
3. V **vytvořit bránu místní sítě** > **název**, zadejte název, který používá Azure pro vaše objektu brány místní sítě.
4. **IP adresa**, zadejte veřejnou IP adresu místního zařízení VPN ke kterému se připojí Azure. IP adresa by neměla být NAT'ed a Azure musí být schopen se spojit.
5. V **adresní prostor**, zadejte rozsahy adres místní sítě, které budou směrovány přes bránu VPN na místní zařízení. Zajistěte, aby že tyto dva intervaly nepřekrývaly s rozsahy na virtuální síť Azure.
6. **Konfigurace nastavení protokolu BGP** není relevantní pro tento scénář.
7. V **předplatné**, ověřte, zda je správné předplatné.
8. V **skupiny prostředků**: Vyberte skupinu prostředků, který jste použili k vytvoření sítě (ContosoRG).
9. V **umístění**, vyberte oblast, ve které jste vytvořili virtuální sítě.

    ![Místní brány](media/migrate-scenarios-lift-and-shift/vnet-create-local-gateway.png)

4. Klikněte na tlačítko **vytvořit** vytvořit místní brány.

### <a name="configure-your-on-premises-vpn-device"></a>Nakonfigurujte místní zařízení VPN

Zde je, co je potřeba nakonfigurovat na vaše místní zařízení VPN:

- Na veřejnou IPv4 adresu brány virtuální sítě Azure, kterou jste právě vytvořili.
- Předsdílený klíč (klíč, který jste použili při vytváření připojení site-to-site VPN).  

Můžete nastavit vaše místní zařízení VPN:

- V závislosti na místní zařízení VPN, ke které máte je možné stáhnout konfigurační skript zařízení VPN. [Další informace](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-download-vpndevicescript).
- Zkontrolujte [další užitečné zdroje](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#VPNDevice).

### <a name="create-the-vpn-connection"></a>Vytvoření připojení VPN

Teď vytvořte připojení site-to-site VPN mezi bránu virtuální sítě a vaše místní zařízení VPN.

1. Otevřít stránku virtuální sítě v **virtuální sítě** > **přehled** > **připojená zařízení**. 
2. Klikněte na tlačítko **připojení** > **+ přidat**.
3. V **přidat připojení** > **název**, zadejte název pro připojení.
4. V **typ připojení**, vyberte **Site-to-site (IPSec)**.
5. **Brána virtuální sítě** má pevnou hodnotu, protože se připojujete z této brány.
6. V **brány místní sítě**, zadejte bránu místní sítě, který jste vytvořili.
7. V **sdílený klíč**, zadejte klíč, který odpovídá hodnotě, kterou používáte pro vaše místní zařízení VPN. V příkladu se používá abc123, ale můžete (a měli byste) používat něco složitějšího. Důležité je, aby hodnota, kterou zde zadáte, byla stejná jako hodnota, kterou jste zadali při konfiguraci zařízení VPN.
8. Hodnoty pro **předplatné**, **skupiny prostředků**, a **umístění** jsou pevné.

    ![Připojení VPN](media/migrate-scenarios-lift-and-shift/vpn-connection.png) 

4. Klikněte na tlačítko **OK** k vytvoření připojení. Budete najdete v části "Vytvoření připojení" flash na obrazovce.
5. Po vytvoření připojení ji zobrazit na **připojení** stránky bránu virtuální sítě. Stav přejde z neznámý > připojení > bylo úspěšné.

### <a name="verify-the-vpn-connection"></a>Ověření připojení VPN

Na webu Azure Portal můžete zobrazit stav připojení VPN Gateway Resource Manageru otevřením připojení. 

1. Klikněte na tlačítko **všechny prostředky**a přejděte do brány virtuální sítě.
2. Na stránce brány virtuální sítě, klikněte na tlačítko **připojení**. Můžete zobrazit stav každého připojení.
3. Klikněte na název připojení a zobrazit další informace v **Essentials**. Po provedení úspěšného připojení, je ve stavu 'Úspěch' a 'Připojeno'.

Nyní ověřte, zda se můžete připojit k virtuální počítač SQL Server prostřednictvím sítě VPN. Pomocí připojení vzdálené plochy a připojte na adresu IP virtuálního počítače.



## <a name="step-2-prepare-an-azure-sql-managed-instance"></a>Krok 2: Příprava spravované Instance Azure SQL

### <a name="set-up-a-virtual-network"></a>Nastavení virtuální sítě

V tomto scénáři musíte vytvořit jinou virtuální sítí, který je vyhrazen pro spravované Instance SQL Azure.  Vezměte na vědomí následující požadavky:

- Vytvoření spravované instance je nutné vytvořit vyhrazený podsíť.
- Podsíť musí být prázdný a neobsahuje žádné jiné cloudové služby, a nesmí být podsíť brány. Po vytvoření spravované instance nesmí přidat prostředky do podsítě.
- Podsítě nesmí mít skupina NSG s ním spojená.
- Podsíť musí mít uživatele trasy tabulky (UDR) s 0.0.0.0/0 Další směrování Internetu jako pouze trasy přiřazen. 
- Volitelné vlastní DNS: Pokud je zadán pro vlastní DNS virtuální sítě, musíte do seznamu přidat Azure rekurzivní překladače IP adresu (například 168.63.129.16). [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Podsítě nesmí mít koncového bodu služby (úložiště nebo SQL) s ním spojená. Koncové body služby by mělo být zakázáno ve virtuální síti.
- Podsíť musí mít minimálně 16 IP adres. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) o dimenzování podsíť spravované instance.

Virtuální sítě nastavte takto:

1.  Na portálu Azure Portal klikněte na **Vytvořit prostředek**. 
2. Vyberte **sítě** > **virtuální síť**.
3. V **virtuální sítě** > **vybrat model nasazení**, vyberte **Resource Manager** > **vytvořit**.
4. V **vytvořit virtuální síť** > **název**, zadejte název sítě jedinečné. Pro náš scénář **ContosoVNETSQLMI**.
5. V **adresní prostor**, přidat rozsah IP adres, viz následující obrázek. Rozsah se nesmí překrývat s vašimi místními a ContosoVNET adresní prostor.
6. V **skupiny prostředků**, vytvořte novou skupinu, nebo vyberte nějaký existující. V tomto scénáři používáme **ContosoRG**.
7. V **umístění**, vyberte oblast, ve které chcete vytvořit virtuální síť. Používáme **východní USA 2**.
8. V **podsíť**přidejte první název podsítě a rozsah adres. Jsme vytvořili **ManagedInstances** podsítě.
9. Zakážete koncové body služby.

    ![Spravované Instance sítě](media/migrate-scenarios-lift-and-shift/network-mi.png)

10. Po nasazení sítě potřebujete změnit nastavení služby DNS. V tomto scénáři DNS nejdřív odkazuje na řadič domény v podsíti Identity pomocí statickou privátní IP adresu (172.16.3.4) a pak překladač Azure DNS 168.63.129.16.

    ![Spravované Instance sítě](media/migrate-scenarios-lift-and-shift/network-mi2.png)


### <a name="set-up-routing"></a>Nastavení směrování

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal. Klikněte na tlačítko **směrovací tabulku** > **vytvořit v tabulce směrování** stránky.
2. V **vytvořit směrovací tabulku** > **název**, zadejte název tabulky.
3. Vyberte vaše předplatné, skupinu prostředků a umístění. Nechte BGP zakázané a klikněte na **vytvořit**.
    ![Směrovací tabulka](media/migrate-scenarios-lift-and-shift/route-table.png)

4. Po vytvoření směrovací tabulka, otevřete ho a klikněte na tlačítko **trasy** > **+ přidat**.
5. V **přidat trasy** > **název**, zadejte název trasy.
6. V **předpona adresy**, zadejte 0.0.0.0/0.
7. V **typ dalšího směrování**, vyberte **Internet**. Pak klikněte na **OK**.

     ![Tabulka směrování](media/migrate-scenarios-lift-and-shift/route-table2.png)


### <a name="apply-the-route-to-the-managed-instance-subnet"></a>Použít trasy k podsíti spravované instance

1. Otevřete virtuální síť, kterou jste vytvořili. Klikněte na tlačítko **podsítě** > váš název podsítě.
2. Klikněte na tlačítko **směrovací tabulku** > si název tabulky. Potom klikněte na **Uložit**.

     ![Tabulka směrování](media/migrate-scenarios-lift-and-shift/route-table3.png)

### <a name="create-a-managed-instance"></a>Vytvoření spravované instance

1. Klikněte na tlačítko **vytvořit prostředek**, vyhledejte **spravované Instance**a vyberte **Azure databáze spravované Instance SQL (preview)**.
2. Klikněte na možnost **Vytvořit**.
3. V **spravované Instance SQL**, vyberte předplatné a ověřte, že **Náhled podmínky** zobrazit **platných**.
4. V **název spravované Instance**, zadejte název. 
5. Zadejte uživatelské jméno a heslo jako správce pro instanci.
6. Vyberte skupiny prostředků, umístění a virtuální sítě pro instanci.
7. Klikněte na tlačítko **cenová úroveň** na velikost výpočetních prostředků a úložiště. Ve výchozím nastavení získá instanci 32 GB úložného prostoru zdarma (duben 2018).
8. Zadejte úložiště a počet jader na virtuální.
9. Klikněte na tlačítko **použít** se uložit nastavení, a **vytvořit** nasadit spravované instance. Chcete-li zobrazit stav nasazení, klikněte na tlačítko **oznámení**, a **nasazení v průběhu**.

    ![Spravovaná instance](media/migrate-scenarios-lift-and-shift/mi-1.png)

Po spravované Instance se nasazení dokončí, zobrazí dva nové prostředky ve skupině prostředků ContosoRG: virtuálního clusteru pro spravované instance a spravované Instance SQL. Obě jsou v umístění východní USA 2.

![Spravovaná instance](media/migrate-scenarios-lift-and-shift/managed-instance2.png)


## <a name="step-3-get-a-sas-uri-for-dms"></a>Krok 3: Získáte identifikátor URI SAS pro DMS

Získáte identifikátor URI SAS, aby služba DMS přístup vaší kontejneru účtu úložiště, k nahrání souborů zálohování, které se používají k migraci databáze do Azure SQL Database spravované Instance. 

1. Otevřete Storage Explorer (Preview).
2. V levém podokně rozbalte obsahující kontejner objektů blob, pro kterou chcete získat SAS účtu úložiště. Rozbalte účet úložiště **kontejnery objektů Blob**.
3. Klikněte pravým tlačítkem na kontejner a vyberte **sdíleného přístupového podpisu**.

     ![SAS](media/migrate-scenarios-lift-and-shift/sas-1.png)

4. V **sdíleného přístupového podpisu**, zadejte zásady, spuštění a datum vypršení platnosti, časové pásmo a chcete použít pro prostředek úrovně přístupu. Poté klikněte na **Vytvořit**.

    ![SAS](media/migrate-scenarios-lift-and-shift/sas-2.png)

5. Zobrazí se druhé dialogové okno zobrazí kontejner objektů blob, společně s adresu URL a QueryStrings můžete použít pro přístup k prostředku úložiště. Vyberte **kopie** ke zkopírování hodnot. Uložte je na bezpečné místo. Musíte je při nastavování DMS.

      ![SAS](media/migrate-scenarios-lift-and-shift/sas-3.png)  

## <a name="step-4-prepare-dms"></a>Krok 4: Příprava DMS

Zaregistrujte zprostředkovatele migrace databáze a vytvořit instanci.

### <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. V rámci vašeho předplatného, vyberte **zprostředkovatelé prostředků**. 
2. Vyhledejte "migrace" a potom vyberte napravo od Microsoft.DataMigration **zaregistrovat**. 


### <a name="create-an-instance"></a>Vytvoření instance

1. Vyberte **+ vytvořit prostředek**, vyhledejte "služba migrace databáze Azure a pak vyberte **služba migrace databáze Azure** z rozevíracího seznamu.
2. Na obrazovce služba migrace databáze Azure (preview) vyberte **vytvořit**.
3. Zadejte název pro tuto službu, předplatné, skupinu prostředků, virtuální sítě a cenovou úroveň.
4. Vyberte **vytvořit** vytvořte službu.

    ![SLUŽBA DMS](media/migrate-scenarios-lift-and-shift/dms-instance.png)  




## <a name="step-5-prepare-azure-for-the-site-recovery-service"></a>Krok 5: Příprava Azure pro službu Site Recovery

### <a name="set-up-a-virtual-network"></a>Nastavení virtuální sítě

Budete potřebovat síť Azure, ve kterém virtuální počítače Azure budou umístěné když jste vytvořili po převzetí služeb při selhání a účet úložiště Azure, ve kterém je uložený replikovaná data.

- Pro účely tohoto scénáře použijeme síť Azure, kterou jsme vytvořili výše, pro DMS.
- Všimněte si, že síť, kterou používáte, musí být ve stejné oblasti jako trezor Site Recovery.


### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

Site Recovery replikuje data virtuálního počítače do úložiště Azure. Virtuální počítače Azure se vytvoří z úložiště, když převezmete služby při selhání z místní lokality do Azure.

1. V nabídce webu [Azure Portal](https://portal.azure.com) vyberte **Nový** > **Úložiště** > **Účet úložiště**.
2. V části **Vytvořit účet úložiště** zadejte název účtu. Pro účely těchto kurzů použijte název **contosovmsacct1910171607**. Název musí být v rámci Azure jedinečný, musí být dlouhý 3 až 24 znaků a obsahovat pouze číslice a malá písmena.
3. V části **Model nasazení** vyberte **Resource Manager**.
4. V části **Druh účtu** vyberte **Obecné účely**. V části **Výkon** vyberte **Standard**. Nevybírejte úložiště objektů blob.
5. V části **Replikace** vyberte výchozí redundanci úložiště **Geograficky redundantní úložiště jen pro čtení**.
6. V části **Předplatné** vyberte předplatné, ve kterém chcete nový účet úložiště vytvořit.
7. V části **Skupina prostředků** zadejte název nové skupiny prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pro účely těchto kurzů použijte název **ContosoRG**.
8. V části **Umístění** vyberte zeměpisné umístění účtu úložiště. Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services. V tomto scénáři používáme **východní USA 2** oblast.

   ![vytvořit účet úložiště](media/migrate-scenarios-lift-and-shift/create-storageacct.png)

9. Vyberte **Vytvořit** a vytvořte účet úložiště.

### <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Monitorování a správa** > **Backup a Site Recovery**.
2. Jako **Název** zadejte popisný název pro identifikaci trezoru. Pro účely tohoto kurzu použijte **ContosoVMVault**.
3. V části **Skupina prostředků** vyberte existující skupinu prostředků **contosoRG**.
4. V **umístění**, zadejte oblast Azure **východní USA 2**.
5. Pokud chcete mít k trezoru rychlý přístup z řídicího panelu, vyberte **Připnout na řídicí panel** > **Vytvořit**.
Nový trezor se zobrazí v části **Řídicí panel** > **Všechny prostředky** a na hlavní stránce **Trezory služby Recovery Services**.

## <a name="step-6-prepare-on-premises-vmware-for-site-recovery"></a>Krok 6: Příprava místní VMware Site Recovery

Příprava VMware pro Site Recovery, zde co musíte udělat:

- Příprava účtu na serveru vCenter nebo hostiteli vSphere ESXi pro automatizaci zjišťování virtuálních počítačů
- Příprava účtu pro automatickou instalaci služby Mobility na virtuální počítače VMware
- Příprava místní virtuální počítače, pokud se chcete připojit k virtuálním počítačům Azure po převzetí služeb při selhání.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automaticky zjistit virtuální počítače. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Je třeba účet, který můžete spustit operací, jako je vytváření a odebrání disků a zapnutí virtuálních počítačů.

Vytvořte účet následujícím způsobem:

1. Pokud chcete použít vyhrazený účet, vytvořte roli na úrovni vCenter. Roli pojmenujte například **Azure_Site_Recovery**.
2. Přiřaďte roli oprávnění uvedená v následující tabulce.
3. Vytvořte uživatele na serveru vCenter nebo hostiteli vSphere. Přiřaďte uživateli roli.

**Úkol** | **Role/oprávnění** | **Podrobnosti**
--- | --- | ---
**Zjišťování virtuálních počítačů** | Alespoň uživatel jen pro čtení<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **Žádný přístup** s objektem **Rozšířit na podřízený objekt**.
**Úplná replikace, převzetí služeb při selhání a navrácení služeb po obnovení** |  Vytvořte roli (Azure_Site_Recovery) s požadovanými oprávněními a pak ji přiřaďte uživateli nebo skupině VMware.<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Azure_Site_Recovery<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **Žádný přístup** s objektem **Rozšířit na podřízený objekt**.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na virtuálním počítači, který chcete replikovat, musí být nainstalovaná služba Mobility.

- Site Recovery můžete provést automatické nabízené instalace této součásti, když povolíte replikaci pro virtuální počítač.
- Pro automatickou nabízenou instalaci je nutné připravit účet, který Site Recovery bude používat pro přístup k virtuálnímu počítači.
- Tento účet zadáte při nastavování replikace v konzole Azure.
- Musíte domény nebo místní účet s oprávněními k instalaci na virtuálním počítači.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání do Azure můžete připojit k replikované virtuální počítače v Azure z vaší místní sítě.

Pokud se po převzetí služeb při selhání chcete připojit k virtuálním počítačům s Windows pomocí protokolu RDP, postupujte následovně:

1. Pokud chcete mít přístup přes internet, před převzetím služeb při selhání povolte na místním virtuálním počítači protokol RDP. Ujistěte se, že pro přidána pravidla TCP a UDP **veřejné** profilu a že je v povolené RDP **brány Windows Firewall** > **povolené aplikace** pro všechny profily.
2. Pokud chcete mít přístup přes síť VPN typu Site-to-Site, povolte na místním počítači protokol RDP. Protokol RDP musí být povolený v části **Brána Windows Firewall** -> **Povolené aplikace a funkce** pro **doménovou a privátní** síť.
3. Zkontrolujte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135).
4. Při aktivaci převzetí služeb při selhání by na virtuálním počítači neměly být žádné čekající aktualizace Windows. Jinak se nebudete moci k virtuálnímu počítači přihlásit, dokud se aktualizace nedokončí.
5. Po převzetí služeb při selhání na virtuálním počítači Azure s Windows zkontrolujte **diagnostiku spuštění**, kde se zobrazí snímek obrazovky virtuálního počítače. Pokud se nemůžete připojit, zkontrolujte, že je virtuální počítač spuštěný, a přečtěte si tyto [tipy pro řešení potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).



## <a name="step-7-replicate-vms-with-site-recovery"></a>Krok 7: Replikace virtuálních počítačů pomocí Site Recovery

### <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. V části **Trezory služby Recovery Services** vyberte název trezoru **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V části **Cíl ochrany** > **Kde jsou vaše počítače umístěné?** vyberte **Místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere Hypervisor**. Pak vyberte **OK**.

    ![Šablona OVF](./media/migrate-scenarios-lift-and-shift/replication-goal.png)


### <a name="confirm-deployment-planning"></a>Potvrďte plánování nasazení

V **plánování nasazení**, klikněte na tlačítko **Ano, mám to hotové**, v rozevíracím seznamu.

### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí


Pro nastavení zdrojového prostředí potřebujete jeden vysoce dostupný místní počítač, který bude hostovat místní komponenty Site Recovery. Součásti zahrnují konfigurační server a procesový server.

- Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat.
- Procesní server funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.


Nastavení konfigurace serveru jako vysoce dostupný virtuální počítač VMware:

- Stáhněte si šablonu připravené otevřete virtualizace formát OVF ().
- Importujte šablonu do VMware vytvořte virtuální počítač.
- Nastavení konfigurace serveru a zaregistrovat v trezoru. 

Po registraci služba Site Recovery zjistí místní virtuální počítače VMware.



#### <a name="download-the-vm-template"></a>Stažení šablony virtuálního počítače

1. V daném trezoru klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.

    ![Stažení šablony](./media/migrate-scenarios-lift-and-shift/new-cs.png)

4. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
2. Stáhněte šablonu OVF pro konfigurační server.

    ![Stáhnout OVF](./media/migrate-scenarios-lift-and-shift/add-cs.png)

  > [!TIP]
  Nejnovější verzi šablony konfiguračního serveru můžete stáhnout přímo z webu [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

#### <a name="import-the-template-in-vmware"></a>Import šablony do VMware

1. Přihlaste se k serveru VMware vCenter server pomocí klientem VMWare vSphere.
2. V nabídce **File** (Soubor) vyberte **Deploy OVF Template** (Nasadit šablonu OVF). Tím spustíte průvodce nasazením šablony OVF. 

     ![Šablona OVF](./media/migrate-scenarios-lift-and-shift/vcenter-wizard.png)

3. V části **Select source** (Vybrat zdroj) zadejte umístění staženého souboru OVF.
4. V části **Review details** (Kontrola podrobností) vyberte **Next** (Další).
5. V části **Select name and folder** (Vybrat název a složku) a **Select configuration** (Vybrat konfiguraci) přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V části **Ready to complete** (Připraveno k dokončení):

    * Pokud chcete vytvořit virtuální počítač s výchozím nastavením, vyberte **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit).

    * Pokud chcete přidat další síťové rozhraní, zrušte zaškrtnutí políčka **Power on after deployment** (Spustit po nasazení). Pak vyberte **Dokončit**. Ve výchozím nastavení se šablona konfiguračního serveru nasadí s jedním síťovým adaptérem. Po nasazení můžete přidat další síťové adaptéry.



#### <a name="register-the-configuration-server"></a>Registrace konfiguračního serveru 

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.
4. Při prvním přihlášení se spustí nástroj pro konfiguraci služby Azure Site Recovery.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.

    ![Šablona OVF](./media/migrate-scenarios-lift-and-shift/config-server-register1.png)

6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.

    ![Šablona OVF](./media/migrate-scenarios-lift-and-shift/config-server-register2.png)

7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Automaticky se spustí průvodce správou konfiguračního serveru.

#### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurace nastavení a přidání serveru VMware

1. V průvodci správou konfiguračního serveru vyberte **Nastavit připojení** a pak vyberte síťový adaptér, který bude přijímat provoz replikace. Potom vyberte **Uložit**. Po dokončení konfigurace není možné toto nastavení změnit.
2. V části **Vyberte trezor služby Recovery Services** vyberte své předplatné Azure a příslušnou skupinu prostředků a trezor.

    ![Trezoru](./media/migrate-scenarios-lift-and-shift/cswiz1.png) 

3. V části **Nainstalovat software třetí strany** přijměte licenční smlouvu. Vyberte **Stáhnout a nainstalovat** a nainstalujte MySQL Server.
4. Vyberte **Nainstalovat VMware PowerCLI**. Než to uděláte, zkontrolujte, jestli jsou zavřená všechna okna prohlížeče. Potom vyberte **Pokračovat**.
5. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
6. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
7. Zadejte přihlašovací údaje, které bude konfigurační server používat pro připojení k serveru VMware. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **Přidat** a pak **Pokračovat**.

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

8. V části **Nakonfigurovat přihlašovací údaje virtuálního počítače** zadejte uživatelské jméno a heslo, které se po povolení replikace použijí pro automatickou instalaci služby mobility na počítače. Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce. 

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

7. Vyberte **Dokončit konfiguraci** a dokončete registraci. 
8. Jakmile bude registrace dokončená, zkontrolujte Azure Portal, jestli je ve vybraném trezoru na stránce **Zdroj** uvedený konfigurační server a VMware server. Pak vyberte **OK** a nakonfigurujte nastavení cíle.
9. Site Recovery se připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače.

> [!NOTE]
> Může trvat 15 minut nebo déle, než se název účtu objeví na portálu. Pokud chcete provést aktualizaci okamžitě, vyberte **Konfigurační servery** > ***název serveru*** > **Aktualizovat server**.

### <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. V **připravit infrastrukturu** > **cíl**, vyberte předplatné Azure, kterou chcete použít.
2. Pro model cíle nasazení, vyberte **Azure Resource Manager**.

3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.


### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace**, klikněte na tlačítko **vytvořit a přidružit** .
1. V části **Vytvoření zásady replikace** zadejte jako název zásady **VMwareRepPolicy**.
2. V části **Prahová hodnota cíle bodu obnovení (RPO)** použijte výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
3. V části **Uchování bodu obnovení** použijte výchozí hodnotu 24 hodin a nastavte délku okna uchování jednotlivých bodů obnovení. Pro účely tohoto kurzu použijte 72 hodin. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
4. V části **Frekvence snímků konzistentní vzhledem k aplikacím** použijte jako frekvenci pro vytváření snímků konzistentních vzhledem k aplikacím výchozí hodnotu 60 minut. Vyberte **OK** a vytvořte zásadu.

    ![Vytvoření zásady replikace](./media/migrate-scenarios-lift-and-shift/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružení zásady replikace](./media/migrate-scenarios-lift-and-shift/replication-policy2.png)



### <a name="enable-replication"></a>Povolení replikace

Nyní spusťte replikaci virtuálních počítačů. Obnovení lokality nainstaluje službu Mobility na každý virtuální počítač povolit pro něho replikaci. 


1. Vyberte **Replikovat aplikaci** > **Zdroj**.
2. V části **Zdroj** vyberte konfigurační server.
3. V části **Typ počítače** vyberte **Virtuální počítače**.
4. V části **vCenter/vSphere Hypervisor** vyberte vCenter Server, který spravuje hostitele vSphere, nebo vyberte samotného hostitele.
5. Vyberte procesní server (konfigurační server). Pak vyberte **OK**.

    ![Povolení replikace](./media/migrate-scenarios-lift-and-shift/enable-replication1.png)

1. V části **Cíl** vyberte předplatné a skupinu prostředků, ve které chcete vytvořit virtuální počítače, jejichž služby se převezmou při selhání. Zvolte model nasazení (Classic nebo Resource Manager), který chcete v Azure použít pro virtuální počítače, jejichž služby se převezmou při selhání.
2. Vyberte účet úložiště Azure, který chcete použít pro replikaci dat.
3. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se vytvoří po převzetí služeb při selhání.
4. Výběrem možnosti **Nakonfigurovat pro vybrané počítače** použijte nastavení sítě pro všechny počítače, které jste vybrali pro ochranu. Vyberte **Nakonfigurovat později** a vyberte síť Azure pro konkrétní počítač.
5. Vyberte podsíť ve virtuální síti. Pak vyberte **OK**.

    ![Povolení replikace](./media/migrate-scenarios-lift-and-shift/enable-replication2.png)

6. V **virtuální počítače** > **vybrat virtuální počítače**, vyberte virtuální počítač (WEBVM), které chcete. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. 

    ![Povolení replikace](./media/migrate-scenarios-lift-and-shift/enable-replication3.png)

7. Pokud chcete monitorovat virtuální počítače, které jste přidali, zkontrolujte čas posledního zjištění virtuálních počítačů v části **Konfigurační servery** > **Poslední kontakt**. Pokud chcete přidat virtuální počítače bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (nevybírejte ho) a vyberte **Aktualizovat**. Může to trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.
8. V části **Vlastnosti** > **Konfigurace vlastností** vyberte účet, který použije procesový server při automatické instalaci služby mobility na počítači. 

    ![Povolení replikace](./media/migrate-scenarios-lift-and-shift/enable-replication4.png)

9. V části **Nastavení replikace** > **Konfigurace nastavení replikace** zkontrolujte, jestli je vybraná správná zásada replikace.
10. Vyberte **Povolit replikaci**.

    ![Povolení replikace](./media/migrate-scenarios-lift-and-shift/enable-replication5.png)

11. Sledovat průběh **povolení ochrany** úlohy v **nastavení** > **úlohy** > **úlohy Site Recovery**. 
12. Po **dokončení ochrany** spuštění úloh, je počítač připraven k převzetí služeb při selhání a zobrazí se v **přehled** > **Essentials**.

    ![Základy](./media/migrate-scenarios-lift-and-shift/essentials.png)


## <a name="step-8-migrate-the-database-with-dms"></a>Krok 8: Migrace databáze s DMS

Vytvoření projektu DMS a spusťte migrace.


### <a name="create-a-database-migration-project"></a>Vytvoření projektu migrace databáze

1. Vyhledejte DMS prostředků na portálu Azure a otevřete jej.
2. Vyberte **+ nový projekt migrace**.
3. V **nový projekt migrace**, zadejte název projektu.
4. V části **Source server type** (Typ zdrojového serveru) vyberte **SQL Server**. V **cíle typ serveru**, vyberte **Azure SQL Database spravované Instance**.
5. Klikněte na tlačítko **vytvořit** a vytvořte tak projekt.
6. V **zdroje podrobnosti**, zadejte podrobnosti připojení pro místní zdroje systému SQL Server. Klikněte na **Uložit**.
7. V **cíle podrobnosti**, zadat podrobnosti připojení pro cíl, který je předem zřízená Azure SQL spravované Instance databáze do kterého budou migrovány místní databázi. Potom klikněte na **Uložit**.
8. V **souhrn projektu**zkontrolujte a zkontrolujte podrobnosti o přidružený k projektu migrace.

    ![Služba DMS projektu](./media/migrate-scenarios-lift-and-shift/dms-project.png)

### <a name="migrate-the-database"></a>Migrace databáze

1. Po vytvoření projektu, zobrazí se Průvodce migrací.
2. Zadejte pověření pro zdrojové a cílové servery a klikněte na tlačítko **Uložit**. Průvodce se pokusí přihlásit k systému SQL Server na místě.

    ![Služba DMS Průvodce](./media/migrate-scenarios-lift-and-shift/dms-wiz1.png)

3. V **mapu, která bude cílovým databázím**, vyberte zdrojové databáze, kterou chcete migrovat. Potom klikněte na **Uložit**.

    ![Služba DMS Průvodce](./media/migrate-scenarios-lift-and-shift/dms-wiz2.png)

4. V **cíle podrobnosti**, vyberte **poznám, že moje cíl podrobnosti**. Zadejte název DNS vašeho spravované instance SQL, spolu s přihlašovací údaje. Potom klikněte na **Uložit**.

    ![Služba DMS Průvodce](./media/migrate-scenarios-lift-and-shift/dms-wiz3.png)

5. V projektu uložený, vyberte **+ nová aktivita**. Potom vyberte **spuštění migrace**.
6. Po zobrazení výzvy zadejte přihlašovací údaje pro zdrojové a cílové servery. Potom klikněte na **Uložit**.
7. V **mapu, která bude cílovým databázím**, vyberte zdrojové databáze, kterou chcete migrovat. Pak klikněte na tlačítko **uložit**
8. V **nakonfigurovat nastavení migrace** > **umístění zálohy serveru**, zadejte sdílené síťové složce jste vytvořili na místním počítači. Služba DMS trvá zálohy zdroje k této sdílené složce.  Mějte na paměti, že účet služby spuštění instance systému SQL Server zdrojové musí mít oprávnění k zápisu v této sdílené složce.
9. Zadejte uživatelské jméno a heslo, které může služba DMS zosobnit pro nahrávání záložní soubory do kontejneru úložiště Azure pro obnovení.
10. Zadejte identifikátor URI SAS, která poskytuje přístup k vaší kontejner účet úložiště, ke kterému službu ukládání záložních souborů DMS a který se používá pro migraci na Azure SQL Database spravované Instance.  Potom klikněte na **Uložit**.
11. V **migrace Souhrn**, zadejte název aktivity migrace > **spuštění migrace**.
12. V projektu > **přehled**, sledování stavu migrace. Po dokončení migrace, ověřte, že cílovým databázím existují na spravované instance.


## <a name="step-9-migrate-vms-with-site-recovery"></a>Krok 9: Migrace virtuálních počítačů pomocí Site Recovery

Doporučujeme spustit převzetí služeb při selhání a ujistěte se, zda vše funguje podle očekávání, před spuštěním z úplné migrace. Když spustíte testovací převzetí služeb při selhání, stane se následující:

1. Kontrola předpokladů postupné a zkontrolujte, zda jsou všechny podmínky, požadována pro migraci na místě.
2. Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
3. Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1. V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.
2. Ve vlastnostech virtuálního počítače, klikněte na tlačítko **+ testovací převzetí služeb při selhání**.

    ![Testovací převzetí služeb při selhání](./media/migrate-scenarios-lift-and-shift/test-failover.png)

3. Vyberte **nejnovější zpracovat**, aby převzetí služeb při selhání virtuálního počítače k nejnovějšímu dostupnému bodu v čase. Časové razítko je vidět. Tato možnost neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
2. V části **Testovací převzetí služeb při selhání** vyberte cílovou síť Azure, ke které se virtuální počítače Azure po převzetí služeb při selhání připojí.
3. Kliknutím na **OK** zahajte převzetí služeb při selhání. Můžete sledovat průběh bin trezoru > **nastavení** > **úlohy** > **testovací převzetí služeb při selhání**.
4. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Zkontrolujte, že má virtuální počítač odpovídající velikost, je připojený ke správné síti a běží. Nyní byste se měli moct k replikovanému virtuálnímu počítači v Azure připojit.
5. Virtuální počítače Azure vytvořené během testu převzetí služeb při selhání odstraníte kliknutím na **Vyčistit testovací převzetí služeb při selhání** na virtuálním počítači. V části **Poznámky** si zaznamenejte a uložte jakékoli připomínky související s testovacím převzetím služeb při selhání.

### <a name="migrate-the-machines"></a>Migrace počítačů

Jakmile ověříte, že převzetí služeb při selhání funguje podle očekávání, vytvořte plán obnovení migrovat do Azure.

### <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení

1. V úložišti, vyberte **plány obnovení (služba Site Recovery)** > **+ plán obnovení**.
2. V **vytvořit plán obnovení**, zadejte název plánu.
3. Vyberte zdroj konfigurační server a Azure jako cíl. Vyberte **Resource Manager** pro model nasazení. Zdrojové umístění musí mít počítače, které jsou povolené pro převzetí služeb při selhání a obnovení. 
4. V **vyberte položky**, přidejte počítače (WEBVM) do plánu. Pak klikněte na **OK**.
5. Klikněte na tlačítko **OK** vytvořte plán.

    ![Plán obnovení](./media/migrate-scenarios-lift-and-shift/recovery-plan1.png)

### <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1. V **plány obnovení**, klikněte na plán > **převzetí služeb při selhání**.
2. V **převzetí služeb při selhání** > **bod obnovení**, vyberte nejnovější bod obnovení.
3. Nastavení šifrovacího klíče není pro tento scénář podstatné.
4. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se pokusí před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.

    ![Převzetí služeb při selhání](./media/migrate-scenarios-lift-and-shift/failover1.png)

5. Zkontrolujte, že se virtuální počítač Azure zobrazuje v Azure podle očekávání.

    ![Převzetí služeb při selhání](./media/migrate-scenarios-lift-and-shift/failover2.png)

6. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a klikněte na **Dokončit migraci**. Tím se dokončí proce migrace, zastaví se replikace virtuálního počítače a zastaví se fakturace služby Site Recovery pro daný virtuální počítač.

    ![Převzetí služeb při selhání](./media/migrate-scenarios-lift-and-shift/failover3.png)

### <a name="update-the-connection-string"></a>Aktualizovat připojovací řetězec

Posledním krokem v procesu migrace je aktualizovat připojovací řetězec aplikace tak, aby odkazoval na databázi migrované systémem vaší MI SQL.

1. Najít nový připojovací řetězec na portálu Azure kliknutím **nastavení** > **připojovací řetězce**.

    ![Převzetí služeb při selhání](./media/migrate-scenarios-lift-and-shift/failover4.png)  

2. Je potřeba aktualizovat tento řetězec pomocí uživatelského jména a hesla pro vaše MI SQL.
3. Po dokončení konfigurace řetězec, je třeba nahradit aktuálního připojovacího řetězce v souboru web.config vaší aplikace.
4. Po aktualizaci souboru a jeho uložení, restartujte službu IIS na WEBVM. To lze provést pomocí IISRESET /RESTART z příkazový řádek.
5. Po restartování služby IIS, aplikace se teď databáze spuštěná na vaše MI SQL používat.
6. V tomto okamžiku můžete vypnout SQLVM počítače na místní a migrace je kompletní.



## <a name="conclusion"></a>Závěr

V tomto scénáři jsme provedli následující:

> [!div class="checklist"]
> * K migraci naší místní databázi spravované Instance Azure SQL s DMS.
> * Naše místní virtuální počítače migrovat na virtuálních počítačích Azure, se službou Azure Site Recovery.
