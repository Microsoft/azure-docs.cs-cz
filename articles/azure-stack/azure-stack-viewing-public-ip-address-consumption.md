---
title: Zobrazit veřejnou IP adresu spotřebu v zásobníku Azure | Microsoft Docs
description: Správci mohou prohlížet spotřeby veřejné IP adresy v oblasti.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 50bf01d6de6105d3041c6bb88e803f3d110f751d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
ms.locfileid: "29742454"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Zobrazení veřejnou IP adresu energie v zásobníku Azure

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Jako správce cloudu můžete zobrazit:
 - Počet veřejné IP adresy, které byly přiděleny klientům.
 - Počet veřejné IP adresy, které jsou stále k dispozici pro přidělení.
 - Procento veřejné IP adresy, které byly přiděleny v tomto umístění.

**Veřejnou IP adresu do fondu využití** dlaždice zobrazuje počet veřejných IP adres spotřebováno přes veřejný fondy IP adres. Pro každou IP adresu na dlaždici zobrazuje využití pro klienta virtuálních počítačů IaaS instancí služby infrastruktury prostředků infrastruktury a adresu prostředky veřejné IP adresy, explicitně vytvořené klienty.

Účelem dlaždice je umožnit operátory Azure zásobníku představu o počet veřejné IP adresy použité v tomto umístění. Číslo pomáhá správcům určit, zda běží nízkou u tohoto prostředku.

**Veřejné IP adresy** položky nabídky v části **prostředky klienta** jsou uvedeny pouze veřejné IP adresy, které byly *explicitně vytvořené klienty*. Položky nabídky můžete najít na **zprostředkovatelé prostředků**, **sítě** podokně. Počet **používá** veřejné IP adresy na **veřejnou IP adresu do fondu využití** dlaždice se liší od (větší než) vždy na číslo **veřejné IP adresy** dlaždici v části  **Prostředky klienta**.

## <a name="view-the-public-ip-address-usage-information"></a>Zobrazení informací o veřejné IP adresy používání
Chcete-li zobrazit celkový počet veřejné IP adresy, které bylo spotřebováno v oblasti:

1. V portálu správce Azure zásobníku vyberte **další služby**v části **prostředky pro správu**, vyberte **zprostředkovatelé prostředků**.
2. Ze seznamu **zprostředkovatelé prostředků**, vyberte **sítě**.
3. **Sítě** podokně se zobrazí **veřejnou IP adresu do fondu využití** dlaždice v nástroji **přehled** části.

![Podokno poskytovatele prostředků sítě](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

**Používá** číslo představuje číslo přiřazené veřejné IP adresy z veřejné fondy IP adres. **Volné** číslo představuje počet veřejných IP adres z veřejných IP adres, fondy, které nebyly přiřazeny a jsou stále k dispozici. **% Používá** číslo představuje počet použít nebo přiřazené adresy jako procento z celkového počtu veřejné IP adresy v veřejné fondy IP adres v daném umístění.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Zobrazit veřejné IP adresy, které byly vytvořeny odběry klienta
Vyberte **veřejné IP adresy** pod **prostředky klienta**. Projděte si seznam veřejné IP adresy, které explicitně vytvořené odběry klienta v určité oblasti.

![Veřejné IP adresy klienta](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Můžete si všimnout, že některé veřejné IP adresy, které byly přiděleny dynamicky se objeví v seznamu. Ale adresu nebyl přidružen je ještě. Prostředek adresy byla vytvořena v poskytovatele síťových prostředků, ale ještě nebyla v síťovém adaptéru.

Síťový adaptér nepřiřazuje adresu k prostředku, dokud se váže k rozhraní, síťová karta (NIC), nástroj pro vyrovnávání zatížení nebo brány virtuální sítě. Když veřejnou IP adresu se váže k rozhraní, síťový adaptér přiděluje IP adresu. Adresa se zobrazí v **adresu** pole.

## <a name="view-the-public-ip-address-information-summary-table"></a>Zobrazení veřejnou IP adresu informace tabulku souhrnu
V různých případech veřejné IP adresy přiřazené, které určují, zda adresa se zobrazí v seznamu jednoho nebo druhého.

| **Veřejná IP adresa přiřazení případu** | **Zobrazí se v souhrnu využití** | **Zobrazí se v seznamu klienta veřejné IP adresy** |
| --- | --- | --- |
| Dynamické veřejnou IP adresu dosud není přiřazen k seskupování nebo službu Vyrovnávání zatížení (dočasný) |Ne |Ano |
| Dynamické veřejnou IP adresu přiřazené seskupování nebo službu Vyrovnávání zatížení. |Ano |Ano |
| Statickou veřejnou IP adresu se přiřadila klienta síťový adaptér nebo službu Vyrovnávání zatížení. |Ano |Ano |
| Statickou veřejnou IP adresu přiřadit pro koncový bod služby infrastruktury prostředků infrastruktury. |Ano |Ne |
| Veřejná IP adresa implicitně vytvořené pro instance virtuálních počítačů IaaS a používat pro odchozí NAT ve virtuální síti. Tyto soubory jsou vytvořeny na pozadí vždy, když klient vytvoří instanci virtuálního počítače tak, aby virtuální počítače můžete odesílat informace k Internetu. |Ano |Ne |

## <a name="next-steps"></a>Další postup
[Správa účtů úložiště v Azure zásobníku](azure-stack-manage-storage-accounts.md)