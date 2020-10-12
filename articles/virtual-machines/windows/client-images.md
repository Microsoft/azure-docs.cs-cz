---
title: Použití imagí klienta Windows v Azure
description: Jak používat výhody předplatného sady Visual Studio k nasazení Windows 7, Windows 8 nebo Windows 10 v Azure pro scénáře vývoje a testování
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 26bfd9a65cd3e6d36b8190e87d382543cc7f0f60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292050"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Použití klienta Windows v Azure pro scénáře vývoje a testování
V Azure můžete použít Windows 7, Windows 8 nebo Windows 10 Enterprise (x64) pro scénáře vývoje a testování, pokud máte příslušné předplatné sady Visual Studio (dříve MSDN). Tento článek popisuje požadavky na způsobilost pro používání Windows 7, Windows 8.1, Windows 10 Enterprise v Azure a používání následujících imagí Azure Gallery.

![Podrobnosti o imagi z Azure Portal](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Pro image Windows 10 pro a Windows 10 pro N v galerii Azure si prosím přečtěte [článek nasazení Windows 10 v Azure s právy](windows-desktop-multitenant-hosting-deployment.md) 
> ![ pro Image hostování pro víceklientské prostředí z Azure Portal](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Způsobilost pro předplatné
Aktivní předplatitelé sady Visual Studio (lidé, kteří získali licenci k předplatnému sady Visual Studio) můžou používat klienta Windows pro účely vývoje a testování. Klient Windows se dá použít na vlastním hardwaru a virtuálních počítačích Azure, které běží v jakémkoli typu předplatného Azure. Klient Windows se nemusí nasadit do Azure nebo ho používat pro normální produkční použití, nebo ho můžou použít lidé, kteří nejsou aktivními předplatiteli sady Visual Studio.

Pro usnadnění práce jsou některé image Windows 10 dostupné z Galerie Azure v rámci [způsobilých nabídek pro vývoj a testování](#eligible-offers). Předplatitelé sady Visual Studio v jakémkoli typu nabídky můžou také [dostatečně připravit a vytvářet](prepare-for-upload-vhd-image.md) image s 64 Windows 7, Windows 8 nebo Windows 10 a pak [nahrávat do Azure](upload-generalized-managed.md). Použití zůstává omezené na vývoj a testování prostřednictvím aktivních předplatitelů sady Visual Studio.

## <a name="eligible-offers"></a>Opravňující nabídky
Následující tabulka popisuje ID nabídek, které mají nárok na nasazení Windows 10 prostřednictvím Galerie Azure. Image Windows 10 jsou viditelné jenom pro následující nabídky. Předplatitelé sady Visual Studio, kteří potřebují spustit klienta Windows v jiném typu nabídky, vyžadují, abyste si [vhodně připravili a vytvořili](prepare-for-upload-vhd-image.md) 64 bitovou kopii Windows 7, Windows 8 nebo Windows 10 a [pak ji nahráli do Azure](upload-generalized-managed.md).

| Název nabídky | Číslo nabídky | Dostupné image klientů |
|:--- |:---:|:---:|
| [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Předplatitelé sady Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Visual Studio Professional předplatitelé](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Předplatitelé sady Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium with MSDN (výhoda)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Předplatitelé sady Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Předplatitelé sady Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Kontrolovat předplatné Azure
Pokud ID vaší nabídky neznáte, můžete ho získat pomocí Azure Portal jedním z těchto dvou způsobů:  

- V okně *předplatná* :

  ![Podrobnosti ID nabídky z Azure Portal](./media/client-images/offer-id-azure-portal.png) 

- Případně klikněte na **fakturace** a potom klikněte na ID vašeho předplatného. ID nabídky se zobrazí v okně *fakturace* .

ID nabídky si můžete prohlédnout také na [kartě předplatné](https://account.windowsazure.com/Subscriptions) na portálu účtů Azure:

![Podrobnosti ID nabídky na portálu účtů Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Další kroky
Virtuální počítače teď můžete nasadit pomocí [PowerShellu](quick-create-powershell.md), [Správce prostředků šablon](ps-template.md)nebo sady [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
