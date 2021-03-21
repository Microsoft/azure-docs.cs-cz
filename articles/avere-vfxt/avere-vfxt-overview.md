---
title: Avere vFXT for Azure
description: Přečtěte si o avere vFXT pro Azure, což je cloudové řešení pro ukládání souborů do mezipaměti pro vysoce výkonné výpočetní úkoly náročné na data.
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 5095f05b0c9ce9061781c3558ca6c7091ef37aa5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270952"
---
# <a name="what-is-avere-vfxt-for-azure"></a>Co je Avere vFXT for Azure?

Avere vFXT for Azure je řešení ukládání do mezipaměti na úrovni systému souborů pro úlohy vysokovýkonného výpočetního prostředí (HPC) s velkými nároky na data. Umožňuje využít škálovatelnost cloud computingu ke zpřístupnění dat kdykoli a kdekoli jsou potřeba, a to i dat uložených na vlastním místním hardwaru.

Avere vFXT podporuje tyto běžné scénáře výpočetního prostředí:

* Hybridní cloudová architektura – avere vFXT pro Azure může pracovat s hardwarovým úložným systémem, který přináší výhody cloud computingu, aniž by bylo nutné přesouvat soubory.

* Shlukování cloudu – avere vFXT pro Azure vám může přispět k přesunu dat do cloudu pro jeden projekt nebo "zvednutí a posun" celého pracovního postupu.

![Diagram znázorňující podrobnosti o systému Avere vFXT v předplatném Azure připojeném k úložišti objektů blob a místnímu datacentru](media/avere-vfxt-hybrid.png)

Avere vFXT pro Azure se nejlépe hodí pro tyto situace:

* Operace s velkými nároky na čtení pro úlohy HPC
* Aplikace využívající běžný protokol NFS
* Výpočetní farmy s 1 000 až 40 000 procesorových jader
* Integrace s místním hardwarovým řešením NAS, úložištěm objektů blob v Azure nebo oběma

Další informace najdete na adrese <https://azure.microsoft.com/services/storage/avere-vfxt/>.

## <a name="who-uses-avere-vfxt-for-azure"></a>Kdo používá Avere vFXT for Azure?

Avere vFXT může pomáhat s nejrůznějšími výpočetními úlohami náročnými na čtení:

### <a name="visual-effects-rendering"></a>Vykreslování vizuálních efektů

V oblasti médií a zábavy může cluster Avere vFXT zrychlit přístup k datům u projektů vykreslování náročných na čas. Díky možnosti přidat více místa v mezipaměti i výpočetních uzlů v Azure získáte flexibilitu efektivně zpracovávat velké projekty.

### <a name="life-sciences"></a>Lékařské technologie

Avere vFXT umožňuje výzkumným pracovníkům provádět sekundární analytické pracovní postupy ve službě Azure COMPUTE a přistupovat k datům genom bez ohledu na jejich umístění.

Ve farmaceutickém výzkumu mohou clustery avere vFXT zrychlit zjišťování drog tím, že pomáhá výzkumným pracovníkům předpovědět interakce s cílem drog a analyzovat data výzkumu.

### <a name="financial-services-analytics"></a>Analýza finančních služeb

Cluster Avere vFXT může pomoct s urychlením výpočtů kvantitativní analýzy a poskytnout tak společnostem poskytujícím finanční služby lepší přehled, který jim umožní provádět strategická rozhodnutí.

## <a name="features-and-specifications"></a>Funkce a specifikace

Systém Avere vFXT se skládá ze tří nebo více virtuálních hraničních souborových uzlů nakonfigurovaných v clusteru. Může se nacházet blízko klientským počítačům, které místo přímého připojování úložiště připojují cluster.

Cluster Avere vFXT ukládá požadované soubory okamžitě do mezipaměti. Opakované požadavky je možné ve více než 80 % případů obsluhovat z mezipaměti.

### <a name="compatibility"></a>Kompatibilita

* Kompatibilní s hardwarovými systémy NAS od NetApp nebo Dell EMC Isilon
* Kompatibilní s Azure Blob
* Používá protokol NFSv3 nebo SMB2

Avere vFXT pro Azure používá následující prostředky Azure:

|Komponenta Azure| Prostředek |
|----------|-----------|
|Virtuální počítače|3 nebo více E32s_v3|
|Úložiště SSD úrovně Premium|200 GB místa na operační systém a 1 až 4 TB místa v mezipaměti na uzel |
|Účet úložiště (volitelné) |v2|
|Back-endové úložiště dat (volitelné) | Jeden prázdný kontejner objektů blob místně redundantního úložiště |

## <a name="next-steps"></a>Další kroky

Přečtěte si tyto články, abyste naplánovali a vytvořili vlastní avere vFXT pro nasazení Azure.

* [Plánování systému](avere-vfxt-deploy-plan.md)
* [Přehled nasazení](avere-vfxt-deploy-overview.md)
* [Vytvoření vFXT](avere-vfxt-deploy.md)
