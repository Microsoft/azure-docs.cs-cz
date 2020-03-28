---
title: Příprava Azure na zotavení po havárii technologie Hyper-V pomocí Azure Site Recovery
description: Zjistěte, jak připravit Azure na zotavení po havárii místních virtuálních virtuálních počítačích Hyper-V pomocí Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ef623b95e104b485c6bfc8b2f489afeca436d81e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084175"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Příprava prostředků Azure pro zotavení po havárii technologie Hyper-V

 [Azure Site Recovery](site-recovery-overview.md) pomáhá kontinuitu podnikání a zotavení po havárii (BCDR) tím, že udržuje obchodní aplikace spuštěné během plánovaných a neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.

Tento kurz je první z řady, která popisuje, jak nastavit zotavení po havárii pro místní virtuální počítačové servery Hyper-V.

> [!NOTE]
> Navrhujeme kurzy, které ukazují nejjednodušší cestu nasazení pro scénář. Tyto kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Další informace naleznete v části "Jak" pro každý odpovídající scénář.

Tento kurz ukazuje, jak připravit součásti Azure, když chcete replikovat místní virtuální počítače (Hyper-V) do Azure. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Ověření oprávnění účtu Azure k replikaci
> * Vytvořte účet úložiště Azure, ve kterém jsou uloženy image replikovaných počítačů.
> * Vytvořte trezor služby Recovery Services, ve kterém jsou uložena metadata a informace o konfiguraci pro virtuální počítače a další součásti replikace.
> * Nastavit síť Azure. Když se virtuální počítače Azure vytvoří po převzetí služeb při selhání, připojí se k této síti.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.

## <a name="sign-in"></a>Přihlášení

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Ověření oprávnění k účtu

Pokud jste právě vytvořili bezplatný účet Azure, jste správcem tohoto předplatného. Pokud nejste správce, spolupracujte se správcem a přiřaďte potřebná oprávnění. Pokud chcete povolit replikaci pro nový virtuální počítač, musíte mít následující oprávnění:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zápis do vybraného účtu úložiště

K dokončení těchto úkolů by měl být vašemu účtu přiřazena předdefinovaná role přispěvatele virtuálního počítače. Chcete-li spravovat operace obnovení webu v trezoru, měl by být vašemu účtu přiřazena předdefinovaná role Přispěvatel obnovení webu.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Bitové kopie replikovaných počítačů jsou uložené v úložišti Azure. Virtuální počítače Azure se vytvoří z úložiště, když převezmete služby při selhání z místní lokality do Azure. Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.

1. V nabídce [Portál Azure](https://portal.azure.com) vyberte **Vytvořit** > účet**úložiště prostředků** > **– objekt blob, soubor, tabulka, fronta**.
2. V části **Vytvořit účet úložiště** zadejte název účtu.  Název, který zvolíte, musí být jedinečný v rámci Azure, musí být dlouhý 3 až 24 znaků a používat jenom malá písmena a čísla. Pro účely tohoto kurzu použijte **contosovmsacct1910171607**.
3. V části **Model nasazení** vyberte **Resource Manager**.
4. V **naturáliích účtu**vyberte **Úložiště (pro obecné účely v1)**. Nevybírejte úložiště objektů blob.
5. V části **Replikace** vyberte výchozí redundanci úložiště **Geograficky redundantní úložiště jen pro čtení**. Nastavení Zabezpečené přenosy ponechejte jako zakázané.
6. V části **Výkon** vyberte **Standard**. Dále v **části Access tier**vyberte výchozí možnost Možnost i **aktivní**.
7. V **části Předplatné**zvolte předplatné, ve kterém chcete vytvořit nový účet úložiště.
8. V části **Skupina prostředků** zadejte název nové skupiny prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. V tomto kurzu použijte **ContosoRG**.
9. V **části Poloha**zvolte geografickou polohu svého účtu úložiště. Pro účely tohoto kurzu použijte **západní Evropu**.
10. Vyberte **Vytvořit** a vytvořte účet úložiště.

   ![vytvořit účet úložiště](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

1. Na webu Azure Portal vyberte **+Vytvořit prostředek**a pak vyhledejte Azure Marketplace pro služby obnovení.
2. Vyberte **možnost Zálohování a obnovení sítě (OMS).** Dále na stránce **Zálohování a Obnovení webu** vyberte **Vytvořit**.
1. V **trezoru služeb obnovení > název**zadejte popisný název pro identifikaci trezoru. Pro účely tohoto kurzu použijte **ContosoVMVault**.
2. Ve **skupině Prostředků**vyberte existující skupinu prostředků nebo vytvořte novou. Pro účely tohoto kurzu použijte **contosoRG**.
3. V **části Umístění**vyberte oblast, ve které má být úschovna umístěna. Pro účely tohoto kurzu použijte **západní Evropu**.
4. Chcete-li rychle přistupovat k úschovně z řídicího panelu, vyberte **připnout k řídicímu panelu** > **Vytvořit**.

![Vytvoření nového trezoru](./media/tutorial-prepare-azure/new-vault-settings.png)

Nový trezor se zobrazí na **řídicím panelu** > **Všechny prostředky**a na hlavní stránce **úschovny služby Recovery Services.**

## <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Když se po převzetí služeb při selhání vytvoří z úložiště virtuální počítače Azure, připojí se do této sítě.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Sítě** > **Virtuální síť**. Jako model nasazení nechte vybraný Resource Manager.
2. V části **Název** zadejte název sítě. Název musí být v rámci skupiny prostředků Azure jedinečný. V tomto kurzu použijte **ContosoASRnet**.
3. Zadejte skupinu prostředků, ve které chcete síť vytvořit. Pro účely tohoto kurzu použijte existující skupinu prostředků **contosoRG**.
4. V **rozsahu Adresa**zadejte jako rozsah sítě **hodnotu 10.0.0.0/24.** Pro tuto síť není žádná podsíť.
5. V části **Předplatné** vyberte předplatné, ve kterém chcete síť vytvořit.
6. V **lokalitě**zvolte **Západní Evropa**. Síť musí být ve stejné oblasti jako trezor Služeb zotavení.
7. Ponechte výchozí možnosti základní ochrany DDoS bez koncového bodu služby v síti.
8. Vyberte **Vytvořit**.

![Vytvoření virtuální sítě](media/tutorial-prepare-azure/create-network.png)

Vytvoření virtuální sítě trvá několik sekund. Po vytvoření se zobrazí na řídicím panelu portálu Azure.

## <a name="useful-links"></a>Užitečné odkazy

Projdeme si tyto možnosti:
- [Sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Spravované disky](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Příprava místní infrastruktury Hyper-V pro zotavení po havárii do Azure](hyper-v-prepare-on-premises-tutorial.md)
