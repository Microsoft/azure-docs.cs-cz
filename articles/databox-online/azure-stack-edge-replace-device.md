---
title: Výměna zařízení Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak získat náhradní zařízení Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 91cb446da31f353162f90778d855056a9697d455
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726564"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Výměna zařízení Azure Stack Edge pro

Tento článek popisuje, jak nahradit zařízení Azure Stack Edge pro. Náhradní zařízení je potřeba, když dojde k selhání hardwaru v existujícím zařízení nebo pokud potřebuje upgrade. 


V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Otevření lístku podpory pro hardwarový problém
> * Vytvořte novou objednávku pro náhradní zařízení v Azure Portal
> * Instalace, aktivace náhradního zařízení
> * Vrátit původní zařízení

## <a name="open-a-support-ticket"></a>Otevření lístku podpory

Pokud dojde k selhání hardwaru v existujícím zařízení, otevřete lístek podpory pomocí následujících kroků:

1. Otevřete lístek podpory s podpora Microsoftu, který indikuje, že chcete zařízení vrátit. Vyberte typ problému **hardwaru Azure Stack Edge pro** a zvolte podtyp **problémy s hardwarem** .  

    ![Otevření lístku podpory](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Podpora Microsoftu inženýr se spojí s vámi a určí, jestli se může problém opravit (jednotka FRU) a že je k dispozici pro tuto instanci. Pokud jednotka FRU není k dispozici nebo zařízení potřebuje upgradovat hardware, bude se vám podpora pořídit, abyste naumístili nové pořadí a vrátíte původní zařízení.

## <a name="create-a-new-order"></a>Vytvořit novou objednávku

Pomocí postupu v části [Vytvoření nového prostředku](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)vytvořte nový prostředek pro aktivaci náhradního zařízení.

> [!NOTE]
> Aktivace náhradního zařízení proti stávajícímu prostředku není podporovaná. Nový prostředek se považuje za nové pořadí. Začne se účtovat 14 dní po odeslání zařízení.

## <a name="install-and-activate-the-replacement-device"></a>Instalace a aktivace náhradního zařízení

Pomocí těchto kroků nainstalujete a aktivujete náhradní zařízení:

1. [Nainstalujte své zařízení](azure-stack-edge-deploy-install.md).
2. [Aktivujte své zařízení](azure-stack-edge-deploy-connect-setup-activate.md) s novým prostředkem, který jste vytvořili dříve.

## <a name="return-your-existing-device"></a>Vrátit existující zařízení

Pomocí všech kroků vraťte původní zařízení:

1. [Vymaže data na zařízení](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Zahajte návrat zařízení](azure-stack-edge-return-device.md#initiate-device-return) pro původní zařízení.
3. [Naplánování výdeje](azure-stack-edge-return-device.md#schedule-a-pickup).
4. Jakmile se zařízení obdrží v Microsoftu, můžete [odstranit prostředek](azure-stack-edge-return-device.md#delete-the-resource) přidružený k vrácenému zařízení.


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vrátit Azure Stack Edge pro zařízení](azure-stack-edge-return-device.md).
