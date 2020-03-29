---
title: Kurz konfigurace runtime integrace Azure-SSIS pro připojení k virtuální síti
description: Zjistěte, jak se připojit k runtime integrace Azure-SSIS do virtuální sítě Azure.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 065610a9de4898d012cef8a16849c09a81f0774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841093"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Konfigurace modulu runtime integrace služby Azure-SQL Server Integration Services (SSIS) pro připojení k virtuální síti

Tento kurz obsahuje základní kroky pro použití portálu Azure ke konfiguraci modulu runtime integrace Azure-SQL Server Integration Services (SSIS) pro připojení k virtuální síti.

Tyto kroky zahrnují:

- Konfigurace virtuální sítě.
- Připojte infračervený přenos Azure-SSIS k virtuální síti z portálu Azure Data Factory.

## <a name="prerequisites"></a>Požadavky

- **Doba runtime integrace Azure-SSIS**. Pokud nemáte runtime integrace Azure-SSIS, [zřídit runtime integrace Azure-SSIS v Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) před zahájením.

- **Oprávnění uživatele**. Uživatel, který vytvoří indiční zařízení Azure-SSIS Ir, musí mít [přiřazení role](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) alespoň v prostředku Azure Data Factory s jednou z možností níže:

    - Použijte předdefinovanou roli přispěvatele sítě. Tato role je dodávána s oprávněním _Microsoft.Network/,\* _ které má mnohem větší rozsah, než je nutné.
    - Vytvořte vlastní roli, která obsahuje pouze potřebná oprávnění _Microsoft.Network/virtualNetworks/\*/join/action._ Pokud také chcete přenést vlastní veřejné IP adresy pro Azure-SSIS IR při jeho připojení k virtuální síti Azure Resource Manager, zahrňte do role také oprávnění _Microsoft.Network/publicIPAddresses/*/join/action._

- **Virtuální síť**.

    - Pokud nemáte virtuální síť, [vytvořte virtuální síť pomocí portálu Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

    - Ujistěte se, že skupina prostředků virtuální sítě můžete vytvořit a odstranit určité síťové prostředky Azure.
    
        Azure-SSIS Ir potřebuje vytvořit určité síťové prostředky ve stejné skupině prostředků jako virtuální síť. Mezi tyto zdroje patří:
        - Nástroj pro vyrovnávání zatížení Azure s názvem * \<Guid>-azurebatch-cloudserviceloadbalancer*
        - Skupina zabezpečení sítě s názvem\<* Guid>-azurebatch-cloudservicenetworksecuritygroup
        - Veřejná IP adresa Azure s názvem -azurebatch-cloudservicepublicip
    
        Tyto prostředky se vytvoří při spuštění infračerveného zařízení Azure-SSIS. Budou odstraněny, když se zastaví vaše infračervená infračervená infračervená zručná zařízení Azure-SSIS. Chcete-li zabránit blokování azure-SSIS IR zastavení, nepoužívejte znovu tyto síťové prostředky v jiných prostředků.

    - Ujistěte se, že nemáte [zámek prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) na skupinu prostředků nebo odběr, do kterého patří virtuální síť. Pokud nakonfigurujete zámek jen pro čtení nebo odstranění, spuštění a zastavení zařízení Azure-SSIS IR se nezdaří nebo přestane reagovat.

    - Ujistěte se, že nemáte zásady Azure, které brání vytváření následujících prostředků v rámci skupiny prostředků nebo předplatného, do kterého virtuální síť patří:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Níže uvedené scénáře **konfigurace sítě** nejsou zahrnuty v tomto kurzu:
    - Pokud si přinesete vlastní veřejné IP adresy pro Azure-SSIS IR.
    - Pokud používáte vlastní server DNS (Domain Name System).
    - Pokud v podsíti používáte skupinu zabezpečení sítě (NSG).
    - Pokud používáte Azure ExpressRoute nebo uživatelem definovanou trasu (UDR).
    - Pokud používáte vlastní Azure-SSIS IR.
    
    Další informace najdete v [konfiguraci virtuální sítě](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Konfigurace virtuální sítě

Než se pokusíte připojit infračervený přenos Azure-SSIS, použijte portál Azure ke konfiguraci virtuální sítě.

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují uživatelské nastavení datové továrny.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Vyberte **Další služby**. Filtrovat a vybrat **virtuální sítě**.

1. Filtrujte a vyberte virtuální síť v seznamu.

1. Na stránce **Virtuální síť** vyberte **Vlastnosti**.

1. Vyberte tlačítko kopírovat pro **ID prostředku,** chcete-li zkopírovat ID prostředku pro virtuální síť do schránky. Uložte ID ze schránky ve OneNotu nebo v souboru.

1. V levé nabídce vyberte **Podsítě**.

    - Ujistěte se, že vybraná podsíť má dostatek dostupného adresního prostoru pro použití infračerveného prostoru Azure-SSIS. Ponechte dostupné IP adresy alespoň pro dvojnásobné číslo infračerveného uzlu. Azure si vyhrazuje některé IP adresy v rámci každé podsítě. Tyto adresy nelze použít. První a poslední IP adresy podsítí jsou vyhrazeny pro shodu protokolu a pro služby Azure se používají další tři adresy. Další informace naleznete v tématu [Existují nějaká omezení pro používání IP adres v těchto podsítích?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Nevybírejte GatewaySubnet pro nasazení infračerveného počítače Azure-SSIS. Je určen pro brány virtuální sítě.
    - Nepoužívejte podsíť, která je výhradně obsazena jinými službami Azure (například instance spravované databází SQL, služba App Service a tak dále).

1. Ověřte, že poskytovatel Azure Batch je registrovaný v předplatném Azure, které má virtuální síť. Nebo zaregistrujte zprostředkovatele Azure Batch. Pokud už máte účet Azure Batch ve vašem předplatném, vaše předplatné je registrované pro Azure Batch. (Pokud vytvoříte Indiovní počítač Azure-SSIS na portálu Data Factory, poskytovatel Azure Batch se automaticky zaregistruje za vás.)

   1. Na webu Azure Portal v yberte v levé nabídce **Předplatná**.

   1. Vyberte své předplatné.

   1. Na levé straně vyberte **zprostředkovatele prostředků**a potvrďte, že **Microsoft.Batch** je registrovaným poskytovatelem.

   ![Potvrzení stavu "Registrovaní"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud v seznamu nevidíte **Microsoft.Batch,** zaregistrujte ho, vytvořte ve svém [předplatném prázdný účet Azure Batch.](../batch/batch-account-create-portal.md) Později jej můžete odstranit.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Připojení infračerveného zařízení Azure-SSIS k virtuální síti

Po konfiguraci virtuální sítě Azure Resource Manager nebo klasické virtuální sítě se můžete k virtuální síti připojit k infračervenému zařízení Azure-SSIS:

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují uživatelské nastavení datové továrny.

1. Na [portálu Azure](https://portal.azure.com)v yberte v levé nabídce **položku Datové továrny**. Pokud v nabídce nevidíte **továrny dat,** vyberte **Další služby**a pak v části **INTELLIGENCE + ANALYTICS** vyberte **Datové továrny**.

   ![Seznam datových továren](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Vyberte svou datovou továrnu s Azure-SSIS IR v seznamu. Zobrazí se domovská stránka datové továrny. Vyberte dlaždici **Author & Monitor.** Uvidíte udata factory uj.

   ![Domovská stránka objektu pro vytváření dat](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. V uzdu Factory dat přepněte na kartu **Úpravy,** vyberte **Připojení**a přepněte na kartu **Integrační runtimes.**

   ![Karta "Integrační běhové zaběhu"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Pokud je vaše infračervená infračervená infračervená induiční služba Azure-SSIS spuštěná, vyberte v seznamu **Infikované běhy** integrace ve sloupci **Akce** tlačítko **Stop** pro infračervený přenos Azure-SSIS. Azure-SSIS IR nemůžete upravovat, dokud ho nezastavíte.

   ![Zastavit infračervený přenos](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. V seznamu **Integrační běhové časy** ve sloupci **Akce** vyberte tlačítko **Upravit** pro infračervený přenos Azure-SSIS.

   ![Úprava prostředí runtime integrace](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Na panelu nastavení modulu runtime integrace přejděte v částech **Obecné nastavení** a **Nastavení SQL** výběrem tlačítka **Další.**

1. V části **Upřesnit nastavení:**
   1. Zaškrtněte **políčko Vybrat virtuální síť pro prostředí Integrace Azure-SSIS, chcete-li se připojit, povolte adf k vytvoření určitých síťových prostředků a volitelně přenést vlastní statické veřejné IP adresy.**

   1. V **části Předplatné**vyberte předplatné Azure, které má vaši virtuální síť.

   1. V **části Umístění**je vybráno stejné umístění integračního běhu.

   1. V **části Typ**vyberte typ virtuální sítě: klasický nebo Správce prostředků Azure. Doporučujeme vybrat virtuální síť Azure Resource Manager, protože klasické virtuální sítě se brzy zastaralá.

   1. V **části Název virtuální sítě**vyberte název virtuální sítě. Měl by to být stejný, který se používá pro váš server Azure SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB. Nebo by to mělo být stejné připojení k místní síti. V opačném případě může být libovolná virtuální síť, která přinese vlastní statické veřejné IP adresy pro Azure-SSIS IR.

   1. V **části Název podsítě**vyberte název podsítě pro virtuální síť. Měl by být stejný jako ten, který se používá pro váš server Azure SQL Database s koncovými body služby virtuální sítě pro hostování SSISDB. Nebo by měla být jiná podsíť než ta, která se používá pro vaši spravovanou instanci s privátním koncovým bodem pro hostování SSISDB. V opačném případě může být libovolná podsíť pro přenos vlastních statických veřejných IP adres pro Azure-SSIS IR.

   1. Vyberte **ověření virtuální sítě**. Pokud je ověření úspěšné, vyberte **pokračovat**.

   ![Rozšířené nastavení virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. V části **Souhrn** zkontrolujte všechna nastavení pro azure-SSIS IR. Pak vyberte **Aktualizovat**.

1. Spusťte infračervený přenos Azure-SSIS tak, že vyberete tlačítko **Start** ve **sloupci Akce** pro infračervený přenos Azure-SSIS. Spuštění zařízení Azure-SSIS IR, které se připojuje k virtuální síti, trvá přibližně 20 až 30 minut.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [připojení azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).
