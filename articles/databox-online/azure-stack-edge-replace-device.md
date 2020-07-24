---
title: Výměna zařízení Azure Stack Edge | Microsoft Docs
description: Popisuje, jak získat náhradní Azure Stack hraniční zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 5d5e8cc3a959c7800019107d2c682e144449b46c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097173"
---
# <a name="replace-your-azure-stack-edge-device"></a>Výměna zařízení Azure Stack Edge

Tento článek popisuje, jak získat náhradní Azure Stack hraniční zařízení. Náhradní zařízení je potřeba, když dojde k selhání hardwaru v existujícím zařízení nebo pokud potřebuje upgrade. 


V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Otevření lístku podpory pro hardwarový problém
> * Vytvořit nový prostředek pro náhradní zařízení v Azure Portal
> * Instalace, aktivace náhradního zařízení
> * Vrátit původní zařízení

## <a name="open-a-support-ticket"></a>Otevření lístku podpory

Pokud dojde k selhání hardwaru v existujícím zařízení, otevřete lístek podpory. Podpora Microsoftu určí, že jednotka pro nahrazení pole (FRU) není pro tuto instanci k dispozici, nebo zařízení potřebuje upgradovat hardware. V obou případech bude podpora vyřídit náhradní zařízení.

1. Otevřete lístek podpory s podpora Microsoftu, který indikuje, že chcete zařízení vrátit. Vyberte typ problému jako **Hardware Azure Stack Edge**.

    ![Otevření lístku podpory](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Vás bude kontaktovat pracovník podpora Microsoftu. Zadejte podrobnosti expedice.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Vytvoření prostředku pro náhradní zařízení

Pomocí těchto kroků vytvořte prostředek.

1. Postupujte podle kroků v části [Vytvoření nového prostředku](azure-stack-edge-deploy-prep.md#create-a-new-resource) a vytvořte prostředek pro náhradní zařízení. 

2. Nezapomeňte zaškrtnout políčko pro **zařízení Azure Stack Edge**. 

    ![Prostředek pro náhradní zařízení](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>Instalace a aktivace náhradního zařízení

Pomocí těchto kroků nainstalujete a aktivujete náhradní zařízení:

1. [Nainstalujte své zařízení](azure-stack-edge-deploy-install.md).

2. [Aktivujte své zařízení](azure-stack-edge-deploy-connect-setup-activate.md) s novým prostředkem, který jste vytvořili dříve.

## <a name="return-your-existing-device"></a>Vrátit existující zařízení

Pomocí všech kroků vraťte původní zařízení:

1. [Vymaže data na zařízení](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Zahajte návrat zařízení](azure-stack-edge-return-device.md#initiate-device-return) pro původní zařízení.
3. [Naplánování výdeje](azure-stack-edge-return-device.md#schedule-a-pickup).
4. [Odstraní prostředek](azure-stack-edge-return-device.md#delete-the-resource) přidružený ke vrácenému zařízení.


## <a name="next-steps"></a>Další kroky

- Naučte se [vracet Azure Stack hraniční zařízení](azure-stack-edge-return-device.md).
