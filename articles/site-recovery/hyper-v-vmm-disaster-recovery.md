---
title: Nastavení zotavení po havárii mezi místními lokalitami pro virtuální počítače Hyper-V pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak nastavit zotavení po havárii mezi místními lokalitami pro místní virtuální počítače Hyper-V pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 040d0dae7bb16e0c2a79640812b18ad75754ca3e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846602"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Nastavení zotavení po havárii do sekundární místní lokality pro virtuální počítače Hyper-V

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

V tomto článku se dozvíte, jak nastavit zotavení po havárii do sekundární lokality pro místní virtuální počítače Hyper-V spravované v cloudech System Center Virtual Machine Manager (VMM). V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Příprava místních serverů VMM a hostitelů Hyper-V
> * Vytvoření trezoru služby Recovery Services pro Site Recovery 
> * Nastavení zdrojového a cílového prostředí replikace 
> * Nastavení mapování sítě 
> * Vytvoření zásady replikace
> * Povolit replikaci virtuálního počítače

## <a name="prerequisites"></a>Požadavky

Požadavky pro dokončení tohoto scénáře:

- Prostudujte si [architekturu a komponenty scénáře](hyper-v-vmm-architecture.md).
- Ujistěte se, že servery VMM a hostitelé Hyper-V splňují [požadavky podpory](hyper-v-vmm-secondary-support-matrix.md).
- Zkontrolujte, že virtuální počítače, které chcete replikovat, odpovídají [podporovaným replikovaným počítačům](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Připravte servery VMM na mapování sítě.

### <a name="prepare-for-network-mapping"></a>Příprava mapování sítě

[Mapování sítě](hyper-v-vmm-network-mapping.md) provádí mapování mezi místními sítěmi virtuálních počítačů VMM ve zdrojových a cílových cloudech. Mapování provádí následující:

- Po převzetí služeb při selhání připojí virtuální počítače k odpovídajícím cílovým sítím virtuálních počítačů. 
- Optimálně umístí repliky virtuálních počítačů na cílové hostitelské servery Hyper-V. 
- Pokud mapování sítě nenakonfigurujete, repliky virtuálních počítačů se po převzetí služeb při selhání nepřipojí k žádné síti virtuálních počítačů.

Připravte VMM následujícím způsobem:

1. Ujistěte se, že na zdrojovém a cílovém serveru VMM máte [logické sítě VMM](https://docs.microsoft.com/system-center/vmm/network-logical).
    - Logická síť na zdrojovém serveru musí být přidružená ke zdrojovému cloudu, ve kterém se nacházejí hostitelé Hyper-V.
    - Logická síť na cílovém serveru musí být přidružená k cílovému cloudu.
1. Ujistěte se, že na zdrojovém a cílovém serveru VMM máte [sítě virtuálních počítačů](https://docs.microsoft.com/system-center/vmm/network-virtual). Sítě virtuálních počítačů musí být v obou umístěních propojené s logickou sítí.
2. Připojte virtuální počítače na zdrojových hostitelích Hyper-V ke zdrojové síti virtuálních počítačů. 


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Výběr cíle ochrany

Vyberte, jak chcete počítače replikovat a kam je chcete replikovat.

1. Klikněte na **Site Recovery** > **Krok 1: Příprava infrastruktury** > **Cíl ochrany**.
2. Vyberte **Do lokality pro obnovení** a vyberte **Ano, s technologií Hyper-V**.
3. Vyberte **Ano**, abyste určili, že ke správě hostitelů Hyper-V používáte VMM.
4. Vyberte **Ano**, pokud máte sekundární server VMM. Pokud nasazujete replikaci mezi cloudy na jeden server VMM, klikněte na **Ne**. Pak klikněte na **OK**.


## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nainstalujte na servery VMM zprostředkovatele Azure Site Recovery a vyhledejte a zaregistrujte servery v trezoru.

1. Klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V okně **Připravit zdroj** klikněte na **+ VMM** a přidejte server VMM.
3. V části **Přidat server** zkontrolujte, že se v poli **Typ serveru** zobrazí **Server System Center VMM**.
4. Stáhněte si instalační soubor zprostředkovatele Azure Site Recovery.
5. Stáhněte si registrační klíč. Budete ho potřebovat při instalaci zprostředkovatele. Klíč je platný pět dní od jeho vygenerování.

    ![Nastavení zdroje](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Nainstalujte na oba servery VMM zprostředkovatele. Na hostitele Hyper-V není potřeba nic explicitně instalovat.

### <a name="install-the-azure-site-recovery-provider"></a>Instalace zprostředkovatele Azure Site Recovery

1. Na obou serverech VMM spusťte instalační soubor zprostředkovatele. Pokud je nástroj VMM nasazený v clusteru, postupujte při počáteční instalaci následovně:
    -  Nainstalujte zprostředkovatele na aktivní uzel a dokončete instalaci, aby se server VMM zaregistroval v trezoru.
    - Potom zprostředkovatele nainstalujte na ostatní uzly. Na všech uzlech clusteru musí být stejná verze zprostředkovatele.
2. Instalační program provede několik kontrol požadavků a požádá o oprávnění k zastavení služby VMM. Služba VMM se po dokončení instalace automaticky restartuje. Pokud zprostředkovatele instalujete na clusteru VMM, budete vyzváni k zastavení role Cluster.
3. V části **Microsoft Update** můžete vyjádřit výslovný souhlas s tím, že se aktualizace zprostředkovatele nainstalovaly v souladu s vašimi zásadami Microsoft Update.
4. V části **Instalace** přijměte nebo změňte výchozí umístění instalace a klikněte na **Nainstalovat**.
5. Po dokončení instalace klikněte na **Zaregistrovat**, aby se server zaregistroval v trezoru.

    ![Umístění instalace](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. V poli **Název trezoru** ověřte název trezoru, ve kterém bude server zaregistrovaný. Klikněte na **Další**.
7. V části **Připojení k proxy serveru** určete, jak se zprostředkovatel, který běží na serveru VMM, připojí k Azure.
   - Můžete určit, že se zprostředkovatel má k internetu připojit přímo nebo přes proxy server. Podle potřeby zadejte nastavení proxy serveru.
   - Pokud používáte proxy server, vytvoří se automaticky účet VMM RunAs (DRAProxyAccount) pomocí zadaných přihlašovacích údajů proxy serveru. Proxy server nakonfigurujte tak, aby tento účet bylo možné úspěšně ověřit. Nastavení účtu RunAs můžete upravovat v konzole VMM v části **Nastavení** > **Zabezpečení** > **Účty Spustit jako**.
   - Restartujte službu VMM, aby se projevily změny.
8. V části **Registrační klíč** vyberte klíč, který jste si stáhli a zkopírovali na server VMM.
9. Nastavení šifrování není pro tento scénář podstatné. 
10. Do pole **Název serveru** zadejte popisný název, který bude identifikovat server VMM v trezoru. V clusteru zadejte název role clusteru VMM.
11. V části **Synchronizovat metadata cloudu** vyberte, zda chcete synchronizovat metadata pro všechny cloudy na serveru VMM. Tuto akci stačí na každém serveru provést pouze jednou. Pokud nechcete synchronizovat všechny cloudy, nechte toto nastavení nezaškrtnuté. Synchronizaci jednotlivých cloudů můžete nastavit ve vlastnostech cloudu v konzole VMM.
12. Dokončete proces kliknutím na **Další**. Po registraci načte Site Recovery metadata ze serveru VMM. Server se v trezoru zobrazí v části **Servery** > **Servery VMM**.
13. Jakmile se server zobrazí v trezoru, v části **Zdroj** > **Připravit zdroj** vyberte server VMM a cloud, ve kterém se nachází hostitel Hyper-V. Pak klikněte na **OK**.


## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte cílový server VMM a cloud:

1. Klikněte na **Připravit infrastrukturu** > **Cíl** a vyberte cílový server VMM.
2. Zobrazí se cloudy VMM synchronizované se Site Recovery. Vyberte cílový cloud.

   ![Cíl](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

Než začnete, ujistěte se, že všichni hostitelé, na které se zásada vztahuje, používají stejný operační systém. Pokud hostitelé používají různé verze Windows Serveru, budete potřebovat několik zásad replikace.

1. Pokud chcete vytvořit novou zásadu replikace, klikněte na **Připravit infrastrukturu** > **Nastavení replikace** > **+Vytvořit a přidružit**.
2. V části **Vytvořit a přidružit zásady** zadejte název zásady. Typ zdroje i cíle musí být **Hyper-V**.
3. V poli **Verze hostitele Hyper-V** vyberte, který operační systém běží na hostiteli.
4. V polích **Typ ověřování** a **Port ověřování** určete, jak se ověřuje provoz mezi primárním hostitelským serverem Hyper-V a hostitelským serverem Hyper-V pro obnovení.
    - Pokud nemáte funkční prostředí Kerberos, vyberte **Certifikát**. Azure Site Recovery automaticky nakonfiguruje certifikáty pro ověřování protokolu HTTPS. Není potřeba cokoli dělat ručně.
    - Ve výchozím nastavení se v branách Windows Firewall na hostitelských serverech Hyper-V otevřou porty 8083 a 8084 (pro certifikáty).
    - Pokud vyberete **Kerberos**, ke vzájemnému ověřování hostitelských serverů se použije lístek Kerberos. Protokol Kerberos je relevantní pouze pro hostitelské servery Hyper-V ve Windows Serveru 2012 R2 nebo novějším.
1. V části **Frekvence kopírování** určete, jak často chcete replikovat rozdílová data po počáteční replikaci (každých 30 sekund, 5 minut nebo 15 minut).
2. V části **Uchování bodu obnovení** zadejte (v hodinách), jak dlouhý bude interval uchovávání dat pro jednotlivé body obnovení. Replikované počítače je možné obnovit do libovolného bodu v rámci tohoto intervalu.
3. V části **Frekvence pořizování snímků konzistentních vzhledem k aplikacím** určete, jak často (1–12 hodin) se mají vytvářet body obnovení obsahující snímky konzistentní vzhledem k aplikacím. Technologie Hyper-V používá dva typy snímků:
    - **Standardní snímek:** Poskytuje přírůstkový snímek celého virtuálního počítače.
    - **Snímek konzistentní vzhledem k aplikacím:** Pořizuje snímek dat aplikací ve virtuálním počítači v daném okamžiku. Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace budou při pořízení snímku v konzistentním stavu. Povolení snímků konzistentních vzhledem k aplikacím má vliv na výkon aplikací na zdrojových virtuálních počítačích. Nastavte hodnotu menší než počet dalších bodů obnovení, které nakonfigurujete.
4. V části **Komprese přenosu dat** určete, jestli se mají přenášená data replikace komprimovat.
5. Výběrem možnosti **Odstranit repliku virtuálního počítače** určíte, že se má v případě, že zakážete ochranu zdrojového virtuálního počítače, odstranit jeho replika. Pokud povolíte toto nastavení, pak se v případě, že zakážete ochranu zdrojového virtuálního počítače, tento virtuální počítač odebere z konzoly Site Recovery, z konzoly VMM se odeberou nastavení Site Recovery pro VMM a odstraní se příslušná replika.
6. Pokud provádíte replikaci přes síť, v části **Metoda počáteční replikace** určete, jestli se má spustit počáteční replikace, nebo ji naplánujte. Pokud chcete snížit využití šířky pásma, měli byste ji naplánovat na dobu mimo špičky. Pak klikněte na **OK**.

     ![Zásady replikace](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Nová zásada se automaticky přidruží ke cloudu VMM. V části **Zásady replikace** klikněte na **OK**. 


## <a name="enable-replication"></a>Povolení replikace

1. Klikněte na **Replikovat aplikaci** > **Zdroj**. 
2. V části **Zdroj** vyberte server VMM a cloud, ve kterém jsou umístění hostitelé Hyper-V, které chcete replikovat. Pak klikněte na **OK**.
3. V části **Cíl** zkontrolujte sekundární server VMM a cloud.
4. V části **Virtuální počítače** vyberte ze seznamu virtuální počítače, které chcete chránit.


Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po dokončení úlohy **Dokončení ochrany** bude počáteční replikace dokončená a virtuální počítač bude připravený na převzetí služeb při selhání.

## <a name="next-steps"></a>Další postup

[Spuštění postupu zotavení po havárii](hyper-v-vmm-test-failover.md)
