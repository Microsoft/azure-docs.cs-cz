---
title: Podpora Azure Resource Manageru pro nástroj pro vyrovnávání zatížení | Dokumentace Microsoftu
description: Použití prostředí powershell pro nástroj pro vyrovnávání zatížení pomocí Azure Resource Manageru. Nástroje pro vyrovnávání zatížení pomocí šablony
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 596ac871067886ee3124c0f21beb35cb3b8fe1ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888982"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Podpora Azure Resource Manageru pomocí nástroje pro vyrovnávání zatížení Azure



Azure Resource Manager je upřednostňovaný správu platforma pro služby v Azure. Nástroj Azure Load Balancer je možné spravovat pomocí nástroje a rozhraní API založená na Azure Resource Manageru.

## <a name="concepts"></a>Koncepty

S Resource Managerem se Azure Load Balancer obsahuje následující podřízené prostředky:

* Konfiguraci front-end IP adresy – nástroj pro vyrovnávání zatížení může zahrnovat jeden nebo více front-endové IP adresy, jinak známé jako virtuální IP adresy (VIP). Tyto IP adresy slouží jako vstup pro přenos.
* Back endový fond adres – Toto jsou IP adresy, které jsou spojené s virtuální počítač síťových adaptérů (NIC) ke kterému se distribuuje zatížení.
* Pravidla Vyrovnávání zatížení – vlastnost pravidla mapuje danou front-endová IP a kombinace portu na sadu back endové IP adresy a portu. Nástroj pro vyrovnávání zatížení může mít několik pravidel vyrovnávání zatížení. Každé pravidlo je kombinace IP front-endová IP a port a back-end a port přidružený k virtuálním počítačům.
* Sondy – testy umožňují udržovat přehled o stavu instance virtuálních počítačů. Pokud selže test stavu instance virtuálního počítače je automaticky vyřazen ze smyčky.
* Pravidla příchozího překladu adres – NAT pravidla definující příchozí provoz prostřednictvím protokolu IP front-endu a distribuovat do back endovou IP adresu.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Šablony Rychlý start

Azure Resource Manager umožňuje zřizovat aplikace pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Stejnou šablonu můžete použít k opakovanému nasazení aplikace během každé fáze životního cyklu této aplikace.

Šablony mohou obsahovat definice pro virtuální počítače, virtuální sítě, dostupnosti, síťová rozhraní (NIC), účty úložiště, nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě a veřejné IP adresy. Pomocí šablon můžete vytvořit vše potřebné pro komplexní aplikace. Soubor šablony může být zařazeno do systému správy obsahu pro správu verzí a spolupráci.

[Další informace o šablonách](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Další informace o síťových prostředků](../networking/networking-overview.md)

Používání služby Azure Load Balancer šablony pro rychlý start, najdete v článku [úložiště GitHub](https://github.com/Azure/azure-quickstart-templates) , který je hostitelem sadu community vygenerované šablony.

Příklady šablon:

* [2 virtuální počítače v Load Balanceru a pravidla Vyrovnávání zatížení](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 virtuální počítače ve virtuální síti pomocí pravidel pro interní nástroj pro vyrovnávání zatížení a nástroje pro vyrovnávání zatížení](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 virtuální počítače v nástroji pro vyrovnávání zatížení a nakonfigurujte pravidla překladu adres na LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Nastavení nástroje pro vyrovnávání zatížení Azure pomocí Powershellu nebo rozhraní příkazového řádku

Začínáme s Azure Resource Manageru rutiny, nástroje příkazového řádku a rozhraní REST API

* [Rutiny Azure sítě](https://docs.microsoft.com/powershell/module/az.network#networking) slouží k vytvoření nástroje pro vyrovnávání zatížení.
* [Jak vytvořit nástroj pro vyrovnávání zatížení pomocí Azure Resource Manageru](load-balancer-get-started-ilb-arm-ps.md)
* [Použití Azure CLI s Azure Resource Management](../xplat-cli-azure-resource-manager.md)
* [Rozhraní REST API nástroje pro vyrovnávání zatížení](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Další postup

Můžete také [Začínáme vytvářet internetový nástroj pro vyrovnávání zatížení](load-balancer-get-started-internet-arm-ps.md) a nakonfigurovat, jaký typ [distribučního režimu](load-balancer-distribution-mode.md) pro konkrétní zatížení nástroje pro vyrovnávání síťové přenosy chování.

Další informace o správě [nastavení časového limitu TCP pro nástroj pro vyrovnávání zatížení nečinnosti](load-balancer-tcp-idle-timeout.md). To je důležité, když vaše aplikace potřebuje udržovat aktivní servery za nástroj pro vyrovnávání zatížení připojení.
