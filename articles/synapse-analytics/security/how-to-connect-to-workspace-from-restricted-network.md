---
title: Připojení k prostředku pracovního prostoru synapse Studio z omezené sítě
description: Tento článek vás seznámí s tím, jak se připojit k prostředkům pracovního prostoru Azure synapse Studio z omezené sítě.
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d94ee3145fb073dae982019fd4096cc2ceb7cd86
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578327"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>Připojení k prostředkům pracovního prostoru synapse Studio z omezené sítě

Cílový čtenář tohoto článku je firemní správce, který spravuje omezenou síť společnosti. Správce IT se chystá povolit síťové připojení mezi službou Azure synapse Studio a pracovní stanicí v rámci této omezené sítě.

V tomto článku se dozvíte, jak se připojit k pracovnímu prostoru Azure synapse z omezeného síťového prostředí. 

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure** : Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Pracovní prostor Azure synapse** : Pokud nemáte synapse Studio, vytvořte pracovní prostor synapse z Azure synapse Analytics. Název pracovního prostoru bude potřeba v následujícím kroku 4.
* **Omezená síť** : omezená síť je spravovaná správcem IT společnosti. správce IT má oprávnění ke konfiguraci zásad sítě. Název virtuální sítě a jeho podsíť budou potřeba v následujícím kroku 3.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Krok 1: Přidání odchozích pravidel zabezpečení sítě do omezené sítě

Budete muset přidat čtyři pravidla pro odchozí síť sítě se čtyřmi značkami služby. Další informace o [přehledu značek služeb](/azure/virtual-network/service-tags-overview) 
* AzureResourceManager
* AzureFrontDoor. front-end
* Azureactivedirectory selhala
* AzureMonitor (volitelné. Tento typ pravidla přidejte pouze v případě, že chcete data sdílet s Microsoftem.)

**Azure Resource Manager** podrobnosti odchozího pravidla, jak je uvedeno níže. Když vytváříte další tři pravidla, nahraďte hodnotu " **jmenovka cílové služby** " volbou názvu značky služby " **AzureFrontDoor. front** ", " **azureactivedirectory selhala** ", " **AzureMonitor** " ze seznamu rozevíracích seznamů pro výběr.

![AzureResourceManager](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)


## <a name="step-2-create-azure-synapse-analytics-private-link-hubs"></a>Krok 2: vytvoření služby Azure synapse Analytics (centra privátních odkazů)

Z Azure Portal budete muset vytvořit Azure synapse Analytics (centra privátních odkazů). Vyhledejte " **Azure synapse Analytics" (centra privátních propojení)** "prostřednictvím Azure Portal a potom vyplňte potřebné pole a vytvořte ho. 

> [!Note]
> Oblast by měla být stejná jako ta, ve které je váš pracovní prostor synapse.

![Vytváření Synapsech privátních Link Analytics](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-private-endpoint-for-synapse-studio-gateway"></a>Krok 3: Vytvoření privátního koncového bodu pro bránu synapse Studio

Pokud chcete získat přístup k bráně synapse Studio, budete muset vytvořit privátní koncový bod z Azure Portal. Vyhledejte " **soukromé propojení** " prostřednictvím Azure Portal. V části " **soukromé linkové centrum** " vyberte **vytvořit privátní koncový bod** a pak vyplňte požadované pole a vytvořte ho. 

> [!Note]
> Oblast by měla být stejná jako ta, ve které je váš pracovní prostor synapse.

![Vytváření privátního koncového bodu pro synapse Studio 1](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

Na další kartě " **prostředek** " Vyberte centrum privátních odkazů, které bylo vytvořeno v kroku 2 výše.

![Vytváření privátního koncového bodu pro synapse Studio 2](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

Na další kartě " **Konfigurace** ", 
* Vyberte název omezené virtuální sítě, který je k dispozici pro " **virtuální síť** ".
* Vyberte podsíť omezené virtuální sítě pro " **podsíť** ". 
* Vyberte **Ano** pro **integraci s privátní zónou DNS**.

![Vytvoření privátního koncového bodu pro synapse Studio 3](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Po vytvoření koncového bodu privátního propojení můžete získat přístup k přihlašovací stránce webového nástroje synapse Studio. Nemůžete ale k prostředkům v pracovním prostoru synapse přistupovat, dokud nebudete muset dokončit další krok.

## <a name="step-4-create-private-endpoints-for-synapse-studio-workspace-resource"></a>Krok 4: vytvoření privátních koncových bodů pro prostředek pracovního prostoru synapse Studio

Pokud chcete získat přístup k prostředkům v rámci svého prostředku pracovního prostoru synapse Studio, budete muset vytvořit alespoň jeden koncový bod privátního propojení s typem " **vývoj** " **cílového dílčího prostředku** "a dva další nepovinné koncové body privátních odkazů s typy" **SQL** "nebo" **SqlOnDemand** "závisí na tom, k jakým prostředkům v pracovním prostoru synapse Studio Chcete získat přístup. Vytváření tohoto koncového bodu privátního propojení pro pracovní prostor synapse Studio je podobné jako při vytváření koncového bodu.  

Věnujte pozornost níže uvedeným oblastem na kartě " **prostředek** ":
* Pro **typ prostředku** vyberte **Microsoft. synapse/pracovní prostory**.
* Vyberte " **YourWorkSpaceName** " do " **prostředku** ", který jste vytvořili dříve.
* V části **cílový dílčí prostředek** vyberte typ koncového bodu:
  * **SQL** : je pro spuštění dotazu SQL ve fondu SQL.
  * **SqlOnDemand** : je určeno pro provádění předdefinovaného dotazu SQL.
  * **Vývoj** : slouží pro přístup k všechno ostatnímu v pracovních prostorech synapse studia. Pro tento typ je třeba vytvořit alespoň koncový bod privátního propojení.

![Vytváření privátního koncového bodu pro pracovní prostor synapse Studio](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-synapse-studio-workspace-linked-storage"></a>Krok 5: vytvoření privátních koncových bodů pro propojené úložiště synapse Studio v pracovním prostoru

Pokud chcete získat přístup k propojenému úložišti pomocí Průzkumníka služby Storage v pracovním prostoru synapse Studio, budete muset vytvořit jeden privátní koncový bod s podobným postupem, který je uvedený výše v kroku 3. 

Věnujte pozornost níže uvedeným oblastem na kartě " **prostředek** ":
* Pro **typ prostředku** vyberte **Microsoft. synapse/storageAccounts**.
* Vyberte " **YourWorkSpaceName** " do " **prostředku** ", který jste vytvořili dříve.
* V části **cílový dílčí prostředek** vyberte typ koncového bodu:
  * **objekt BLOB** : slouží pro úložiště objektů BLOB v Azure.
  * **DFS** : je určen pro Azure Data Lake Storage Gen2.

![Vytváření privátního koncového bodu pro propojené úložiště synapse Studio](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Teď můžete získat přístup k prostředku propojeného úložiště z Průzkumníka služby Storage v pracovním prostoru synapse Studio v rámci virtuální sítě.

Pokud má váš pracovní prostor " **Povolit spravovanou virtuální síť** " během vytváření pracovního prostoru, jak je uvedeno níže,

![Vytváření privátního koncového bodu pro propojené úložiště synapse Studio v pracovním prostoru 1](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

A chcete Poznámkový blok získat přístup k prostředkům propojeného úložiště v rámci určitého účtu úložiště, musíte do synapse studia přidat " **spravované privátní koncové body** ". **Název účtu úložiště** by měl být ten, ke kterému Poznámkový blok potřebuje přístup. Seznamte se s podrobnými kroky z části [vytvoření spravovaného privátního koncového bodu pro zdroj dat](./how-to-create-managed-private-endpoints.md).

Po vytvoření tohoto koncového bodu bude " **stav schválení** " "čeká na **vyřízení** ". musíte požádat vlastníka tohoto účtu úložiště, aby ho schválil na kartě " **připojení privátního koncového bodu** " tohoto účtu úložiště v Azure Portal. Po schválení bude mít váš Poznámkový blok přístup k prostředkům propojených úložiště v rámci tohoto účtu úložiště.

Nyní všechno je nastavené. K prostředku pracovního prostoru aplikace synapse Studio můžete přistupovat.

## <a name="next-steps"></a>Další kroky

Další informace o [spravovaném pracovním prostoru Virtual Network](./synapse-workspace-managed-vnet.md)

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)
