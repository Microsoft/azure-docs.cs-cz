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
ms.openlocfilehash: b657d54c3ebbe5afc20fc98c1348bb783410df60
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444059"
---
Pokud generujete klientský certifikát, je automaticky nainstalován v počítači, který jste použili k jeho vygenerování. Pokud chcete nainstalovat klientský certifikát na jiný klientský počítač, musíte exportovat klientský certifikát, který jste vygenerovali.

1. Pokud chcete exportovat klientský certifikát, otevřete **správu uživatelských certifikátů**. Klientské certifikáty, které jste vygenerovali nacházejí ve výchozím nastavení, v "Certificates - Current User\Personal\Certificates". Klikněte pravým tlačítkem na klientský certifikát, který chcete exportovat, klikněte na **všechny úkoly**a potom klikněte na tlačítko **exportovat** otevřít **Průvodce exportem certifikátu**.

   ![Export](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. V Průvodci exportem certifikátu klikněte na tlačítko **Další** pokračujte.

   ![Další](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Vyberte **Ano, exportovat soukromý klíč**a potom klikněte na tlačítko **Další**.

   ![exportovat privátní klíč](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na stránce **Formát souboru pro export** ponechte vybrané výchozí nastavení. Ujistěte se, že je vybrána možnost **Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné**. Toto nastavení kromě exportuje informace o kořenovém certifikátu potřebné pro ověřování klientů úspěšné. Bez ověřování klientů nezdaří, protože klient nemá důvěryhodný kořenový certifikát. Pak klikněte na **Další**.

   ![Formát souboru pro export](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na stránce **Zabezpečení** je nutné chránit soukromý klíč. Pokud vyberete použití hesla, ujistěte se, že jste si poznamenali nebo si pamatujete heslo, které jste pro tento certifikát nastavili. Pak klikněte na **Další**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. V části **Soubor pro export** **přejděte** do umístění, do kterého chcete certifikát vyexportovat. V části **Název souboru** zadejte název souboru. Pak klikněte na **Další**.

   ![soubor pro export](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Certifikát vyexportujte kliknutím na **Dokončit**.

   ![dokončit](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)