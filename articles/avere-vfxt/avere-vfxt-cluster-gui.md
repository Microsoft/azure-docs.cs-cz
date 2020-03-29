---
title: Přístup k ovládacímu panelu Avere vFXT – Azure
description: Jak se připojit ke clusteru vFXT a ovládacímu panelu Avere založenému na prohlížeči pro konfiguraci Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: fe2fc062f690498f3d1f588887279aa33d2434b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416148"
---
# <a name="access-the-vfxt-cluster"></a>Přístup ke clusteru vFXT

Chcete-li upravit nastavení clusteru a sledovat cluster, použijte ovládací panel Avere. Ovládací panely Avere jsou grafické rozhraní clusteru založené na prohlížeči.

Vzhledem k tomu, že cluster vFXT je v privátní virtuální síti, musíte vytvořit tunelové propojení SSH nebo použít jinou metodu k dosažení IP adresy správy clusteru.

Existují dva základní kroky:

1. Vytvoření připojení mezi pracovní stanicí a privátní virtuální sítí
1. Načtení ovládacího panelu clusteru ve webovém prohlížeči

> [!NOTE]
> Tento článek předpokládá, že jste nastavili veřejnou IP adresu na řadiči clusteru nebo na jiném virtuálním počítači uvnitř virtuální sítě clusteru. Tento článek popisuje, jak používat tento virtuální virtuální ms jako hostitele pro přístup ke clusteru. Pokud používáte vpn nebo ExpressRoute pro přístup k virtuální síti, přeskočte [na připojit k ovládacímu panelu Avere](#connect-to-the-avere-control-panel-in-a-browser).

Před připojením se ujistěte, že dvojice veřejných a soukromých klíčů SSH, kterou jste použili při vytváření řadiče clusteru, je nainstalována v místním počítači. Přečtěte si dokumentaci ke klíčům SSH pro [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) nebo Pro [Linux,](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) pokud potřebujete pomoc. Pokud jste místo veřejného klíče použili heslo, budete při připojení vyzváni k jeho zadání.

## <a name="create-an-ssh-tunnel"></a>Vytvoření tunelu SSH

TunelOvý propojení SSH můžete vytvořit z příkazového řádku klientského systému založeného na Linuxu nebo Windows 10.

Pomocí příkazu tunelového propojení SSH s tímto formulářem:

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Tento příkaz se připojuje k adrese IP správy clusteru prostřednictvím adresy IP řadiče clusteru.

Příklad:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Ověřování je automatické, pokud jste k vytvoření clusteru použili veřejný klíč SSH a odpovídající klíč je nainstalován v klientském systému. Pokud jste použili heslo, systém vás vyzve k jeho zadání.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Připojení k ovládacímu panelu Avere v prohlížeči

Tento krok používá webový prohlížeč pro připojení k konfiguračnímu nástroji v clusteru vFXT.

* Chcete-li získat připojení tunelového propojení SSH, otevřete webový prohlížeč a přejděte na stránku `https://127.0.0.1:8443`.

  Při vytváření tunelového propojení jste se připojili k IP adrese clusteru, takže stačí použít ip adresu localhost v prohlížeči. Pokud jste použili jiný místní port než 8443, použijte místo toho číslo portu.

* Pokud k dosažení clusteru používáte síť VPN nebo ExpressRoute, přejděte v prohlížeči na adresu IP pro správu clusteru. Příklad: ``https://203.0.113.51``

V závislosti na prohlížeči může být nutné kliknout na **tlačítko Upřesnit** a ověřit, zda je bezpečné přejít na stránku.

Zadejte uživatelské `admin` jméno a heslo pro správu, které jste zadali při vytváření clusteru.

![Snímek obrazovky s přihlašovací stránkou Avere naplněnou uživatelským jménem "admin" a heslem](media/avere-vfxt-gui-login.png)

Klikněte na **Přihlásit se** nebo stiskněte enter na klávesnici.

## <a name="next-steps"></a>Další kroky

Po přihlášení k ovládacímu panelu clusteru povolte [podporu](avere-vfxt-enable-support.md).
