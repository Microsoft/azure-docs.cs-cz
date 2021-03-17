---
title: Přístup k ovládacímu panelu avere vFXT – Azure
description: Jak se připojit ke clusteru vFXT a ovládacímu panelu avere na bázi prohlížeče pro konfiguraci avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 79e7c5db2a2c445ae740a21744a0bdfe0736c01a
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342429"
---
# <a name="access-the-vfxt-cluster"></a>Přístup ke clusteru vFXT

Chcete-li upravit nastavení clusteru a monitorovat cluster, použijte ovládací panel avere. Ovládací panel avere je grafické rozhraní založené na prohlížeči pro cluster.

Vzhledem k tomu, že cluster vFXT je uvnitř privátní virtuální sítě, je nutné vytvořit tunel SSH nebo použít jinou metodu pro dosažení IP adresy pro správu clusteru.

Existují dva základní kroky:

1. Vytvoření připojení mezi vaší pracovní stanicí a privátní virtuální sítí
1. Načtěte ovládací panel clusteru ve webovém prohlížeči.

> [!NOTE]
> V tomto článku se předpokládá, že jste nastavili veřejnou IP adresu na řadiči clusteru nebo na jiném VIRTUÁLNÍm počítači uvnitř virtuální sítě vašeho clusteru. Tento článek popisuje, jak tento virtuální počítač používat jako hostitele pro přístup ke clusteru. Pokud pro přístup k virtuální síti používáte síť VPN nebo ExpressRoute, přejděte k [části připojení k ovládacímu panelu avere](#connect-to-the-avere-control-panel-in-a-browser).

Před připojením se ujistěte, že je v místním počítači nainstalovaný pár veřejného a privátního klíče SSH, který jste použili při vytváření řadiče clusteru. Pokud potřebujete nápovědu, přečtěte si dokumentaci k klíčům SSH pro [Windows](../virtual-machines/linux/ssh-from-windows.md) nebo pro [Linux](../virtual-machines/linux/mac-create-ssh-keys.md) . Pokud jste místo veřejného klíče použili heslo, budete vyzváni k jeho zadání, až se připojíte.

## <a name="create-an-ssh-tunnel"></a>Vytvoření tunelu SSH

Tunel SSH můžete vytvořit z příkazového řádku klientského systému se systémem Linux nebo Windows 10.

Použijte příkaz pro tunelování SSH s tímto formulářem:

*local_port*SSH-L:*cluster_mgmt_ip*: 443 *controller_username* \@ *controller_public_IP*

Tento příkaz se připojí k IP adrese správy clusteru prostřednictvím IP adresy řadiče clusteru.

Příklad:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Ověřování je automatické, pokud jste k vytvoření clusteru použili veřejný klíč SSH a v klientském systému je nainstalovaný shodný klíč. Pokud jste použili heslo, systém vás vyzve k jeho zadání.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Připojení k ovládacímu panelu avere v prohlížeči

Tento krok používá webový prohlížeč pro připojení ke konfiguračnímu nástroji v clusteru vFXT.

* Pro tunelové připojení SSH otevřete webový prohlížeč a přejděte na `https://127.0.0.1:8443` .

  Při vytváření tunelu jste se připojili k IP adrese clusteru, takže v prohlížeči stačí použít IP adresu místního hostitele. Pokud jste použili místní port jiný než 8443, použijte místo něj číslo portu.

* Pokud ke clusteru máte přístup pomocí sítě VPN nebo ExpressRoute, přejděte v prohlížeči na IP adresu pro správu clusteru. Příklad: ``https://203.0.113.51``

V závislosti na prohlížeči možná budete muset kliknout na **Upřesnit** a ověřit, jestli je bezpečné pokračovat na stránce.

Zadejte uživatelské jméno `admin` a heslo správce, které jste zadali při vytváření clusteru.

![Snímek obrazovky avere přihlašovací stránky naplněný uživatelským jménem admin a heslem](media/avere-vfxt-gui-login.png)

Klikněte na **Přihlásit** nebo na klávesnici stiskněte klávesu ENTER.

## <a name="next-steps"></a>Další kroky

Po přihlášení k ovládacímu panelu clusteru povolte [podporu](avere-vfxt-enable-support.md).