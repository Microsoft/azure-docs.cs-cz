---
title: Typy kvót ve službě Azure Stack | Dokumentace Microsoftu
description: Zobrazit a upravit jinou kvóty typů dostupných pro služby a prostředky ve službě Azure Stack.
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
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 88cbfc895d188162d0f242a5aceaab721a6c877f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239091"
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
| Maximální kapacita (v GB) spravovaných disků úrovně standard | 2 048 | Maximální kapacita spravované disky úrovně standard, vytvořené v tomto umístění. |
| Maximální kapacita spravovaný disk úrovně premium (v GB) | 2 048 | Maximální kapacita premium spravované disky, které lze vytvořit v tomto umístění. |

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
| Maximální veřejné IP adresy |50 |Maximální počet veřejných IP adres, které předplatné může vytvořit v tomto umístění. |
| Maximální virtuální sítě |50 |Maximální počet virtuálních sítí, které předplatné můžete vytvořit v tomto umístění. |
| Maximální virtuální síťové brány |1 |Maximální počet brány virtuální sítě (VPN Gateway), které předplatného můžete vytvořit v tomto umístění. |
| Maximální síťová připojení |2 |Maximální počet síťových připojení (point-to-point nebo site-to-site), které předplatné můžete vytvořit přes všechny brány virtuální sítě v tomto umístění. |
| Nástroje pro vyrovnávání zatížení maximální |50 |Maximální počet nástrojů pro vyrovnávání zatížení, které předplatné můžete vytvořit v tomto umístění. |
| Maximální síťových adaptérů |100 |Maximální počet síťových rozhraní, které předplatné můžete vytvořit v tomto umístění. |
| Skupiny zabezpečení sítě maximální |50 |Maximální počet skupin zabezpečení sítě, které předplatné můžete vytvořit v tomto umístění. |

## <a name="view-an-existing-quota"></a>Zobrazit existující kvótu

Chcete-li zobrazit existující kvótu dvěma způsoby:

### <a name="plans"></a>Plány

1.  V levém navigačním podokně portálu správce, vyberte **plány**.
2.  Vyberte plán, že chcete zobrazit podrobnosti, kliknutím na jeho název.
3.  V okně, které se otevře, vyberte **služby a kvóty**.
4.  Vyberte kvót chcete zobrazíte ho kliknutím **název** sloupce.

    [ ![Kvóty](media/azure-stack-quota-types/quotas1sm.png "zobrazit kvóty") ](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Poskytovatelé prostředků

1. Na výchozí řídicí panel portálu pro správu, vyhledejte **poskytovatelů prostředků** dlaždici.
2. Vyberte službu, kterou chcete zobrazit, jako je třeba kvót **Compute**, **sítě**, nebo **úložiště**.
3. Vyberte **kvóty**a pak vyberte kvóty, které chcete zobrazit.

## <a name="edit-a-quota"></a>Upravit kvótu

Upravit kvótu dvěma různými způsoby:

### <a name="edit-a-plan"></a>Upravit plán

1.  V levém navigačním podokně portálu správce, vyberte **plány**.
2.  Vyberte plán, pro kterou chcete upravit kvótu, kliknutím na jeho název.
3.  V okně, které se otevře, vyberte **služby a kvóty**.
4.  Vyberte kvótu byste chtěli upravit kliknutím v **název** sloupce.
    [ ![Kvóty](media/azure-stack-quota-types/quotas1sm.png "zobrazit kvóty") ](media/azure-stack-quota-types/quotas1.png#lightbox)

5.  V okně, které se otevře, vyberte **upravit na výpočty**, **upravit v síti**, nebo **upravit ve službě Storage**.
    ![Kvóty](media/azure-stack-quota-types/quotas3.png "zobrazit kvóty")    

Alternativně můžete podle následujícího postupu upravit kvóty:

1. Na výchozí řídicí panel portálu správce, vyhledejte **poskytovatelů prostředků** dlaždici.
2. Vyberte službu, kterou chcete upravit, jako je třeba kvót **Compute**, **sítě**, nebo **úložiště**.
3. V dalším kroku vyberte **kvóty**a pak vyberte kvótu, kterou chcete změnit.
4. Na **kvót úložiště nastavte**, **nastavte výpočetní kvóty**, nebo **nastavit síťové kvóty** podokně (v závislosti na typu kvóty, které jste se rozhodli úpravy), upravte hodnoty a pak vyberte **Uložit**.

### <a name="edit-original-configuration"></a>Upravit původní konfiguraci.
  
Můžete také upravit původní konfigurace se kvóta místo [pomocí doplňkový plán](create-add-on-plan.md). Při úpravě kvótu novou konfiguraci automaticky platí globálně pro všechny plány, které používají tuto kvótu a všech stávajících předplatných, které používají tyto plány. Úprava kvóty je jiná než při použití doplňkový plán k poskytuje upravenou kvóty, které uživatel vybere pro přihlášení k odběru. 

Nové hodnoty pro kvótu platí globálně pro všechny plány, které používají upravené kvótu a ke všem stávajícím předplatným, které používají tyto plány. 

## <a name="next-steps"></a>Další postup

- [Další informace o plánů, nabídek a kvót.](azure-stack-plan-offer-quota-overview.md)
- [Vytvoření kvóty při vytváření plánu.](azure-stack-create-plan.md)
