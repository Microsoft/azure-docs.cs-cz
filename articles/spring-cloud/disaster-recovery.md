---
title: Azure jaře Cloud – geografická zotavení po havárii | Microsoft Docs
description: Informace o tom, jak chránit svoji jarní cloudovou aplikaci před místními výpadky
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: e18193b40bb7c59a21a279f451673dc7d11140e4
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092898"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Zotavení po havárii v cloudu Azure jaře

**Tento článek se týká:** ✔️ Java ✔️ C #

Tento článek vysvětluje některé strategie, které můžete použít k ochraně vašich aplikací v cloudovém prostředí Azure, aby nedocházelo k výpadkům.  Jakékoli oblasti nebo datové centrum může nastat výpadky způsobené místními katastrofami, ale pečlivé plánování může zmírnit dopad na vaše zákazníky.

## <a name="plan-your-application-deployment"></a>Plánování nasazení aplikace

Aplikace pro jarní Cloud v Azure běží v konkrétní oblasti.  Azure funguje v několika zeměpisných oblastech po celém světě. Zeměpisná oblast Azure je definovaná oblast světa, která obsahuje alespoň jednu oblast Azure. Oblast Azure je oblast v rámci geografické oblasti, která obsahuje jedno nebo více datových center.  Každá oblast Azure je spárovaná s jinou oblastí v rámci stejné geografické oblasti a tvoří tak místní dvojici. Azure serializovat aktualizace platforem (plánovaná údržba) napříč místními páry, aby bylo zajištěno, že se v každém páru aktualizuje jenom jedna oblast. V případě výpadku, který má vliv na více oblastí, bude mít nejméně jedna oblast v každém páru určenou prioritu pro obnovení.

Zajištění vysoké dostupnosti a ochrany před haváriemi vyžaduje, abyste nasadili své jarní cloudové aplikace do více oblastí.  Azure poskytuje seznam [spárovaných oblastí](../best-practices-availability-paired-regions.md) , abyste mohli plánovat nasazování vašich jarních cloudů do regionálních párů.  Při navrhování architektury mikroslužeb doporučujeme vzít v úvahu tři klíčové faktory: dostupnost oblastí, spárované oblasti Azure a dostupnost služeb.

*  Dostupnost oblastí: vyberte geografickou oblast blízko vašim uživatelům, abyste minimalizovali prodlevu sítě a dobu přenosu.
*  Spárované oblasti Azure: Vyberte spárované oblasti v rámci zvolené geografické oblasti, abyste zajistili koordinované aktualizace platforem a v případě potřeby nastavili úsilí v oblasti obnovení s prioritou.
*  Dostupnost služby: Rozhodněte, jestli mají spárované oblasti běžet za horkou, horkou, horkou nebo studenou.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Použití Azure Traffic Manager ke směrování provozu

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) poskytuje vyrovnávání zatížení provozu založené na službě DNS a může distribuovat síťový provoz napříč několika oblastmi.  Využijte Azure Traffic Manager k přímému nasměrování zákazníků do nejbližší instance služby Azure jaře Cloud Service.  Abyste dosáhli nejlepšího výkonu a redundance, přesměrujte veškerý provoz aplikací prostřednictvím Azure Traffic Manager před odesláním do vaší jarní cloudové služby Azure.

Pokud máte ve více oblastech aplikace pro jarní cloudy Azure, použijte Azure Traffic Manager k řízení toku provozu do aplikací v jednotlivých oblastech.  Pro každou službu pomocí IP adresy služby Definujte koncový bod Azure Traffic Manager. Zákazníci by se měli připojit k názvu DNS Azure Traffic Manager, který odkazuje na jarní cloudovou službu Azure.  Azure Traffic Manager vyrovnává zatížení napříč definovanými koncovými body.  Pokud dojde k výpadku datového centra, Azure Traffic Manager přesměruje provoz z této oblasti na svůj pár a zajišťuje kontinuitu služeb.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Vytvoření Azure Traffic Manager pro jarní cloud Azure

1. Vytvořte si jarní cloud Azure ve dvou různých oblastech.
Budete potřebovat dvě instance služby jarního cloudu Azure nasazené ve dvou různých oblastech (Východní USA a Západní Evropa). Pokud chcete vytvořit dvě instance služby, spusťte stávající cloudovou aplikaci Azure na základě Azure Portal. Každá z nich bude sloužit jako primární a koncový bod převzetí služeb při selhání pro provoz. 

**Dvě informace o instancích služby:**

| Název služby | Umístění | Aplikace |
|--|--|--|
| Služba – ukázka-a | East US | Brána/ověřování-služba/účet-služba |
| Service-Sample-b | West Europe | Brána/ověřování-služba/účet-služba |

2. Pokud chcete nastavit vlastní doménu pro tyto dvě existující instance služby, nastavte vlastní doménu pro službu dodržovat [vlastní doménový dokument](spring-cloud-tutorial-custom-domain.md) . Po úspěšném vytvoření se obě instance služby sváže s vlastní doménou: bcdr-test.contoso.com

3. Vytvoření Traffic Manageru a dvou koncových bodů: [vytvořte profil Traffic Manager pomocí Azure Portal](../traffic-manager/quickstart-create-traffic-manager-profile.md).

Tady je profil Traffic Manageru:
* Traffic Manager název DNS: `http://asc-bcdr.trafficmanager.net`
* Profily koncového bodu: 

| Profil | Typ | Cíl | Priorita | Vlastní nastavení hlaviček |
|--|--|--|--|--|
| Koncový bod A profil | Externí koncový bod | service-sample-a.asc-test.net | 1 | Hostitel: bcdr-test.contoso.com |
| Profil koncového bodu B | Externí koncový bod | service-sample-b.asc-test.net | 2 | Hostitel: bcdr-test.contoso.com |

4. Vytvořte záznam CNAME v zóně DNS: bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net. 

5. Nyní je prostředí zcela nastaveno. Zákazníci by měli být schopni získat přístup k aplikaci prostřednictvím: bcdr-test.contoso.com

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: nasazení první aplikace pro cloudovou službu Azure jaře](spring-cloud-quickstart.md)
