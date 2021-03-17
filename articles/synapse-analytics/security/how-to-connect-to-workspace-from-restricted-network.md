---
title: Připojení k prostředkům pracovního prostoru v Azure synapse Analytics studia z omezené sítě
description: Tento článek vás seznámí s tím, jak se připojit k prostředkům pracovního prostoru z omezené sítě.
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: de7c5dba5a4868b7a8fdb390f974134cfaef7395
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384516"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>Připojení k prostředkům pracovního prostoru z omezené sítě

Předpokládejme, že jste správcem IT, který spravuje omezenou síť vaší organizace. Chcete povolit síťové připojení mezi Azure synapse Analytics Studio a pracovní stanicí v rámci této omezené sítě. V tomto článku se dozvíte, jak.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Pracovní prostor analýzy Azure synapse**: můžete si ho vytvořit z Azure synapse Analytics. V kroku 4 budete potřebovat název pracovního prostoru.
* **Omezená síť**: správce IT udržuje omezenou síť pro organizaci a má oprávnění ke konfiguraci zásad sítě. V kroku 3 budete potřebovat název virtuální sítě a její podsíť.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Krok 1: Přidání odchozích pravidel zabezpečení sítě do omezené sítě

Budete muset přidat čtyři pravidla pro odchozí síť sítě se čtyřmi značkami služby. 
* AzureResourceManager
* AzureFrontDoor. front-end
* Azureactivedirectory selhala
* AzureMonitor (Tento typ pravidla je nepovinný. Přidejte ho jenom v případě, že chcete data sdílet s Microsoftem.)

Následující snímek obrazovky ukazuje podrobnosti Azure Resource Manager odchozí pravidlo.

![Obrazovka podrobností značek Azure Resource Manager služby](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

Když vytváříte další tři pravidla, nahraďte hodnotu **cílové značky služby** pomocí **AzureFrontDoor. front**- **azureactivedirectory selhala** nebo **AzureMonitor** ze seznamu.

Další informace najdete v tématu [Přehled značek služeb](../../virtual-network/service-tags-overview.md).

## <a name="step-2-create-private-link-hubs"></a>Krok 2: vytvoření rozbočovačů privátního propojení

Pak z Azure Portal vytvořte rozbočovače privátních odkazů. Pokud to chcete najít na portálu, vyhledejte službu *Azure synapse Analytics (centra privátních odkazů)* a pak ji vytvořte zadáním požadovaných informací. 

![Snímek obrazovky s vytvořením centra privátních odkazů pro synapse](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>Krok 3: Vytvoření privátního koncového bodu pro synapse Studio

Pokud chcete získat přístup k Azure synapse Analytics Studio, musíte z Azure Portal vytvořit privátní koncový bod. Pokud to chcete najít na portálu, vyhledejte *privátní odkaz*. V **centru privátních odkazů** vyberte **vytvořit privátní koncový bod** a pak zadáním požadovaných informací vytvořte. 

> [!Note]
> Ujistěte se, že hodnota **oblasti** je stejná jako ta, ve které je váš pracovní prostor Azure synapse Analytics.

![Snímek obrazovky s vytvořením privátního koncového bodu, karta základy](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

Na kartě **prostředek** vyberte centrum privátních odkazů, které jste vytvořili v kroku 2.

![Snímek obrazovky s vytvořením privátního koncového bodu, prostředku karty](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

Na kartě **Konfigurace** : 
* V poli **virtuální síť** vyberte omezený název virtuální sítě.
* V poli **podsíť** vyberte podsíť omezené virtuální sítě. 
* V případě **integrace s privátní zónou DNS** vyberte **Ano**.

![Snímek obrazovky s vytvořením privátního koncového bodu, karty konfigurace](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Po vytvoření koncového bodu privátního propojení můžete získat přístup k přihlašovací stránce webového nástroje pro Azure synapse Analytics Studio. Nemůžete ale ještě přistupovat k prostředkům v pracovním prostoru. V takovém případě je potřeba dokončit další krok.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>Krok 4: vytvoření privátních koncových bodů pro prostředek pracovního prostoru

Pokud chcete získat přístup k prostředkům v rámci svého prostředku pracovního prostoru Azure synapse Analytics Studio, musíte vytvořit následující:

- Aspoň jeden koncový bod privátního propojení s **cílovým typem dílčího prostředku** pro **vývoj**.
- Dva další nepovinné koncové body privátních propojení s typy **SQL** nebo **SqlOnDemand** podle toho, k jakým prostředkům v pracovním prostoru chcete přistupovat.

Vytváření je podobné jako při vytváření koncového bodu v předchozím kroku.  

Na kartě **prostředek** :

* Jako **typ prostředku** vyberte **Microsoft. synapse/pracovní prostory**.
* Jako **prostředek** vyberte název pracovního prostoru, který jste vytvořili dříve.
* Jako **cílový dílčí prostředek** vyberte typ koncového bodu:
  * **SQL** je pro provádění dotazů SQL ve fondu SQL.
  * **SqlOnDemand** je pro integrované provádění dotazů SQL.
  * **Vývoj** je pro přístup ke všem ostatním v pracovních prostorech Azure synapse Analytics Studio. Je nutné vytvořit alespoň jeden koncový bod privátního propojení tohoto typu.

![Snímek obrazovky s vytvořením privátního koncového bodu, karty prostředků, pracovního prostoru](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>Krok 5: vytvoření privátních koncových bodů pro propojené úložiště v pracovním prostoru

Pokud chcete získat přístup k propojenému úložišti pomocí Průzkumníka služby Storage v pracovním prostoru Azure synapse Analytics Studio, musíte vytvořit jeden privátní koncový bod. Postup je podobný jako u kroku 3. 

Na kartě **prostředek** :
* Jako **typ prostředku** vyberte **Microsoft. synapse/storageAccounts**.
* Jako **prostředek** vyberte název účtu úložiště, který jste předtím vytvořili.
* Jako **cílový dílčí prostředek** vyberte typ koncového bodu:
  * **objekt BLOB** je pro Azure Blob Storage.
  * **DFS** je pro Azure Data Lake Storage Gen2.

![Snímek obrazovky s vytvořením privátního koncového bodu, karty prostředku a úložiště](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Teď máte přístup k prostředku propojeného úložiště. V rámci vaší virtuální sítě můžete v pracovním prostoru Azure synapse Analytics Studio použít Průzkumníka služby Storage k přístupu k prostředku propojeného úložiště.

Pro svůj pracovní prostor můžete povolit spravovanou virtuální síť, jak je znázorněno na tomto snímku obrazovky:

![Snímek obrazovky s vytvořením pracovního prostoru synapse se zvýrazněnou možností povolit spravovanou virtuální síť.](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

Pokud chcete, aby váš Poznámkový blok měl přístup k prostředkům propojeného úložiště v rámci určitého účtu úložiště, přidejte do Azure synapse Analytics studia spravované soukromé koncové body. Název účtu úložiště musí být ten, který potřebuje přístup k vašemu poznámkovému bloku. Další informace najdete v tématu [vytvoření spravovaného privátního koncového bodu pro zdroj dat](./how-to-create-managed-private-endpoints.md).

Po vytvoření tohoto koncového bodu stav schválení zobrazí stav **čeká na vyřízení**. Požadavek schválí od vlastníka tohoto účtu úložiště na kartě **připojení privátního koncového bodu** tohoto účtu úložiště v Azure Portal. Po schválení bude mít váš Poznámkový blok přístup k prostředkům propojených úložiště v rámci tohoto účtu úložiště.

Nyní všechno je nastavené. Můžete získat přístup k vašemu prostředku pracovního prostoru Azure synapse Analytics Studio.

## <a name="appendix-dns-registration-for-private-endpoint"></a>Příloha: registrace DNS pro soukromý koncový bod

Pokud se při vytváření privátního koncového bodu v následujícím snímku obrazovky nepovoluje integrace s privátní zónou DNS, musíte pro každý ze svých privátních koncových bodů vytvořit **zónu privátní DNS**.
![Snímek obrazovky s synapse privátní zónou DNS 1](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

Pokud chcete na portálu najít **zónu privátní DNS** , vyhledejte *privátní DNS Zone*. V **zóně privátní DNS** vyplňte níže požadované informace, které chcete vytvořit.

* Do pole **název** zadejte privátní vyhrazený název zóny DNS pro konkrétní soukromý koncový bod, jak je uvedeno níže:
  * **`privatelink.azuresynapse.net`** je pro přístup k bráně Azure synapse Analytics Studio pro soukromý koncový bod. V kroku 3 se zobrazí tento typ vytvoření privátního koncového bodu.
  * **`privatelink.sql.azuresynapse.net`** je pro tento typ privátního koncového bodu provádění dotazu SQL ve fondu SQL a integrovaném fondu. Podívejte se na téma vytváření koncových bodů v kroku 4.
  * **`privatelink.dev.azuresynapse.net`** je pro tento typ privátního koncového bodu přístupu ke všem ostatním v pracovních prostorech Azure synapse Analytics Studio. V kroku 4 se zobrazí tento typ vytvoření privátního koncového bodu.
  * **`privatelink.dfs.core.windows.net`** slouží pro soukromý koncový bod přístupu k Azure Data Lake Storage Gen2 připojenému k pracovnímu prostoru. V kroku 5 se zobrazí tento typ vytvoření privátního koncového bodu.
  * **`privatelink.blob.core.windows.net`** je určen pro soukromý koncový bod s přístupem k pracovnímu prostoru připojeným k Azure Blob Storage. V kroku 5 se zobrazí tento typ vytvoření privátního koncového bodu.

![Snímek obrazovky s synapse privátní zónou DNS 2](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

Po vytvoření **zóny privátní DNS** zadejte vytvořenou privátní zónu DNS a kliknutím na **odkazy virtuální sítě** přidejte odkaz na virtuální síť. 

![Snímek obrazovky s synapse privátní DNS zóna 3](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

Vyplňte povinná pole níže:
* Jako **název odkazu** zadejte název odkazu.
* V případě služby **Virtual Network** vyberte svou virtuální síť.

![Snímek obrazovky s synapse privátní zónou DNS 4.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

Po přidání odkazu virtuální síť budete muset přidat sadu záznamů DNS v **zóně privátní DNS** , kterou jste vytvořili dříve.

* Pro **název** zadejte řetězce vyhrazených názvů pro jiný privátní koncový bod: 
  * **Web** je pro soukromý koncový bod přístupu k Azure synapse Analytics studia.
  * "***YourWorkSpaceName***" je privátní koncový bod provádění dotazu SQL ve fondu SQL a také pro soukromý koncový bod pro přístup k všechno ostatní v pracovních prostorech Azure synapse Analytics Studio.
  * "***YourWorkSpaceName *-OnDemand**" slouží k zadání privátního koncového bodu pro spuštění dotazu SQL ve vestavěném fondu.
* Jako **typ** vyberte pouze typ záznamu DNS **A** . 
* Do pole **IP adresa** zadejte odpovídající IP adresu každého privátního koncového bodu. IP adresu můžete získat v **síťovém rozhraní** z přehledu privátního koncového bodu.

![Snímek obrazovky s synapse privátní DNS zóna 5.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [virtuální síti spravovaného pracovního prostoru](./synapse-workspace-managed-vnet.md).

Přečtěte si další informace o [spravovaných soukromých koncových bodech](./synapse-workspace-managed-private-endpoints.md).
