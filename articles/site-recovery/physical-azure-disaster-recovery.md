---
title: Nastavit zotavení po havárii do Azure pro fyzický místní servery s Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro místní systém Windows a Linux serverů se službou Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/23/2018
ms.author: raynew
ms.openlocfilehash: a4c83e495e269cdca35844a699d714b55cf1f500
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643307"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Nastavit zotavení po havárii do Azure pro místní fyzických serverů

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii v místě fyzických Windows a Linux serverů do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení Azure a místní požadavky
> * Vytvoření trezoru služeb zotavení pro Site Recovery 
> * Nastavit zdroje a cíle replikace prostředí
> * Vytvoření zásady replikace
> * Povolení replikace pro server

[Zkontrolujte architekturu](concepts-hyper-v-to-azure-architecture.md) pro tento scénář obnovení po havárii.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Ujistěte se, že rozumíte [architektura a komponenty](physical-azure-architecture.md) pro tento scénář.
- Zkontrolujte [požadavky na podporu](vmware-physical-secondary-support-matrix.md) pro všechny komponenty.
- Ujistěte se, že servery, které chcete replikovat vyhovují [požadavky virtuálního počítače Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Příprava Azure. Potřebujete předplatné Azure, virtuální sítě Azure a účet úložiště.
- Připravte účet automatická instalace služby Mobility na každém serveru, který chcete replikovat.

Než začnete, Všimněte si, že:

- Po převzetí služeb při selhání do Azure nemůže se nezdařilo fyzických serverů zpět na místní fyzických počítačů. Lze pouze neúspěšné zpátky na virtuální počítače VMware. 
- V tomto kurzu nastaví zotavení po havárii fyzického serveru do Azure s nastavením nejjednodušší. Pokud chcete další informace o další možnosti, přečíst naše postupy příručky:
    - Nastavit [zdroj replikace](physical-azure-set-up-source.md), včetně konfigurace serveru pro obnovení lokality.
    - Nastavit [cílem replikace](physical-azure-set-up-target.md).
    - Konfigurace [zásady replikace](vmware-azure-set-up-replication.md), a [povolit replikaci](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Nastavit účet Azure

Získat Microsoft [účet Azure](http://azure.microsoft.com/).

- Můžete začít s [bezplatnou zkušební verzí](https://azure.microsoft.com/pricing/free-trial/).
- Další informace o [cenách za Site Recovery](site-recovery-faq.md#pricing)a získat [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/site-recovery/).
- Zjistit, které [oblasti jsou podporované](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery.

### <a name="verify-azure-account-permissions"></a>Ověřte oprávnění pro účet Azure

Ujistěte se, že váš účet Azure má oprávnění pro replikaci virtuálních počítačů do Azure.

- Zkontrolujte [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) je potřeba replikovat počítače do Azure.
- Ověřte a upravte [přístupu podle rolí](../role-based-access-control/role-assignments-portal.md) oprávnění. 



### <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Nastavení [síť Azure](../virtual-network/quick-create-portal.md).

- Virtuální počítače Azure jsou umístěny v této síti, když jste vytvořili po převzetí služeb při selhání.
- Síť musí být ve stejné oblasti jako trezor služeb zotavení


## <a name="set-up-an-azure-storage-account"></a>Nastavení účtu úložiště Azure

Nastavení [účtu úložiště Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- Site Recovery replikuje místní počítače do úložiště Azure. Virtuální počítače Azure jsou vytvořené z úložiště po převzetí služeb při selhání.
- Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.
- Účet úložiště může být standardní nebo [premium](../virtual-machines/windows/premium-storage.md).
- Pokud jste nastavili prémiového účtu taky musíte další standardní účet pro data protokolu.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Služba Mobility musí být nainstalovaná na každém serveru, který chcete replikovat. Site Recovery tato služba nainstaluje automaticky při povolení replikace pro server. Pokud chcete automaticky nainstalovat, je nutné připravit účtu, který Site Recovery bude používat pro přístup k serveru.

- Můžete použít domény nebo místní účet
- Pro virtuální počítače Windows, pokud nepoužíváte účet domény, zakažte řízení vzdáleného přístupu uživatele v místním počítači. Chcete-li to provést, v registru podle **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, přidejte položku DWORD **LocalAccountTokenFilterPolicy**, s hodnotou 1.
- Chcete-li přidat položku registru zakázat s nastavením rozhraní příkazového řádku, zadejte:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Pro systémy Linux musí být účet root na zdrojovém serveru Linux.


## <a name="create-a-vault"></a>Vytvoření trezoru

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Vyberte cíl ochrany

Vyberte obsah k replikaci a jeho replikace.

1. Klikněte na **Trezory služby Recovery Services** > trezor.
2. V nabídce prostředku klikněte na **Site Recovery** > **Příprava infrastruktury** > **Cíl ochrany**.
3. V **cíl ochrany**, vyberte **do Azure** > **není virtualizované/jiné**.

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nastavení konfigurace serveru, registrace v trezoru a vyhledání virtuálních počítačů.

1. Klikněte na tlačítko **lokality obnovení** > **Příprava infrastruktury** > **zdroj**.
2. Pokud nemáte konfigurační server, klikněte na tlačítko **+ konfigurační server**.
3. V **přidat Server**, zkontrolujte, zda **konfigurační Server** se zobrazí v **typ serveru**.
4. Stáhněte instalační soubor nástroje Unified instalace nástroje Site Recovery.
5. Stáhněte registrační klíč trezoru. Tuto funkci potřebujete po spuštění Unified instalace. Klíč je platný pět dní od jeho vygenerování.

   ![Nastavení zdroje](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Zaregistrujte konfigurační server v trezoru

Než začnete, postupujte takto: 

- Ujistěte se, jestli se systémové hodiny synchronizované se na počítač serveru konfigurace, [čas serveru](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Měla by se shodovat. Pokud je 15 minut před nebo za instalace může selhat.
- Zkontrolujte, zda že má počítač přístup tyto adresy URL:       [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

- Pravidla brány firewall založená na adresu IP by měl povolit komunikaci s Azure.
- Povolte [Rozsahy IP adres datového centra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) a port HTTPS (443).
- Povolte rozsahy IP adres pro oblast Azure svého předplatného a pro oblast Západní USA (používá se pro řízení přístupu a správu identit).

Spusťte instalaci Unified jako místní správce a instalace konfigurační server. Procesový server a hlavní cílový server jsou také nainstalované ve výchozím nastavení na konfiguračním serveru.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Po dokončení registrace se konfigurační server se zobrazí na **nastavení** > **servery** stránky v úložišti.


## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit infrastrukturu** > **Cíl** a vyberte předplatné Azure, které chcete použít.
2. Zadejte model nasazení cíl.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

   ![Cíl](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Chcete-li vytvořit novou zásadu replikace, klikněte na tlačítko **infrastruktura Site Recovery** > **zásady replikace** > **+ zásady replikace**.
2. V **vytvořit zásadu replikace**, zadejte název zásady.
3. V **prahovou hodnotu RPO**, zadejte cíle (RPO) limitu bodu obnovení. Tato hodnota určuje, jak často jsou vytvořeny body obnovení data. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
4. V **uchování bodu obnovení**, určete, jak dlouho (v hodinách) je okno uchování pro každý bod obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu. Až 24 hodin uchování je podporována pro počítače, na které se replikují do úložiště úrovně premium a 72 hodin, než standardní úložiště.
5. V **frekvence snímkování konzistentní aplikace vzhledem**, určit, jak často (v minutách) budou vytvořeny body obnovení obsahující snímky konzistentní s aplikacemi. Kliknutím na tlačítko **OK** vytvořte zásadu.

    ![Zásady replikace](./media/physical-azure-disaster-recovery/replication-policy.png)


Tato zásada se automaticky přidruží ke konfiguračnímu serveru. Ve výchozím nastavení se pro navrácení služeb po obnovení automaticky vytvoří zásada párování. Například, pokud je zásada replikace **rep zásad** pak zásadu navrácení služeb po obnovení **rep. zásady navrácení** je vytvořena. Tato zásada se nepoužije, dokud nespustíte navrácení služeb po obnovení z Azure.

## <a name="enable-replication"></a>Povolení replikace

Povolení replikace pro každý server.

- Site Recovery bude instalace služby Mobility, pokud je zapnutá replikace.
- Pokud povolíte replikaci pro server, může trvat 15 minut nebo déle, než změny vstoupí v platnost a zobrazovat na portálu.

1. Klikněte na **Replikovat aplikaci** > **Zdroj**.
2. V části **Zdroj** vyberte konfigurační server.
3. V **počítač typ**, vyberte **fyzických počítačů**.
4. Vyberte procesový server (konfigurační server). Pak klikněte na **OK**.
5. V **cíl**, vyberte předplatné a skupině prostředků, ve kterém chcete vytvořit virtuální počítače Azure po převzetí služeb při selhání. Vyberte model nasazení, který chcete použít v Azure (klasický nebo prostředek management).
6. Vyberte účet úložiště Azure, který chcete použít pro replikaci dat. 
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se po převzetí služeb při selhání vytvoří.
8. Výběrem možnosti **Nakonfigurovat pro vybrané počítače** použijte nastavení sítě pro všechny počítače, které jste vybrali pro ochranu. Vyberte **Nakonfigurovat později** a vyberte síť Azure pro konkrétní počítač. 
9. V **fyzické počítače**a klikněte na tlačítko **+ fyzický počítač**. Zadejte název a IP adresu. Vyberte operační systém počítače, který chcete replikovat. Trvá několik minut pro servery, aby byly zjištěny a uvedené. 
10. V **vlastnosti** > **konfigurovat vlastnosti**, vyberte účet, který se použije pro automaticky instalaci služby Mobility na počítači procesní server.
11. V části **Nastavení replikace** > **Konfigurace nastavení replikace** zkontrolujte, jestli je vybraná správná zásada replikace. 
12. Klikněte na **Povolit replikaci**. Průběh úlohy **Povolení ochrany** můžete sledovat tady: **Nastavení** > **Úlohy** > **Úlohy Site Recovery**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.


Ke sledování serverů, které přidáte, můžete zkontrolovat, čas poslední zjištěné u nich **konfigurační servery** > **poslední obraťte se na**. Chcete-li přidat počítače bez čekání naplánovaný čas zjišťování, zvýrazněte konfigurační server (nemáte klikněte na něj) a klikněte na tlačítko **aktualizovat**.

## <a name="next-steps"></a>Další postup

[Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md).
