---
title: Kurz konfigurace prostředí Azure-SSIS Integration runtime pro připojení k virtuální síti
description: Přečtěte si, jak připojit prostředí Azure-SSIS Integration runtime a připojit se k virtuální síti Azure.
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
ms.openlocfilehash: ef2bd2fa9badc7c299099b647e1f67c50e997024
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91292298"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Konfigurace prostředí Azure-služba SSIS (SQL Server Integration Services) (SSIS) Integration runtime (IR) pro připojení k virtuální síti

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto kurzu najdete základní kroky pro použití Azure Portal ke konfiguraci prostředí Azure-služba SSIS (SQL Server Integration Services) (IR) Integration runtime (SSIS) pro připojení k virtuální síti.

Postup je následující:

- Nakonfigurujte virtuální síť.
- Připojte Azure-SSIS IR k virtuální síti z portálu Azure Data Factory.

## <a name="prerequisites"></a>Požadavky

- **Prostředí Azure-SSIS Integration runtime**. Pokud nemáte prostředí Azure-SSIS Integration runtime, před zahájením [zajistěte Azure-SSIS Integration runtime v Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) .

- **Oprávnění uživatele**. Uživatel, který vytváří Azure-SSIS IR, musí mít [přiřazení role](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) aspoň u prostředku Azure Data Factory s jednou z následujících možností:

    - Použijte integrovanou roli Přispěvatel sítě. Tato role se dodává s oprávněním _Microsoft. \* Network/_ , které má mnohem větší rozsah, než je nutné.
    - Vytvořte vlastní roli, která bude obsahovat jenom potřebná oprávnění _Microsoft. Network/virtualNetworks/ \* /Join/Action_ . Pokud chcete pro Azure-SSIS IR při připojení k virtuální síti Azure Resource Manager využít vlastní veřejné IP adresy, uveďte také v roli oprávnění _Microsoft. Network/publicIPAddresses/*/JOIN/Action_ .

- **Virtuální síť:**

    - Pokud nemáte virtuální síť, [vytvořte virtuální síť pomocí Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

    - Ujistěte se, že skupina prostředků virtuální sítě může vytvářet a odstraňovat určité síťové prostředky Azure.
    
        Azure-SSIS IR musí vytvořit určité síťové prostředky ve stejné skupině prostředků jako virtuální síť. Mezi tyto prostředky patří:
        - Nástroj pro vyrovnávání zatížení Azure s názvem * \<Guid> -azurebatch-cloudserviceloadbalancer*
        - Skupina zabezpečení sítě s názvem * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Veřejná IP adresa Azure s názvem-azurebatch-cloudservicepublicip
    
        Tyto prostředky budou vytvořeny při spuštění Azure-SSIS IR. Po zastavení Azure-SSIS IR se odstraní. Chcete-li zabránit zablokování Azure-SSIS IR zastavovat, nepoužívejte tyto síťové prostředky v jiných prostředcích.

    - Ujistěte se, že nemáte [Zámek prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) u skupiny prostředků nebo předplatného, ke kterému virtuální síť patří. Pokud nakonfigurujete zámek jen pro čtení nebo odstranění, začnete Azure-SSIS IR a zastavuje se, nebo přestane reagovat.

    - Ujistěte se, že nemáte přiřazení Azure Policy, které brání vytvoření následujících prostředků v rámci skupiny prostředků nebo předplatného, ke kterému patří virtuální síť:
        - Microsoft. Network/LoadBalancers
        - Microsoft. Network/NetworkSecurityGroups

- V tomto kurzu se nevztahují na scénáře **Konfigurace sítě** :
    - Pokud přenesete vlastní veřejné IP adresy pro Azure-SSIS IR.
    - Pokud používáte vlastní server DNS (Domain Name System).
    - Použijete-li v podsíti skupinu zabezpečení sítě (NSG).
    - Pokud používáte Azure ExpressRoute nebo trasu definovanou uživatelem (UDR).
    - Pokud používáte vlastní Azure-SSIS IR.
    
    Další informace najdete v [konfiguraci virtuální sítě](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Konfigurace virtuální sítě

Pomocí Azure Portal nakonfigurujte virtuální síť předtím, než se pokusíte připojit k Azure-SSIS IR.

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují rozhraní Data Factory.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Vyberte **Další služby**. Vyfiltrujte a vyberte **virtuální sítě**.

1. Vyfiltrujte a vyberte svou virtuální síť v seznamu.

1. Na stránce **virtuální síť** vyberte **vlastnosti**.

1. Pro zkopírování ID prostředku pro virtuální síť do schránky vyberte tlačítko Kopírovat pro **ID prostředku** . Uloží ID ze schránky ve OneNotu nebo v souboru.

1. V nabídce vlevo vyberte **podsítě**.

    - Ujistěte se, že vybraná podsíť má dostatek dostupného adresního prostoru, aby bylo možné Azure-SSIS IR použít. Nechte dostupné IP adresy aspoň po dobu nejméně dvojnásobku čísla uzlu IR. Azure rezervuje některé IP adresy v každé podsíti. Tyto adresy se nedají použít. První a poslední IP adresa podsítí jsou vyhrazené pro shodu protokolu a pro služby Azure se používají tři další adresy. Další informace najdete v tématu [jakákoli omezení používání IP adres v těchto podsítích](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) .
    - Nevybírejte GatewaySubnet pro nasazení Azure-SSIS IR. Je vyhrazený pro brány virtuální sítě.
    - Nepoužívejte podsíť, která je exkluzivně zabraná jinými službami Azure (například SQL Database spravované instance SQL, App Service atd.).

1. Ověřte, jestli je poskytovatel Azure Batch zaregistrovaný v předplatném Azure, které má virtuální síť. Nebo zaregistrujte poskytovatele Azure Batch. Pokud už máte účet Azure Batch v předplatném, předplatné se zaregistruje pro Azure Batch. (Pokud vytvoříte Azure-SSIS IR na portálu Data Factory, poskytovatel Azure Batch se automaticky zaregistruje.)

   1. V Azure Portal v nabídce vlevo vyberte **předplatná**.

   1. Vyberte své předplatné.

   1. Na levé straně vyberte **poskytovatelé prostředků**a potvrďte, že **Microsoft.Batch** je registrovaný poskytovatel.

   ![Potvrzení stavu "zaregistrován"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud v seznamu nevidíte **Microsoft.Batch** a zaregistrujete ho, vytvořte ve svém předplatném [prázdný Azure Batch účet](../batch/batch-account-create-portal.md) . Můžete ho odstranit později.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Připojit Azure-SSIS IR k virtuální síti

Po nakonfigurování Azure Resource Manager virtuální sítě nebo klasické virtuální sítě se můžete připojit k Azure-SSIS IR k virtuální síti:

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují rozhraní Data Factory.

1. V [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **Datové továrny**. Pokud v nabídce nevidíte **Datové továrny** , vyberte **Další služby**a potom v části **Intelligence + Analytics** vyberte **Datové továrny**.

   ![Seznam datových továren](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Vyberte datovou továrnu pomocí Azure-SSIS IR v seznamu. Zobrazí se Domovská stránka pro datovou továrnu. Vyberte dlaždici **Author & monitor** . Na samostatné kartě se zobrazí uživatelské rozhraní Data Factory.

   ![Domovská stránka objektu pro vytváření dat](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. V uživatelském rozhraní Data Factory přepněte na kartu **Upravit** , vyberte **připojení**a přepněte na kartu **prostředí Integration runtime** .

   ![Karta prostředí Integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Pokud je váš Azure-SSIS IR spuštěný, vyberte v seznamu **Integration runtime** ve sloupci **Akce** tlačítko **zastavit** pro vaši Azure-SSIS IR. Azure-SSIS IR dokud ho nezastavíte, nemůžete ho upravovat.

   ![Zastavit IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. V seznamu **prostředí Integration runtime** vyberte ve sloupci **Akce** tlačítko **Upravit** pro Azure-SSIS IR.

   ![Úprava prostředí Integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. V instalačním panelu prostředí Integration runtime se v sekcích **Obecné nastavení** a **nastavení SQL** pokračujte výběrem tlačítka **Další** .

1. V části **Upřesnit nastavení** :
   1. Vyberte **virtuální síť, ke které se má Azure-SSIS Integration runtime připojit, umožněte ADF vytvořit určité síťové prostředky a volitelně uveďte vlastní statické veřejné IP adresy** .

   1. V poli **předplatné**vyberte předplatné Azure, které má vaši virtuální síť.

   1. Pro **umístění**je vybrané stejné umístění prostředí Integration runtime.

   1. Jako **typ**vyberte typ virtuální sítě: classic nebo Azure Resource Manager. Doporučujeme vybrat Azure Resource Manager virtuální síť, protože klasické virtuální sítě budou brzy zastaralé.

   1. V poli **název virtuální**sítě vyberte název vaší virtuální sítě. Měla by být stejná jako ta, která se používá pro SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí SQL s privátním koncovým bodem pro hostování SSISDB. Nebo by měl být stejný jako připojený k vaší místní síti. V opačném případě může být libovolná virtuální síť, která bude obsahovat vlastní statické veřejné IP adresy pro Azure-SSIS IR.

   1. Jako **název podsítě**vyberte název podsítě pro virtuální síť. Měl by být stejný jako používaný pro SQL datbase s koncovými body služby virtuální sítě pro hostování SSISDB. Nebo by měla být jiná podsíť než ta, která se používá pro vaši spravovanou instanci SQL s privátním koncovým bodem pro hostování SSISDB. V opačném případě může to být jakákoli podsíť, která přináší vlastní statické veřejné IP adresy pro Azure-SSIS IR.

   1. Vyberte **ověření virtuální**sítě. Pokud je ověření úspěšné, vyberte **pokračovat**.

   ![Rozšířené nastavení virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. V části **Souhrn** Zkontrolujte všechna nastavení Azure-SSIS IR. Pak vyberte **aktualizovat**.

1. Spusťte Azure-SSIS IR výběrem tlačítka **Start** ve sloupci **akce** pro vaši Azure-SSIS IR. Spuštění Azure-SSIS IR, který se připojuje k virtuální síti, trvá přibližně 20 až 30 minut.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).
