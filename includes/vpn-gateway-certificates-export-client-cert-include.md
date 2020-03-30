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
ms.openlocfilehash: d16412e4e35714c840516670f520f77daed1676d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059960"
---
Při generování klientského certifikátu se automaticky nainstaluje do počítače, který jste použili ke generování. Chcete-li nainstalovat klientský certifikát do jiného klientského počítače, je třeba exportovat klientský certifikát, který jste vygenerovali.

1. Pokud chcete exportovat klientský certifikát, otevřete **správu uživatelských certifikátů**. Klientské certifikáty, které jste vygenerovali, jsou ve výchozím nastavení umístěny v části Certifikáty – aktuální uživatel\Osobní\Certifikáty. Klepněte pravým tlačítkem myši na klientský certifikát, který chcete exportovat, klepněte na **všechny úkoly**a klepnutím na příkaz **Export** otevřete **Průvodce exportem certifikátu**.

   ![Export](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. V Průvodci exportem certifikátu pokračujte klepnutím na tlačítko **Další.**

   ![Další](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Vyberte **Ano, exportujte soukromý klíč**a klepněte na tlačítko **Další**.

   ![export ovat soukromý klíč](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na stránce **Formát souboru pro export** ponechte vybrané výchozí nastavení. Ujistěte se, že je vybrána možnost **Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné**. Toto nastavení navíc exportuje informace o kořenovém certifikátu, které jsou vyžadovány pro úspěšné ověření klienta. Bez něj se ověření klienta nezdaří, protože klient nemá důvěryhodný kořenový certifikát. Potom klepněte na tlačítko **Další**.

   ![exportovat formát souboru](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na stránce **Zabezpečení** je nutné chránit soukromý klíč. Pokud vyberete použití hesla, ujistěte se, že jste si poznamenali nebo si pamatujete heslo, které jste pro tento certifikát nastavili. Potom klepněte na tlačítko **Další**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. V části **Soubor pro export****přejděte** do umístění, do kterého chcete certifikát vyexportovat. V části **Název souboru** zadejte název souboru. Potom klepněte na tlačítko **Další**.

   ![soubor k exportu](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Certifikát vyexportujte kliknutím na **Dokončit**.

   ![Dokončit](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)