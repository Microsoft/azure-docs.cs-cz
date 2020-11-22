---
title: Přehled clusterů Azure Stream Analytics (Preview)
description: Přečtěte si o samostatném tenantovi vyhrazené nabídce Stream Analytics clusteru.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: f67df45b6b663584e848983251031146cdf8df60
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2020
ms.locfileid: "95247689"
---
# <a name="overview-of-azure-stream-analytics-cluster-preview"></a>Přehled clusteru Azure Stream Analytics (Preview)

Azure Stream Analytics cluster nabízí nasazení jednoho tenanta pro složité a náročné scénáře streamování. Ve velkém měřítku můžou clustery Stream Analytics zpracovávat více než 200 MB za sekundu v reálném čase. Úlohy Stream Analytics spuštěné na vyhrazených clusterech můžou využívat všechny funkce v rámci standardní nabídky a zahrnují podporu připojení privátního propojení k vašim vstupům a výstupům.

Clustery Stream Analytics se účtují podle jednotek streamování (SUs), které představují množství prostředků procesoru a paměti přidělených vašemu clusteru. Jednotka streamování je stejná u standardních a vyhrazených nabídek. Pro každý cluster si můžete koupit 36, 72, 108, 144, 180 nebo 216 služby SUs. Cluster Stream Analytics může sloužit jako platforma pro streamování vaší organizace a může být sdílen různými týmy pracujícími v různých případech použití.

## <a name="what-are-stream-analytics-clusters"></a>Co jsou clustery Stream Analytics

Clustery Stream Analytics využívají stejný modul jako úlohy Stream Analytics spuštěné v prostředí s více tenanty. Vyhrazený cluster s jedním tenantem nabízí následující funkce:

* Hostování jednoho tenanta bez šumu z ostatních tenantů Skutečná izolace vašich prostředků a lepší fungování v případě prudkého zvýšení provozu

* Škálujte svůj cluster mezi 36 až 216 SUs, protože využití streamování se v průběhu času zvyšuje.

* Podpora virtuální sítě, která umožňuje, aby se vaše Stream Analytics úlohy připojovaly k jiným prostředkům zabezpečeným pomocí soukromých koncových bodů.

* Možnost vytvářet uživatelsky definované funkce jazyka C# a vlastní deserializace v libovolné oblasti.

* Nulové náklady na údržbu vám umožní zaměřit se na úsilí při vytváření řešení pro analýzu v reálném čase.

## <a name="how-to-get-started"></a>Jak začít

[Cluster Stream Analytics můžete vytvořit](create-cluster.md) pomocí [Azure Portal](https://aka.ms/asaclustercreateportal). Pokud máte nějaké dotazy nebo potřebujete pomáhat s připojováním, můžete se obrátit na [tým Stream Analytics](mailto:askasa@microsoft.com).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>Návody zvolit mezi clusterem Stream Analytics a Stream Analytics úlohou?

Nejjednodušším způsobem, jak začít, je vytvořit a vyvinout Stream Analyticsovou úlohu, která bude obeznámená se službou a zjistit, jak může splnit vaše požadavky na analýzu.

Virtuální sítě úlohy samostatně nepodporují. Stream Analytics Pokud jsou vstupy nebo výstupy zabezpečené za bránou firewall nebo Virtual Network Azure, máte tyto dvě možnosti:

* Pokud má váš místní počítač přístup ke vstupnímu a výstupnímu prostředku, který je zabezpečený virtuální sítí (například Azure Event Hubs nebo Azure SQL Database), můžete na svém místním počítači [nainstalovat Azure Stream Analytics Tools for Visual Studio](stream-analytics-tools-for-visual-studio-install.md) . Můžete vyvíjet a [testovat Stream Analytics úlohy místně](stream-analytics-live-data-local-testing.md) na vašem zařízení bez jakýchkoli nákladů. Až budete připraveni použít Stream Analytics ve vaší architektuře, můžete vytvořit cluster Stream Analytics, nakonfigurovat privátní koncové body a spouštět úlohy ve velkém měřítku.

* Můžete vytvořit cluster Stream Analytics, nakonfigurovat cluster s privátními koncovými body potřebnými pro váš kanál a spustit úlohy Stream Analytics v clusteru.

### <a name="what-performance-can-i-expect"></a>Jaký výkon můžu očekávat?

SU je stejný jako u standardních a vyhrazených nabídek. Jedna úloha, která využívá plný cluster 36 SU, může dosáhnout přibližně 36 MB/s propustnosti s latencí milisekund. Přesné číslo závisí na formátu událostí a typu analýz. Protože je vyhrazený, Stream Analytics cluster nabízí spolehlivější záruky výkonu. Všechny úlohy spuštěné v clusteru patří pouze vám.

### <a name="can-i-scale-my-cluster"></a>Můžu svůj cluster škálovat?

Ano. Kapacitu clusteru můžete snadno nakonfigurovat tak, abyste mohli podle potřeby [škálovat směrem nahoru nebo dolů](scale-cluster.md) , aby splňovala vaše měnící se požadavky.

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>Můžu na těchto nových clusterech spouštět stávající úlohy?

Ano. Existující úlohy můžete propojit s nově vytvořeným Stream Analyticsm clusterem a spustit je obvyklým způsobem. Nemusíte znovu vytvářet existující Stream Analytics úlohy od začátku.

### <a name="how-much-will-these-clusters-cost-me"></a>Do kolika se tyto clustery budou zasílat?

Vaše clustery Stream Analytics se účtují na základě zvolené kapacity SU. Clustery se účtují po hodinách a za úlohu spuštěnou v těchto clusterech se neúčtují žádné další poplatky. Fakturační aktualizace privátního koncového bodu najdete na [stránce s cenami služby Private Link](https://azure.microsoft.com/pricing/details/private-link/) .

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>K jakým vstupům a výstupům se můžu soukromě připojit z mého clusteru Stream Analytics?

Stream Analytics podporuje různé typy vstupů a výstupů. V clusteru můžete [vytvořit soukromé koncové body](private-endpoints.md) , které umožní úlohám přístup k vstupnímu a výstupnímu prostředku. V současné době je Azure SQL Database, Azure Storage, Azure Data Lake Storage Gen2, centrum událostí Azure a Azure Service Bus podporované služby a další typy budou brzy přidány. 

## <a name="next-steps"></a>Další kroky

Nyní máte přehled o Azure Stream Analytics clusteru. Dále můžete vytvořit cluster a spustit úlohu Stream Analytics: 

* [Vytvoření clusteru Stream Analytics](create-cluster.md)
* [Správa privátních koncových bodů v clusteru Azure Stream Analytics](private-endpoints.md)
* [Správa úloh Stream Analytics v clusteru Stream Analytics](manage-jobs-cluster.md)
* [Vytvoření úlohy Stream Analytics](stream-analytics-quick-create-portal.md)
