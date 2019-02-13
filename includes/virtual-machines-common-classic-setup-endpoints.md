---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/23/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: e7dfd7d2a0363a95acb76a5dc214dbd4036de11d
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735890"
---
Každý koncový bod má *veřejný port* a *privátní port*:

* Veřejný port se používá nástroj pro vyrovnávání zatížení Azure k naslouchání příchozímu provozu k virtuálnímu počítači z Internetu.
* Privátní port se používá pro virtuální počítač k naslouchání příchozímu provozu, obvykle směřující do aplikace nebo služby spuštěné na virtuálním počítači.

Výchozí hodnoty pro protokol IP a porty TCP nebo UDP pro dobře známé síťové protokoly jsou k dispozici koncové body vytvořené pomocí webu Azure portal. Vlastní koncové body zadejte správný protokol IP (TCP nebo UDP) a veřejné a soukromé porty. Chcete-li náhodně distribuce příchozího provozu napříč několika virtuálními počítači, vytvořte sadu s vyrovnáváním zatížení skládající se z více koncových bodů.

Po vytvoření koncového bodu, můžete použít seznam řízení přístupu (ACL) k definování pravidel, která povolují nebo odpírají příchozího provozu na veřejném portu koncového bodu podle jeho Zdrojová IP adresa. Nicméně pokud virtuální počítač je ve službě Azure virtual network, použijte skupiny zabezpečení sítě. Další informace najdete v tématu [o skupinách zabezpečení sítě](../articles/virtual-network/security-overview.md).

> [!NOTE]
> Konfigurace brány firewall pro virtuální počítače Azure se provádí automaticky pro porty, které jsou spojené s koncovými body vzdálené připojení, které Azure nastaví automaticky. Pro porty určené pro všechny ostatní koncové body se automaticky provádí žádná konfigurace pro bránu firewall virtuálního počítače. Když vytvoříte koncový bod pro virtuální počítač, ujistěte se, že brána firewall virtuálního počítače také umožňuje přenos pro protokol a privátní port odpovídající konfigurace koncového bodu. Pokud chcete nakonfigurovat bránu firewall, naleznete v dokumentaci nebo online nápovědu pro operační systém spuštěný na virtuálním počítači.
>
>

## <a name="create-an-endpoint"></a>Vytvoření koncového bodu
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **virtuálních počítačů**a potom vyberte virtuální počítač, který chcete konfigurovat.

3. Vyberte **koncové body** v **nastavení** skupiny. **Koncové body** se zobrazí stránka, která obsahuje všechny aktuální koncové body pro virtuální počítač. (V tomto příkladu je pro virtuální počítač s Windows. Virtuální počítač s Linuxem ve výchozím nastavení zobrazí koncový bod pro SSH.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Koncové body](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)


4. Na panelu příkazů výše položky koncový bod, vyberte **přidat**. **Přidat koncový bod** zobrazí se stránka.

5. Pro **název**, zadejte název koncového bodu.

6. Pro **protokol**, zvolte buď **TCP** nebo **UDP**.

7. Pro **veřejný port**, zadejte číslo portu pro příchozí provoz z Internetu. 

8. Pro **privátní port**, zadejte číslo portu, na kterém naslouchá virtuálního počítače. Čísla veřejný i privátní port může být jiný. Ujistěte se, že brány firewall na virtuálním počítači byla nakonfigurovaná pro povolení provozu odpovídající protokol a privátní port.

9. Vyberte **OK**.

Nový koncový bod je uvedená na **koncové body** stránky.

![Úspěšné vytvoření koncového bodu](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Správa seznamu ACL v koncovém bodě
Chcete-li definovat množinu počítačů, které mohou odesílat provoz, můžete omezit seznam ACL v koncovém bodě provoz na základě zdrojové IP adresy. Použijte následující postup přidání, úprava nebo odebrání seznamu ACL v koncovém bodě.

> [!NOTE]
> Pokud koncový bod je součástí sady s vyrovnáváním zatížení, všechny změny provedené v seznamu ACL v koncovém bodě se použijí pro všechny koncové body v sadě.
>
>

Pokud virtuální počítač je ve službě Azure virtual network, namísto seznamy řízení přístupu pomocí skupin zabezpečení sítě. Další informace najdete v tématu [o skupinách zabezpečení sítě](../articles/virtual-network/security-overview.md).

1. Přihlaste se k portálu Azure.

2. Vyberte **virtuálních počítačů**a potom vyberte název virtuálního počítače, který chcete konfigurovat.

3. Vyberte **koncové body**. V seznamu koncových bodů vyberte příslušný koncový bod. Seznam ACL je v dolní části stránky.

   ![Zadejte podrobnosti seznamu ACL](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Pomocí řádků v seznamu přidat, odstranit nebo upravit pravidla pro seznam ACL a změňte jejich pořadí. **Vzdálené PODSÍTĚ** hodnota je rozsah IP adres pro příchozí provoz z Internetu, která používá Azure load balancer můžete povolit nebo zakázat provoz na základě zdrojové IP adresy. Nezapomeňte zadat rozsah IP adres ve classless Inter-Domain směrování (CIDR Classless) formátu, označované také jako formát předponu adresy. Například, `10.1.0.0/8`.

 ![Nová položka seznamu ACL](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Pravidla můžete použít pro povolení provozu pouze z určitých počítačů odpovídající počítače na Internetu nebo pro odepření provozu z rozsahů adres konkrétní, známé.

Pravidla se vyhodnocují v pořadí od prvního pravidla a končí poslední pravidlo. Proto pravidla by měla být seřazené od nejméně omezující nejvíce omezující. Další informace najdete v tématu [k čemu slouží seznamy řízení přístupu síť](../articles/virtual-network/virtual-networks-acl.md).
