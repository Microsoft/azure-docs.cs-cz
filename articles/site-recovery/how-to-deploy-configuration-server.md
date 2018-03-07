---
title: "Nasazení se konfigurační server pro obnovení po havárii VMware s Azure Site Recovery | Microsoft Docs"
description: "Tento článek popisuje, jak nasadit konfigurační server pro obnovení po havárii VMware s Azure Site Recovery"
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2018
ms.author: anoopkv
ms.openlocfilehash: f7ec02cb4723d8a4ea0847810b1a1822cf20e957
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
---
# <a name="deploy-a-configuration-server"></a>Nasazení konfiguračního serveru

Při použití se nasadit server konfigurace místní [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii virtuálních počítačů VMware a fyzické servery do Azure. Konfigurace serveru souřadnice komunikace mezi místními VMware a Azure. Spravuje taky data replikace. Tento článek vás provede kroky potřebné k nasazení konfigurační server při replikaci virtuálních počítačů VMware do Azure. [Postupujte podle tohoto článku](site-recovery-set-up-physical-to-azure.md) Pokud budete potřebovat k nastavení konfigurace serveru pro replikaci fyzický server.

## <a name="prerequisites"></a>Požadavky

Doporučujeme nasadit jako vysoce dostupný virtuální počítač VMware konfigurační server. Minimální požadavky na hardware jsou shrnuté v následující tabulce.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




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
2. V **připravit zdroj**, vyberte **+ konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte šablonu OVF pro konfigurační server.

  > [!TIP]
  Můžete stáhnout nejnovější verzi šablony serveru konfigurace přímo z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).


## <a name="import-the-template-in-vmware"></a>Import šablony do VMware


1. Přihlaste se k serveru VMware vCenter nebo hostitelů vSphere ESXi pomocí klientem VMWare vSphere.
2. Na **soubor** nabídce vyberte možnost **nasazení šablony OVF** spusťte Průvodce šablonou nasazení OVF.

     ![Šablona OVF](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. V **zvolit zdroj**, zadejte umístění stažené OVF.
4. V **podrobnosti**, vyberte **Další**.
5. V **vybrat název a složku** a **vyberte konfigurace**, přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon.
4. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
5. V části **Ready to complete** (Připraveno k dokončení):

    * Pokud chcete vytvořit virtuální počítač s výchozím nastavením, vyberte **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit).

    * Chcete-li přidat další síťové rozhraní, zrušte **zapnout po nasazení**a potom vyberte **Dokončit**. Ve výchozím nastavení je nasazena šablony konfigurace serveru s jeden síťový adaptér. Po nasazení můžete přidat další síťové adaptéry.


## <a name="add-an-additional-adapter"></a>Přidání dalšího adaptéru

Pokud chcete přidat další síťový adaptér na konfiguračním serveru, přidejte ji předtím, než zaregistrujete server v trezoru. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V **hardwaru**, vyberte **přidat** > **adaptér Ethernet**. Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť. 
4. Vyberte **Connect at power on** (Připojit při spuštění), aby se po spuštění virtuálního počítače připojila virtuální síťová karta. Potom vyberte **Další** > **Dokončit** > **OK**.
 

## <a name="register-the-configuration-server"></a>Registrace konfiguračního serveru 

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace, přihlaste se k virtuálnímu počítači jako správce.
4. Při prvním přihlášení, spustí se nástroje Konfigurace obnovení lokality Azure.
5. Zadejte název, který se použije k registraci konfigurační server pomocí Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po připojení, vyberte **přihlášení** k přihlášení k předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.
7. Nástroj provede několik konfiguračních úloh a pak restartuje počítač.
8. Přihlaste se k počítači znovu. Automaticky se spustí Průvodce konfigurací serveru správy.

### <a name="configure-settings"></a>Konfigurace nastavení

1. V Průvodci konfigurací serveru správy vyberte **nastavení připojení**. Vyberte síťový adaptér a příjem přenosů replikace a pak vyberte **Uložit**. Po dokončení konfigurace není možné toto nastavení změnit.
2. V **trezor služeb zotavení vyberte**, vyberte předplatné Azure a příslušnou skupinu zdrojů a trezoru.
3. V **nainstalovat software třetích stran**, přijměte licenční smlouvu. Vyberte **stáhněte a nainstalujte** instalace serveru MySQL.
4. Vyberte **nainstalovat VMware PowerLCI**. Ujistěte se, že všechna okna prohlížeče jsou uzavřeny před provedením tohoto kroku. Potom vyberte **pokračovat**.
5. V **ověřením konfigurace zařízení**, než budete pokračovat ověření předpokladů.
6. V **konfigurovat vCenter Server vSphere ESXi server**, zadejte plně kvalifikovaný název domény nebo IP adresa serveru vCenter nebo hostitelů vSphere, kde se nachází virtuální počítače, které chcete replikovat. Zadejte port, kterém naslouchá server a popisný název serveru VMware server v trezoru.
7. Zadejte pověření pro použití konfigurační server pro připojení k serveru VMware. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **přidat**a potom vyberte **pokračovat**.
8. V **konfigurace virtuálního počítače pověření**, zadejte uživatelské jméno a heslo používané můžete automaticky nainstalovat službu Azure Site Recovery Mobility na počítačích, pokud je zapnutá replikace. Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce. U počítačů s Linuxem zadejte údaje superuživatele.
9. Vyberte **dokončení konfigurace** k dokončení registrace. 
10. Jakmile bude registrace dokončená, zkontrolujte Azure Portal, jestli je ve vybraném trezoru na stránce **Zdroj** uvedený konfigurační server a VMware server. Potom vyberte **OK** ke konfiguraci nastavení cíle.


## <a name="troubleshoot-deployment-issues"></a>Řešení problémů při nasazování

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Další postup

Nastavit zotavení po havárii [virtuální počítače VMware](tutorial-vmware-to-azure.md) do Azure.
