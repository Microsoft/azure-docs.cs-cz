---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2e72d669abcc784fe8159fd4c54bd074dc60299c
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735862"
---
![Virtuální počítače v cloudové službě samostatné](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Pokud umístíte virtuální počítače ve virtuální síti, můžete se rozhodnout, kolik cloudových služeb, které chcete použít pro načtení vyrovnávání a skupiny dostupnosti. Virtuální počítače v podsítích můžete navíc uspořádat stejným způsobem jako místní sítě a připojení virtuální sítě k místní síti. Tady je příklad:

![Virtuální počítače ve virtuální síti](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Virtuální sítě jsou doporučeným způsobem, jak připojit virtuální počítače v Azure. Osvědčeným postupem je konfigurace jednotlivých vrstev aplikace v samostatných cloudovou službu. Ale budete muset zkombinovat některých virtuálních počítačů z různých aplikačních vrstev do stejné cloudové službě a větší než doporučovaných maximálně 200 cloudové služby na jedno předplatné. Tato a další omezení najdete v tématu [předplatné Azure a limity, kvóty a omezení](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Propojení virtuálních počítačů ve virtuální síti
Připojení virtuálních počítačů ve virtuální síti:

1. Vytvoření virtuální sítě [webu Azure portal](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) a zadejte "modelu nasazení classic".
2. Vytvořte cloudových služeb pro nasazení tak, aby odrážel návrhu pro skupiny dostupnosti a vyrovnávání zatížení. Na webu Azure Portal, klikněte na tlačítko **vytvořit prostředek > Compute > Cloudová služba** pro každou cloudovou službu.

  Při vyplňování podrobnosti cloudové služby, vyberte stejné _skupiny prostředků_ používat s virtuální sítí.

3. K vytvoření každého nového virtuálního počítače, klikněte na tlačítko **vytvořit prostředek > Compute**, pak vyberte vhodnou image virtuálního počítače z **vybrané aplikace**.

  Ve virtuálním počítači **Základy** okně vyberte stejné _skupiny prostředků_ používat s virtuální sítí.

  ![Okno základy virtuálních počítačů při použití virtuální sítě](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. Při vyplňování virtuální počítač **nastavení**, zvolte správné _Cloudová služba_ nebo _virtuální sítě_ pro virtuální počítač.

  Azure vybere další položky na základě vašeho výběru.

  ![Okno nastavení virtuálního počítače při použití virtuální sítě](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Připojte virtuální počítače v cloudové službě samostatné
Připojení virtuálních počítačů v cloudové službě samostatné:

1. Vytvoření cloudové služby v rámci [webu Azure portal](http://portal.azure.com). Klikněte na tlačítko **nový > Compute > Cloudová služba**. Nebo můžete vytvořit cloudovou službu pro nasazení, když vytvoříte svůj první virtuální počítač.
2. Když vytváříte virtuální počítače, zvolte stejnou skupinu prostředků použít s cloudovou službou.

  ![Přidat virtuální počítač do existující cloudové služby](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  Při vyplňování podrobnosti o virtuálním počítači, zvolte název cloudové služby, které jsou vytvořené v prvním kroku.

  ![Výběr cloudové služby pro virtuální počítač](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
