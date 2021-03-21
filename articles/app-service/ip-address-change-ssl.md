---
title: Příprava na změnu IP adresy SSL
description: Pokud se vaše IP adresa SSL bude měnit, zjistěte, co dělat, aby vaše aplikace po změně nadále fungovala.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: dcfe11bcab25f6267a557de5faf7befab467bc29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020955"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Příprava na změnu IP adresy SSL

Pokud jste obdrželi oznámení o změně IP adresy SSL aplikace Azure App Service, postupujte podle pokynů v tomto článku, abyste uvolnili existující IP adresu SSL a přiřadili novou.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Vydávat IP adresy SSL a přiřazovat nové

1.  Otevřete [Azure Portal](https://portal.azure.com).

2.  V navigační nabídce na levé straně vyberte **App Services**.

3.  Ze seznamu vyberte svou aplikaci App Service.

4.  V záhlaví **Nastavení** klikněte na **Nastavení SSL** v levém navigačním panelu.

1. V části vazby TLS/SSL vyberte záznam názvu hostitele. V editoru, který se otevře, vyberte v rozevírací nabídce **typ SSL** možnost **sni SSL** a klikněte na **Přidat vazbu**. Po zobrazení zprávy o úspěchu operace byla uvolněna existující IP adresa.

6.  V části **vazby SSL** znovu vyberte stejný záznam názvu hostitele s certifikátem. V editoru, který se otevře, vyberte v rozevírací nabídce **typ SSL** možnost **SSL na základě IP adresy** a klikněte na **Přidat vazbu**. Po zobrazení zprávy o úspěchu operace jste získali novou IP adresu.

7.  Pokud je záznam A (záznam DNS ukazující přímo na vaši IP adresu) nakonfigurovaný na vašem portálu pro registraci domény (poskytovatel DNS nebo Azure DNS), nahraďte stávající IP adresu nově vytvořeným. Novou IP adresu najdete podle pokynů v následující části.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Najít novou IP adresu SSL na webu Azure Portal

1.  Počkejte pár minut a pak otevřete [Azure Portal](https://portal.azure.com).

2.  V navigační nabídce na levé straně vyberte **App Services**.

3.  Ze seznamu vyberte svou aplikaci App Service.

4.  V záhlaví **Nastavení** klikněte na tlačítko **vlastnosti** v levém navigačním panelu a vyhledejte oddíl označený **virtuální IP adresou**.

5. Zkopírujte IP adresu a překonfigurujte záznam domény nebo mechanismus IP adres.

## <a name="next-steps"></a>Další kroky

Tento článek vysvětluje, jak připravit na změnu IP adresy, kterou iniciovala Azure. Další informace o IP adresách v Azure App Service najdete [v tématu příchozí a odchozí IP adresy v Azure App Service](overview-inbound-outbound-ips.md).
