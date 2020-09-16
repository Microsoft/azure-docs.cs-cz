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
ms.openlocfilehash: 3031ec6ba17c61c780351169c4e8c2e5e94ab257
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90606555"
---
Když vygenerujete certifikát klienta, je automaticky nainstalován v počítači, který jste použili k jeho vygenerování. Chcete-li nainstalovat klientský certifikát do jiného klientského počítače, je nutné exportovat klientský certifikát, který jste vygenerovali.

1. Pokud chcete exportovat klientský certifikát, otevřete **správu uživatelských certifikátů**. Certifikáty klienta, které jste vygenerovali, jsou ve výchozím nastavení umístěné v části Certifikáty – aktuální User\Personal\Certificates. Klikněte pravým tlačítkem na klientský certifikát, který chcete exportovat, klikněte na **všechny úlohy**a potom kliknutím na **exportovat** otevřete **Průvodce exportem certifikátu**.

   ![Snímek obrazovky se zobrazí okno certifikáty pro aktuálního uživatele s vybranými certifikáty a místní nabídku se všemi úlohami a vybraným exportem.](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. V Průvodci exportem certifikátu pokračujte kliknutím na tlačítko **Další** .

   ![Snímek obrazovky se zobrazí úvodní zpráva Průvodce exportem certifikátu.](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Vyberte **Ano, exportovat privátní klíč**a potom klikněte na **Další**.

   ![exportovat privátní klíč](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na stránce **Formát souboru pro export** ponechte vybrané výchozí nastavení. Ujistěte se, že je vybrána možnost **Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné**. Toto nastavení dále exportuje informace o kořenovém certifikátu, které jsou požadovány pro úspěšné ověření klienta. Bez této operace se ověřování klienta nezdařilo, protože klient nemá důvěryhodný kořenový certifikát. Pak klikněte na tlačítko **Další**.

   ![Formát souboru pro export](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na stránce **Zabezpečení** je nutné chránit soukromý klíč. Pokud vyberete použití hesla, ujistěte se, že jste si poznamenali nebo si pamatujete heslo, které jste pro tento certifikát nastavili. Pak klikněte na tlačítko **Další**.

   ![Snímek obrazovky se stránkou zabezpečení Průvodce exportem certifikátu se zadaným a potvrzeným a dalším zvýrazněným heslem.](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. V části **Soubor pro export****přejděte** do umístění, do kterého chcete certifikát vyexportovat. V části **Název souboru** zadejte název souboru. Pak klikněte na tlačítko **Další**.

   ![soubor k exportu](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Certifikát vyexportujte kliknutím na **Dokončit**.

   ![Snímek obrazovky s vybraným nastavením zobrazí poslední stránka Průvodce exportem certifikátu.](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)