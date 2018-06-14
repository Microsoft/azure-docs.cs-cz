---
title: Nastavení zotavení po havárii pro virtuální počítače Hyper-V mezi místními lokalitami s Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak nastavit zotavení po havárii pro virtuální počítače Hyper-V mezi vaší místní sítí pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 13dcc0794c1d89bd27c79cbe6636397da4f008f9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29843455"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Nastavení zotavení po havárii pro virtuální počítače Hyper-V do sekundárního místního lokality

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

Tento článek ukazuje, jak nastavit zotavení po havárii pro sekundární lokalitu, pro místní virtuálních počítačů Hyper-V spravované v cloudech System Center Virtual Machine Manager (VMM). V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Příprava na místní servery VMM a hostitelé Hyper-V
> * Vytvoření trezoru služeb zotavení pro Site Recovery 
> * Nastavit zdroje a cíle replikace prostředí. 
> * Nastavit mapování sítě 
> * Vytvoření zásady replikace
> * Povolit replikaci virtuálního počítače

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto scénáře:

- Zkontrolujte [scénář architektura a komponenty](hyper-v-vmm-architecture.md).
- Ujistěte se, že servery VMM a hostitelé Hyper-V souladu s [podporu požadavků](hyper-v-vmm-secondary-support-matrix.md).
- Zkontrolujte, zda chcete replikovat virtuální počítače v souladu s [replikovat podpora počítačů](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Připravte servery VMM mapování sítě.

### <a name="prepare-for-network-mapping"></a>Příprava mapování sítě

[Mapování sítě](hyper-v-vmm-network-mapping.md) mapy mezi místní sítí virtuálních počítačů nástroje VMM v cloudech zdroje a cíle. Mapování provede následující akce:

- Virtuální počítače se připojí k příslušné cílové sítě virtuálních počítačů po převzetí služeb při selhání. 
- Optimálně umístí virtuální počítače repliky na cílové servery hostitele technologie Hyper-V. 
- Pokud nenakonfigurujete mapování sítě, virtuální počítače replik nebude připojen k síti virtuálních počítačů po převzetí služeb při selhání.

Příprava VMM následujícím způsobem:

1. Zajistěte, aby byla [VMM logické sítě](https://docs.microsoft.com/system-center/vmm/network-logical) na zdrojové a cílové servery VMM.
    - Logické sítě na zdrojovém serveru by měly být přidružené zdrojový cloud, ve které se nacházejí hostitelů Hyper-V.
    - Logické sítě na cílovém serveru by měly být přidružené cílový cloud.
1. Zajistěte, aby byla [sítě virtuálních počítačů](https://docs.microsoft.com/system-center/vmm/network-virtual) na zdrojové a cílové servery VMM. Sítě virtuálních počítačů musí být propojena na logickou síť v každém umístění.
2. Připojte virtuální počítače na hostitelích technologie Hyper-V zdroj ke zdrojové síti virtuálních počítačů. 


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Vyberte cíl ochrany

Vyberte, jak chcete počítače replikovat a kam je chcete replikovat.

1. Klikněte na tlačítko **Site Recovery** > **krok 1: připravte infrastrukturu** > **cíl ochrany**.
2. Vyberte **k obnovení lokality**a vyberte **Ano, s technologií Hyper-V**.
3. Vyberte **Ano** indikující, že používáte VMM ke správě hostitelů technologie Hyper-V.
4. Vyberte **Ano** Pokud máte sekundární server VMM. Pokud nasazujete replikace mezi cloudy na jednom serveru VMM, klikněte na tlačítko **ne**. Pak klikněte na **OK**.


## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nainstalujte zprostředkovatele Azure Site Recovery na serverech VMM a zjišťovat a zaregistrujte server v trezoru.

1. Klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V okně **Připravit zdroj** klikněte na **+ VMM** a přidejte server VMM.
3. V **přidat Server**, zkontrolujte, zda **serveru System Center VMM** se zobrazí v **typ serveru**.
4. Stáhněte si instalační soubor zprostředkovatele Azure Site Recovery.
5. Stáhněte si registrační klíč. Budete potřebovat při instalaci zprostředkovatele. Klíč je platný pět dní od jeho vygenerování.

    ![Nastavení zdroje](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Nainstalujte zprostředkovatele na každý server VMM. Nemusíte explicitně nic instalovat na hostitelích technologie Hyper-V.

### <a name="install-the-azure-site-recovery-provider"></a>Nainstalujte zprostředkovatele Azure Site Recovery

1. Spusťte instalační soubor na každý server VMM zprostředkovatele. Pokud VMM je nasazen v clusteru, nainstalujte poprvé následujícím způsobem:
    -  Nainstalujte poskytovatele na aktivní uzel a dokončením instalace zaregistrujte VMM server v trezoru.
    - Potom nainstalujte zprostředkovatele na ostatních uzlech. Všechny uzly clusteru by měl spuštěna stejná verze zprostředkovatele.
2. Instalační program spustí několik kontrol požadovaných součástí a požádá o oprávnění k zastavení služby VMM. Služba VMM se restartuje automaticky po dokončení instalace. Pokud instalujete na clusteru VMM, se zobrazí výzva k zastavení Cluster role.
3. V **Microsoft Update**, můžete vyjádřit výslovný souhlas k určení, že se aktualizace zprostředkovatele nainstalovaly v souladu s vašimi zásadami Microsoft Update.
4. V **instalace**, přijmout nebo upravte výchozí umístění instalace a klikněte na tlačítko **nainstalovat**.
5. Po dokončení instalace klikněte na tlačítko **zaregistrovat** zaregistrujte server v trezoru.

    ![Umístění instalace](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. V poli **Název trezoru** ověřte název trezoru, ve kterém bude server zaregistrovaný. Klikněte na **Další**.
7. V **Proxy připojení**, určete, jak se zprostředkovatel, který běží na serveru VMM připojuje k Azure.
   - Můžete určit, zda zprostředkovatel by měl připojit přímo k Internetu, nebo prostřednictvím proxy serveru. Zadejte nastavení proxy serveru podle potřeby.
   - Pokud používáte proxy server, se automaticky vytvoří účet VMM RunAs (DRAProxyAccount) pomocí přihlašovacích údajů, zadaný proxy serveru. Proxy server nakonfigurujte tak, aby tento účet bylo možné úspěšně ověřit. V konzole VMM lze upravovat nastavení účtu RunAs > **nastavení** > **zabezpečení** > **účty spustit jako**.
   - Restartujte službu VMM k aktualizaci změn.
8. V **registrační klíč**, vyberte klíč, který jste stáhli a zkopírovali do serveru VMM.
9. Nastavení šifrování není relevantní v tomto scénáři. 
10. Do pole **Název serveru** zadejte popisný název, který bude identifikovat server VMM v trezoru. V clusteru zadejte název role clusteru VMM.
11. V **synchronizovat metadata cloudu**vyberte, jestli chcete synchronizovat metadata pro všechny cloudy na serveru VMM. Tuto akci stačí na každém serveru provést pouze jednou. Pokud nechcete, aby k synchronizaci veškerých cloudů, nechte toto nastavení není zaškrtnuto. Můžete synchronizovat každý cloud jednotlivě, ve vlastnostech cloudu v konzole nástroje VMM.
12. Dokončete proces kliknutím na **Další**. Po registraci načte Site Recovery metadata ze serveru VMM. Server se zobrazí v **servery** > **servery VMM** v trezoru.
13. Jakmile se objeví na server v trezoru, v **zdroj** > **připravit zdroj** vyberte VMM server a vyberte cloud, ve kterém se nachází hostiteli Hyper-V. Pak klikněte na **OK**.


## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte cílový server VMM a cloud:

1. Klikněte na tlačítko **připravit infrastrukturu** > **cíl**a vyberte cílovém serveru VMM.
2. Zobrazí se cloudech VMM, které jsou synchronizovány s Site Recovery. Vyberte cílový cloud.

   ![Cíl](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

Než začnete, ujistěte se, že všichni hostitelé pomocí zásad, mají stejný operační systém. Pokud hostitelé používají různé verze systému Windows Server, je třeba víc zásad replikace.

1. Pokud chcete vytvořit novou zásadu replikace, klikněte na **Připravit infrastrukturu** > **Nastavení replikace** > **+Vytvořit a přidružit**.
2. V části **Vytvořit a přidružit zásady** zadejte název zásady. Typ zdroje a cíle musí být **technologie Hyper-V**.
3. V **verze hostitele technologie Hyper-V**, vyberte, jaký operační systém běží na hostiteli.
4. V **typ ověřování** a **port ověřování**, zadejte, jak ověření přenosů mezi primárními a obnovovacími hostitelské servery technologie Hyper-V.
    - Vyberte **certifikát** pouze tehdy, je pracovní prostředí protokolu Kerberos. Azure Site Recovery automaticky nakonfiguruje certifikáty pro ověřování pomocí protokolu HTTPS. Nemusíte dělat nic ručně.
    - Ve výchozím nastavení bude otevřen port 8083 a 8084 (pro certifikáty) v bráně Windows Firewall na hostitelských serverech technologie Hyper-V.
    - Pokud vyberete **Kerberos**, lístek protokolu Kerberos se použije pro vzájemné ověřování serverů hostitele. Kerberos je pouze relevantní pro hostitelské servery technologie Hyper-V běžící na Windows Server 2012 R2 nebo novějším.
1. V části **Frekvence kopírování** určete, jak často chcete replikovat rozdílová data po počáteční replikaci (každých 30 sekund, 5 minut nebo 15 minut).
2. V **uchování bodu obnovení**, zadejte \how dlouho (v hodinách) bude interval uchovávání dat pro každý bod obnovení. Replikované počítače je možné obnovit do libovolného bodu v rámci časového období.
3. V **frekvence snímkování konzistentní aplikace vzhledem**, zadejte, jak často (1 – 12 hodin) body obnovení obsahující snímky konzistentní s aplikacemi jsou vytvořeny. Technologie Hyper-V používá dva typy snímků:
    - **Standardní snímek**: je přírůstkový snímek celého virtuálního počítače.
    - **Snímky konzistentní s aplikací**: pořídí snímek dat aplikací ve virtuálním počítači v daném okamžiku. Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace budou v konzistentním stavu při pořízení snímku. Pokud povolíte snímky konzistentní s aplikacemi, ovlivňuje výkon aplikace na zdrojové virtuální počítače. Nastaví hodnotu, která je menší než počet dalších bodů obnovení, které nakonfigurujete.
4. V **kompresi přenosu dat**, zadejte, zda by měl být komprimované data přenášená replikace.
5. Vyberte **odstranit repliku virtuálního počítače**, chcete-li určit, že virtuální počítač repliky měla by být odstraněna, pokud zakažte ochranu pro zdrojový virtuální počítač. Pokud povolíte toto nastavení, když zakažte ochranu pro zdrojový virtuální počítač se odebere z konzole Site Recovery, nastavení obnovení lokality pro nástroj VMM se odeberou z konzoly nástroje VMM a replika odstraněn.
6. V **počáteční metodu replikace**, pokud replikujete přes síť, určete, zda chcete spustit počáteční replikaci nebo ji můžete naplánovat. Pokud chcete uložit šířku pásma sítě, můžete chtít naplánovat dobu mimo špičky. Pak klikněte na **OK**.

     ![Zásady replikace](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Nová zásada se automaticky přidruží cloudu VMM. V **zásady replikace**, klikněte na tlačítko **OK**. 


## <a name="enable-replication"></a>Povolení replikace

1. Klikněte na **Replikovat aplikaci** > **Zdroj**. 
2. V **zdroj**, vyberte VMM server a cloudu, ve které se nacházejí hostitelů Hyper-V, které chcete replikovat. Pak klikněte na **OK**.
3. V **cíl**, ověřte sekundární server VMM a cloud.
4. V **virtuální počítače**, vyberte virtuální počítače, které chcete chránit ze seznamu.


Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po **dokončení ochrany** dokončení úlohy, je dokončena počáteční replikace a je připravený pro převzetí služeb při selhání virtuálního počítače.

## <a name="next-steps"></a>Další postup

[Spuštění postupu zotavení po havárii](hyper-v-vmm-test-failover.md)
