---
title: Azure Load Balancer koncepty
description: Přehled konceptů Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 5c12b7d0bf9bed551e54ccb2e54a4674a8323a9c
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930913"
---
# <a name="azure-load-balancer-algorithm"></a>Azure Load Balancer algoritmus

Nástroj pro vyrovnávání zatížení poskytuje několik možností pro aplikace UDP i TCP.

## <a name="load-balancing-algorithm"></a>Algoritmus vyrovnávání zatížení

Můžete vytvořit pravidlo vyrovnávání zatížení pro distribuci provozu z front-endu do back-endového fondu. Azure Load Balancer používá algoritmus hash pro distribuci příchozích toků (ne bajtů). Nástroj pro vyrovnávání zatížení přepíše hlavičky toků do instancí fondu back-endu. Server je k dispozici pro příjem nových toků, když sonda stavu indikuje back-end koncový bod, který je v pořádku.

Nástroj pro vyrovnávání zatížení ve výchozím nastavení používá hodnotu hash s pěti řazenými kolekcemi členů.

Hodnota hash zahrnuje:

- **Zdrojová IP adresa**
- **Zdrojový port**
- **Cílová IP adresa**
- **Cílový port**
- **Číslo protokolu IP pro mapování toků na dostupné servery**

Spřažení ke zdrojové IP adrese je vytvořeno pomocí dvou nebo tří řazených kolekcí hash. Pakety stejného toku přicházejí na stejnou instanci za front-end s vyrovnáváním zatížení.

Zdrojový port se změní, když klient spustí nový tok ze stejné zdrojové IP adresy. Výsledkem je, že hodnota hash pěti řazených kolekcí by mohla způsobit, že se provoz přejde do jiného koncového bodu back-endu.
Další informace najdete v tématu [Konfigurace distribučního režimu pro Azure Load Balancer](./load-balancer-distribution-mode.md).

Následující obrázek ukazuje distribuci na základě hodnoty hash:

![Distribuce na základě hodnoty hash](./media/load-balancer-overview/load-balancer-distribution.png)

*Obrázek: Distribuce na základě hodnoty hash*

## <a name="application-independence-and-transparency"></a>Nezávislost a průhlednost aplikace

Nástroj pro vyrovnávání zatížení přímo nekomunikuje s protokolem TCP nebo UDP nebo aplikační vrstvou. Může být podporován jakýkoli scénář aplikace TCP nebo UDP. Load Balancer neukončí ani nepochází z toků ani nekomunikuje s datovou částí toku. Nástroj pro vyrovnávání zatížení neposkytuje funkci brány aplikační vrstvy. K protokolu handshake se vždy dochází přímo mezi klientem a instancí back-end fondu. Odpověď na příchozí tok je vždy odpověď z virtuálního počítače. Když virtuální počítač přijme tok, zachová se také původní zdrojová IP adresa.

- Každý koncový bod je zodpovězený virtuálním počítačem. Například dojde k chybě handshake TCP mezi klientem a vybraným back-end virtuálním počítačem. Odpověď na požadavek na front-end je odpověď generovaná back-end virtuálním počítačem. Když úspěšně ověříte připojení k front-endu, ověřujete připojení v rámci alespoň jednoho back-endu virtuálního počítače.
- Datové části aplikace jsou pro nástroj pro vyrovnávání zatížení transparentní. Je možné, že bude podporována jakákoli aplikace UDP nebo TCP.
- Vzhledem k tomu, že nástroj pro vyrovnávání zatížení nekomunikuje s datovou částí TCP a poskytuje přesměrování zpracování TLS, můžete vytvořit komplexní šifrované scénáře. Použití nástroje pro vyrovnávání zatížení získává velký rozsah pro aplikace TLS tím, že ukončuje připojení TLS na samotném virtuálním počítači. Například kapacita klíče relace TLS je omezená jenom typem a počtem virtuálních počítačů, které přidáte do fondu back-end.

## <a name="next-steps"></a>Další kroky

- V tématu [Vytvoření veřejné Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) můžete začít s používáním Load Balancer: Vytvořte si virtuální počítače s nainstalovanou vlastní příponou IIS a vyrovnávat zatížení webové aplikace mezi virtuálními počítači.
- Přečtěte si o [Azure Load Balancer odchozích připojeních](load-balancer-outbound-connections.md).
- Přečtěte si další informace o [Azure Load Balancer](load-balancer-overview.md).
- Seznamte se s [sondami stavu](load-balancer-custom-probe-overview.md).
- Další informace o [diagnostice Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
