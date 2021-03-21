---
title: Použití imagí klienta Windows v Azure
description: Jak používat výhody předplatného sady Visual Studio k nasazení Windows 7, Windows 8 nebo Windows 10 v Azure pro scénáře vývoje a testování
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 21038a8d1eabfcca21329c093b866607f0343070
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200010"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Použití klienta Windows v Azure pro scénáře vývoje a testování
V Azure můžete použít Windows 7, Windows 8 nebo Windows 10 Enterprise (x64) pro scénáře vývoje a testování, pokud máte příslušné předplatné sady Visual Studio (dříve MSDN). 

Pokud chcete spustit Windows 10 v produkčním prostředí, přečtěte si [článek nasazení Windows 10 v Azure s právy pro hostování s více klienty](windows-desktop-multitenant-hosting-deployment.md).


## <a name="subscription-eligibility"></a>Způsobilost pro předplatné
Aktivní předplatitelé sady Visual Studio (lidé, kteří získali licenci k předplatnému sady Visual Studio) můžou používat image klientů Windows pro účely vývoje a testování. Image klientů Windows se dají používat na vlastním hardwaru nebo na virtuálních počítačích Azure.

Některé Image klientů Windows jsou dostupné z Azure Marketplace. Předplatitelé sady Visual Studio v jakémkoli typu nabídky můžou také [připravit a vytvářet](prepare-for-upload-vhd-image.md) 64 bitových kopií Windows 7, Windows 8 nebo Windows 10 a pak je [nahrát do Azure](upload-generalized-managed.md).

## <a name="eligible-offers-and-client-images"></a>Opravňující nabídky a image klientů
Následující tabulka popisuje ID nabídek, které mají nárok na nasazení imagí klientů Windows prostřednictvím Azure Marketplace. Image klientů Windows jsou viditelné jenom pro následující nabídky. 

| Název nabídky | Číslo nabídky | Dostupné image klientů | 
|:--- |:---:|:---:|
| [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N s aktualizací SP1 (x64) |
| [Předplatitelé sady Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N s aktualizací SP1 (x64) |
| [Visual Studio Professional předplatitelé](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N s aktualizací SP1 (x64) |
| [Předplatitelé sady Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N s aktualizací SP1 (x64) |
| [Visual Studio Premium with MSDN (výhoda)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N s aktualizací SP1 (x64) |
| [Předplatitelé sady Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N s aktualizací SP1 (x64) |
| [Předplatitelé sady Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N s aktualizací SP1 (x64) |
| [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N s aktualizací SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Kontrolovat předplatné Azure
Pokud ID vaší nabídky neznáte, můžete ho získat pomocí Azure Portal.  
- V okně *odběry* : ![ Podrobnosti ID nabídky z Azure Portal](./media/client-images/offer-id-azure-portal.png) 
- Případně klikněte na **fakturace** a potom klikněte na ID vašeho předplatného. ID nabídky se zobrazí v okně *fakturace* . 
- ID nabídky si můžete prohlédnout také na [kartě předplatné](https://account.windowsazure.com/Subscriptions) na portálu účtů Azure: ![ Podrobnosti ID nabídky na portálu účtů Azure.](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Další kroky
Virtuální počítače teď můžete nasadit pomocí [PowerShellu](quick-create-powershell.md), [Správce prostředků šablon](ps-template.md)nebo sady [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
