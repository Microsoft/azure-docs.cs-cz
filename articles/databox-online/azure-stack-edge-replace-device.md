---
title: Výměna zařízení Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak získat náhradní zařízení Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: 501136c6f5015dc8c68a1aba004432900675b11c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017965"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Výměna zařízení Azure Stack Edge pro

Tento článek popisuje, jak získat náhradní zařízení Azure Stack Edge pro. Náhradní zařízení je potřeba, když dojde k selhání hardwaru v existujícím zařízení nebo pokud potřebuje upgrade. 


V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Otevření lístku podpory pro hardwarový problém
> * Vytvořit nový prostředek pro náhradní zařízení v Azure Portal
> * Instalace, aktivace náhradního zařízení
> * Vrátit původní zařízení

## <a name="open-a-support-ticket"></a>Otevření lístku podpory

Pokud dojde k selhání hardwaru v existujícím zařízení, otevřete lístek podpory. Podpora Microsoftu určí, že pro tuto instanci není k dispozici jednotka nahraditelná v terénu nebo že zařízení vyžaduje upgrade hardwaru. V obou případech bude podpora vyřídit náhradní zařízení.

1. Otevřete lístek podpory s podpora Microsoftu, který indikuje, že chcete zařízení vrátit. Jako **Hardware Azure Stack Edge pro** vyberte typ problému.

    ![Otevření lístku podpory](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Vás bude kontaktovat pracovník podpora Microsoftu. Zadejte podrobnosti expedice.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Vytvoření prostředku pro náhradní zařízení

> [!NOTE]
> Vytvořte nový prostředek pro aktivaci náhradního zařízení. Aktivace náhradního zařízení proti stávajícímu prostředku není podporovaná.

Pomocí těchto kroků vytvořte prostředek.

1. Postupujte podle kroků v části [Vytvoření nového prostředku](azure-stack-edge-deploy-prep.md#create-a-new-resource) a vytvořte prostředek pro náhradní zařízení. 

2. Nezapomeňte zaškrtnout políčko **pro zařízení Azure Stack Edge pro**. 

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

- Přečtěte si, jak [vrátit Azure Stack Edge pro zařízení](azure-stack-edge-return-device.md).
