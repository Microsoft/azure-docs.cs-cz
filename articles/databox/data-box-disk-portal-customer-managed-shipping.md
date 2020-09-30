---
title: Microsoft Azure Data Box Diski samostatně spravovaného přenosu | Microsoft Docs v datech
description: Popisuje pracovní postup pro expedici spravovaný pro Azure Data Box Disk zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: alkohli
ms.openlocfilehash: c4b3479e5728c32e66bc40f950bc948bf61dce42
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575159"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Použití samoobslužného expedice pro Azure Data Box Disk v Azure Portal

Tento článek popisuje samostatné spravované úkoly, které je možné objednat, vybrat a vyřadit z Azure Data Box Disk. Data Box Disk můžete spravovat pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

Samostatně spravovaná dodávka je k dispozici jako možnost při [objednání Azure Data box disk](data-box-disk-deploy-ordered.md). Samostatně spravovaná dodávka je k dispozici pouze v následujících oblastech:

* Vláda USA
* Západní Evropa
* Japonsko
* Singapur
* Jižní Korea
* Jižní Afrika
* Indie (Preview)

## <a name="use-self-managed-shipping"></a>Použití samoobslužné expedice

Když umístíte Data Box Disk pořadí, můžete vybrat možnost samostatně spravovaná dodávka.

1. V pořadí Azure Data Box Disk v části **kontaktní údaje**vyberte **+ Přidat dodací adresu**.

   ![Snímek obrazovky s průvodcem objednávkou zobrazující krok kontaktní údaje s možností přidat dodací adresu s názvem.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Když zvolíte typ přenosu, vyberte možnost **expedice samostatně spravovaná** . Tato možnost je k dispozici pouze v případě, že jste v podporované oblasti, jak je popsáno v části požadavky.

3. Po zadání dodací adresy ji budete muset ověřit a dokončit objednávku.

   ![Snímek obrazovky dialogového okna Přidat doručovací adresu s použitím možností a možnosti Přidat adresu pro odeslání s názvem.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Po přípravě zařízení a obdržení e-mailového oznámení můžete naplánovat vyzvednutí. V pořadí Azure Data Box Disk klikněte na **Přehled** a pak vyberte **Naplánovat vyzvednutí**.

   ![Objednávání zařízení Data Box pro vyzvednutí](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Postupujte podle pokynů v části **plánování vyzvednutí pro Azure**. Než budete moct získat autorizační kód, musíte poslat e-mail, [adbops@microsoft.com](mailto:adbops@microsoft.com) abyste naplánovali vyzvednutí zařízení z datového centra vaší oblasti.

   ![Naplánovat vyzvednutí](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Po naplánování vyzvednutí zařízení budete moct zobrazit autorizační kód v  **plánu vyzvednutí pro Azure**.

   ![Snímek obrazovky dialogového okna naplánovat výběr pro Azure s autorizačním kódem pro výstupní pole vyvolaným textem](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Poznamenejte si tento **autorizační kód**.

   Podle požadavků na zabezpečení je při plánování naplánování potřeba, abyste si vybrali jméno osoby, která bude přicházet k výběru.

   Je také potřeba zadat podrobnosti o tom, kdo bude mít k dispozici datové centrum pro účely vyzvednutí. Vy nebo kontaktní bod musí mít ID fotografie schválené státní správou, která bude ověřena v datacentru.

   Kromě toho musí mít uživatel, který je vyzvednutím zařízení, také **autorizační kód**. Autorizační kód je jedinečný pro vyzvednutí nebo odložení a je ověřený v datacentru.

7. Jakmile se zařízení z datového centra vybralo, vaše objednávka se automaticky přesune na stav **vyskladněno** .

   ![Vyzvednuto](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Po výběru zařízení můžete zkopírovat data do Data Box Disků v lokalitě. Po dokončení kopírování dat se můžete připravit na dodávání Data Box Disk.

   Jakmile dokončíte kopii dat, budete muset kontaktovat operace, abyste naplánovali schůzku pro odkládací adresář. Aby se disky dostaly, budete muset sdílet podrobnosti o osobě, která přichází do datového centra. Datové centrum bude také potřebovat ověřit autorizační kód v době odložení. Autorizační kód pro vyřazení bude k dispozici v Azure Portal v části **naplánovat vyřazení**.

   > [!NOTE]
   > Nesdílejte autorizační kód přes e-mail. To se dá ověřit jenom v datacentru během odkládacího prostředí.

9. Pokud jste obdrželi schůzku pro odložení od chvíle, kdy by měla být objednávka **připravena k přijetí ve stavu datacentra Azure** v Azure Portal.

   ![Snímek obrazovky dialogového okna Přidat dodací adresu s expedicí s využitím možností a možnosti Přidat adresu pro odeslání s názvem.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Po ověření ID a autorizačního kódu a **obdržení**zařízení v datovém centru by se mělo přijmout stav objednávky.

    ![Přijatý úplný](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Po přijetí zařízení bude kopírování dat pokračovat. Po dokončení kopírování se objednávka dokončí.

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: nasazení Azure Data Box Disk pomocí Azure Portal](data-box-disk-quickstart-portal.md)
