---
title: Zobrazení spotřeby veřejných IP adres ve službě Azure Stack | Dokumentace Microsoftu
description: Správci mohou prohlížet spotřeby veřejných IP adres v oblasti
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/14/2019
ms.author: mabrigg
ms.openlocfilehash: 6d202ab9e2f6a8e96eb494d3d93caf66624f8364
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465189"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Zobrazení spotřeby veřejných IP adres ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Jako správce cloudu můžete zobrazit:
 - Počet veřejných IP adres, které byly přiděleny klientům.
 - Počet veřejných IP adres, které jsou stále k dispozici pro přidělení.
 - Procento veřejné IP adresy, které byly přiděleny v dané oblasti.

**Veřejnou IP adresu fondu využití** dlaždice ukazuje počet veřejných IP adres spotřebované ve veřejných IP adres. Pro každou IP adresu zobrazí na dlaždici využití pro tenanta virtuálních počítačů IaaS instancí, služby infrastruktury prostředků infrastruktury a veřejné IP adresy prostředků, které se byly explicitně tenanty.

Účelem dlaždice je poskytovat operátorům Azure stacku představu o počtu veřejné IP adresy používané v tomto umístění. Číslo pomáhá správcům určit, jestli jsou spuštěné s nízkou pro tento prostředek.

**Veřejné IP adresy** položky nabídky v části **prostředků klienta** uvádí pouze veřejné IP adresy, které byly *explicitně vytvořený tenanty*. Položka nabídky můžete najít na **poskytovatelů prostředků**, **sítě** podokně. Počet **používá** veřejné IP adresy na **veřejnou IP adresu fondu využití** dlaždici se liší od (větší než) vždy na číslo **veřejné IP adresy** dlaždici v části  **Tenant prostředky**.

## <a name="view-the-public-ip-address-usage-information"></a>Zobrazení využití informace o veřejné IP adresy

Chcete-li zobrazit celkový počet veřejných IP adres, které spotřebovaly v oblasti:

1. Na portálu správce Azure Stack, vyberte **všechny služby**. Potom v části **správu** vyberte kategorii **sítě**.
1. **Sítě** podokně se zobrazí **veřejnou IP adresu fondu využití** v dlaždici **přehled** části.

![Podokno sítě poskytovatele prostředků](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

**Používá** číslo představuje číslo přiřazené veřejné IP adresy z veřejných IP adres. **Free** číslo představuje počet veřejných IP adres z veřejných IP adres fondy, které nebyly přiřazeny a stále k dispozici. **% Used** číslo představuje číslo použít nebo přiřazené adresy jako procento z celkového počtu veřejných IP adres v veřejných IP adres v dané oblasti.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Zobrazení veřejné IP adresy, které byly vytvořeny odběry tenanta

Vyberte **veřejné IP adresy** pod **prostředků klienta**. Projděte si seznam veřejných IP adres, které explicitně vytvořeny odběry tenanta v určité oblasti.

![Veřejné IP adresy klienta](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Můžete si všimnout, že některé veřejné IP adresy, které dynamicky přiřazovány zobrazí v seznamu. Ale adresu nebyl přidružen s nimi ještě. Poskytovatel síťových prostředků, ale ještě není v síťovém adaptéru se vytvořil prostředek adresy.

Síťový adaptér nepřiřazuje adresu prostředku, dokud se váže k rozhraní, karty síťového rozhraní (NIC), nástroj pro vyrovnávání zatížení nebo bránu virtuální sítě. Když veřejnou IP adresu vytvoří vazbu na rozhraní, se síťovým adaptérem přidělí IP adresu. Adresa se zobrazí v **adresu** pole.

## <a name="view-the-public-ip-address-information-summary-table"></a>Zobrazení veřejné IP adresy informace tabulku souhrnu

V různých případech jsou přiřazené veřejné IP adresy, které určují, zda se adresa bude zobrazovat v jednom seznamu nebo jiného.

| **Veřejné IP adresy přiřazení případu** | **Zobrazí se v souhrnu využití** | **Zobrazí se v seznamu v tenantovi veřejné IP adresy** |
| --- | --- | --- |
| Dynamická veřejná IP adresa ještě nebyly přiřazeny síťové KARTĚ nebo službu Vyrovnávání zatížení (dočasný) |Ne |Ano |
| Dynamická veřejná IP adresa přiřazená síťové KARTĚ nebo službu Vyrovnávání zatížení. |Ano |Ano |
| Statickou veřejnou IP adresu přiřazenou tenanta síťovou kartu nebo službu Vyrovnávání zatížení. |Ano |Ano |
| Statickou veřejnou IP adresu přiřadit ke koncovému bodu služby infrastruktury prostředků infrastruktury. |Ano |Ne |
| Veřejná IP adresa implicitně vytvoří pro instance virtuálních počítačů IaaS a používá pro odchozí NAT ve virtuální síti. Tyto jsou vytvořeny na pozadí, pokaždé, když si tenant vytvoří instanci virtuálního počítače tak, aby virtuální počítače můžete odeslat informace k Internetu. |Ano |Ne |

## <a name="next-steps"></a>Další postup

[Správa účtů úložiště v Azure stacku](azure-stack-manage-storage-accounts.md)