---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2dba0f657b418267db90c07014dc8996ed12a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059949"
---
Po vytvoření kořenového certifikátu podepsaného svým držitelem exportujte soubor CER s kořenovým certifikátem (nikoli soukromý klíč). Tento soubor později nahrajete do Azure. Následující kroky vám pomohou exportovat soubor CER pro kořenový certifikát podepsaný svým držitelem:

1. Chcete-li získat soubor .cer z certifikátu, otevřete **správu uživatelských certifikátů**. Vyhledejte kořenový certifikát podepsaný svým držitelem, obvykle v Certificates - Current User\Personal\Certificates, a klikněte pravým tlačítkem myši. Klikněte na **Všechny úlohy** a potom klikněte na **Exportovat**. Otevře se **Průvodce exportem certifikátu**. Pokud nemůžete najít certifikát v části Aktuální uživatel\Osobní\Certifikáty, je možné, že jste omylem otevřeli "Certifikáty – místní počítač" místo "Certifikáty – aktuální uživatel"). Pokud chcete otevřít Správce certifikátů v aktuálním uživatelském oboru pomocí prostředí PowerShell, zadejte *certmgr* do okna konzoly.

   ![Export](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. V průvodci klikněte na **Další**.

   ![Export certifikátu](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Vyberte **Ne, neexportovat privátní klíč** a klikněte na **Další**.

   ![Neexportovat soukromý klíč](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na stránce **Formát souboru pro export** vyberte **X.509, kódování Base-64 (CER)** a klikněte na **Další**.

   ![Základní-64 kódované](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. V **případě exportu souboru** **vyhledejte** umístění, do kterého chcete certifikát exportovat. V části **Název souboru** zadejte název souboru. Potom klepněte na tlačítko **Další**.

   ![Procházet](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Certifikát vyexportujte kliknutím na **Dokončit**.

   ![Dokončit](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Certifikát je úspěšně exportován.

   ![Úspěch](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Exportovaný certifikát vypadá podobně jako tento:

   ![Exportovat](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Pokud exportovaný certifikát otevřete pomocí poznámkového bloku, uvidíte něco podobného tomuto příkladu. Modrá část obsahuje informace, které se nahrají do Azure. Pokud otevřete certifikát s poznámkovým blokem a nevypadá podobně jako tento, obvykle to znamená, že jste jej neexportovali pomocí kódu Base-64 X.509(. CER). Navíc pokud chcete použít jiný textový editor, pochopit, že některé editory mohou zavést nechtěné formátování na pozadí. To může způsobit problémy při nahrávání textu z tohoto certifikátu do Azure.

   ![Otevřít pomocí poznámkového bloku](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
