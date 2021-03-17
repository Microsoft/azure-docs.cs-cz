---
title: Nastavení partnerského vztahu s Microsoftem
titleSuffix: Azure
description: Přehled partnerského vztahu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 8f960e58d47963cbb239d498af52e7adfc77caa9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586862"
---
# <a name="internet-peering-overview"></a>Přehled internetového partnerského vztahu

Partnerský vztah je propojení mezi globální sítí společnosti Microsoft (AS8075) a vaší sítí pro účely výměny internetového provozu od společnosti Microsoft online služby a Microsoft Azure Services. Nosiče nebo poskytovatelé služeb můžou požádat o spojení se společností Microsoft na kterékoli z našich míst na okrajích. Každý požadavek prověřuje společnost Microsoft, aby se zajistilo, že bude vyhovovat našim zásadám partnerských vztahů. Partnerský vztah se sítí Microsoftu můžete nastavit dvěma způsoby:

* **Přímý partnerský vztah:**

    Partnerské vztahy se navázaly prostřednictvím přímých fyzických připojení mezi sítí Microsoftu na hraničních zařízeních Microsoftu a v síti. Relace protokolu BGP se v těchto připojeních konfigurují podle našich zásad směrování a používají předem vyjednanou smlouvu. To se také označuje jako PNI.

* **Partnerský vztah Exchange:**

    Tato položka odkazuje na standardní připojení veřejných partnerských vztahů při internetových výměnách (IX). Fyzické připojení mezi sítí Microsoftu a vaší sítí jsou prostřednictvím přepínače prostředků infrastruktury provozovaných v rámci IX. Relace protokolu BGP se konfigurují pomocí ADRESního prostoru, který poskytuje služba IX.

## <a name="benefits-of-peering-with-microsoft"></a>Výhody partnerského vztahu s Microsoftem
* Snižte náklady na přenos díky poskytování provozu Microsoftu pomocí partnerského vztahu s Microsoftem.
* Snižte počet směrování a latence sítě v síti Microsoft Edge tím, že Vylepšete výkon pro vaše zákazníky.
* Pomocí partnerského vztahu se společností Microsoft na redundantních místech chránit provoz zákazníků proti chybám ve vaší síti nebo síti poskytovatele přenosu.
* Podívejte se na metriky výkonu o připojeních partnerských vztahů a využijte přehledy k řešení problémů se sítí.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Výhody použití Azure k nastavení partnerského vztahu

Můžete požádat o partnerský vztah s Microsoftem pomocí Azure PowerShell nebo portálu. Nastavení partnerských vztahů tímto způsobem se spravuje jako prostředek Azure a nabízí následující výhody:
* Zjednodušené a automatizovatelné kroky pro nastavení a správu partnerského vztahu s Microsoftem
* Rychlý a snadný způsob, jak zobrazit a spravovat všechny partnerské vztahy na jednom místě.
* Sledujte data o stavu a šířce pásma pro všechna vaše připojení.
* Pro přístup k Cloud Services Azure můžete použít stejné předplatné.

Pokud jste už navázali partnerské vztahy s Microsoftem, označují se jako **starší partnerské vztahy**. Můžete se rozhodnout spravovat takové partnerské vztahy jako prostředek Azure a využít výše uvedené výhody. Pokud chcete odeslat novou žádost o vytvoření partnerského vztahu nebo převést starší partnerský vztah na prostředek Azure, použijte odkazy v části **Další kroky** níže.

## <a name="peering-policy"></a>Zásady peeringu
Microsoft má selektivní, ale obecně otevřené zásady partnerského vztahu. Partnerské vztahy jsou vybrány na základě výkonu, schopnosti a tam, kde se vzájemně využívají výhody, a podléhají určitým technickým, obchodním a zákonným požadavkům. Podrobnosti najdete v tématu [zásady partnerského vztahu](policy.md).

## <a name="faq"></a>Nejčastější dotazy
Nejčastější dotazy týkající se partnerských vztahů najdete v tématu [Internet peering – Nejčastější dotazy](faqs.md).

## <a name="next-steps"></a>Další kroky

* Další informace o krocích pro nastavení přímých partnerských vztahů s Microsoftem najdete v postupu [přímého partnerského vztahu](walkthrough-direct-all.md) .
* Pokud se chcete dozvědět o krocích pro nastavení partnerského vztahu Exchange s Microsoftem, postupujte podle pokynů pro [partnerský vztah Exchange](walkthrough-exchange-all.md) .
* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.