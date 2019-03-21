---
title: Plánování kapacity pro role serveru služby Azure App Service ve službě Azure Stack | Dokumentace Microsoftu
description: Plánování kapacity pro role serveru služby Azure App Service ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: 06bafbcf3e668ba17b1245b9352e942e02569997
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852361"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Plánování kapacity pro role serveru služby Azure App Service ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Nastavení připravené produkční nasazení služby Azure App Service ve službě Azure Stack, je nutné naplánovat kapacitu očekáváte, že systém pro podporu.  

Tento článek obsahuje pokyny pro minimální počet výpočetních instancí a výpočetní výkon skladové položky byste měli použít pro každého produkčního nasazení.

Můžete naplánovat strategie kapacitu služby App Service pomocí následujících pokynů.

| Role serveru služby App Service | Minimální doporučená počet instancí | Doporučené výpočetní SKU|
| --- | --- | --- |
| Kontrolér | 2 | A1 |
| Front-end | 2 | A1 |
| Správa | 2 | A3 |
| Vydavatel | 2 | A1 |
| Webových pracovních procesů – sdílené | 2 | A1 |
| Webových pracovních procesů – vyhrazené | 2 a úroveň | A1 |

## <a name="controller-role"></a>Role kontroleru

**Doporučená minimální**: Dvě instance A1 Standard

Řadič služby Azure App Service obvykle dojde nízké využití procesoru, paměti a síťové prostředky. Pro zajištění vysoké dostupnosti, ale musí mít dva řadiče. Dva řadiče jsou také maximální počet řadičů povolené. Můžete vytvořit druhý řadič webů s přímým přístupem v instalačním programu během nasazení.

## <a name="front-end-role"></a>Role front-endu

**Doporučená minimální**: Dvě instance A1 Standard

Front-endových tras požadavky na webových pracovních procesů v závislosti na dostupnosti webových pracovních procesů. Pro zajištění vysoké dostupnosti měli byste mít více než jeden front-endu a může mít více než dvě. Pro účely plánování kapacity vezměte v úvahu, každé jádro může zpracovat přibližně 100 požadavků za sekundu.

## <a name="management-role"></a>Role pro správu

**Doporučená minimální**: Dvě instance A3 Standard

Role pro správu služby Azure App Service je odpovědná za aplikace služby Azure Resource Manageru a koncových bodů rozhraní API, rozšíření portálu (admin, tenanta, portálu Functions) a datové služby. Role serveru pro správu se obvykle vyžaduje jenom o 4 GB paměti RAM v produkčním prostředí. Však dojít vysokou úroveň využití procesoru, pokud se provádí řadu úloh správy (jako je například vytváření webu). Pro zajištění vysoké dostupnosti byste měli mít více než jeden server, které jsou přiřazené k této roli a minimálně dva jader na server.

## <a name="publisher-role"></a>Role vydavatele

**Doporučená minimální**: Dvě instance A1 Standard

Pokud mnoho uživatelů současně publikujete, roli vydavatele může docházet k velkým využití procesoru. Pro zajištění vysoké dostupnosti Ujistěte se, že je k dispozici více než jedné role vydavatele. Vydavatel zpracovává jenom provoz FTP/FTPS.

## <a name="web-worker-role"></a>Webová role pracovního procesu

**Doporučená minimální**: Dvě instance A1 Standard

Pro zajištění vysoké dostupnosti měli byste mít alespoň čtyři webových rolí pracovního procesu, dvě pro režim sdílený web a dvě pro jednotlivé vrstvy vyhrazených pracovních serverů, které máte v plánu nabídnout. Sdílené a vyhrazené výpočetní režimy poskytovat různé úrovně služby klientům. Pokud mnoho vašich zákazníků, může být nutné další webových pracovních procesů:

- Pomocí vyhrazené výpočetní vrstvy pracovních procesů režimu, (které jsou náročné).
- Používané sdílený výpočetní režim.

Po vytvoření plánu služby App Service pro vyhrazený výpočetní režim skladové položky uživatele číslo zadané v tomto plánu služby App Service pracovníky webové již není k dispozici pro uživatele.

K poskytování Azure Functions pro uživatele v modelu plánu consumption, je nutné nasadit sdílených webových pracovních procesů.

Při rozhodování o počet sdílených webových rolí pracovního procesu k použití, přečtěte si tyto aspekty:

- **Paměť**: Paměť je nejdůležitějším prostředkem pro roli webového pracovního procesu. Nedostatek paměti ovlivňuje výkonu webu, když se virtuální paměti Prohodí z disku. Každý server vyžaduje asi 1,2 GB paměti RAM pro operační systém. Paměť RAM nad touto prahovou hodnotou lze použít ke spuštění webové stránky.
- **Procento weby aktivní**: Obvykle jsou aktivní o 5 procent aplikací v Azure App Service v nasazení Azure stacku. Procento aplikací, které jsou v kterémkoli daném okamžiku aktivní však může být vyšší nebo nižší. S objemem aktivní aplikace 5 procent maximální počet aplikací, umístíte do služby Azure App Service v nasazení Azure stacku by být menší než 20 opakováních počtu aktivní weby (5 × 20 = 100).
- **Průměrná paměť**: Průměrná paměť pro aplikace v produkčním prostředí je asi 70 MB. Pomocí této nároky na místo, je paměť přidělená přes všechny webové pracovní role počítače nebo virtuální počítače můžete vypočítat následujícím způsobem:

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Například pokud je 5 000 aplikací v prostředí, na kterém běží 10 webových rolí pracovního procesu, každá webová role pracovního procesu virtuálního počítače by měl mít 7060 MB paměti RAM:

   `5,000 * 70 * 0.05 – (10 * 1044) = 7060 (= about 7 GB)`

   Informace o přidání více instancí pracovního procesu najdete v tématu [přidávání dalších rolí pracovního procesu](azure-stack-app-service-add-worker-roles.md).

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>Další informace pro vyhrazené pracovní procesy během upgradu a údržby

Během upgradu a údržby pracovních procesů, které služby Azure App Service ve službě Azure Stack se údržbě 20 % jednotlivé vrstvy pracovního procesu v daný okamžik.  Správci cloudu proto musíte mít vždy 20 % fond nepřiděleného pracovních procesů na vrstvu pracovního procesu k zajištění, že se že svým klientům nedochází ke ztrátě služeb během upgradu a údržby.  Například pokud máte 10 pracovních procesů v vrstvu pracovního procesu byste měli zajistit, že 2 jsou volné umožňující upgradu a údržby, pokud úplné 10 pracovní procesy budou přidělené můžete by se měly škálovat vrstvu pracovního procesu udržovat fond nepřiděleného pracovních procesů. Během upgradu a údržby služby Azure App Service se přesune, že úlohy nepřidělené zaměstnancům zajistit, že úlohy budou i nadále fungovat, ale pokud neexistují žádné volné pracovní procesy k dispozici během upgradu pak existuje bude potenciál pro úlohu tenanta výpadek.  S ohledem na sdílených pracovních procesů zákazníci není nutné zřídit další pracovní procesy, protože služba se přidělí tenanta aplikací v rámci dostupné pracovní procesy automaticky, pro zajištění vysoké dostupnosti, ale je minimální požadavek pro dva pracovní procesy v tomto úroveň.

Správci cloudu můžete monitorovat jejich přidělení vrstvy pracovního procesu v oblasti správy služeb aplikace v portálu pro správu služby Azure Stack.  Přejděte do služby App Service a potom v levém podokně vyberte vrstvy pracovních procesů.  Vrstvy pracovních procesů uvádí název vrstvy pracovního procesu, velikost, bitová kopie používaná, počet dostupných pracovních procesů (volné), celkový počet pracovních procesů v jednotlivých vrstvách a celkový stav vrstvy pracovního procesu.

![Správa služby App Service – vrstvy pracovních procesů][1]

## <a name="file-server-role"></a>Role souborového serveru

Pro roli souborového serveru můžete použít samostatný souborový server pro vývoj a testování. například při nasazování služby Azure App Service v Azure Stack Development Kit (ASDK) může být využit [šablony](https://aka.ms/appsvconmasdkfstemplate).  Pro produkční účely byste použít předem nakonfigurovaný souborový server Windows nebo předem nakonfigurovaný soubor bez Windows serveru.

V produkčním prostředí roli souborového serveru narazí vstupně-výstupní operace náročné na disku. Protože to jsou uloženy všechny soubory obsahu a aplikace pro uživatelské web sites, musíte předem nakonfigurovat jednu z následujících prostředků pro tuto roli:

- Souborový server Windows
- Cluster souborových serverů Windows
- Soubor bez Windows serveru
- Cluster Windows bez souborových serverů
- Zařízení NAS (Network Attached Storage)

Zobrazit další informace najdete v následujícím článku [zřízení souborového serveru](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Další postup

Naleznete v následujícím článku pro další informace:

[Před zahájením práce s App Service ve službě Azure Stack](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png