---
title: Avere vFXT pro Azure
description: Seznámení s avere vFXT pro Azure, vrstva mezipaměti cloudu pro HPC
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
# <a name="what-is-avere-vfxt-for-azure"></a>Co je avere vFXT pro Azure? 

Avere vFXT for Azure je řešení pro ukládání do mezipaměti systému souborů pro úlohy HPC (High Performance Computing) náročné na data. Umožňuje využít škálovatelnost cloud computingu a zpřístupnit data v případě potřeby a tam, kde je potřeba, a to i pro data uložená ve vašem vlastním místním hardwaru.

Avere vFXT podporuje tyto běžné výpočetní scénáře: 

* Hybridní cloudová architektura: avere vFXT for Azure může pracovat s hardwarovým úložným systémem, který přináší výhody cloud computingu, aniž byste museli přesouvat soubory. 
* Shlukování cloudu: avere vFXT pro Azure vám může přispět k přesunu dat do cloudu pro jeden projekt nebo "zvednutí a posunutí" celého pracovního postupu. 

![Diagram znázorňující podrobnosti o systému avere vFXT v rámci předplatného Azure připojeného k úložišti objektů BLOB a místnímu datovému centru](media/avere-vfxt-hybrid.png)

Avere vFXT pro Azure se nejlépe hodí pro tyto situace: 

* Operace čtení – těžké pro úlohy prostředí HPC
* Aplikace používající běžný protokol NFS
* Výpočetní farmy 1000 až 40 000 PROCESORových jader
* Integrace s místním hardwarovým serverem NAS, úložištěm objektů BLOB v Azure nebo obojím

Další informace najdete na stránce @no__t – 0.

## <a name="who-uses-avere-vfxt-for-azure"></a>Kdo používá avere vFXT pro Azure? 

Avere vFXT může pomáhat s nejrůznějšími výpočetními úkoly náročnými na čtení:

### <a name="visual-effects-rendering"></a>Vykreslování vizuálních efektů 

V případě médií a zábavy může cluster avere vFXT zrychlit přístup k datům pro projekty vykreslování kritické pro čas. Vzhledem k tomu, že v Azure můžete přidat další místo pro mezipaměť i více výpočetních uzlů, máte flexibilitu efektivně zpracovávat velké projekty. 

### <a name="life-sciences"></a>Vědy o života 

Avere vFXT může umožnit výzkumným pracovníkům spouštět své sekundární analytické pracovní postupy ve službě Azure COMPUTE a přistupovat k datům genom bez ohledu na jejich umístění.

Ve farmaceutickém výzkumu se clustery avere vFXT dají využít k urychlení zjišťování drog tím, že pomáhají výzkumným pracovníkům cílit interakce zaměřené na drogy a analyzovat data o výzkumu.

### <a name="financial-services-analytics"></a>Analýzy finančních služeb

Cluster avere vFXT může pomoci zrychlit kvantitativní výpočty analýz, které poskytují společnosti finančních služeb lepší přehled o strategických rozhodnutích. 

## <a name="features-and-specifications"></a>Funkce a specifikace

Systém avere vFXT se skládá ze tří nebo více uzlů souborového virtuální hrany, které jsou nakonfigurovány v clusteru. Může se nacházet poblíž klientských počítačů, které připojovat cluster místo přímého připojení úložiště. 

Cluster avere vFXT ukládá soubory do mezipaměti, jak jsou požadovány. Opakované požadavky lze zpracovat z mezipaměti více než 80% času.

### <a name="compatibility"></a>Režim 

* Kompatibilní s hardwarovými systémy NAS z NetApp nebo Dell EMC Isilon
* Kompatibilní s Azure Blob
* Používá protokol NFSv3 nebo SMB2.

Avere vFXT používá následující prostředky Azure: 

|Komponenta Azure|   |
|----------|-----------|
|Virtuální počítače|3 nebo více E32s_v3|
|SSD úrovně Premium úložiště|200 GB místa pro operační systém a 1 TB až 4 TB místa v mezipaměti na uzel |
|Účet úložiště (volitelné) |2|
|Úložiště back-endu dat (volitelné) | Jeden prázdný kontejner objektů BLOB LRS |

## <a name="next-steps"></a>Další kroky

Tady je několik odkazů, které vám pomohou začít vytvářet vlastní nasazení služby avere vFXT. 

* [Plánování systému](avere-vfxt-deploy-plan.md)
* [Přehled nasazení](avere-vfxt-deploy-overview.md)
* [Vytvoření vFXT](avere-vfxt-deploy.md)
