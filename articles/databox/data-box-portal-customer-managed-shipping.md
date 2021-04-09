---
title: Microsoft Azure Data Boxi samostatně spravovaného přenosu | Microsoft Docs v datech
description: Popisuje pracovní postup pro expedici spravovaný pro Azure Data Box zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 07529b18191c71776a9a36edbfa4cfd8ded5af4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524545"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Použití samoobslužného expedice pro Azure Data Box v Azure Portal

Tento článek popisuje samostatné spravované úkoly, které je možné objednat, vybrat a vyřadit z Azure Data Boxho zařízení. Zařízení Data Box můžete spravovat pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

Samostatně spravovaná dodávka je k dispozici jako možnost při [objednání Azure Data box](data-box-deploy-ordered.md). Samostatně spravovaná dodávka je k dispozici pouze v následujících oblastech:

* Vláda USA
* Spojené království
* Západní Evropa
* Japonsko
* Singapur
* Jižní Korea
* Indie
* Jižní Afrika
* Austrálie

## <a name="use-self-managed-shipping"></a>Použití samoobslužné expedice

Když umístíte Data Box pořadí, můžete zvolit možnost dodání samostatně spravovaná.

1. V pořadí Azure Data Box v části **kontaktní údaje** vyberte **+ Přidat dodací adresu**.
 
   ![Samostatně spravovaná dodávka, přidat dodací adresu](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Když zvolíte typ expedice, vyberte možnost **expedice samostatně spravovaná** . Tato možnost je k dispozici pouze v případě, že jste v podporované oblasti, jak je popsáno v části požadavky.

3. Po zadání dodací adresy ji budete muset ověřit a dokončit objednávku.

   ![Samostatně spravovaná dodávka, ověření a přidání adresy](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Po přípravě zařízení a obdržení e-mailových oznámení můžete naplánovat vyzvednutí.

   V pořadí Azure Data Box klikněte na **Přehled** a pak vyberte **Naplánovat vyzvednutí**.

   ![Pořadí Data Box, možnost plánování vyzvednutí](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Postupujte podle pokynů v části **plánování vyzvednutí pro Azure**.

   Než budete moct získat autorizační kód, musíte poslat e-mail, [adbops@microsoft.com](mailto:adbops@microsoft.com) abyste naplánovali vyzvednutí zařízení z datového centra vaší oblasti.

   ![Pokyny k naplánování vyzvednutí pro Azure](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Po naplánování vyzvednutí zařízení můžete zobrazit autorizační kód zařízení v podokně **vyzvednutí plánu pro Azure** .

   ![Zobrazení autorizačního kódu zařízení](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Poznamenejte si tento **autorizační kód**. V případě požadavků na zabezpečení, v době plánování naplánování, je nutné předložit jméno uživatele, který by se dostal k výběru.

   Je také potřeba zadat podrobnosti o tom, kdo bude mít k dispozici datové centrum pro účely vyzvednutí. Vy nebo kontaktní bod musí mít ID fotografie schválené státní správou, která bude ověřena v datacentru.

   Kromě toho musí mít uživatel, který je vyzvednutím zařízení, také **autorizační kód**. Autorizační kód se ověří v průběhu vyzvednutí datacentra.

7. Jakmile se zařízení z datového centra vybralo, vaše objednávka se automaticky přesune na stav **vyskladněno** .

    ![Objednávka ve stavu vyzvednutí](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Až se zařízení vyzvednutí, zkopírujte data do Data Box ve vaší lokalitě. Po dokončení kopírování dat se můžete připravit na dodávání Data Box. Další informace najdete v tématu [Příprava k odeslání](data-box-deploy-picked-up.md#prepare-to-ship).

   **Příprava k odeslání** krok je nutné provést bez kritických chyb. V opačném případě bude nutné tento krok znovu spustit po provedení nezbytných oprav. Po úspěšném dokončení kroku **Příprava k odeslání** můžete zobrazit autorizační kód pro vypínání v místním uživatelském rozhraní zařízení.

   > [!NOTE]
   > Nesdílejte autorizační kód přes e-mail. To se dá ověřit jenom v datacentru během odkládacího prostředí.

9. Pokud jste obdrželi událost pro odložení, měla by být objednávka **připravena k přijetí ve stavu datacentra Azure** v Azure Portal. Postupujte podle pokynů v části **plán vypnutí** a vraťte zařízení.

   ![Pokyny pro vypnutí zařízení](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Po ověření ID a autorizačního kódu a vypnutí zařízení v datovém centru by se mělo **přijmout** stav objednávky.

    ![Objednávka s přijatým stavem](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Po přijetí zařízení bude kopírování dat pokračovat. Po dokončení kopírování se objednávka dokončí.

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Data Boxem](data-box-quickstart-portal.md)
