---
title: Nastavení zotavení po havárii pro virtuální počítače Hyper-V mezi místními lokalitami pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zotavení po havárii pro virtuální počítače Hyper-V mezi vaše místní weby pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: b8bc34d3786293aeae2c184ce5c44f4622ec57f1
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078592"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Nastavení zotavení po havárii pro virtuální počítače Hyper-V do sekundární místní lokality

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

Tento článek ukazuje, jak nastavit zotavení po havárii do sekundární lokality pro místní virtuální počítače Hyper-V spravované v cloudech System Center Virtual Machine Manager (VMM). V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Příprava místních serverů VMM a hostitelé Hyper-V
> * Vytvoření trezoru služby Recovery Services pro Site Recovery 
> * Nastavit zdrojové a cílové prostředí replikace. 
> * Nastavit mapování sítě 
> * Vytvoření zásady replikace
> * Povolit replikaci virtuálního počítače

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto scénáře:

- Zkontrolujte [komponent a architektury scénáře](hyper-v-vmm-architecture.md).
- Ujistěte se, že servery VMM a hostitelé Hyper-V souladu s [požadavky na podporu](hyper-v-vmm-secondary-support-matrix.md).
- Zkontrolujte, jestli je chcete replikovat virtuální počítače v souladu s [počítač podporu replikovaných](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Připravte servery VMM mapování sítě.

### <a name="prepare-for-network-mapping"></a>Příprava mapování sítě

[Mapování sítě](hyper-v-vmm-network-mapping.md) mapování mezi místní sítí virtuálních počítačů ve VMM v zdrojovým a cílovým cloudem. Mapování provede následující akce:

- Připojí virtuální počítače do příslušné cílové sítě virtuálních počítačů po převzetí služeb při selhání. 
- Virtuální počítače repliky optimálně umístí na hostitelské servery Hyper-V cílové. 
- Pokud nenakonfigurujete mapování sítě, virtuální počítače replik nebude připojen k síti virtuálních počítačů po převzetí služeb při selhání.

Příprava VMM následujícím způsobem:

1. Ujistěte se, že máte [logické sítě VMM](https://docs.microsoft.com/system-center/vmm/network-logical) ve zdrojové i cílové servery VMM.
    - Přidružený zdrojový cloud, ve kterém se nachází hostitelé Hyper-V by měl být logická síť na zdrojovém serveru.
    - Přidružený cílový cloud by měl být logická síť na cílovém serveru.
1. Ujistěte se, že máte [sítě virtuálních počítačů](https://docs.microsoft.com/system-center/vmm/network-virtual) ve zdrojové i cílové servery VMM. Sítě virtuálních počítačů musí být propojena na logickou síť v jednotlivých oblastech.
2. Propojení virtuálních počítačů na hostitelích Hyper-V zdrojového ke zdrojové síti virtuálních počítačů. 


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Zvolte cíl ochrany

Vyberte, jak chcete počítače replikovat a kam je chcete replikovat.

1. Klikněte na tlačítko **Site Recovery** > **krok 1: připravte infrastrukturu** > **cíl ochrany**.
2. Vyberte **do lokality pro obnovení**a vyberte **Ano, s technologií Hyper-V**.
3. Vyberte **Ano** k označení, že používáte VMM ke správě hostitelů technologie Hyper-V.
4. Vyberte **Ano** máte sekundární server VMM. Pokud nasazení provádíte replikaci mezi cloudy na jeden server VMM, klikněte na tlačítko **ne**. Pak klikněte na **OK**.


## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nainstalujte zprostředkovatele Azure Site Recovery na servery VMM a zjistit a zaregistrovat servery v trezoru.

1. Klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V okně **Připravit zdroj** klikněte na **+ VMM** a přidejte server VMM.
3. V **přidat Server**, zkontrolujte, že **serveru System Center VMM** se zobrazí v **typ serveru**.
4. Stáhněte si instalační soubor zprostředkovatele Azure Site Recovery.
5. Stáhněte si registrační klíč. Budete potřebovat při instalaci poskytovatele. Klíč je platný pět dní od jeho vygenerování.

    ![Nastavení zdroje](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Nainstalujte zprostředkovatele na každém serveru VMM. Není nutné explicitně nic instalovat na hostitelích Hyper-V.

### <a name="install-the-azure-site-recovery-provider"></a>Nainstalujte zprostředkovatele Azure Site Recovery

1. Spusťte instalační soubor na každém serveru VMM zprostředkovatele. Pokud nasadíte VMM v clusteru, nainstalujte prvním následujícím způsobem:
    -  Nainstalujte zprostředkovatele na aktivní uzel a dokončením instalace zaregistrujte VMM server v trezoru.
    - Nainstalujte zprostředkovatele na jiných uzlech. Všechny uzly clusteru musí používat stejnou verzi zprostředkovatele.
2. Instalační program spustí několik kontrol požadovaných součástí a požádá o oprávnění k zastavení služby VMM. Služba VMM se restartuje automaticky po dokončení instalace. Pokud instalujete na clusteru VMM, budete vyzváni k zastavení Cluster role.
3. V **Microsoft Update**, můžete přejít k určení, že se aktualizace zprostředkovatele nainstalovaly v souladu s vašimi zásadami Microsoft Update.
4. V **instalace**, přijměte nebo změňte výchozí umístění instalace a klikněte na tlačítko **nainstalovat**.
5. Po dokončení instalace klikněte na tlačítko **zaregistrovat** zaregistrujte server v trezoru.

    ![Umístění instalace](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. V poli **Název trezoru** ověřte název trezoru, ve kterém bude server zaregistrovaný. Klikněte na **Další**.
7. V **připojení Proxy**, určete, jak se zprostředkovatel, který běží na serveru VMM připojí k Azure.
   - Můžete určit, že poskytovatel by měl připojit přímo k Internetu, nebo prostřednictvím proxy serveru. Podle potřeby zadejte nastavení proxy serveru.
   - Pokud používáte proxy server, se automaticky vytvoří účet VMM RunAs (DRAProxyAccount) pomocí přihlašovacích údajů zadaný proxy serveru. Proxy server nakonfigurujte tak, aby tento účet bylo možné úspěšně ověřit. Nastavení účtu spustit jako lze upravit v konzole VMM > **nastavení** > **zabezpečení** > **účty spustit jako**.
   - Restartujte službu VMM aktualizovat změny.
8. V **registrační klíč**, vyberte klíč, který jste stáhli a zkopírovali na VMM server.
9. Nastavení šifrování se na tento scénář nevztahuje. 
10. Do pole **Název serveru** zadejte popisný název, který bude identifikovat server VMM v trezoru. V clusteru zadejte název role clusteru VMM.
11. V **synchronizovat metadata cloudu**, vyberte, zda chcete synchronizovat metadata pro všechny cloudy na serveru VMM. Tuto akci stačí na každém serveru provést pouze jednou. Pokud nechcete, aby k synchronizaci se všemi cloudy, toto políčko nechte nezaškrtnuté. Můžete synchronizovat každý cloud jednotlivě ve vlastnostech cloudu v konzole VMM.
12. Dokončete proces kliknutím na **Další**. Po registraci Site Recovery načte metadata ze serveru VMM. Server se zobrazí v **servery** > **servery VMM** v trezoru.
13. Jakmile se objeví na server v trezoru, v **zdroj** > **připravit zdroj** vyberte VMM server a vyberte cloud, ve kterém se nachází hostitel Hyper-V. Pak klikněte na **OK**.


## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte cílový server VMM a cloud:

1. Klikněte na tlačítko **připravit infrastrukturu** > **cílové**a vyberte cílový server VMM.
2. Zobrazí se cloudech VMM, které jsou synchronizovány s využitím Site Recovery. Vyberte cílový cloud.

   ![Cíl](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

Než začnete, ujistěte se, že všichni hostitelé, které zásadu používají mít stejný operační systém. Pokud hostitelé používají různé verze systému Windows Server, budete potřebovat víc zásad replikace.

1. Pokud chcete vytvořit novou zásadu replikace, klikněte na **Připravit infrastrukturu** > **Nastavení replikace** > **+Vytvořit a přidružit**.
2. V části **Vytvořit a přidružit zásady** zadejte název zásady. Typ zdroje a cíle by měl být **Hyper-V**.
3. V **verze hostitele Hyper-V**, vyberte, jaký operační systém běží na hostiteli.
4. V **typ ověřování** a **port ověřování**, určete, jak se ověřuje provoz mezi primárními a obnovovacími hostitelskými servery Hyper-V.
    - Vyberte **certifikát** Pokud nemáte pracovní prostředí protokolu Kerberos. Azure Site Recovery automaticky nakonfigurují certifikáty pro ověřování pomocí protokolu HTTPS. Nemusíte dělat nic. ručně.
    - Ve výchozím nastavení bude otevřen port 8083 a 8084 (pro certifikáty) v bráně Windows Firewall na hostitelských serverech technologie Hyper-V.
    - Pokud vyberete **Kerberos**, lístek Kerberos se použije pro vzájemné ověřování na hostitelských serverech. Pomocí protokolu Kerberos je platné pouze pro hostitelské servery Hyper-V systému Windows Server 2012 R2 nebo novějším.
1. V části **Frekvence kopírování** určete, jak často chcete replikovat rozdílová data po počáteční replikaci (každých 30 sekund, 5 minut nebo 15 minut).
2. V **uchování bodu obnovení**, zadejte \how dlouho (v hodinách) bude interval uchovávání každého bodu obnovení. Replikované počítače je možné obnovit do libovolného bodu v rámci časového období.
3. V **frekvence snímků konzistentní vzhledem k**, určete, jak často (1 – 12 hodin) body obnovení obsahující snímky konzistentní s aplikací jsou vytvořeny. Technologie Hyper-V používá dva typy snímků:
    - **Standardní snímek**: poskytuje přírůstkový snímek celého virtuálního počítače.
    - **Snímky konzistentní s aplikací**: pořídí snímek dat aplikací ve virtuálním počítači v daném okamžiku. Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace budou v konzistentním stavu při pořízení snímku. Když povolíte snímky konzistentní s aplikací, má vliv na výkon aplikací na zdrojové virtuální počítače. Nastavte hodnotu, která je menší než počet dalších bodů obnovení, které nakonfigurujete.
4. V **komprese přenosu dat**, určete, zda je nutné zkomprimovat data přenášená replikace.
5. Vyberte **odstranit repliku virtuálního počítače**, chcete-li určit, že by virtuální počítač repliky odstranit, pokud zakažte ochranu pro zdrojový virtuální počítač. Pokud povolíte toto nastavení, když zakažte ochranu pro zdrojový virtuální počítač se odebere z konzole Site Recovery, odeberou se nastavení Site Recovery pro nástroj VMM pomocí konzoly VMM a odstranit repliku.
6. V **počáteční metodu replikace**, pokud provádíte replikaci přes síť, určete, jestli se má spustit počáteční replikace nebo ji můžete naplánovat. Chcete-li snížit využití šířky pásma sítě, může být vhodné naplánovat ji na dobu mimo špičky. Pak klikněte na **OK**.

     ![Zásady replikace](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Nová zásada se automaticky přidruží ke cloudu VMM. V **zásady replikace**, klikněte na tlačítko **OK**. 


## <a name="enable-replication"></a>Povolení replikace

1. Klikněte na **Replikovat aplikaci** > **Zdroj**. 
2. V **zdroj**, vyberte VMM server a cloud, ve kterém jsou umístění hostitelé Hyper-V, které chcete replikovat. Pak klikněte na **OK**.
3. V **cílové**, ověřte na sekundární server VMM a cloud.
4. V **virtuálních počítačů**, vyberte virtuální počítače, které chcete chránit ze seznamu.


Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po **dokončit ochranu** příslušná úloha skončí, dokončení počáteční replikace a virtuální počítač připravený k převzetí služeb při selhání.

## <a name="next-steps"></a>Další postup

[Spuštění postupu zotavení po havárii](hyper-v-vmm-test-failover.md)
