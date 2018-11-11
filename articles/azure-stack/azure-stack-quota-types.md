---
title: Typy kvót ve službě Azure Stack | Dokumentace Microsoftu
description: Zkontrolujte typy různých kvót k dispozici pro služby a prostředky ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/08/2018
ms.author: sethm
ms.reviewer: xiaofmao
ms.openlocfilehash: ec84c87e047d6426d24ef339d1247c6cb53954dd
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51299415"
---
# <a name="quota-types-in-azure-stack"></a>Typy kvót ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

[Kvóty](azure-stack-plan-offer-quota-overview.md#plans) definování omezení prostředků, které můžete zřizovat nebo využívat předplatného uživatele. Například může kvótu umožňují uživateli vytvořit až pět virtuálních počítačů. Každý prostředek může mít svůj vlastní typy kvót.

## <a name="compute-quota-types"></a>COMPUTE typy kvót 
| **Typ** | **Výchozí hodnota** | **Popis** |
| --- | --- | --- |
| Maximální počet virtuálních počítačů | 50 | Maximální počet virtuálních počítačů, které předplatné můžete vytvořit v tomto umístění. |
| Maximální počet jader virtuálního počítače | 100 | Maximální počet jader, které předplatné můžete vytvořit v tomto umístění (třeba virtuální počítač A3 má čtyři jádra). |
| Maximální počet skupin dostupnosti | 10 | Maximální počet skupin dostupnosti, které lze vytvořit v tomto umístění. |
| Maximální počet virtuálních počítačů škálovacích sad | 100 | Maximální počet škálovacích sad virtuálních počítačů, které lze vytvořit v tomto umístění. |

## <a name="storage-quota-types"></a>Typy kvót úložiště 
| **Položka** | **Výchozí hodnota** | **Popis** |
| --- | --- | --- |
| Maximální kapacita (GB) |2 048 |Celková velikost úložiště kapacitu, mohou být spotřebovány předplatné na tomto místě. |
| Celkový počet účtů úložiště |20 |Maximální počet účtů úložiště, které předplatné můžete vytvořit v tomto umístění. |

> [!NOTE]  
> Může trvat až dvě hodiny, než bude vynucovat kvóty úložiště.


## <a name="network-quota-types"></a>Typy kvót sítě
| **Položka** | **Výchozí hodnota** | **Popis** |
| --- | --- | --- |
| Maximální počet veřejných IP adres |50 |Maximální počet veřejných IP adres, které předplatné může vytvořit v tomto umístění. |
| Maximální počet virtuálních sítí |50 |Maximální počet virtuálních sítí, které předplatné můžete vytvořit v tomto umístění. |
| Maximální počet brány virtuální sítě |1 |Maximální počet brány virtuální sítě (VPN Gateway), které předplatného můžete vytvořit v tomto umístění. |
| Maximální počet síťových připojení |2 |Maximální počet síťových připojení (point-to-point nebo site-to-site), které předplatné můžete vytvořit přes všechny brány virtuální sítě v tomto umístění. |
| Maximální počet nástrojů pro vyrovnávání zatížení |50 |Maximální počet nástrojů pro vyrovnávání zatížení, které předplatné můžete vytvořit v tomto umístění. |
| Maximální počet síťových karet |100 |Maximální počet síťových rozhraní, které předplatné můžete vytvořit v tomto umístění. |
| Maximální počet skupin zabezpečení sítě |50 |Maximální počet skupin zabezpečení sítě, které předplatné můžete vytvořit v tomto umístění. |

## <a name="view-an-existing-quota"></a>Zobrazit existující kvótu
1. Na výchozí řídicí panel portálu pro správu, vyhledejte **poskytovatelů prostředků** dlaždici.
2. Vyberte službu, kterou chcete zobrazit, jako je třeba kvót **Compute** nebo **úložiště**.
3. Vyberte **kvóty**a pak vyberte kvóty, které chcete zobrazit.


## <a name="edit-a-quota"></a>Upravit kvótu  
Můžete také upravit původní konfigurace se kvóta místo [pomocí doplňkový plán](create-add-on-plan.md). Při úpravě kvótu novou konfiguraci automaticky platí globálně pro všechny plány, které používají tuto kvótu a všech stávajících předplatných, které používají tyto plány. Úprava kvóty je jiná než při použití doplňkový plán k poskytuje upravenou kvóty, které uživatel vybere pro přihlášení k odběru. 

### <a name="to-edit-a-quota"></a>Chcete-li upravit kvótu  
1. Na výchozí řídicí panel portálu pro správu, vyhledejte **poskytovatelů prostředků** dlaždici.
2. Vyberte službu, kterou chcete upravit, jako je třeba kvót **Compute**, **sítě**, nebo **úložiště**.
3. V dalším kroku vyberte **kvóty**a pak vyberte kvótu, kterou chcete změnit.
4. Na **nastavit kvóty** podokně upravit hodnoty a pak vyberte **Uložit**. 

Nové hodnoty pro kvótu platí globálně pro všechny plány, které používají upravené kvótu a ke všem stávajícím předplatným, které používají tyto plány. 



## <a name="next-steps"></a>Další postup

- [Další informace o plánů, nabídek a kvót.](azure-stack-plan-offer-quota-overview.md)
- [Vytvoření kvóty při vytváření plánu.](azure-stack-create-plan.md)
