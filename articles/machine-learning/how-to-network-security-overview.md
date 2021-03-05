---
title: Přehled izolace a zabezpečení virtuální sítě
titleSuffix: Azure Machine Learning
description: Použijte izolovaný Virtual Network Azure s Azure Machine Learning k zabezpečení prostředků pracovních prostorů a výpočetních prostředí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, references_regions, contperf-fy21q1
ms.openlocfilehash: 1309ad1b3e3f6bd6f9b543959220bf71c569f083
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175001"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>Přehled izolace a ochrany osobních údajů virtuální sítě

V tomto článku se dozvíte, jak používat virtuální sítě (virtuální sítě) k zabezpečení síťové komunikace v Azure Machine Learning. Tento článek používá ukázkový scénář, který vám ukáže, jak nakonfigurovat kompletní virtuální síť.

Tento článek je součástí jedné z pěti částí série, která vás provede zabezpečením Azure Machine Learningho pracovního postupu. Důrazně doporučujeme, abyste si přečetli tento článek přehledu, abyste si nejdřív pochopili koncepty. 

Tady jsou další články v této sérii:

**1. virtuální síť – přehled**  >  [2 Zabezpečte pracovní prostor](how-to-secure-workspace-vnet.md)  >  [3. Zabezpečte školicí prostředí](how-to-secure-training-vnet.md)  >  [4. Zabezpečte prostředí Inferencing](how-to-secure-inferencing-vnet.md)  >  [5. Povolit funkci studia](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte zkušenosti s následujícími tématy:
+ [Virtuální sítě Azure](../virtual-network/virtual-networks-overview.md)
+ [Síť IP](../virtual-network/public-ip-addresses.md)
+ [Azure Private Link](how-to-configure-private-link.md)
+ [Skupiny zabezpečení sítě (NSG)](../virtual-network/network-security-groups-overview.md)
+ [Síťové brány firewall](../firewall/overview.md)

## <a name="example-scenario"></a>Ukázkový scénář

V této části se dozvíte, jak je nastaven společný scénář sítě pro zabezpečení Azure Machine Learning komunikace s privátními IP adresami.

Následující tabulka porovnává, jak služby přistupují k různým částem Azure Machine Learning sítě jak s virtuální sítí, tak bez virtuální sítě.

| Scenario | Pracovní prostor | Přidružené prostředky | Školení výpočetního prostředí | Výpočetní prostředí Inferencing |
|-|-|-|-|-|-|
|**Žádná virtuální síť**| Veřejná IP adresa | Veřejná IP adresa | Veřejná IP adresa | Veřejná IP adresa |
|**Zabezpečení prostředků ve virtuální síti**| Privátní IP adresa (soukromý koncový bod) | Veřejná IP adresa (koncový bod služby) <br> **ani** <br> Privátní IP adresa (soukromý koncový bod) | Privátní IP adresa | Privátní IP adresa  | 

* **Pracovní prostor** – vytvoření privátního koncového bodu z vaší virtuální sítě pro připojení k privátnímu propojení v pracovním prostoru. Privátní koncový bod připojuje pracovní prostor k virtuální síti prostřednictvím několika privátních IP adres.
* **Přidružený prostředek** – pomocí koncových bodů služby nebo privátních koncových bodů se připojte k prostředkům pracovního prostoru, jako je Azure storage, Azure Key Vault a Azure Container Service.
    * **Koncové body služby** poskytují identitu vaší virtuální sítě ke službě Azure. Po povolení koncových bodů služby ve virtuální síti můžete přidat pravidlo virtuální sítě pro zabezpečení prostředků služby Azure do vaší virtuální sítě. Koncové body služby používají veřejné IP adresy.
    * **Soukromé koncové body** jsou síťová rozhraní, která vás bezpečně připojují ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě.
* **Školení pro výpočetní přístup** – přístup k výpočetním cílům, jako je Azure Machine Learning výpočetní Instance a Azure Machine Learning výpočetních clusterů pomocí privátních IP adres. 
* **Inferencing COMPUTE Access** – přístup k výpočetním clusterům Azure Kubernetes Services (AKS) pomocí privátních IP adres.


V následujících pěti částech se dozvíte, jak zabezpečit scénář sítě popsaný výše. K zabezpečení sítě je potřeba:

1. Zabezpečte [**pracovní prostor a přidružené prostředky**](#secure-the-workspace-and-associated-resources).
1. Zabezpečte [**školicí prostředí**](#secure-the-training-environment).
1. Zabezpečte [**prostředí Inferencing**](#secure-the-inferencing-environment).
1. Volitelně: [**Povolit funkce studia**](#optional-enable-studio-functionality).
1. Konfigurace [ **nastavení brány firewall**](#configure-firewall-settings)

## <a name="secure-the-workspace-and-associated-resources"></a>Zabezpečení pracovního prostoru a přidružených prostředků

Pomocí následujícího postupu Zabezpečte svůj pracovní prostor a přidružené prostředky. Tyto kroky umožní vašim službám komunikovat ve virtuální síti.

1. Vytvořte [pracovní prostor podporující privátní linku](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) , který umožní komunikaci mezi vaší virtuální sítí a pracovním prostorem.
1. Přidejte Azure Key Vault do virtuální sítě s [koncovým bodem služby](../key-vault/general/overview-vnet-service-endpoints.md) nebo [soukromým koncovým bodem](../key-vault/general/private-link-service.md). Nastavte Key Vault na ["umožňuje důvěryhodným službám Microsoftu obejít tuto bránu firewall"](how-to-secure-workspace-vnet.md#secure-azure-key-vault).
1. Přidejte svůj účet Azure Storage do virtuální sítě s [koncovým bodem služby](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) nebo [soukromým koncovým bodem](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints).
1. [Nakonfigurujte Azure Container registry pro použití privátního koncového bodu](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr).

![Diagram architektury znázorňující, jak pracovní prostor a přidružené prostředky komunikují přes koncové body služby nebo privátní koncové body uvnitř virtuální sítě](./media/how-to-network-security-overview/secure-workspace-resources.png)

Podrobné pokyny k provedení tohoto postupu najdete v tématu [zabezpečení Azure Machine Learningho pracovního prostoru](how-to-secure-workspace-vnet.md). 

### <a name="limitations"></a>Omezení

Zabezpečení pracovního prostoru a přidružených prostředků v rámci virtuální sítě má následující omezení:
- Použití Azure Machine Learningho pracovního prostoru s privátním odkazem není v oblastech Azure Government nebo Azure Čína 21Vianet k dispozici.
- Všechny prostředky musí být za stejnou virtuální sítí. Nicméně podsítě v rámci stejné virtuální sítě jsou povoleny.

## <a name="secure-the-training-environment"></a>Zabezpečení školicího prostředí

V této části se dozvíte, jak zabezpečit školicí prostředí v Azure Machine Learning. Naučíte se také, jak Azure Machine Learning dokončuje školicí úlohu, abyste porozuměli tomu, jak tyto konfigurace sítě vzájemně spolupracují.

K zabezpečení školicího prostředí použijte následující postup:

1. Vytvořte Azure Machine Learning [výpočetní instanci a počítačového clusteru ve virtuální síti](how-to-secure-training-vnet.md#compute-instance) ke spuštění úlohy školení.
1. [Povolí příchozí komunikaci od služby Azure Batch](how-to-secure-training-vnet.md#mlcports) , aby služba Batch mohla odesílat úlohy do výpočetních prostředků. 

![Diagram architektury znázorňující zabezpečení spravovaných výpočetních clusterů a instancí](./media/how-to-network-security-overview/secure-training-environment.png)

Podrobné pokyny k provedení tohoto postupu najdete v tématu [zabezpečení školicího prostředí](how-to-secure-training-vnet.md). 

### <a name="example-training-job-submission"></a>Příklad odeslání úlohy školení 

V této části se dozvíte, jak Azure Machine Learning zabezpečeně komunikuje mezi službami k odeslání školicí úlohy. Tím se dozvíte, jak všechny konfigurace spolupracují na zabezpečení komunikace.

1. Klient nahraje školicí skripty a školicí data do účtů úložiště, které jsou zabezpečené pomocí služby nebo privátního koncového bodu.

1. Klient odešle školicí úlohu do pracovního prostoru Azure Machine Learning prostřednictvím privátního koncového bodu.

1. Služba Azure Batch Services obdrží úlohu z pracovního prostoru a odešle úlohu školení do výpočetního prostředí prostřednictvím veřejného nástroje pro vyrovnávání zatížení, který je zřízen s výpočetním prostředkem. 

1. Výpočetní prostředek obdrží úlohu a začne školení. Výpočetní prostředky přistupují k účtům zabezpečeného úložiště a stahují školicí soubory a nahrávají výstup.

### <a name="limitations"></a>Omezení

- Výpočetní instance Azure a výpočetní clustery Azure musí být ve stejné virtuální síti, oblasti a předplatném jako pracovní prostor a jeho přidružené prostředky. 

## <a name="secure-the-inferencing-environment"></a>Zabezpečení prostředí Inferencing

V této části se seznámíte s možnostmi, které jsou k dispozici pro zabezpečení prostředí Inferencing. Pro nasazení v produkčním prostředí s vysokým škálováním doporučujeme používat clustery Azure Kubernetes Services (AKS).

Pro clustery AKS ve virtuální síti máte dvě možnosti:

- Nasaďte nebo připojte výchozí cluster AKS k vaší virtuální síti.
- Připojte ke svojí virtuální síti privátní cluster AKS.

**Výchozí clustery AKS** mají řídicí plochu s veřejnými IP adresami. Výchozí cluster AKS můžete přidat do své virtuální sítě během nasazování nebo připojit cluster po jeho vytvoření.

**Soukromé clustery AKS** mají řídicí plochu, ke které se dá dostat jenom prostřednictvím privátních IP adres. Privátní clustery AKS musí být připojeny po vytvoření clusteru.

Podrobné pokyny k přidání výchozích a privátních clusterů najdete v tématu [zabezpečení prostředí Inferencing](how-to-secure-inferencing-vnet.md). 

Následující diagram sítě zobrazuje zabezpečený Azure Machine Learning pracovní prostor s privátním AKS clusterem připojeným k virtuální síti.

![Diagram architektury ukazující, jak připojit privátní cluster AKS k virtuální síti. Rovina ovládacího prvku AKS se nachází mimo virtuální síť zákazníka.](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>Omezení
- Clustery AKS musí patřit do stejné virtuální sítě jako pracovní prostor a přidružené prostředky. 

## <a name="optional-enable-public-access"></a>Volitelné: Povolit veřejný přístup

Pracovní prostor můžete zabezpečit za virtuální sítí pomocí privátního koncového bodu a zároveň jim zajistit přístup přes veřejný Internet. Počáteční konfigurace je stejná jako při [zabezpečení pracovního prostoru a přidružených prostředků](#secure-the-workspace-and-associated-resources). 

Po zabezpečení pracovního prostoru pomocí privátního odkazu pak [Povolte veřejný přístup](how-to-configure-private-link.md#enable-public-access). Potom budete mít přístup k pracovnímu prostoru z veřejného internetu i virtuální sítě.

### <a name="limitations"></a>Omezení

- Pokud používáte aplikaci Azure Machine Learning Studio prostřednictvím veřejného Internetu, některé funkce, jako je například Návrhář, nemusí mít přístup k vašim datům. K tomuto problému dochází, když jsou data uložená ve službě, která je zabezpečená za virtuální sítí. Například účet Azure Storage.
## <a name="optional-enable-studio-functionality"></a>Volitelné: povolit funkce studia

[Zabezpečte pracovní prostor](#secure-the-workspace-and-associated-resources)  >  [Zabezpečení školicího prostředí](#secure-the-training-environment)  >  [Zabezpečení prostředí Inferencing](#secure-the-inferencing-environment)  >  **Povolit funkci studia**  >  [Konfigurace nastavení brány firewall](#configure-firewall-settings)

Pokud je vaše úložiště ve virtuální síti, musíte nejdřív provést další kroky konfigurace, abyste mohli v [studiu](overview-what-is-machine-learning-studio.md)povolit všechny funkce. Ve výchozím nastavení jsou zakázány následující funkce:

* Náhled dat v studiu
* Vizualizujte data v návrháři.
* Nasaďte model v návrháři.
* Odešlete experiment AutoML.
* Spusťte Popis projektu.

Pokud chcete povolit úplnou funkci studia i v rámci virtuální sítě, přečtěte si téma [použití Azure Machine Learning studia ve virtuální síti](how-to-enable-studio-virtual-network.md#configure-data-access-in-the-studio). Studio podporuje účty úložiště buď pomocí koncových bodů služby, nebo soukromých koncových bodů.

### <a name="limitations"></a>Omezení

[Popisky dat s asistencí ml](how-to-create-labeling-projects.md#use-ml-assisted-data-labeling) nepodporují výchozí účty úložiště zabezpečené za virtuální sítí. Pro popisky dat s podporou ML s asistencí je nutné použít jiný než výchozí účet úložiště. Všimněte si, že účet úložiště, který není výchozí, může být zabezpečený za virtuální sítí. 

## <a name="configure-firewall-settings"></a>Konfigurace nastavení brány firewall

Nakonfigurujte bránu firewall pro řízení přístupu k prostředkům vašeho Azure Machine Learning pracovního prostoru a k veřejnému Internetu. I když doporučujeme Azure Firewall, měli byste být schopni použít jiné produkty brány firewall k zabezpečení sítě. Pokud máte dotazy týkající se povolení komunikace přes bránu firewall, přečtěte si prosím dokumentaci pro bránu firewall, kterou používáte.

Další informace o nastavení brány firewall najdete v tématu [použití pracovního prostoru za bránou firewall](how-to-access-azureml-behind-firewall.md).

## <a name="custom-dns"></a>Vlastní DNS

Pokud pro svoji virtuální síť potřebujete použít vlastní řešení DNS, musíte přidat záznamy hostitele pro svůj pracovní prostor.

Další informace o požadovaných názvech domén a IP adresách najdete v tématu [použití pracovního prostoru s vlastním serverem DNS](how-to-custom-dns.md).

## <a name="next-steps"></a>Další kroky

Tento článek je součástí jedné z řady virtuálních sítí s pěti částmi. Další informace o tom, jak zabezpečit virtuální síť, najdete v dalších článcích:

* [Část 2: Přehled virtuální sítě](how-to-secure-workspace-vnet.md)
* [Část 3: zabezpečení školicího prostředí](how-to-secure-training-vnet.md)
* [4. část: zabezpečení prostředí Inferencing](how-to-secure-inferencing-vnet.md)
* [Část 5: povolení funkcí studia](how-to-enable-studio-virtual-network.md)
