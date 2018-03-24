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
ms.openlocfilehash: 517ebbf739c64c0364dc21386fee86ebc740e997
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
Při generování klientský certifikát se automaticky nainstaluje na počítači, který jste použili k jeho vygenerování. Pokud chcete nainstalovat certifikát klienta v jiném počítači klienta, musíte exportovat certifikát klienta, který jste vygenerovali.

1. Pokud chcete exportovat klientský certifikát, otevřete **správu uživatelských certifikátů**. Klientské certifikáty, které jste vygenerovali nacházejí ve výchozím nastavení, v 'Certifikáty – aktuální User\Personal\Certificates'. Klikněte pravým tlačítkem na certifikát klienta, který chcete exportovat, klikněte na **všechny úlohy**a potom klikněte na **exportovat** otevřete **Průvodce exportem certifikátu**.

  ![Export](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. V Průvodci exportem certifikátu klikněte na **Další** pokračujte.

  ![Další](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Vyberte **Ano, exportovat soukromý klíč**a potom klikněte na **Další**.

  ![exportovat privátní klíč](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na stránce **Formát souboru pro export** ponechte vybrané výchozí nastavení. Ujistěte se, že je vybrána možnost **Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné**. Toto nastavení kromě exportuje informace kořenového certifikátu, který je vyžadován pro ověření úspěšné klienta. Bez, ověření klienta se nezdaří, protože klient nemá důvěryhodný kořenový certifikát. Pak klikněte na **Další**.

  ![Formát souboru pro export](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na stránce **Zabezpečení** je nutné chránit soukromý klíč. Pokud vyberete použití hesla, ujistěte se, že jste si poznamenali nebo si pamatujete heslo, které jste pro tento certifikát nastavili. Pak klikněte na **Další**.

  ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. V části **Soubor pro export** **přejděte** do umístění, do kterého chcete certifikát vyexportovat. V části **Název souboru** zadejte název souboru. Pak klikněte na **Další**.

  ![soubor k exportu](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Certifikát vyexportujte kliknutím na **Dokončit**.

  ![dokončit](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)