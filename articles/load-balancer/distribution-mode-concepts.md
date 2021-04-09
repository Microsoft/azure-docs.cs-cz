---
title: Azure Load Balancer distribuční režimy
description: Začněte učit se o různých režimech distribuce Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99551252"
---
# <a name="azure-load-balancer-distribution-modes"></a>Azure Load Balancer distribuční režimy

Azure Load Balancer podporuje pro směrování připojení do vaší aplikace s vyrovnáváním zatížení dva režimy distribuce:

* Založené na hodnotě hash
* Spřažení se zdrojovou IP adresou

## <a name="hash-based"></a>Založené na hodnotě hash

Výchozí režim distribuce pro Azure Load Balancer využívá hodnotu hash, kterou tvoří řazená kolekce pěti členů. 

Řazená kolekce členů se skládá z těchto:
* **Zdrojová IP adresa**
* **Zdrojový port**
* **Cílová IP adresa**
* **Cílový port**
* **Typ protokolu**

Hodnota hash se používá k mapování provozu na dostupné servery. Algoritmus poskytuje vytrvalost jenom v rámci relace přenosu. Pakety, které jsou ve stejné relaci, se přesměrují na stejnou IP adresu datového centra za koncovým bodem s vyrovnáváním zatížení. Když klient spustí novou relaci ze stejné zdrojové IP adresy, změní se zdrojový port a způsobí, že provoz přejde na jiný koncový bod datového centra.

![Distribuční režim s pěti řazenými kolekcemi členů založený na hodnotě hash](./media/distribution-mode-concepts/load-balancer-distribution.png)

Režim založený na hodnotě hash má jeden typ konfigurace:

* **Žádný (založený na hodnotě hash)** – určuje, že úspěšné požadavky ze stejného klienta můžou být zpracovávány jakýmkoli virtuálním počítačem.

## <a name="source-ip-affinity"></a>Spřažení se zdrojovou IP adresou

Tento režim distribuce se také označuje jako spřažení relací nebo spřažení klientských IP adres. Režim používá k mapování provozu na dostupné servery dvě řazené kolekce členů (zdrojová IP adresa a cílová IP adresa) nebo tři řazené kolekce členů (zdrojová adresa IP, cílová IP adresa a typ protokolu). 

Při použití spřažení zdrojového protokolu IP se připojení spouštěná ze stejného klientského počítače přejdou na stejný koncový bod datového centra.

Následující obrázek znázorňuje konfiguraci se dvěma řazenými kolekcemi členů. Všimněte si, jak se dvě řazené kolekce členů spustí prostřednictvím nástroje pro vyrovnávání zatížení, do virtuálního počítače 1 (VM1). VM1 je pak zálohovaný pomocí VM2 a VM3.

![Distribuční režim spřažení relace se dvěma řazenými kolekcemi členů](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Režim spřažení zdrojové IP adresy má dva typy konfigurace:

* **IP adresa klienta (spřažení zdrojové IP adresy 2 – řazená kolekce členů)** – určuje, že úspěšné požadavky ze stejné IP adresy klienta budou zpracovávány stejným virtuálním počítačem.
* **IP adresa klienta a protokol (přidružení zdrojové IP adresy 3 – řazená kolekce členů)** – určuje, že po jednom virtuálním počítači bude zpracována úspěšná žádost ze stejné kombinace IP adresy klienta a protokolu.

## <a name="use-cases"></a>Případy použití

Spřažení zdrojové IP adresy s IP adresou klienta a protokolem (spřažení zdrojové IP adresy 3-Tuple) řeší nekompatibilitu mezi Azure Load Balancer a Brána vzdálené plochy (Brána VP). 

Dalším scénářem použití je nahrávání médií. Nahrávání dat probíhá prostřednictvím protokolu UDP, ale Řídicí rovina se dosahuje prostřednictvím protokolu TCP:

* Klient spustí relaci TCP na veřejné adrese s vyrovnáváním zatížení a přesměruje na konkrétní DIP. Kanál zůstane aktivní a monitoruje stav připojení.
* Ve stejném klientském koncovém bodu s vyrovnáváním zatížení je spuštěná nová relace UDP ze stejného klientského počítače. Připojení je směrováno na stejný koncový bod DIP jako předchozí připojení TCP. Nahrávání médií je možné spustit při vysoké propustnosti a přitom zachovat řídicí kanál prostřednictvím protokolu TCP.

> [!NOTE]
> Dojde-li ke změně sady s vyrovnáváním zatížení odebráním nebo přidáním virtuálního počítače, bude přepočítána distribuce požadavků klientů. Nemůžete záviset na nových připojeních od stávajících klientů po ukončení na stejném serveru. Kromě toho může použití distribučního režimu spřažení zdrojového protokolu IP způsobit nerovnost distribuce provozu. Klienti, kteří se spouštějí za proxy servery, se mohou zobrazit jako jedna jedinečná klientská aplikace.


## <a name="next-steps"></a>Další kroky

Další informace o tom, jak nakonfigurovat distribuční režim Azure Load Balancer, najdete v tématu [Konfigurace distribučního režimu pro Azure Load Balancer](load-balancer-distribution-mode.md).

