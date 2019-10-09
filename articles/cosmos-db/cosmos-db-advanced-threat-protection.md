---
title: Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB
description: Přečtěte si, jak Azure Cosmos DB poskytuje šifrování neaktivních dat a jejich implementaci.
author: rkarlin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 2b12d639e734502113b6afdd7250fca6a520c687
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176020"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB

Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Azure Cosmos DB nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby, i když není odborník na zabezpečení a integruje se se systémy centrálního monitorování zabezpečení.

Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. Tyto výstrahy zabezpečení jsou integrovány do [Azure Security Center](https://azure.microsoft.com/services/security-center/)a jsou také odesílány prostřednictvím e-mailu správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak tyto hrozby prozkoumat a opravit.

> [!NOTE]
>
> * Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB je aktuálně dostupná jenom pro rozhraní SQL API.
> * Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB není v současnosti dostupná v oblastech cloudu Azure pro státní správu a svrchované oblasti.

V případě úplného šetření výstrah zabezpečení doporučujeme povolit [protokolování diagnostiky v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), které zapisuje operace do samotné databáze, včetně operací CRUD na všech dokumentech, kontejnerech a databázích.

## <a name="set-up-advanced-threat-protection"></a>Nastavení rozšířené ochrany před internetovými útoky

### <a name="set-up-atp-using-the-portal"></a>Nastavení ATP pomocí portálu

1. Spusťte Azure Portal v [https://portal.azure.com](https://portal.azure.com/).

2. Z účtu Azure Cosmos DB v nabídce **Nastavení** vyberte **Rozšířené zabezpečení**.

    ![Nastavení ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. V okně **Upřesnit konfiguraci zabezpečení** :

    * Klikněte na možnost **Rozšířená ochrana před internetovými útoky** a nastavte ji na **zapnuto**.
    * Kliknutím na **Uložit** uložte nové nebo aktualizované zásady rozšířené ochrany před internetovými útoky.   

### <a name="set-up-atp-using-rest-api"></a>Nastavení ATP pomocí REST API

Pomocí příkazů rozhraní REST API můžete vytvořit, aktualizovat nebo získat nastavení rozšířené ochrany před internetovými útoky pro určitý účet Azure Cosmos DB.

* [Rozšířená ochrana před internetovými útoky – vytvořit](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Rozšířená ochrana před internetovými útoky – získání](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Nastavení ATP pomocí Azure PowerShell

Použijte následující rutiny PowerShellu:

* [Povolit rozšířenou ochranu před internetovými útoky](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Získat rozšířenou ochranu před internetovými útoky](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Zakázat rozšířenou ochranu před internetovými útoky](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Používání šablon Azure Resource Manager

Pomocí šablony Azure Resource Manager můžete nastavit Cosmos DB s povolenou rozšířenou ochranou hrozeb.
Další informace najdete v tématu [Vytvoření účtu CosmosDB s využitím rozšířené ochrany před internetovými útoky](https://azure.microsoft.com/en-us/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="using-azure-policy"></a>Použití Azure Policy

K povolení rozšířené ochrany před internetovými útoky pro Cosmos DB použijte Azure Policy.

1. Spusťte stránku Azure **Policy-definitions** a vyhledejte zásadu **nasazení rozšířené ochrany před internetovými útoky pro Cosmos DB** .

    ![Zásady hledání](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Klikněte na zásady **nasadit rozšířenou ochranu před internetovými útoky pro CosmosDB** a pak klikněte na **přiřadit**.

    ![Vybrat předplatné nebo skupinu](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. V poli **obor** klikněte na tři tečky, vyberte předplatné Azure nebo skupinu prostředků a pak klikněte na **Vybrat**.

    ![Stránka definice zásad](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Zadejte další parametry a klikněte na **přiřadit**.

## <a name="manage-atp-security-alerts"></a>Spravovat výstrahy zabezpečení ATP

Při Azure Cosmos DB výskytu anomálií aktivit se aktivuje výstraha zabezpečení s informacemi o podezřelé události zabezpečení. 

 Z Azure Security Center můžete zkontrolovat a spravovat aktuální [výstrahy zabezpečení](../security-center/security-center-alerts-overview.md).  Kliknutím na konkrétní výstrahu v [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) zobrazíte možné příčiny a doporučené akce pro prošetření a zmírnění potenciální hrozby. Následující obrázek ukazuje příklad podrobností výstrahy, které jsou k dispozici v Security Center.

 ![Podrobnosti hrozby](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

E-mailové oznámení se také pošle s podrobnostmi výstrahy a doporučenými akcemi. Na následujícím obrázku vidíte příklad e-mailu s výstrahou.

 ![Podrobnosti upozornění](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Výstrahy ATP Cosmos DB

 Pokud chcete zobrazit seznam výstrah generovaných při monitorování účtů Azure Cosmos DB, přečtěte si část [Cosmos DB výstrahy](../security-center/security-center-alerts-data-services.md#cosmos-db) v dokumentaci k Security Center.

## <a name="next-steps"></a>Další kroky

* Další informace o [protokolování diagnostiky v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal)
* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
