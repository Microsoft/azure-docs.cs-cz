---
title: Použití imagí klienta Windows v Azure | Dokumentace Microsoftu
description: Jak používat výhody předplatného sady Visual Studio k nasazení Windows 7, Windows 8 nebo Windows 10 v Azure pro scénáře vývoje/testování
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: a2b9a06e8e3b1bda91050e1607c7265d6fe66bb1
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931785"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Použití klienta Windows v Azure pro scénáře vývoje/testování
Můžete použít Windows 7, Windows 8 nebo Windows 10 Enterprise (x64) v Azure pro scénáře vývoje a testování za předpokladu, že máte příslušné předplatné sady Visual Studio (dříve MSDN). Tento článek popisuje, jak požadavky zakládající nárok pro spouštění Windows 7, Windows 8.1, Windows 10 Enterprise v Azure a použití následujících imagí Galerie Azure.

![Podrobnosti o Image z webu Azure portal](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Windows 10 Pro a Windows 10 Pro N image v galerii Azure, najdete [nasazení Windows 10 v Azure s práv hostování více klientů](windows-desktop-multitenant-hosting-deployment.md)
>![podrobnosti Pro Image z webu Azure portal](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Předplatné způsobilosti
Aktivní předplatitelé sady Visual Studio (uživatelů, kteří získali licenci předplatného sady Visual Studio) můžete použít klienta Windows pro účely vývoje a testování. Klient Windows je možné na hardware a virtuálních počítačů Azure s z libovolného typu předplatného Azure. Klient Windows nemusí nasazené do nebo používané na platformě Azure pro použití v produkčním prostředí normální nebo používají lidé, kteří nejsou aktivní předplatitelé sady Visual Studio.

Pro usnadnění práce určité Image Windows 10 jsou k dispozici z Galerie Azure v rámci [způsobilé pro vývoj/testování nabízí](#eligible-offers). Předplatitelé sady Visual Studio v rámci libovolného typu nabídka lze také [odpovídajícím způsobem připravit a vytvořit](prepare-for-upload-vhd-image.md) 64bitová verze Windows 7, Windows 8 nebo Windows 10 image a pak [nahrát do Azure](upload-generalized-managed.md). Použití zůstává omezen na vývoj a testování aktivní předplatitelé sady Visual Studio.

## <a name="eligible-offers"></a>Nabídky oprávněné
Následující tabulka obsahuje podrobnosti o nabídce ID, které jsou vhodné pro nasazení Windows 10 v galerii Azure. Image Windows 10 jsou viditelné pouze pro následující nabídky. Předplatitelé sady Visual Studio, kteří potřebují používat klienta Windows v typu jinou nabídku vyžadují, abyste [odpovídajícím způsobem připravit a vytvořit](prepare-for-upload-vhd-image.md) bitovou kopii 64bitová verze Windows 7, Windows 8 nebo Windows 10 a [nahrajte do Azure](upload-generalized-managed.md).

| Název nabídky | Číslo nabídky | Image dostupných klientů |
|:--- |:---:|:---:|
| [Vývoj/testování s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Předplatitelé sady Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Předplatitelé sady Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Předplatitelé sady Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium with MSDN (výhoda)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Předplatitelé sady Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Předplatitelé sady Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise pro vývoj/testování](https://azure.microsoft.com/en-us/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Zkontrolujte vaše předplatné Azure
Pokud si nejste jisti vaše ID nabídky, můžete ho získat prostřednictvím webu Azure portal v jednom z těchto dvou způsobů:  

- Na *předplatná* okno:

  ![Podrobnosti ID nabídky na webu Azure Portal](./media/client-images/offer-id-azure-portal.png) 

- Nebo klikněte na tlačítko **fakturace** a potom klikněte na ID vašeho předplatného. ID se zobrazí v nabídce *fakturace* okna.

Můžete také zobrazit ID nabídky z [kartu "Předplatné"](http://account.windowsazure.com/Subscriptions) portálu účtů Azure:

![Podrobnosti o nabídce ID z portálu účtu Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Další postup
Teď můžete nasadit virtuální počítače pomocí [PowerShell](quick-create-powershell.md), [šablon Resource Manageru](ps-template.md), nebo [sady Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

