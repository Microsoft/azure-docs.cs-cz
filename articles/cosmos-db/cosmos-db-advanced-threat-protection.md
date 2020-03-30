---
title: Pokročilá ochrana před hrozbami pro Azure Cosmos DB
description: Zjistěte, jak Azure Cosmos DB poskytuje šifrování dat v klidovém stavu a jak se implementuje.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614834"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Pokročilá ochrana před hrozbami pro Azure Cosmos DB (preview)

Pokročilá ochrana před hrozbami pro Azure Cosmos DB poskytuje další vrstvu inteligence zabezpečení, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo zneužití účtů Azure Cosmos DB. Tato vrstva ochrany umožňuje řešit hrozby, a to i bez odborníka na zabezpečení, a integrovat je s centrálními systémy monitorování zabezpečení.

Výstrahy zabezpečení se aktivují, když dojde k anomáliím v aktivitě. Tyto výstrahy zabezpečení jsou integrované s [Azure Security Center](https://azure.microsoft.com/services/security-center/)a jsou také odesílány e-mailem správcům předplatného, s podrobnostmi o podezřelé aktivity a doporučení, jak prozkoumat a napravit hrozby.

> [!NOTE]
>
> * Pokročilá ochrana před hrozbami pro Azure Cosmos DB je momentálně dostupná jenom pro rozhraní SQL API.
> * Pokročilá ochrana před hrozbami pro Azure Cosmos DB momentálně není dostupná ve vládních a suverénních cloudových oblastech Azure.

Pro úplné zkoumání prostředí výstrah zabezpečení doporučujeme povolení [protokolování diagnostiky v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), který zaznamenává operace na samotné databázi, včetně crud operací na všechny dokumenty, kontejnery a databáze.

## <a name="threat-types"></a>Typy hrozeb

Pokročilá ochrana před hrozbami pro Azure Cosmos DB detekuje neobvyklé aktivity označující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. V současné době může aktivovat následující výstrahy:

- **Přístup z neobvyklých umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k účtu Azure Cosmos, kde se někdo připojil ke koncovému bodu Azure Cosmos DB z neobvyklé geografické polohy. V některých případech výstraha zjistí legitimní akci, což znamená novou aplikaci nebo operaci údržby vývojáře. V ostatních případech výstraha detekuje škodlivou akci od bývalého zaměstnance, externího útočníka atd.

- **Neobvyklé extrakce dat**: Tato výstraha se aktivuje, když klient extrahuje neobvyklé množství dat z účtu Azure Cosmos DB. To může být příznakem některých exfiltrací dat provedených k přenosu všech dat uložených v účtu do externího úložiště dat.

## <a name="set-up-advanced-threat-protection"></a>Nastavení rozšířené ochrany před hrozbami

### <a name="set-up-atp-using-the-portal"></a>Nastavení atp pomocí portálu

1. Spusťte portál [https://portal.azure.com](https://portal.azure.com/)Azure na webu .

2. Z účtu Azure Cosmos DB vyberte v nabídce **Nastavení** **rozšířené zabezpečení**.

    ![Nastavit ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. V okně **konfigurace rozšířeného zabezpečení:**

    * Klepnutím na možnost **Rozšířená ochrana před internetovými hrozbami** nastavte možnost **ZAPNUTO**.
    * Klikněte na **Uložit** a uložte nové nebo aktualizované zásady Advanced Threat Protection.   

### <a name="set-up-atp-using-rest-api"></a>Nastavení atp pomocí rozhraní REST API

Pomocí příkazů rest API můžete vytvořit, aktualizovat nebo získat nastavení Rozšířené ochrany před hrozbami pro konkrétní účet Azure Cosmos DB.

* [Pokročilá ochrana před hrozbami – vytvoření](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Pokročilá ochrana před hrozbami – získat](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Nastavení atp pomocí Azure PowerShellu

Použijte následující rutiny prostředí PowerShell:

* [Povolení Rozšířené ochrany před internetovými útoky](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Získejte pokročilou ochranu před hrozbami](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Zakázat pokročilou ochranu před hrozbami](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru

Pomocí šablony Azure Resource Manager nastavte Cosmos DB s povolenou pokročilou ochranou před internetovými hrozbami.
Další informace naleznete [v tématu Vytvoření účtu CosmosDB s pokročilou ochranou před hrozbami](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="using-azure-policy"></a>Používání zásad Azure

Pomocí zásad Azure povolte pokročilou ochranu před internetovými hrozbami pro Cosmos DB.

1. Spusťte stránku **Zásady** Azure – definice a vyhledejte zásady **Deploy Advanced Threat Protection for Cosmos DB.**

    ![Zásady hledání](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Klikněte na **zásadu Nasadit pokročilou ochranu před hrozbami pro CosmosDB** a potom klikněte na **Přiřadit**.

    ![Vybrat předplatné nebo skupinu](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. V poli **Obor** klikněte na tři tečky, vyberte předplatné Azure nebo skupinu prostředků a klikněte na **Vybrat**.

    ![Stránka Definice zásad](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Zadejte další parametry a klepněte na **přiřadit**.

## <a name="manage-atp-security-alerts"></a>Správa výstrah zabezpečení ATP

Když dojde k anomáliím aktivity Azure Cosmos DB, aktivuje se výstraha zabezpečení s informacemi o podezřelé události zabezpečení. 

 V Centru zabezpečení Azure můžete zkontrolovat a spravovat aktuální [výstrahy zabezpečení](../security-center/security-center-alerts-overview.md).  Kliknutím na konkrétní výstrahu v [Centru zabezpečení](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) zobrazíte možné příčiny a doporučené akce k prozkoumání a zmírnění potenciální hrozby. Následující obrázek ukazuje příklad podrobností výstrah uvedených v Centru zabezpečení.

 ![Podrobnosti o hrozbě](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

E-mailové oznámení je také odesláno s podrobnostmi výstrahy a doporučenými akcemi. Následující obrázek ukazuje příklad e-mailu s výstrahou.

 ![Podrobnosti upozornění](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Výstrahy ATP cosmos DB

 Pokud jde zobrazit seznam výstrah generovaných při monitorování účtů Azure Cosmos DB, přečtěte si část [výstrah Cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) v dokumentaci k Centru zabezpečení Azure.

## <a name="next-steps"></a>Další kroky

* Další informace o [protokolování diagnostiky v Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
