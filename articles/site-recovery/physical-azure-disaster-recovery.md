---
title: Nastavení zotavení po havárii fyzických místních serverů pomocí Azure Site Recovery
description: Naučte se, jak nastavit zotavení po havárii do Azure pro místní servery s Windows a Linux pomocí služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 9b05d9952628e550beae5cedc49e051936a9d633
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87927279"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Nastavení zotavení po havárii do Azure pro místní fyzické servery

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii místních fyzických serverů s Windows a Linux do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení požadavků na Azure a místní požadavky
> * Vytvoření trezoru služby Recovery Services pro Site Recovery 
> * Nastavení prostředí pro replikaci zdrojového a cílového umístění
> * Vytvoření zásady replikace
> * Povolení replikace pro server

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Ujistěte se, že rozumíte [architektuře a komponentám](physical-azure-architecture.md) pro tento scénář.
- Zkontrolujte [požadavky na podporu](vmware-physical-secondary-support-matrix.md) pro všechny komponenty.
- Ujistěte se, že servery, které chcete replikovat, jsou v rozporu s [požadavky na virtuální počítače Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Připravte Azure. Potřebujete předplatné Azure, virtuální síť Azure a účet úložiště.
- Připravte účet na automatickou instalaci služby mobility na každý server, který chcete replikovat.

Než začnete, mějte na paměti, že:

- Po převzetí služeb při selhání do Azure se fyzické servery nemůžou navrátit do místních fyzických počítačů. Navrácení služeb po obnovení do virtuálních počítačů VMware je možné. 
- V tomto kurzu se v Azure nastaví zotavení po havárii fyzického serveru do Azure s nejjednodušším nastavením. Pokud se chcete dozvědět o dalších možnostech, přečtěte si naše příručky:
    - Nastavte [zdroj replikace](physical-azure-set-up-source.md), včetně konfiguračního serveru Site Recovery.
    - Nastavení [cíle replikace](physical-azure-set-up-target.md)
    - Konfigurace [zásady replikace](vmware-azure-set-up-replication.md) a [povolení replikace](vmware-azure-enable-replication.md)


### <a name="set-up-an-azure-account"></a>Nastavení účtu Azure

Získejte účet Microsoft [Azure](https://azure.microsoft.com/).

- Můžete začít s [bezplatnou zkušební verzí](https://azure.microsoft.com/pricing/free-trial/).
- Přečtěte si o [cenách Site Recovery](site-recovery-faq.md#pricing)a získejte [podrobné informace o cenách](https://azure.microsoft.com/pricing/details/site-recovery/).
- Zjistěte, které [oblasti jsou podporované](https://azure.microsoft.com/pricing/details/site-recovery/) pro Site Recovery.

### <a name="verify-azure-account-permissions"></a>Ověření oprávnění účtu Azure

Ujistěte se, že váš účet Azure má oprávnění k replikaci virtuálních počítačů do Azure.

- Zkontrolujte [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) , která potřebujete k replikaci počítačů do Azure.
- Ověřte a upravte oprávnění [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) . 



### <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Nastavte [síť Azure](../virtual-network/quick-create-portal.md).

- Virtuální počítače Azure jsou umístěné v této síti, když se vytvoří po převzetí služeb při selhání.
- Síť by měla být ve stejné oblasti jako úložiště Recovery Services.


## <a name="set-up-an-azure-storage-account"></a>Nastavení účtu úložiště Azure

Nastavte [účet úložiště Azure](../storage/common/storage-account-create.md).

- Site Recovery replikuje místní počítače do služby Azure Storage. Virtuální počítače Azure se vytvoří z úložiště po převzetí služeb při selhání.
- Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na každém serveru, který chcete replikovat, musí být nainstalovaná služba mobility. Site Recovery tuto službu nainstaluje automaticky, když povolíte replikaci pro server. K automatické instalaci musíte připravit účet, který Site Recovery použít pro přístup k serveru.

- Můžete použít doménový nebo místní účet.
- Pokud v případě virtuálních počítačů s Windows nepoužíváte doménový účet, zakažte na místním počítači vzdálené řízení přístupu uživatele. To provedete tak, že v registru pod **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**PŘIDÁTE položku DWORD **LocalAccountTokenFilterPolicy**s hodnotou 1.
- Chcete-li přidat položku registru pro zakázání nastavení z rozhraní příkazového řádku, zadejte:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Pro Linux by měl být účet rootem na zdrojovém serveru Linux.


## <a name="create-a-vault"></a>Vytvoření trezoru

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Výběr cíle ochrany

Vyberte, co se má replikovat, a proveďte jejich replikaci do.

1. Klikněte na **Trezory služby Recovery Services** > trezor.
2. V nabídce prostředek klikněte na **Site Recovery**  >  **připravit**  >  **cíl ochrany**infrastruktury.
3. V **cíli ochrany**vyberte možnost **To Azure**  >  **nevirtualizovaný/jiný**Azure.

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nastavte konfigurační server, zaregistrujte ho v trezoru a vyhledejte virtuální počítače.

1. Klikněte na **Site Recovery**  >  **připravit**  >  **zdroj**infrastruktury.
2. Pokud nemáte konfigurační server, klikněte na **+ konfigurační server**.
3. V nástroji **Přidat server**ověřte, že se **konfigurační server** zobrazuje v **typu serveru**.
4. Stáhněte instalační soubor sjednocené instalace Site Recovery.
5. Stáhněte registrační klíč trezoru. Budete ho potřebovat při spuštění sjednocené instalace. Klíč je platný pět dní od jeho vygenerování.

   ![Snímek obrazovky zobrazující možnosti stažení instalačního souboru a registračního klíče.](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrace konfiguračního serveru v trezoru

Než začnete, udělejte toto: 

#### <a name="verify-time-accuracy"></a>Ověřit časovou přesnost
Na počítači konfiguračního serveru se ujistěte, že jsou systémové hodiny synchronizované s [časovým serverem](/windows-server/networking/windows-time-service/windows-time-service-top). Měla by odpovídat. Pokud je v popředí nebo na pozadí 15 minut, instalace může selhat.

#### <a name="verify-connectivity"></a>Ověřit připojení
Ujistěte se, že počítač má přístup k těmto adresám URL na základě vašeho prostředí: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Pravidla brány firewall založená na IP adrese by měla umožňovat komunikaci se všemi adresami URL Azure, které jsou uvedené výše přes port HTTPS (443). Pro zjednodušení a omezení rozsahu IP adres doporučujeme, abyste provedli filtrování adres URL.

- **Komerční IP adresy** – povolte [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)a port HTTPS (443). Povolte rozsahy IP adres pro oblast Azure svého předplatného, aby se podporovaly adresy URL AAD, zálohy, replikace a úložiště.  
- **IP adresy státní správy** – povoluje [rozsahy IP adres datového centra Azure Government](https://www.microsoft.com/en-us/download/details.aspx?id=57063)a port HTTPS (443) pro všechny oblasti USGov () – Virginia, Texas, Arizona a Iowa) pro podporu AAD, zálohování, replikace a adres URL úložiště.  

#### <a name="run-setup"></a>Spuštění instalace
Chcete-li nainstalovat konfigurační server, spusťte sjednocené nastavení jako místní správce. Procesový Server a hlavní cílový server jsou také nainstalovány ve výchozím nastavení na konfiguračním serveru.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]


## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **připravit**  >  **cíl**infrastruktury a vyberte předplatné Azure, které chcete použít.
2. Zadejte cílový model nasazení.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

   ![Snímek obrazovky s možnostmi pro nastavení cílového prostředí](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Pokud chcete vytvořit novou zásadu replikace, klikněte na **Site Recovery**  >  **Zásady replikace**infrastruktury  >  **a zásady replikace**.
2. V části **Vytvoření zásad replikace** zadejte název zásady.
3. V části **Prahová hodnota cíle bodu obnovení** (RPO) zadejte omezení cíle bodu obnovení. Tato hodnota určuje, jak často se budou vytvářet body obnovení dat. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
4. V části **Uchování bodu obnovení** zadejte (v hodinách), jak dlouhý je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu. Pro počítače replikované do úložiště úrovně Premium se podporuje uchování po dobu až 24 hodin. V případě úložiště úrovně Standard je to 72 hodin.
5. V nastavení **frekvence snímků konzistentní vzhledem k aplikacím**určete, jak často (v minutách) se mají vytvářet body obnovení obsahující snímky konzistentní vzhledem k aplikacím. Kliknutím na tlačítko **OK** vytvořte zásadu.

    ![Snímek obrazovky s možnostmi pro vytváření zásad replikace](./media/physical-azure-disaster-recovery/replication-policy.png)


Tato zásada se automaticky přidruží ke konfiguračnímu serveru. Ve výchozím nastavení se pro navrácení služeb po obnovení automaticky vytvoří zásada párování. Pokud je například zásada replikace zásadou pro **replikaci** , vytvoří se zásady navrácení služeb **po obnovení.** Tato zásada se nepoužije, dokud nespustíte navrácení služeb po obnovení z Azure.

## <a name="enable-replication"></a>Povolení replikace

Povolte replikaci pro každý server.

- Site Recovery nainstaluje službu mobility, když je povolená replikace.
- Pokud povolíte replikaci pro server, může trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.

1. Klikněte na **replikovat**  >  **zdroj**aplikace.
2. V části **Zdroj** vyberte konfigurační server.
3. V **typ počítače**vyberte **fyzické počítače**.
4. Vyberte procesový Server (konfigurační server). Pak klikněte na **OK**.
5. V části **cíl**vyberte předplatné a skupinu prostředků, ve které chcete vytvořit virtuální počítače Azure po převzetí služeb při selhání. Vyberte model nasazení, který chcete použít v Azure (Classic nebo Správa prostředků).
6. Vyberte účet úložiště Azure, který chcete použít pro replikaci dat. 
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se po převzetí služeb při selhání vytvoří.
8. Vyberte **nakonfigurovat pro vybrané počítače**a použijte nastavení sítě pro všechny počítače, které vyberete pro ochranu. Vyberte **Konfigurovat později** a vyberte síť Azure na jeden počítač. 
9. Na **fyzických počítačích**klikněte na **+ fyzický počítač**. Zadejte název a IP adresu. Vyberte operační systém počítače, který chcete replikovat. Zjištění a vypsání serverů trvá několik minut. 
10. V části **vlastnosti**  >  **Konfigurace vlastností**vyberte účet, který bude procesový Server používat k automatické instalaci služby mobility na počítači.
11. V **nastavení replikace**  >  **nakonfigurujte nastavení replikace**a ověřte, jestli je vybraná správná zásada replikace. 
12. Klikněte na **Povolit replikaci**. Průběh úlohy **Povolení ochrany** můžete sledovat v části **Nastavení**  >  **úlohy**  >  **Site Recovery úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený na převzetí služeb při selhání.


Chcete-li monitorovat servery, které jste přidali, můžete zjistit čas poslední zjištěné konfigurace v části **konfigurační servery**  >  **Poslední kontakt na**. Chcete-li přidat počítače bez čekání na naplánovaný čas zjišťování, zvýrazněte konfigurační server (neklikejte na něj) a klikněte na tlačítko **aktualizovat**.

## <a name="next-steps"></a>Další kroky

[Spusťte postup zotavení po havárii](tutorial-dr-drill-azure.md).
