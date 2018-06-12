---
title: Nasazení se konfigurační server pro obnovení po havárii VMware s Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak nasadit konfigurační server pro obnovení po havárii VMware s Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/06/2018
ms.author: raynew
ms.openlocfilehash: 3e7a9196d928fb8a5d12647e1916b046ebedd261
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267505"
---
# <a name="deploy-a-configuration-server"></a>Nasazení konfiguračního serveru

Při použití se nasadit server konfigurace místní [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii virtuálních počítačů VMware a fyzické servery do Azure. Konfigurace serveru souřadnice komunikace mezi místními VMware a Azure. Spravuje taky data replikace. Tento článek vás provede kroky potřebné k nasazení konfigurační server při replikaci virtuálních počítačů VMware do Azure. [Postupujte podle tohoto článku](physical-azure-set-up-source.md) Pokud budete potřebovat k nastavení konfigurace serveru pro replikaci fyzický server.

## <a name="prerequisites"></a>Požadavky

Doporučujeme nasadit jako vysoce dostupný virtuální počítač VMware konfigurační server. V následující tabulce jsou shrnuté požadavky na konfiguraci serveru.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]



### <a name="prepare-for-mysql-installation"></a>Příprava pro instalaci databáze MySQL

MySQL musí být nainstalován na konfiguračním serveru. To provedete pomocí jedné z následujících metod:

- Let – Site Recovery, stáhněte a nainstalujte ho při, když spustí Průvodce konfigurací serveru správy. Není nutné provádět žádnou zvláštní akci.
- Ručně stáhnout MySQL a umístěte jej do složky C:\Temp\ASRSetup. Spusťte instalaci. Site Recovery rozpozná, že je nainstalováno, když spustíte průvodce.
- Ručně stáhnout MySQL a umístěte jej do složky C:\Temp\ASRSetup. Když spustíte Průvodce bude Najděte instalační soubor a nainstalujte ji z tohoto umístění. 


## <a name="capacity-planning"></a>Plánování kapacity

Nastavení velikosti požadavky pro konfiguraci serveru závisí na potenciální míry změny dat. Tuto tabulku použijte jako vodítko.

| **CPU** | **Paměť** | **Velikost disku mezipaměti** | **Míry změny dat** | **Chráněné počítače** |
| --- | --- | --- | --- | --- |
| 8 Vcpu (2 sockets * @ 2,5 GHz 4 jádra) |16 GB |300 GB |500 GB nebo méně |Replikovat počítače méně než 100. |
| 12 Vcpu (2 sockets * @ 2,5 GHz 6 jader) |18 GB |600 GB |500 GB až 1 TB |Replikovat počítače 100 150. |
| 16 Vcpu (2 sockets * @ 2,5 GHz 8 jader) |32 GB |1 TB |1 TB 2 TB |Replikovat počítače 150 až 200. |


Pokud replikujete virtuální počítače VMware, další informace o [informace o plánování kapacity](/site-recovery-plan-capacity-vmware.md). Spustit [nástroj pro nasazení planner](site-recovery-deployment-planner.md) pro replikaci VMWare.



## <a name="download-the-template"></a>Stažení šablony

Site Recovery poskytuje ke stažení šablony k nastavení konfigurace serveru jako vysoce dostupný virtuální počítač VMware. 

1. V daném trezoru klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte šablonu OVF pro konfigurační server.

  > [!TIP]
  Můžete stáhnout nejnovější verzi šablony serveru konfigurace přímo z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).


## <a name="import-the-template-in-vmware"></a>Import šablony do VMware


1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **File** (Soubor) vyberte **Deploy OVF Template** (Nasadit šablonu OVF). Tím spustíte průvodce nasazením šablony OVF.

     ![Šablona OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. V **zvolit zdroj**, zadejte umístění stažené OVF.
4. V **podrobnosti**, vyberte **Další**.
5. V **vybrat název a složku** a **vyberte konfigurace**, přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon.
4. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
5. V části **Ready to complete** (Připraveno k dokončení):

    * Pokud chcete vytvořit virtuální počítač s výchozím nastavením, vyberte **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit).

    * Chcete-li přidat další síťové rozhraní, zrušte **zapnout po nasazení**a potom vyberte **Dokončit**. Ve výchozím nastavení se šablona konfiguračního serveru nasadí s jedním síťovým adaptérem. Po nasazení můžete přidat další síťové adaptéry.


## <a name="add-an-additional-adapter"></a>Přidání dalšího adaptéru

Pokud chcete přidat další síťový adaptér na konfiguračním serveru, přidejte ji předtím, než zaregistrujete server v trezoru. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V části **Hardware** vyberte **Add** (Přidat) > **Ethernet Adapter** (Adaptér sítě Ethernet). Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť. 
4. Vyberte **Connect at power on** (Připojit při spuštění), aby se po spuštění virtuálního počítače připojila virtuální síťová karta. Potom vyberte **Další** > **Dokončit** > **OK**.
 

## <a name="register-the-configuration-server"></a>Registrace konfiguračního serveru 

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.
4. Při prvním přihlášení se spustí nástroj pro konfiguraci služby Azure Site Recovery.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.
7. Nástroj provede několik konfiguračních úloh a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Automaticky se spustí průvodce správou konfiguračního serveru.

### <a name="configure-settings"></a>Konfigurace nastavení

1. V Průvodci konfigurací serveru správy vyberte **nastavení připojení**. Vyberte síťový adaptér a příjem přenosů replikace a pak vyberte **Uložit**. Po dokončení konfigurace není možné toto nastavení změnit.
2. V části **Vyberte trezor služby Recovery Services** vyberte své předplatné Azure a příslušnou skupinu prostředků a trezor.
3. V části **Nainstalovat software třetí strany** přijměte licenční smlouvu. Nainstalujte MySQL v souladu s [metoda používáte k instalaci MySQL](#prepare-for-mysql-installation).
4. Vyberte **nainstalovat VMware PowerLCI**. Ujistěte se, že všechna okna prohlížeče jsou uzavřeny před provedením tohoto kroku. Potom vyberte **Pokračovat**.
5. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
6. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, kterém naslouchá server a popisný název serveru VMware server v trezoru.
7. Zadejte přihlašovací údaje, které bude konfigurační server používat pro připojení k serveru VMware. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **Přidat** a pak **Pokračovat**.
8. V **konfigurace virtuálního počítače pověření**, zadejte uživatelské jméno a heslo používané můžete automaticky nainstalovat službu Azure Site Recovery Mobility na počítačích, pokud je zapnutá replikace. Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce. U počítačů s Linuxem zadejte údaje superuživatele.
9. Vyberte **dokončení konfigurace** k dokončení registrace. 
10. Jakmile bude registrace dokončená, zkontrolujte Azure Portal, jestli je ve vybraném trezoru na stránce **Zdroj** uvedený konfigurační server a VMware server. Pak vyberte **OK** a nakonfigurujte nastavení cíle.


## <a name="upgrade-the-configuration-server"></a>Upgrade na konfiguračním serveru

Konfigurační server upgradovat na nejnovější verzi, přečtěte si téma kroky uvedené v [sem](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)


## <a name="troubleshoot-deployment-issues"></a>Řešení problémů při nasazování

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Další postup

Nastavit zotavení po havárii [virtuální počítače VMware](vmware-azure-tutorial.md) do Azure.
