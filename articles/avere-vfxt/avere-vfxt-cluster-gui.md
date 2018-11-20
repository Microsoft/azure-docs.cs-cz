---
title: Přístup k ovládacím panelům vFXT Avere – Azure
description: Jak se připojit ke clusteru vFXT a založené na prohlížeči Avere ovládacím panelem konfigurace Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 72d1676613de699abda2136a7743a974b2b17c01
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162856"
---
# <a name="access-the-vfxt-cluster"></a>Přístup ke clusteru vFXT

Chcete-li změnit nastavení a monitorování clusteru vFXT Avere, pomocí ovládacího panelu Avere. Avere ovládací panely je založené na prohlížeči grafické rozhraní pro cluster.

Protože vFXT cluster nachází v rámci privátní virtuální síť, musíte vytvořit tunel SSH nebo použít jinou metodou k dosažení IP adresa pro správu clusteru. Existují dva základní kroky: 

1. Vytvořte připojení mezi pracovní stanice a privátní virtuální síť 
1. Zatížení clusteru ovládací panely ve webovém prohlížeči 

> [!NOTE] 
> Tento článek předpokládá, že jste nastavili veřejné IP adresy na adaptéru clusteru nebo na jiný virtuální počítač ve virtuální síti vašeho clusteru. Tento článek popisuje, jak používat tento virtuální počítač jako hostitele pro přístup ke clusteru. Pokud používáte síť VPN nebo ExpressRoute pro přístup k virtuální síti, přejděte k [připojení v Ovládacích panelech Avere](#connect-to-the-avere-control-panel-in-a-browser).

Před připojením, ujistěte se, že veřejného/soukromého páru klíčů SSH, který jste použili při vytváření clusteru kontroleru je nainstalována na místním počítači. Přečtěte si jejich dokumentaci klíče SSH [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) Pokud potřebujete pomoc.  

## <a name="ssh-tunnel-with-a-linux-host"></a>Tunel SSH s hostiteli se systémem Linux

Pokud používáte klienta se systémem Linux, použijte příkaz v tomto formuláři tunelování SSH: 

SSH -L *local_port*:*cluster_mgmt_ip*: 443 *controller_username*@*controller_public_IP*

Tento příkaz se připojí k IP adresa pro správu clusteru prostřednictvím IP adresa adaptéru clusteru.

Příklad:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Ověřování je automatické, pokud jste použili veřejný klíč SSH k vytvoření clusteru a v klientském systému je nainstalován odpovídajícího klíče.

## <a name="ssh-tunnel-with-a-windows-host"></a>Tunel SSH s Windows hostitele

Tento příklad používá běžné založené na Windows terminálu nástroj PuTTY.

Vyplňte PuTTY **hostname** pole uživatelské jméno clusteru kontroleru a jeho IP adresu: *your_username*@*controller_public_IP*.

Příklad: ``azureuser@203.0.113.51``

V **konfigurace** panelu:

1. Rozbalte **připojení** > **SSH** na levé straně. 
1. Klikněte na tlačítko **tunely**. 
1. Zadejte zdrojový port, jako je 8443. 
1. Jako cíl zadejte vFXT cluster IP adresa pro správu a port 443. 
   Příklad: ``203.0.113.51:443``
1. Klikněte na tlačítko **Add** (Přidat).
1. Klikněte na **Otevřít**.

![Aplikace Putty snímek obrazovky znázorňující, kde klikněte na tlačítko Přidat tunel](media/avere-vfxt-ptty-numbered.png)

Ověřování je automatické, pokud jste použili veřejný klíč SSH k vytvoření clusteru a v klientském systému je nainstalován odpovídajícího klíče.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Připojení v Ovládacích panelech Avere v prohlížeči

Tento krok používá pro připojení k nástroji pro konfiguraci spuštěné v clusteru vFXT webový prohlížeč.

* Pro připojení tunelu SSH, otevřete webový prohlížeč a přejděte do https://127.0.0.1:8443. 

  Jste připojeni ke clusteru IP adresu při vytváření tunelového propojení, takže stačí použít IP adresu místního hostitele v prohlížeči. Pokud jste použili jiný místní port než 8443, použijte místo toho vaše číslo portu.

* Pokud používáte síť VPN nebo ExpressRoute k dosažení clusteru, přejděte na IP adresa pro správu clusteru ve vašem prohlížeči. Příklad: ``https://203.0.113.51``

V závislosti na vašem prohlížeči možná budete muset kliknout na **Upřesnit** a ověřte, zda je a pokračujte na stránku.

Zadejte uživatelské jméno `admin` a heslo, které jste zadali při vytváření clusteru.

![Snímek obrazovky Avere přihlašovací stránka vyplní uživatelské jméno admin a heslo](media/avere-vfxt-gui-login.png)

Klikněte na tlačítko **přihlášení** nebo stiskněte klávesu enter na klávesnici.

## <a name="next-steps"></a>Další postup

Teď, když má přístup ke clusteru, povolte [podporují](avere-vfxt-enable-support.md).
