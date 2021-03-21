---
title: Vytvoření účtu aktualizace zařízení v aktualizaci zařízení pro Azure IoT Hub | Microsoft Docs
description: Vytvořte účet aktualizace zařízení v aktualizaci zařízení pro Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0201882b74d2acf57c246d2dc63530ca8b1f010
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692608"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Aktualizace zařízení pro správu prostředků IoT Hubu

Pokud chcete začít s aktualizací zařízení, budete muset vytvořit účet aktualizace zařízení, instanci a nastavit role řízení přístupu. 

## <a name="prerequisites"></a>Předpoklady

* Přístup k IoT Hub. Doporučuje se použít úroveň S1 (Standard) nebo vyšší. 
* Podporované prohlížeče:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Vytvoření účtu aktualizace zařízení

1. Přejít na [Azure Portal](https://portal.azure.com)

2. Klikněte na vytvořit prostředek a vyhledejte "aktualizace zařízení pro IoT Hub".

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Snímek obrazovky s aktualizací zařízení pro IoT Hub prostředek" lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Klikněte na možnost vytvořit > aktualizace zařízení pro IoT Hub

4. Zadejte předplatné Azure, které se má přidružit k vašemu účtu aktualizace zařízení a skupině prostředků.

5. Zadejte název a umístění pro účet aktualizace zařízení.

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Snímek obrazovky s podrobnostmi o účtu" lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > Můžete přejít na [stránku produkty Azure – podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) a vyhledat oblasti, ve kterých je dostupná aktualizace zařízení pro IoT Hub. Pokud aktualizace zařízení pro IoT Hub ve vaší oblasti není dostupná, můžete si vytvořit účet v dostupné oblasti, která je pro vás nejblíže. 

6. Klikněte na další: zkontrolovat + vytvořit>.

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Snímek obrazovky s revizí podrobností o účtu" lightbox="media/create-device-update-account/account-review.png":::

7. Zkontrolujte podrobnosti a pak vyberte vytvořit. Uvidíte, že nasazení probíhá. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Snímek obrazovky s probíhajícím nasazením účtu" lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. Během několika minut se zobrazí změna stav nasazení na dokončeno. Klikněte na přejít k prostředku.

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Snímek obrazovky se dokončil nasazení účtu." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Vytvoření instance aktualizace zařízení 

Instance aktualizace zařízení je přidružená k jednomu centru IoT. Vyberte centrum IoT, které se bude používat s aktualizací zařízení. V tomto kroku vytvoříme novou zásadu sdíleného přístupu, která zajistí, že aktualizace zařízení bude používat jenom potřebná oprávnění pro práci s IoT Hub (zápis do registru a připojení služby). Tato zásada zajišťuje, že přístup je omezený jenom na aktualizace zařízení.

Po vytvoření účtu se vytvoří instance aktualizace zařízení.

1. Až budete u svého nově vytvořeného prostředku účtu, přejdete do okna instance správy instancí.

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Snímek obrazovky správy instancí v rámci účtu." lightbox="media/create-device-update-account/instance-blade.png":::

2. Klikněte na vytvořit a zadejte název instance a vyberte IoT Hub

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Snímek obrazovky s podrobnostmi instance" lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > IoT Hub propojíte se svým prostředkem aktualizace zařízení, nemusí být ve stejné oblasti jako účet aktualizace zařízení. Pro lepší výkon ale doporučujeme, aby IoT Hub být v oblasti stejné jako nebo v oblasti svého účtu aktualizace zařízení. 

3. Klikněte na Vytvořit. Instance se zobrazí ve stavu "vytváření". 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Snímek obrazovky instance, která vytváří." lightbox="media/create-device-update-account/instance-creating.png":::

4. Povolte 5-10 minut, než se nasazení instance dokončí. Aktualizujte stav, dokud se zobrazí zpráva "stav zřizování" se změní na úspěšné.

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Snímek obrazovky vytvoření instance se úspěšně vytvořil." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Konfigurace IoT Hub 

Aby aktualizace zařízení přijímala oznámení o změnách z IoT Hub, aktualizace zařízení se integruje do integrovaného centra událostí. Kliknutím na tlačítko Konfigurovat IoT Hub nakonfigurujete požadované zásady směrování a přístupu ke zprávám, které jsou potřeba ke komunikaci se zařízeními IoT. 

Konfigurace IoT Hub

1. Po úspěšném dokončení instance "stav zřizování" Vyberte instanci v okně Správa instancí. Klikněte na konfigurovat IoT Hub.

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Snímek obrazovky s konfigurací IoT Hub pro instanci" lightbox="media/create-device-update-account/instance-configure.png":::

2. Vyberte Souhlasím s provedením těchto změn.

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Snímek obrazovky s souhlasem s konfigurací IoT Hub pro instanci" lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Klikněte na aktualizovat.

[Přečtěte si o nakonfigurovaných trasách zpráv.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Konfigurace rolí řízení přístupu

Aby jiní uživatelé měli přístup k aktualizaci zařízení, musí mít k tomuto prostředku udělený přístup. 

1. Přejít na řízení přístupu (IAM) v rámci účtu aktualizace zařízení

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Snímek obrazovky s řízením přístupu v rámci účtu aktualizace zařízení." lightbox="media/create-device-update-account/account-access-control.png":::

2. Klikněte na přidat přiřazení rolí.

3. V části Vybrat roli vyberte roli aktualizace zařízení z daných možností.
     - Správce aktualizace zařízení
     - Čtenář aktualizace zařízení
     - Správce obsahu aktualizace zařízení
     - Čtenář obsahu aktualizace zařízení
     - Správce nasazení aktualizací zařízení
     - Čtečka nasazení aktualizací zařízení
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Snímek obrazovky s přiřazením rolí řízení přístupu v rámci účtu aktualizace zařízení" lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Přečtěte si o řízení přístupu na základě role v aktualizaci zařízení pro IoT Hub](device-update-control-access.md) 
    
4. Přiřazení přístupu k uživateli nebo skupině Azure AD
5. Kliknutí na Uložit
6. Nyní jste připraveni použít možnosti aktualizace zařízení z IoT Hub

## <a name="next-steps"></a>Další kroky

[Naimportujte aktualizaci pomocí aktualizace zařízení pro IoT Hub.](import-update.md)

[Seznamte se s informacemi o účtu a instanci aktualizace zařízení.](device-update-resources.md) 

[Přečtěte si o rolích řízení přístupu aktualizace zařízení. ](device-update-control-access.md) 

