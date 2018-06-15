---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: cfe675ca269a69c7c2bfa67638acd0afbcd1c8ea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371282"
---
Každý koncový bod má *veřejný port* a *privátní port*:

* Veřejný port se používá nástrojem pro vyrovnávání zatížení Azure pro naslouchání pro příchozí provoz na virtuální počítač z Internetu.
* Privátní port se používá pro virtuální počítač tak, aby naslouchala pro příchozí provoz, obvykle určené do aplikace nebo služby spuštěné na virtuálním počítači.

Výchozí hodnoty pro protokol IP a porty TCP nebo UDP pro dobře známé sítě, které jsou určeny protokoly, když vytvoříte koncové body pomocí portálu Azure. Pro vlastní koncové body musíte zadat správný protokol IP (TCP nebo UDP) a veřejné a privátní porty. Pokud chcete distribuovat náhodně příchozí provoz napříč více virtuálních počítačů, budete muset vytvořit sadu Vyrovnávání zatížení sítě skládající se z více koncových bodů.

Po vytvoření koncového bodu, můžete použít seznam řízení přístupu (ACL) můžete definovat pravidla povolení nebo odmítnutí příchozí provoz veřejný port, na základě jeho IP adresy zdrojového koncového bodu. Ale pokud virtuální počítač je ve virtuální síť Azure, je vhodnější použít skupiny zabezpečení sítě. Podrobnosti najdete v tématu [o skupinách zabezpečení sítě](../articles/virtual-network/security-overview.md).

> [!NOTE]
> Konfigurace brány firewall pro virtuální počítače Azure se provádí automaticky pro porty, které jsou přidružené k připojení k vzdálené koncové body, které Azure nastaví automaticky. Pro porty určené pro všechny ostatní koncové body se automaticky provádí žádná konfigurace brány firewall virtuálního počítače. Když vytvoříte koncový bod virtuálního počítače, musíte zajistit, že brána firewall virtuálního počítače také umožňuje přenos pro protokol a privátní port odpovídající konfiguraci koncového bodu. Chcete-li nakonfigurovat bránu firewall, naleznete v dokumentaci nebo online nápověda pro operační systém spuštěný na virtuálním počítači.
>
>

## <a name="create-an-endpoint"></a>Vytvoření koncového bodu
1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com).
2. Klikněte na tlačítko **virtuální počítače**a potom klikněte na název virtuálního počítače, který chcete nakonfigurovat.
3. Klikněte na tlačítko **koncové body** v **nastavení** skupiny. **Koncové body** stránce uvedeny všechny aktuální koncové body pro virtuální počítač. (V tomto příkladu je virtuální počítač s Windows. Linux virtuálního počítače se ve výchozím nastavení zobrazí koncový bod SSH.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Koncové body](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)

4. Na panelu příkazů výše položky koncového bodu, klikněte na tlačítko **přidat**.
5. Na **přidání koncového bodu** stránky, zadejte název koncového bodu v **název**.
6. V **protokol**, vyberte buď **TCP** nebo **UDP**.
7. V **veřejný Port**, zadejte číslo portu pro příchozí provoz z Internetu. V **privátní Port**, zadejte číslo portu, na kterém je virtuální počítač přijímá. Tato čísla portů se může lišit. Ujistěte se, že v bráně firewall na virtuální počítač nakonfigurovaný umožňující přenos odpovídající protokol (v kroku 6) a privátní port.
10. Klikněte na tlačítko **OK**.

Nový koncový bod se objeví na **koncové body** stránky.

![Úspěšné vytvoření koncového bodu](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Správa seznamu ACL v koncovém bodě
K definování sady počítačů, které mohou odesílat provoz, můžete omezit seznam ACL u koncového bodu provozu na základě zdrojové IP adresy. Postupujte podle těchto kroků přidávat, upravovat nebo odebírat ACL v koncovém bodě.

> [!NOTE]
> Pokud koncový bod je součástí skupiny s vyrovnáváním zatížení, všechny změny provedené v seznamu ACL v koncovém bodě platí pro všechny koncové body v sadě.
>
>

Pokud virtuální počítač je ve virtuální síť Azure, doporučujeme skupin zabezpečení sítě místo seznamy ACL. Podrobnosti najdete v tématu [o skupinách zabezpečení sítě](../articles/virtual-network/security-overview.md).

1. Pokud jste zatím žádný nevytvořili, přihlášený k portálu Azure.
2. Klikněte na tlačítko **virtuální počítače**a potom klikněte na název virtuálního počítače, který chcete nakonfigurovat.
3. Klikněte na **Koncové body**. Ze seznamu vyberte příslušný koncový bod. V seznamu ACL je v dolní části stránky.

   ![Zadejte podrobnosti seznamu ACL](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Řádky v seznamu použijte k přidání, odstranění, nebo upravit pravidla pro seznam ACL a změnit jejich pořadí. **Vzdálené podsíti** hodnota je rozsah IP adres pro příchozí provoz z Internetu pomocí nástroje pro vyrovnávání zatížení Azure, která povolí nebo zakážou provoz na základě jeho zdrojové IP adresy. Nezapomeňte zadat rozsah IP adres ve formátu CIDR, také známé jako formát předponu adresy. Příklad: `10.1.0.0/8`.

 ![Nová položka seznamu ACL](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Můžete vytvořit pravidla a povolit pouze provoz z konkrétní počítače odpovídající počítače na Internetu nebo zakážou provoz z rozsahů adres konkrétní, známé.

Pravidla jsou vyhodnocovány v pořadí od první pravidlo a konče posledním pravidlem. To znamená, že by měl pravidla řazení z nejméně omezující na nejvíc omezující. Příklady a další informace najdete v tématu [co je seznam řízení přístupu sítě](../articles/virtual-network/virtual-networks-acl.md).
