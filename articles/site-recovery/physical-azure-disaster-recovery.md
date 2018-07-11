---
title: Nastavení zotavení po havárii do Azure pro fyzické na místních serverech pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro místní Windows a Linuxem servery se službou Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 173423c1a578500a990d6a7b43017d06ea96f6e7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917451"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Nastavení zotavení po havárii do Azure pro místní fyzické servery

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii místních fyzických Windows a Linux serverů do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení Azure a místní požadavky
> * Vytvoření trezoru služby Recovery Services pro Site Recovery 
> * Nastavit zdroj a cíl replikace prostředí
> * Vytvoření zásady replikace
> * Povolení replikace pro server

[Kontrola architektury](concepts-hyper-v-to-azure-architecture.md) pro tento scénář zotavení po havárii.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Ujistěte se, že rozumíte [komponent a architektury](physical-azure-architecture.md) pro tento scénář.
- Zkontrolujte [požadavky na podporu](vmware-physical-secondary-support-matrix.md) pro všechny komponenty.
- Ujistěte se, že servery, které chcete replikovat v souladu s [požadavky na virtuální počítač Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Příprava Azure. Budete potřebovat předplatné Azure, virtuální síť Azure a účet úložiště.
- Příprava účtu pro automatickou instalaci služby Mobility na každém serveru, který chcete replikovat.

Než začnete, Všimněte si, že:

- Po převzetí služeb při selhání do Azure nelze provést fyzických serverů zpět do místních fyzických počítačů. Můžete předat jenom zpět do virtuálních počítačů VMware. 
- V tomto kurzu nastaví zotavení po havárii fyzického serveru do Azure s nastavením nejjednodušší. Pokud chcete informace o dalších možnostech, přečtěte si naše postupy průvodce:
    - Nastavit [zdroj replikace](physical-azure-set-up-source.md), včetně konfiguračního serveru Site Recovery.
    - Nastavení [cíle replikace](physical-azure-set-up-target.md)
    - Konfigurace [zásady replikace](vmware-azure-set-up-replication.md) a [povolení replikace](vmware-azure-enable-replication.md)


### <a name="set-up-an-azure-account"></a>Nastavte si účet Azure

Získejte Microsoft [účtu Azure](http://azure.microsoft.com/).

- Můžete začít s [bezplatnou zkušební verzí](https://azure.microsoft.com/pricing/free-trial/).
- Další informace o [cenách za Site Recovery](site-recovery-faq.md#pricing)a získat [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/site-recovery/).
- Zjistit, které [oblasti jsou podporovány](https://azure.microsoft.com/pricing/details/site-recovery/) pro Site Recovery.

### <a name="verify-azure-account-permissions"></a>Ověření oprávnění účtu Azure

Ujistěte se, že váš účet Azure má oprávnění pro replikaci virtuálních počítačů do Azure.

- Zkontrolujte [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) je potřeba replikovat počítače do Azure.
- Ověřte a upravte [přístupu podle rolí](../role-based-access-control/role-assignments-portal.md) oprávnění. 



### <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Nastavení [síť Azure](../virtual-network/quick-create-portal.md).

- Virtuální počítače Azure jsou umístěny v této síti při jejich vytvoření po převzetí služeb při selhání.
- Síť musí být ve stejné oblasti jako trezor služby Recovery Services


## <a name="set-up-an-azure-storage-account"></a>Nastavení účtu úložiště Azure

Nastavení [účtu služby Azure storage](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- Site Recovery replikuje místní počítače do služby Azure storage. Virtuální počítače Azure se vytvoří z úložiště, když dojde k převzetí služeb při selhání.
- Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.
- Účet úložiště může být standardní nebo [premium](../virtual-machines/windows/premium-storage.md).
- Pokud nastavíte účet premium, bude potřebovat další účet standard dat protokolu.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na každém serveru, který chcete replikovat, musí se nainstalovat službu Mobility. Site Recovery tuto službu nainstaluje automaticky při aktivaci replikace pro server. Automatická instalace musíte připravit účet, který Site Recovery použije pro přístup k serveru.

- Můžete použít doménový nebo místní účet
- Pro Windows virtuální počítače, pokud nepoužíváte doménový účet, zakažte vzdálené řízení přístupu uživatele na místním počítači. Chcete-li to provést, v registru pod **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, přidejte položku DWORD **LocalAccountTokenFilterPolicy**, s hodnotou 1.
- Pokud chcete přidat položku registru zakázat nastavení z rozhraní příkazového řádku, zadejte:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Pro Linux musí být použít účet root na zdrojovém serveru s Linuxem.


## <a name="create-a-vault"></a>Vytvoření trezoru

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Výběr cíle ochrany

Vyberte, co k replikaci a replikaci pro.

1. Klikněte na **Trezory služby Recovery Services** > trezor.
2. V nabídce prostředku klikněte na **Site Recovery** > **Příprava infrastruktury** > **Cíl ochrany**.
3. V **cíl ochrany**vyberte **do Azure** > **nevirtualizované/jiné**.

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nastavit konfigurační server zaregistrujete v trezoru a zjištění virtuálních počítačů.

1. Klikněte na tlačítko **Site Recovery** > **připravit infrastrukturu** > **zdroj**.
2. Pokud nemáte k dispozici konfigurační server, klikněte na tlačítko **+ konfigurační server**.
3. V **přidat Server**, zkontrolujte, že **konfigurační Server** se zobrazí v **typ serveru**.
4. Stáhněte si instalační soubor sjednocené instalace Site Recovery.
5. Stáhněte registrační klíč trezoru. Budete potřebovat při spuštění sjednocené instalace. Klíč je platný pět dní od jeho vygenerování.

   ![Nastavení zdroje](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrace konfiguračního serveru v trezoru

Než začnete, postupujte následovně: 

#### <a name="verify-time-accuracy"></a>Zkontrolujte časové přesnosti
Na počítači serveru konfigurace, ujistěte se, že jsou systémové hodiny synchronizované s [časovým serverem](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). By měl odpovídat. Instalace může selhat, pokud je 15 minut v popředí nebo pozadí.

#### <a name="verify-connectivity"></a>Ověření připojení
Ujistěte se, že tento počítač přístup k těmto adresám URL podle vašeho prostředí: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Pravidla brány firewall založená na adresu IP musí umožňovat komunikaci pro všechny adresy URL Azure, které jsou uvedené výše přes port HTTPS (443). Pro zjednodušení a omezit rozsahy IP adres, se doporučuje, že filtrování adres URL udělat.

- **Komerční IP adresy** -povolit [Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/confirmation.aspx?id=41653)a port HTTPS (443). Povolte rozsahy IP adres pro oblast Azure svého předplatného na podporu AAD, zálohování, replikace a adresy URL úložiště.  
- **Státní správy IP adres** -povolit [Azure Government rozsahů IP adres Datacentra](https://www.microsoft.com/en-us/download/details.aspx?id=57063)a port HTTPS (443) pro všechny oblasti USGov (Virginie, Texas, Arizona a Iowa) pro podporu AAD, zálohování, replikace a adresy URL úložiště.  

#### <a name="run-setup"></a>Spusťte instalační program
Spuštění sjednocené instalace jako místní správce, chcete-li nainstalovat konfigurační server. Procesový server a hlavní cílový server jsou také nainstalované ve výchozím nastavení na konfiguračním serveru.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Po dokončení registrace konfiguračního serveru se zobrazí na **nastavení** > **servery** stránky v trezoru.

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit infrastrukturu** > **Cíl** a vyberte předplatné Azure, které chcete použít.
2. Zadejte model nasazení cíl.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

   ![Cíl](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Chcete-li vytvořit novou zásadu replikace, klikněte na tlačítko **infrastruktura Site Recovery** > **zásady replikace** > **+ zásada replikace**.
2. V **vytvoření zásady replikace**, zadejte název zásady.
3. V **prahová hodnota cíle bodu obnovení**, zadejte cíle (RPO) limit bodů obnovení. Tato hodnota určuje, jak často jsou vytvořeny body obnovení pro data. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
4. V **uchování bodu obnovení**, určete, jak dlouho (v hodinách) je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu. Je pro počítače replikované do služby premium storage a 72 hodin pro úložiště úrovně standard podporuje až uchování 24 hodin.
5. V **frekvence snímků konzistentní vzhledem k**, zadat jak často (v minutách) se vytvoří body obnovení obsahující snímky konzistentní s aplikací. Kliknutím na tlačítko **OK** vytvořte zásadu.

    ![Zásady replikace](./media/physical-azure-disaster-recovery/replication-policy.png)


Tato zásada se automaticky přidruží ke konfiguračnímu serveru. Ve výchozím nastavení se pro navrácení služeb po obnovení automaticky vytvoří zásada párování. Například, pokud má zásada replikace **rep-policy** pak zásady navrácení služeb po obnovení **rep-policy-failback** se vytvoří. Tato zásada se nepoužije, dokud nespustíte navrácení služeb po obnovení z Azure.

## <a name="enable-replication"></a>Povolení replikace

Povolte replikaci pro každý server.

- Site Recovery nainstaluje službu Mobility po povolení replikace.
- Když povolíte replikaci pro server, může trvat 15 minut nebo i delší dobu se změny projeví a objeví na portálu.

1. Klikněte na **Replikovat aplikaci** > **Zdroj**.
2. V části **Zdroj** vyberte konfigurační server.
3. V **typ počítače**vyberte **fyzické počítače**.
4. Vyberte procesový server (konfigurační server). Pak klikněte na **OK**.
5. V **cílové**, vyberte předplatné a skupinu prostředků, ve kterém chcete vytvořit virtuální počítače Azure po převzetí služeb při selhání. Vyberte model nasazení, který chcete použít v Azure (classic nebo resource Manager).
6. Vyberte účet úložiště Azure, který chcete použít pro replikaci dat. 
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se po převzetí služeb při selhání vytvoří.
8. Výběrem možnosti **Nakonfigurovat pro vybrané počítače** použijte nastavení sítě pro všechny počítače, které jste vybrali pro ochranu. Vyberte **Nakonfigurovat později** a vyberte síť Azure pro konkrétní počítač. 
9. V **fyzické počítače**a klikněte na tlačítko **+ fyzický počítač**. Zadejte název a IP adresu. Vyberte operační systém počítače, který chcete replikovat. Trvá několik minut, než se servery, které zjišťují a uvádějí. 
10. V **vlastnosti** > **konfigurovat vlastnosti**, vyberte účet, který použije procesový server při automatické instalaci služby Mobility na počítači.
11. V části **Nastavení replikace** > **Konfigurace nastavení replikace** zkontrolujte, jestli je vybraná správná zásada replikace. 
12. Klikněte na **Povolit replikaci**. Průběh úlohy **Povolení ochrany** můžete sledovat tady: **Nastavení** > **Úlohy** > **Úlohy Site Recovery**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.


K monitorování serverů, které přidáte, můžete zkontrolovat čas posledního zjištění u nich **konfigurační servery** > **poslední kontakt**. Chcete-li přidat počítače bez čekání naplánovaný čas zjišťování, zvýrazněte konfigurační server (neklikejte na něj) a klikněte na tlačítko **aktualizovat**.

## <a name="next-steps"></a>Další postup

[Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md).
