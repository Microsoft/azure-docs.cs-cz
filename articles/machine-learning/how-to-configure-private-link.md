---
title: Konfigurace privátního koncového bodu (Preview)
titleSuffix: Azure Machine Learning
description: Pomocí privátního odkazu Azure získáte zabezpečený přístup k pracovnímu prostoru Azure Machine Learning z virtuální sítě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/21/2020
ms.openlocfilehash: 619960238125191e7bd4e702a49016c8fd58c847
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296650"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Konfigurace privátního odkazu Azure pro pracovní prostor Azure Machine Learning (Preview)

V tomto dokumentu se dozvíte, jak pomocí privátního propojení Azure s vaším pracovním prostorem Azure Machine Learning. Informace o nastavení virtuální sítě pro Azure Machine Learning najdete v tématu [Přehled izolace a ochrany osobních údajů ve virtuální síti](how-to-network-security-overview.md) .

> [!IMPORTANT]
> Používání privátního odkazu Azure s Azure Machine Learning pracovním prostorem je v současnosti ve verzi Public Preview. Tato funkce je k dispozici pouze v následujících oblastech:
>
> * **East US**
> * **Středojižní USA**
> * **USA – západ**
> * **Západní USA 2**
> * **Střední Kanada**
> * **Southeast Asia**
> * **Japan East**
> * **Severní Evropa**
> * **Východní Austrálie**
> * **Spojené království – jih**
>
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. 
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Privátní odkaz Azure umožňuje připojit se k pracovnímu prostoru pomocí privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v rámci vaší virtuální sítě. Přístup k pracovnímu prostoru pak můžete omezit tak, aby se nacházet jenom přes privátní IP adresy. Soukromý odkaz pomáhá snižovat riziko exfiltrace dat. Další informace o privátních koncových bodech najdete v článku věnovaném [privátním odkazům Azure](/azure/private-link/private-link-overview) .

> [!IMPORTANT]
> Privátní propojení Azure neovlivňuje plochu ovládacího prvku Azure (operace správy), jako je například odstranění pracovního prostoru nebo Správa výpočetních prostředků. Například vytvoření, aktualizace nebo odstranění cíle služby Compute. Tyto operace se provádějí na veřejném Internetu jako normální. Operace roviny dat, jako je například použití Azure Machine Learning studia, rozhraní API (včetně publikovaných kanálů) nebo sady SDK, používají privátní koncový bod.
>
> Pokud používáte Mozilla Firefox, může dojít k potížím při pokusu o přístup k privátnímu koncovému bodu pro váš pracovní prostor. Tento problém může souviset s DNS přes HTTPS v Mozilla. Jako alternativní řešení doporučujeme používat Microsoft Edge Google Chrome.

> [!TIP]
> Instanci služby Azure Machine Learning COMPUTE lze použít s pracovním prostorem a soukromým koncovým bodem. Tato funkce je aktuálně ve verzi Public Preview v oblastech **USA – východ**, **USA (střed) – jih** a **USA – západ 2** .

## <a name="prerequisites"></a>Požadavky

Pokud plánujete použít pracovní prostor s povoleným privátním propojením s klíčem spravovaným zákazníkem, musíte požádat o tuto funkci pomocí lístku podpory. Další informace najdete v tématu [Správa a zvýšení kvót](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Vytvoření pracovního prostoru, který používá privátní koncový bod

> [!IMPORTANT]
> V současné době podporujeme při vytváření nového pracovního prostoru Azure Machine Learning jenom povolení privátního koncového bodu.

Šablona v se [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) dá použít k vytvoření pracovního prostoru s privátním koncovým bodem.

Informace o použití této šablony, včetně privátních koncových bodů, najdete v tématu [použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Použití pracovního prostoru v rámci privátního koncového bodu

Vzhledem k tomu, že komunikace s pracovním prostorem je povolená jenom z virtuální sítě, musí být všechna vývojová prostředí, která používají pracovní prostory, členy virtuální sítě. Například virtuální počítač ve virtuální síti.

> [!IMPORTANT]
> Aby nedošlo k dočasnému přerušení připojení, společnost Microsoft doporučuje po povolení privátního odkazu vyprázdnit mezipaměť DNS na počítačích, které se připojují k pracovnímu prostoru. 

Informace o službě Azure Virtual Machines najdete v [dokumentaci k Virtual Machines](/azure/virtual-machines/).


## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení Azure Machine Learning pracovního prostoru najdete v článku věnovaném [izolaci virtuální sítě a ochraně osobních údajů](how-to-network-security-overview.md) .
