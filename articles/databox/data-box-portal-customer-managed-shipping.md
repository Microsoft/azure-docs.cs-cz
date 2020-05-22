---
title: Microsoft Azure Data Boxi samostatně spravovaného přenosu | Microsoft Docs v datech
description: Popisuje pracovní postup pro expedici spravovaný pro Azure Data Box zařízení.
services: databox
author: priestlg
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-grpr
ms.openlocfilehash: 2933eb216b7faed7b28fb53d79fec2f50bc4458d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749414"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Použití samoobslužného expedice pro Azure Data Box v Azure Portal

Tento článek popisuje samostatné spravované úkoly, které je možné objednat, vybrat a vyřadit z Azure Data Boxho zařízení. Zařízení Data Box můžete spravovat pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

Samostatně spravovaná dodávka je k dispozici jako možnost při [objednání Azure Data box](data-box-deploy-ordered.md). Samostatně spravovaná dodávka je k dispozici pouze v následujících oblastech:

* Vláda USA
* Západní Evropa
* Japonsko
* Singapur
* Jižní Korea

## <a name="use-self-managed-shipping"></a>Použití samoobslužného expedice

Když umístíte Data Box pořadí, můžete vybrat možnost samostatně spravovaná dodávka.

1. V pořadí Azure Data Box v části **kontaktní údaje**vyberte **+ Přidat dodací adresu**.
   ![Samostatně spravovaná dodávka](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Když zvolíte typ přenosu, vyberte možnost **expedice samostatně spravovaná** . Tato možnost je k dispozici pouze v případě, že jste v podporované oblasti, jak je popsáno v části požadavky.

3. Po zadání dodací adresy ji budete muset ověřit a dokončit objednávku.
   ![Samostatně spravovaná dodávka](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Po přípravě zařízení můžete naplánovat vyzvednutí.

   V pořadí Azure Data Box klikněte na **Přehled** a pak vyberte **Naplánovat vyzvednutí**.

   ![Objednávání zařízení Data Box pro vyzvednutí](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Postupujte podle pokynů v části **plánování vyzvednutí pro Azure**.

   Než budete moct získat autorizační kód, musíte poslat e-mail, [adbops@microsoft.com](mailto:adbops@microsoft.com) abyste naplánovali vyzvednutí zařízení z datového centra vaší oblasti.

   ![Naplánovat vyzvednutí](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Po naplánování vyzvednutí zařízení budete moct zobrazit autorizační kód zařízení v podokně **vyzvednutí plánu pro Azure** .

   ![Zobrazení autorizačního kódu](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Poznamenejte si tento **autorizační kód**. Podle požadavků na zabezpečení, v době plánování a vyřazení, je nutné, abyste si poznamenali jméno osoby, která se dostala k výběru a vyřazení.

   Je také potřeba zadat podrobnosti o tom, kdo bude mít k dispozici datové centrum pro účely vyzvednutí. Vy nebo kontaktní bod musí mít ID fotografie schválené státní správou, která bude ověřena v datacentru.

   Kromě toho musí mít uživatel, který je vyzvednutím zařízení, také **autorizační kód**. Autorizační kód se ověří v průběhu vyzvednutí datacentra.

7. Jakmile se zařízení z datového centra vybralo, vaše objednávka se automaticky přesune na stav **vyskladněno** .

    ![Vyzvednuto](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Až se zařízení vyzvednutí, zkopírujte data do Data Box ve vaší lokalitě. Po dokončení kopírování dat se můžete připravit na dodávání Data Box. Další informace najdete v tématu [Příprava k odeslání](data-box-deploy-picked-up.md#prepare-to-ship).

   **Příprava k odeslání** krok se musí dokončit bez kritických chyb, jinak budete muset tento krok znovu spustit po provedení nezbytných oprav. Po úspěšném dokončení přípravy na odeslání můžete zobrazit autorizační kód pro odložení v místním uživatelském rozhraní zařízení.

   > [!NOTE]
   > Nesdílejte autorizační kód přes e-mail. To se dá ověřit jenom v datacentru během odkládacího prostředí.

9. Pokud jste obdrželi událost pro odložení, měla by být objednávka **připravena k přijetí ve stavu datacentra Azure** v Azure Portal. Postupujte podle pokynů v části **plán vypnutí** a vraťte zařízení.

   ![Zobrazení autorizačního kódu](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Po ověření ID a autorizačního kódu a vypnutí zařízení v datovém centru by se mělo **přijmout**stav objednávky.

    ![Přijatý úplný](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Po přijetí zařízení bude kopírování dat pokračovat. Po dokončení kopírování se objednávka dokončí.

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Data Boxem](data-box-quickstart-portal.md)
