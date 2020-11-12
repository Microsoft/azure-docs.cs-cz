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
ms.openlocfilehash: 12e9bec0c560f1b068b07a1b6afe218a112e439f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553129"
---
Po vytvoření kořenového certifikátu podepsaného svým držitelem exportujte soubor. cer veřejného klíče kořenového certifikátu (ne privátní klíč). Později tento soubor nahrajete do Azure. Následující kroky vám pomůžou exportovat soubor. cer pro kořenový certifikát podepsaný svým držitelem:

1. Chcete-li získat soubor .cer z certifikátu, otevřete **správu uživatelských certifikátů**. Vyhledejte kořenový certifikát podepsaný svým držitelem, obvykle v Certificates - Current User\Personal\Certificates, a klikněte pravým tlačítkem myši. Klikněte na **Všechny úlohy** a potom klikněte na **Exportovat**. Otevře se **Průvodce exportem certifikátu**. Pokud certifikát nemůžete najít v rámci aktuálního User\Personal\Certificates, možná jste omylem otevřeli "certifikáty-místní počítač", nikoli "Certifikáty – aktuální uživatel"). Pokud chcete otevřít Správce certifikátů v oboru aktuální uživatel pomocí PowerShellu, zadáte v okně konzoly *certmgr* .

   ![Snímek obrazovky se zobrazí okno certifikáty pro aktuálního uživatele s vybranými certifikáty a místní nabídku s exportem vybraným ze všech úkolů.](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. V průvodci klikněte na **Další**.

   ![Export certifikátu](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Vyberte **Ne, neexportovat privátní klíč** a klikněte na **Další**.

   ![Neexportovat privátní klíč](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na stránce **Formát souboru pro export** vyberte **X.509, kódování Base-64 (CER)** a klikněte na **Další**.

   ![Kódování Base-64](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Pro **Export souboru** **přejděte** do umístění, do kterého chcete certifikát exportovat. V části **Název souboru** zadejte název souboru. Pak klikněte na tlačítko **Další**.

   ![Snímek obrazovky se zobrazí Průvodce exportem certifikátu s textovým polem s názvem souboru a možností procházení.](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Certifikát vyexportujte kliknutím na **Dokončit**.

   ![Snímek obrazovky s vybraným nastavením zobrazí Průvodce exportem certifikátu.](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Váš certifikát byl úspěšně exportován.

   ![Snímek obrazovky se zobrazí zpráva, že export proběhl úspěšně.](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Exportovaný certifikát vypadá nějak takto:

   ![Snímek obrazovky zobrazuje ikonu certifikátu a název souboru s příponou názvu souboru c e r.](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Pokud otevřete exportovaný certifikát pomocí programu Poznámkový blok, zobrazí se podobné jako v tomto příkladu. Oddíl modře obsahuje informace, které se nahrají do Azure. Pokud certifikát otevřete v programu Poznámkový blok a nevypadá to podobně, znamená to, že jste ho neexportovali pomocí kódování Base-64 kódovaného X. 509 (. CER) formátu. Kromě toho, pokud chcete použít jiný textový editor, je třeba pochopit, že některé editory mohou na pozadí způsobit nezamýšlené formátování. To může vytvořit problémy při nahrání textu z tohoto certifikátu do Azure.

   ![Otevřít v programu Poznámkový blok](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
