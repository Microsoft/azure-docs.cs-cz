---
title: Kurz nastavení zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
description: V tomto kurzu nastavíte zotavení po havárii pro virtuální počítače Azure do jiné oblasti Azure pomocí služby Site Recovery.
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 90527ad39055e438e4970ad4686f204f72d20cd2
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394050"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>Kurz: nastavení zotavení po havárii pro virtuální počítače Azure

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii pro virtuální počítače Azure pomocí [Azure Site Recovery](site-recovery-overview.md). V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Ověření nastavení a oprávnění Azure
> * Příprava virtuálních počítačů, které chcete replikovat
> * Vytvoření trezoru Služeb zotavení
> * Povolení replikace virtuálních počítačů

Pokud povolíte replikaci pro virtuální počítač, aby se nastavilo zotavení po havárii, na virtuálním počítači se nainstaluje rozšíření služby Site Recovery mobility a registruje se Azure Site Recovery. Během replikace se zápisy na disk virtuálních počítačů odesílají do účtu úložiště mezipaměti ve zdrojové oblasti. Data se odesílají z umístění do cílové oblasti a body obnovení se generují z dat. Při převzetí služeb při selhání virtuálního počítače během zotavení po havárii se k obnovení virtuálního počítače v cílové oblasti použije bod obnovení.

> [!NOTE]
> Kurzy poskytují pokyny s nejjednodušším výchozím nastavením. Pokud chcete nastavit zotavení po havárii virtuálního počítače Azure s přizpůsobeným nastavením, přečtěte si [Tento článek](azure-to-azure-how-to-enable-replication.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem:

- [Zkontrolujte podporované oblasti](azure-to-azure-support-matrix.md#region-support). Zotavení po havárii pro virtuální počítače Azure můžete nastavit mezi kteroukoli ze dvou oblastí ve stejné zeměpisné oblasti.
- Potřebujete jeden nebo několik virtuálních počítačů Azure. Ověřte, zda jsou virtuální počítače se [systémem Windows](azure-to-azure-support-matrix.md#windows) nebo [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) podporovány.
- Projděte si požadavky na [výpočetní](azure-to-azure-support-matrix.md#replicated-machines---compute-settings)prostředky, [úložiště](azure-to-azure-support-matrix.md#replicated-machines---storage)a [sítě](azure-to-azure-support-matrix.md#replicated-machines---networking) virtuálních počítačů.
- V tomto kurzu se předpokládá, že virtuální počítače nejsou šifrované. Pokud chcete nastavit zotavení po havárii pro šifrované virtuální počítače, [postupujte podle pokynů v tomto článku](azure-to-azure-how-to-enable-replication-ade-vms.md).

## <a name="check-azure-settings"></a>Kontrolovat nastavení Azure

Ověřte oprávnění a nastavení v cílové oblasti.

### <a name="check-permissions"></a>Kontrolovat oprávnění

Váš účet Azure potřebuje oprávnění k vytvoření trezoru Recovery Services a k vytvoření virtuálních počítačů v cílové oblasti.

- Pokud jste právě vytvořili bezplatné předplatné Azure, jste správcem účtu a nemusíte dělat nic dalšího.
- Pokud nejste správcem, ve spolupráci se správcem aplikace Získejte potřebná oprávnění.
    - **Vytvoření trezoru** : oprávnění správce nebo vlastníka v předplatném. 
    - **Správa operací Site Recovery v trezoru** : předdefinovaná role Azure pro *Site Recovery přispěvatele* .
    - **Vytvoření virtuálních počítačů Azure v cílové oblasti** : buď předdefinovaná role *Přispěvatel virtuálních počítačů* , nebo specifická oprávnění pro:
        - Vytvoření virtuálního počítače ve vybrané virtuální síti
        - Zápis do účtu služby Azure Storage.
        - Zápis na disk spravovaný službou Azure.

### <a name="verify-target-settings"></a>Ověřit nastavení cíle

Když při převzetí služeb při selhání ze zdrojové oblasti dojde k obnovení, vytvoří se virtuální počítače v cílové oblasti. 

Ověřte, jestli má vaše předplatné dostatek prostředků v cílové oblasti. Musíte být schopni vytvořit virtuální počítače s velikostmi, které odpovídají virtuálním počítačům ve zdrojové oblasti. Při nastavování zotavení po havárii Site Recovery pro cílový virtuální počítač vybírá stejnou velikost (nebo nejbližší možnou velikost).


## <a name="prepare-vms"></a>Příprava virtuálních počítačů

Ujistěte se, že virtuální počítače mají odchozí připojení a nejnovější kořenové certifikáty. 


### <a name="set-up-vm-connectivity"></a>Nastavení připojení virtuálního počítače

Virtuální počítače, které chcete replikovat, potřebují odchozí připojení k síti.

> [!NOTE]
> Site Recovery nepodporuje připojení k síti pomocí ověřovacího proxy serveru.

#### <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte přístup k těmto adresám URL:

| **Název**                  | **Komerční**                               | **Státní správa**                                 | **Popis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. |
| Replikace               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

Pokud k řízení připojení používáte skupiny zabezpečení sítě (skupin zabezpečení sítě), vytvořte pravidla NSG založená na značkách služby, která umožňují odchozí přenosy HTTPS na port 443 pro tyto [značky služeb](../virtual-network/service-tags-overview.md#available-service-tags)(skupiny IP adres):

**Tag** | **Povolit** 
--- | ---
Značka úložiště  |Umožňuje zapisovat data z virtuálního počítače do účtu úložiště mezipaměti.   
Značka Azure AD | Umožňuje přístup ke všem IP adresám, které odpovídají službě Azure AD.   
Značka EventsHub | Umožňuje přístup k Site Recovery monitorování.  
Značka AzureSiteRecovery | Umožňuje přístup ke službě Site Recovery v libovolné oblasti.   
Značka GuestAndHybridManagement | Tuto možnost použijte, pokud chcete automaticky upgradovat Site Recoveryho agenta mobility, který běží na virtuálních počítačích povolených pro replikaci.

[Přečtěte si další informace](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) o požadovaných značkách a příkladech značek.

### <a name="verify-vm-certificates"></a>Ověření certifikátů virtuálních počítačů

Ověřte, že virtuální počítače mají nejnovější kořenové certifikáty. V opačném případě se virtuální počítač nedá zaregistrovat s Site Recovery z důvodu omezení zabezpečení.

- **Virtuální počítače s Windows** : na virtuálním počítači nainstalujte všechny nejnovější aktualizace Windows, aby se na tomto počítači používaly všechny důvěryhodné kořenové certifikáty. V odpojeném prostředí použijte své standardní procesy pro web Windows Update a aktualizace certifikátů.
- **Virtuální počítače se systémem Linux** : postupujte podle pokynů, které poskytuje distributor pro Linux, abyste získali nejnovější důvěryhodné kořenové certifikáty a seznam odvolaných certifikátů (CRL).

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Vytvořte Trezor Recovery Services v jakékoli oblasti kromě zdrojové oblasti, ze které chcete replikovat virtuální počítače.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Do vyhledávacího pole zadejte *Recovery*. V části **služby** vyberte **úložiště Recovery Services**.

    ![Hledat trezory Recovery Services](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. V **Recovery Services trezory** vyberte **Přidat**.
4. V části **vytvořit Recovery Services úložiště** –  >  **základy** vyberte předplatné, ve kterém chcete trezor vytvořit.
5. V části **Skupina prostředků** vyberte existující skupinu prostředků pro trezor nebo vytvořte novou.
6. Do pole **název trezoru** zadejte popisný název pro identifikaci trezoru.
7. V části **oblast** vyberte oblast Azure, do které se má Trezor umístit. [Ověřte podporované oblasti](https://azure.microsoft.com/pricing/details/site-recovery/).
8. Vyberte **Zkontrolovat a vytvořit**.

   ![Nastavení trezoru na stránce pro vytvoření nového trezoru](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. V **recenzi + vytvořit** vyberte **vytvořit**.

10. Zahájí se nasazení trezoru. Sledujte průběh v oznámeních.
11. Po nasazení trezoru vyberte **Připnout na řídicí panel** a uložte ho pro rychlé odkazy. Vyberte **Přejít k prostředku** a otevřete nový trezor. 
    
    ![Tlačítka pro otevření trezoru po nasazení a připnutí na řídicí panel](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Povolit Site Recovery

V nastavení trezoru vyberte **povolit Site Recovery**.

![Výběr, který povolí Site Recovery v trezoru](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>Povolení replikace

Vyberte nastavení zdroje a Povolte replikaci virtuálních počítačů. 

### <a name="select-source-settings"></a>Vybrat nastavení zdroje

1. Na stránce trezor > **Site Recovery** v části **virtuální počítače Azure** vyberte **Povolit replikaci**.

    ![Výběr pro povolení replikace pro virtuální počítače Azure](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. V části **zdrojové** >  **zdrojové umístění** vyberte zdrojovou oblast Azure, ve které jsou aktuálně spuštěné virtuální počítače.
3. V **modelu nasazení virtuálních počítačů Azure** ponechte výchozí nastavení **Správce prostředků** .
4. V části **zdrojové předplatné** vyberte předplatné, ve kterém jsou virtuální počítače spuštěné. Můžete vybrat libovolné předplatné, které je ve stejném tenantovi Azure Active Directory (AD) jako trezor.
5. V části **zdrojová skupina prostředků** vyberte skupinu prostředků, která obsahuje virtuální počítače.
6. V části **zotavení po havárii mezi zónami dostupnosti** ponechte výchozí nastavení **ne** .

     ![Nastavení zdroje](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. Vyberte **Další**.

### <a name="select-the-vms"></a>Výběr virtuálních počítačů

Site Recovery načte virtuální počítače přidružené k vybranému předplatnému nebo skupině prostředků.

1. V **Virtual Machines** vyberte virtuální počítače, které chcete povolit pro zotavení po havárii.

     ![Stránka pro výběr virtuálních počítačů pro replikaci](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. Vyberte **Další**.

### <a name="review-replication-settings"></a>Kontrola nastavení replikace

1. V **nastavení replikace** zkontrolujte nastavení. Site Recovery vytvoří výchozí nastavení/zásadu pro cílovou oblast. Pro účely tohoto kurzu používáme výchozí nastavení.
2. Vyberte **Povolit replikaci**.

    ![Stránka pro přizpůsobení nastavení a povolení replikace](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. Sledujte průběh replikace v oznámeních.

     ![Sledovat průběh v oznámeních o ](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![ úspěšné replikaci](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. Virtuální počítače, které povolíte, se zobrazí na stránce trezor > **replikované položky** .

    ![Virtuální počítač na stránce replikované položky](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili zotavení po havárii pro virtuální počítač Azure. Nyní spusťte přechod ke kontrole, že převzetí služeb při selhání funguje podle očekávání.

> [!div class="nextstepaction"]
> [Provedení postupu zotavení po havárii](azure-to-azure-tutorial-dr-drill.md)
