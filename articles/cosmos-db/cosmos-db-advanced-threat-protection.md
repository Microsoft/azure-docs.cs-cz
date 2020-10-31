---
title: Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB
description: Přečtěte si, jak Azure Cosmos DB poskytuje šifrování neaktivních dat a jejich implementaci.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: d8398dbded8753bac193f973026fb63d5f1fc6b3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097715"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB (Preview)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Azure Cosmos DB nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby, i když není odborník na zabezpečení a integruje se se systémy centrálního monitorování zabezpečení.

Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. Tyto výstrahy zabezpečení jsou integrovány do  [Azure Security Center](https://azure.microsoft.com/services/security-center/)a jsou také odesílány prostřednictvím e-mailu správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak tyto hrozby prozkoumat a opravit.

> [!NOTE]
>
> * Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB je aktuálně dostupná jenom pro rozhraní SQL API.
> * Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB není v současnosti dostupná v oblastech cloudu Azure pro státní správu a svrchované oblasti.

V případě úplného šetření výstrah zabezpečení doporučujeme povolit [protokolování diagnostiky v Azure Cosmos DB](./monitor-cosmos-db.md), které zapisuje operace do samotné databáze, včetně operací CRUD na všech dokumentech, kontejnerech a databázích.

## <a name="threat-types"></a>Typy hrozeb

Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. V současné době může aktivovat následující výstrahy:

- **Přístup z neobvyklých umístění** : Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k účtu Azure Cosmos, kde se někdo připojil k Azure Cosmos DB koncovému bodu z neobvyklého zeměpisného umístění. V některých případech výstraha detekuje legitimní akci, což znamená, že nová aplikace nebo operace údržby vývojářů. V jiných případech výstraha detekuje škodlivou akci od bývalého zaměstnance, externího útočníka atd.

- **Neobvyklé extrakce dat** : Tato výstraha se aktivuje, když klient extrahuje neobvyklé množství dat z Azure Cosmos DB účtu. Může to být příznak některých exfiltrace dat provedených k přenosu všech dat uložených v účtu do externího úložiště dat.



## <a name="configure-advanced-threat-protection"></a>Konfigurace Rozšířené ochrany před internetovými útoky

Rozšířenou ochranu před internetovými útoky můžete nakonfigurovat libovolným z několika způsobů popsaných v následujících částech.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Spusťte Azure Portal v  [https://portal.azure.com](https://portal.azure.com/) .

2. Z účtu Azure Cosmos DB v nabídce **Nastavení** vyberte **Rozšířené zabezpečení** .

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="Nastavení ATP":::

3. V okně **Upřesnit konfiguraci zabezpečení** :

    * Klikněte na možnost **Rozšířená ochrana před internetovými útoky** a nastavte ji na **zapnuto** .
    * Klikněte na **Uložit** a uložte nové nebo aktualizované zásady Advanced Threat Protection.   

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Pomocí příkazů rozhraní REST API můžete vytvořit, aktualizovat nebo získat nastavení rozšířené ochrany před internetovými útoky pro určitý účet Azure Cosmos DB.

* [Rozšířená ochrana před internetovými útoky – vytvořit](/rest/api/securitycenter/advancedthreatprotection/create)
* [Rozšířená ochrana před internetovými útoky – získání](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Použijte následující rutiny PowerShellu:

* [Povolení Rozšířené ochrany před internetovými útoky](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0)
* [Získat rozšířenou ochranu před internetovými útoky](/powershell/module/az.security/get-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0)
* [Zakázat rozšířenou ochranu před internetovými útoky](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0)

### <a name="arm-template"></a>[Šablona ARM](#tab/arm-template)

Pomocí šablony Azure Resource Manager (ARM) nastavte Cosmos DB s povolenou rozšířenou ochranou hrozeb.
Další informace najdete v tématu [Vytvoření účtu CosmosDB s využitím rozšířené ochrany před internetovými útoky](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

K povolení rozšířené ochrany před internetovými útoky pro Cosmos DB použijte Azure Policy.

1. Spusťte stránku Azure **Policy-definitions** a vyhledejte zásadu **nasazení rozšířené ochrany před internetovými útoky pro Cosmos DB** .

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="Nastavení ATP"::: 

1. Klikněte na zásady **nasadit rozšířenou ochranu před internetovými útoky pro CosmosDB** a pak klikněte na **přiřadit** .

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="Nastavení ATP":::


1. V poli **obor** klikněte na tři tečky, vyberte předplatné Azure nebo skupinu prostředků a pak klikněte na **Vybrat** .

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="Nastavení ATP":::


1. Zadejte další parametry a klikněte na **přiřadit** .




## <a name="manage-atp-security-alerts"></a>Spravovat výstrahy zabezpečení ATP

Při Azure Cosmos DB výskytu anomálií aktivit se aktivuje výstraha zabezpečení s informacemi o podezřelé události zabezpečení. 

 Z Azure Security Center můžete zkontrolovat a spravovat aktuální [výstrahy zabezpečení](../security-center/security-center-alerts-overview.md).  Kliknutím na konkrétní výstrahu v [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) zobrazíte možné příčiny a doporučené akce pro prošetření a zmírnění potenciální hrozby. Následující obrázek ukazuje příklad podrobností výstrahy, které jsou k dispozici v Security Center.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="Nastavení ATP":::

E-mailové oznámení se také pošle s podrobnostmi výstrahy a doporučenými akcemi. Na následujícím obrázku vidíte příklad e-mailu s výstrahou.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="Nastavení ATP":::

## <a name="cosmos-db-atp-alerts"></a>Výstrahy ATP Cosmos DB

 Pokud chcete zobrazit seznam výstrah generovaných při monitorování účtů Azure Cosmos DB, přečtěte si část [Cosmos DB výstrahy](../security-center/alerts-reference.md#alerts-azurecosmos) v dokumentaci k Azure Security Center.

## <a name="next-steps"></a>Další kroky

* Další informace o [protokolování diagnostiky v Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Další informace o [Azure Security Center](../security-center/security-center-introduction.md)