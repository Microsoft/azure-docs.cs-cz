---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: abef93d30ff9cdf1ecdbbfd450d9f96e6352a513
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165815"
---
<!--author=alkohli last changed: 06/22/17-->

#### <a name="to-create-a-volume-container"></a>Vytvoření kontejneru svazků
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte a klikněte na zařízení. 

    ![Okno Kontejner svazků](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. Na řídicím panelu zařízení klikněte na **+ Přidat kontejner svazků**.

    ![Okno Kontejner svazků](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. V okně **Přidat kontejner svazků**:
   
   1. Zařízení se vybere automaticky.
   2. Zadejte **Název** kontejneru svazků. Název musí mít délku 3 až 32 znaků. Kontejner svazků se po vytvoření už nedá přejmenovat.
   3. Výběrem možnosti **Enable Cloud Storage Encryption** (Povolit šifrování cloudového úložiště) povolte šifrování dat odesílaných ze zařízení do cloudu.
   4. Zadejte a potvrďte **Cloud Storage Encryption Key** (Šifrovací klíč cloudového úložiště), který má délku 8 až 32 znaků. Tento klíč zařízení používá k přístupu k zašifrovaným datům.
   5. Vyberte **Účet úložiště**, který chcete k tomuto kontejneru svazků přidružit. Můžete vybrat existující účet úložiště nebo výchozí účet, který se vygeneruje při vytváření služby. Můžete také pomocí možnosti **Přidat nový** zadat účet úložiště, který není spojený s předplatným této služby.
   6. V rozevíracím seznamu **Specify bandwidth** (Zadejte šířku pásma) vyberte možnost **Unlimited** (Neomezená), pokud chcete spotřebovávat veškerou dostupnou šířku pásma. U této možnosti také můžete vybrat **Vlastní**, pokud chcete použít ovládací prvky šířky pásma a zadat hodnotu mezi 1 a 1 000 Mb/s.
      Pokud máte informace o využití šířky pásma, můžete vybrat možnost **Select a bandwidth template** (Vybrat šablonu šířky pásma), abyste mohli přidělovat šířku pásma na základě plánu. Podrobný postup najdete v článku [Přidání šablony šířky pásma](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template).

      ![Okno Kontejner svazků](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. Klikněte na možnost **Vytvořit**.

        ![Okno Kontejner svazků](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       Po úspěšném vytvoření kontejneru svazků se zobrazí oznámení.

       ![Oznámení o vytvoření kontejneru svazků](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   Nově vytvořený kontejner svazků je uveden v seznamu kontejnerů svazků pro vaše zařízení.

   ![Okno Přidat kontejner svazků](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


