---
title: Avere vFXT for Azure
description: Úvod do Avere vFXT for Azure, cloudové vrstvy mezipaměti pro prostředí HPC
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 70ae20daa81ab52d4054dcd4bea3c9432a5ceaeb
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256170"
---
# <a name="what-is-avere-vfxt-for-azure"></a>Co je Avere vFXT for Azure? 

Avere vFXT for Azure je řešení ukládání do mezipaměti na úrovni systému souborů pro úlohy vysokovýkonného výpočetního prostředí (HPC) s velkými nároky na data. Umožňuje využít škálovatelnost cloud computingu ke zpřístupnění dat kdykoli a kdekoli jsou potřeba, a to i dat uložených na vlastním místním hardwaru.

Avere vFXT podporuje tyto běžné scénáře výpočetního prostředí: 

* Hybridní cloudová architektura: Avere vFXT for Azure dokáže pracovat se systémem hardwarového úložiště a poskytuje tak výhody cloud computingu bez nutnosti přesouvat soubory. 
* Shlukování cloudu: Avere vFXT for Azure vám může pomoct s přesunem dat do cloudu pro jeden projekt nebo s trvalou migrací celého pracovního postupu metodou „lift and shift“. 

![Diagram znázorňující podrobnosti o systému Avere vFXT v předplatném Azure připojeném k úložišti objektů blob a místnímu datacentru](media/avere-vfxt-hybrid.png)

Avere vFXT for Azure je nejvhodnější pro tyto situace: 

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

Avere vFXT může výzkumným pracovníkům umožnit spouštět pracovní postupy sekundární analýzy ve službě Azure Compute a přistupovat ke genomickým údajům, ať jsou kdekoli.

Ve farmaceutickém výzkumu můžou clustery Avere vFXT sloužit k urychlení objevování léků díky tomu, že pomůžou výzkumným pracovníkům předvídat interakce léků s cílem a analyzovat data o výzkumu.

### <a name="financial-services-analytics"></a>Analýza finančních služeb

Cluster Avere vFXT může pomoct s urychlením výpočtů kvantitativní analýzy a poskytnout tak společnostem poskytujícím finanční služby lepší přehled, který jim umožní provádět strategická rozhodnutí. 

## <a name="features-and-specifications"></a>Funkce a specifikace

Systém Avere vFXT se skládá ze tří nebo více virtuálních hraničních souborových uzlů nakonfigurovaných v clusteru. Může se nacházet blízko klientským počítačům, které místo přímého připojování úložiště připojují cluster. 

Cluster Avere vFXT ukládá požadované soubory okamžitě do mezipaměti. Opakované požadavky je možné ve více než 80 % případů obsluhovat z mezipaměti.

### <a name="compatibility"></a>Kompatibilita 

* Kompatibilní s hardwarovými systémy NAS od NetApp nebo Dell EMC Isilon
* Kompatibilní s Azure Blob
* Používá protokol NFSv3 nebo SMB2

Azure vFXT používá následující prostředky Azure: 

|Komponenta Azure|   |
|----------|-----------|
|Virtuální počítače|3 nebo více E32s_v3|
|Úložiště SSD úrovně Premium|200 GB místa na operační systém a 1 až 4 TB místa v mezipaměti na uzel |
|Účet úložiště (volitelné) |v2|
|Back-endové úložiště dat (volitelné) | Jeden prázdný kontejner objektů blob místně redundantního úložiště |

## <a name="next-steps"></a>Další kroky

Tady je několik odkazů, které vám pomůžou začít vytvářet vlastní nasazení Avere vFXT. 

* [Plánování systému](avere-vfxt-deploy-plan.md)
* [Přehled nasazení](avere-vfxt-deploy-overview.md)
* [Vytvoření vFXT](avere-vfxt-deploy.md)
