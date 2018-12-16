---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3c6485406c67bf84b9e0fdfb9f4683abe5062d6c
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444046"
---
Po vytvoření certifikátu podepsaného svým držitelem exportujte souboru veřejného klíče .cer kořenového certifikátu (ne privátní klíč). Později tento soubor odešlete do Azure. Následující kroky vám pomůžou exportovat soubor .cer pro váš certifikát podepsaný svým držitelem:

1. Chcete-li získat soubor .cer z certifikátu, otevřete **správu uživatelských certifikátů**. Vyhledejte kořenový certifikát podepsaný svým držitelem, obvykle v Certificates - Current User\Personal\Certificates, a klikněte pravým tlačítkem myši. Klikněte na **Všechny úlohy** a potom klikněte na **Exportovat**. Otevře se **Průvodce exportem certifikátu**. Pokud nemůžete najít certifikát v části Current User\Personal\Certificates je možné otevřít Správce certifikátů certifikátů místního počítače (název bude "Certifikáty – místní počítač" jako rozdíl od "Certifikáty – aktuální uživatel"). Chcete-li otevřít Správce certifikátů v aktuálním oboru uživatele ji spustit z stejné prostředí PowerShell, které certifikáty byly vytvořeny tak, že zadáte ```certmgr```.

   ![Export](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. V průvodci klikněte na **Další**.

   ![Export certifikátu](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Vyberte **Ne, neexportovat privátní klíč** a klikněte na **Další**.

   ![Neexportovat privátní klíč](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na stránce **Formát souboru pro export** vyberte **X.509, kódování Base-64 (CER)** a klikněte na **Další**.

   ![Kódování Base-64](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Pro **soubor pro Export**, **Procházet** do umístění, do které chcete exportovat certifikát. V části **Název souboru** zadejte název souboru. Pak klikněte na **Další**.

   ![Procházet](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Certifikát vyexportujte kliknutím na **Dokončit**.

   ![Dokončit](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Váš certifikát se úspěšně exportoval.

   ![Úspěch](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Exportovaný certifikát vypadá nějak takto:

   ![Exportováno](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Pokud otevřete exportovaný certifikát pomocí poznámkového bloku, vypadá podobně jako tento příklad. Modrá obsahuje informace, které se nahraje do Azure. Pokud otevřete certifikát v aplikaci Poznámkový blok a není vypadat podobně jako tento, obvykle to znamená, že není ho exportovat pomocí Base-64 formát X.509 (. Formátu CER). Pokud chcete použít v jiném textovém editoru, Pochopte, že některé editory může způsobovat nežádoucí formátování na pozadí. To můžete vytvářet problémy při nahrání text z tohoto certifikátu do Azure.

   ![Otevřít v programu Poznámkový blok](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
