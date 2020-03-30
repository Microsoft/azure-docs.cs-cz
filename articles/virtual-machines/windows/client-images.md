---
title: Použití ibizklientů klientů Windows v Azure
description: Jak používat výhody předplatného Visual Studia k nasazení Windows 7, Windows 8 nebo Windows 10 v Azure pro scénáře pro vývoj a testování
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 812e6d251943d4418666f221ad8b5d2b6e501736
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74039505"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Použití klienta Windows v Azure pro scénáře vývoje a testování
Windows 7, Windows 8 nebo Windows 10 Enterprise (x64) můžete používat v Azure pro scénáře pro vývoj a testování za předpokladu, že máte příslušné předplatné Visual Studia (dříve MSDN). Tento článek popisuje požadavky na způsobilost pro systém Windows 7, Windows 8.1, Windows 10 Enterprise v Azure a použití následujících ibií Galerie Azure.

![Podrobnosti o obrázku z webu Azure Portal](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Obrázek Windows 10 Pro a Windows 10 Pro N v Azure Gallery najdete v části [Jak nasadit Windows 10 v Azure s víceklientským hostingovým právem](windows-desktop-multitenant-hosting-deployment.md)
>![Pro image z webu Azure Portal.](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Nárok na předplatné
Aktivní předplatitelé sady Visual Studio (uživatelé, kteří získali licenci předplatného sady Visual Studio) mohou pro účely vývoje a testování používat klienta Windows. Klient a Windows se dá používat na vašem vlastním hardwaru a virtuálních počítačích Azure spuštěných v libovolném typu předplatného Azure. Klient Windows nemusí být nasazený nebo používaný v Azure pro běžné produkční použití nebo používaný lidmi, kteří nejsou aktivnípředplatiteli sady Visual Studio.

Pro vaše pohodlí jsou některé image Windows 10 dostupné v Galerii Azure v rámci [vhodných nabídek pro vývoj a testování](#eligible-offers). Předplatitelé Visual Studia v rámci libovolného typu nabídky mohou také [adekvátně připravit a vytvořit](prepare-for-upload-vhd-image.md) 64bitovou bitovou kopii windows 7, Windows 8 nebo Windows 10 a pak [se nahrát do Azure](upload-generalized-managed.md). Použití zůstává omezeno na vývoj a testování aktivními předplatiteli sady Visual Studio.

## <a name="eligible-offers"></a>Způsobilé nabídky
V následující tabulce jsou podrobnosti o ID nabídky, které jsou způsobilé k nasazení Windows 10 prostřednictvím Galerie Azure. Obrázky windows 10 jsou viditelné pouze pro následující nabídky. Předplatitelé Visual Studia, kteří potřebují spustit klienta Windows v jiném typu nabídky, vyžadují, abyste [adekvátně připravili a vytvořili](prepare-for-upload-vhd-image.md) 64bitovou bitovou bitovou kopii windows 7, Windows 8 nebo Windows 10 a [pak se nahráli do Azure](upload-generalized-managed.md).

| Název nabídky | Číslo nabídky | Dostupné bitové kopie klientů |
|:--- |:---:|:---:|
| [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Předplatitelé sady Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Předplatitelé visual studia Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Předplatitelé sady Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium s MSDN (výhoda)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Předplatitelé sady Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Předplatitelé sady Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Kontrola předplatného Azure
Pokud id nabídky neznáte, můžete ho získat prostřednictvím portálu Azure jedním z těchto dvou způsobů:  

- V okně *Předplatná:*

  ![Podrobnosti o ID nabídky z webu Azure Portal](./media/client-images/offer-id-azure-portal.png) 

- Nebo klikněte na **Fakturace** a potom klikněte na ID předplatného. ID nabídky se zobrazí v okně *Fakturace.*

ID nabídky můžete také zobrazit na [kartě Předplatná](https://account.windowsazure.com/Subscriptions) na portálu účtu Azure:

![Podrobnosti o ID nabídky z portálu účtu Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Další kroky
Virtuální počítače teď můžete nasadit pomocí [PowerShellu](quick-create-powershell.md), [šablon Resource Manageru](ps-template.md)nebo [Sady Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

