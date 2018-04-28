---
title: Typy kvót v zásobníku Azure | Microsoft Docs
description: Projděte si různé kvóty typy, které jsou dostupné pro služby a prostředky v Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/20/2018
ms.author: brenduns
ms.reviewer: xiaofmao
ms.openlocfilehash: b68a963dae4b3621bfd9ecdcbc20146d7b20c457
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="quota-types-in-azure-stack"></a>Typy kvót v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

[Kvóty](azure-stack-plan-offer-quota-overview.md#plans) definovat omezení prostředků, které uživatel předplatné můžete zřizovat nebo využívat. Například může kvótu umožnit uživateli vytvořit až pět virtuální počítače. Každý prostředek může mít svůj vlastní typy kvót.

## <a name="compute-quota-types"></a>Výpočetní kvóty typy
| **Typ** | **Výchozí hodnota** | **Popis** |
| --- | --- | --- |
| Maximální počet virtuálních počítačů | 20 | Maximální počet virtuálních počítačů, které předplatné můžete vytvořit v tomto umístění. |
| Maximální počet jader virtuálního počítače | 50 | Maximální počet jader, které předplatné můžete vytvořit v tomto umístění (například virtuální počítač A3 má čtyři jádra). |
| Nastaví maximální počet dostupnosti | 10 | Maximální počet sad dostupnosti, které lze vytvořit v tomto umístění. |
| Nastaví maximální počet škálování virtuálních počítačů | 20 | Maximální počet sady škálování virtuálního počítače, které lze vytvořit v tomto umístění. |



## <a name="storage-quota-types"></a>Typy kvótu úložiště
| **Položka** | **Výchozí hodnota** | **Popis** |
| --- | --- | --- |
| Maximální kapacita (GB) |500 |Celkový úložný kapacity, které mohou být spotřebovávána předplatné v tomto umístění. |
| Celkový počet účtů úložiště |20 |Maximální počet účtů úložiště, které předplatné můžete vytvořit v tomto umístění. |

> [!NOTE]  
> Může trvat až dvě hodiny před vynucením nového kvótu úložiště. 
> 
> 

## <a name="network-quota-types"></a>Typy síťových kvóty
| **Položka** | **Výchozí hodnota** | **Popis** |
| --- | --- | --- |
| Maximální počet veřejných IP adres |50 |Maximální počet veřejných IP adres, které předplatné můžete vytvořit v tomto umístění. |
| Maximální počet virtuálních sítí |50 |Maximální počet virtuálních sítí, které předplatné můžete vytvořit v tomto umístění. |
| Maximální počet brány virtuální sítě |1 |Maximální počet brány virtuální sítě (VPN Gateway), které předplatné můžete vytvořit v tomto umístění. |
| Maximální počet síťových připojení |2 |Maximální počet připojení sítě (typu point-to-point nebo site-to-site), která může vytvořit odběr pro všechny brány virtuální sítě v tomto umístění. |
| Maximální počet nástroje pro vyrovnávání zatížení |50 |Maximální počet nástroje pro vyrovnávání zatížení, které předplatné můžete vytvořit v tomto umístění. |
| Maximální počet síťových karet |100 |Maximální počet síťových rozhraní, které předplatné můžete vytvořit v tomto umístění. |
| Maximální počet skupin zabezpečení sítě |50 |Maximální počet skupin zabezpečení sítě, které předplatné můžete vytvořit v tomto umístění. |

## <a name="view-an-existing-quota"></a>Zobrazit existující kvótu
1. Klikněte na tlačítko **další služby** > **zprostředkovatelé prostředků**.
2. Vyberte službu s kvótu, kterou chcete zobrazit.
3. Klikněte na tlačítko **kvóty**a vyberte kvóty, které chcete zobrazit.

## <a name="next-steps"></a>Další postup
[Další informace o plánech, nabídky a kvóty.](azure-stack-plan-offer-quota-overview.md)

[Vytvoření kvóty při vytváření plánu.](azure-stack-create-plan.md)
