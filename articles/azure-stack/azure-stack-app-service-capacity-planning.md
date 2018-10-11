---
title: Plánování kapacity pro role serveru služby Azure App Service ve službě Azure Stack | Dokumentace Microsoftu
description: Plánování kapacity pro role serveru služby Azure App Service ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: 7cdcd8b7e9814c206255077fae0af2029fab6583
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078109"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Plánování kapacity pro role serveru služby Azure App Service ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Nastavení připravené produkční nasazení služby Azure App Service ve službě Azure Stack, je nutné naplánovat kapacitu očekáváte, že systém pro podporu.  

Tento článek obsahuje pokyny pro minimální počet výpočetních instancí a výpočetní výkon skladové položky byste měli použít pro každého produkčního nasazení.

Můžete naplánovat strategie kapacitu služby App Service pomocí následujících pokynů. Budoucí verze služby Azure Stack poskytne možnosti vysoké dostupnosti pro službu App Service.

| Role serveru služby App Service | Minimální doporučená počet instancí | Doporučené výpočetní SKU|
| --- | --- | --- |
| Kontroler | 2 | A1 |
| Front-end | 2 | A1 |
| Správa | 2 | A3 |
| Vydavatel | 2 | A1 |
| Webových pracovních procesů – sdílené | 2 | A1 |
| Webových pracovních procesů – vyhrazené | 2 a úroveň | A1 |

## <a name="controller-role"></a>Role kontroleru

**Doporučená minimální**: dvě instance A1 Standard

Azure App Service Controller obvykle dojde nízké využití procesoru, paměti a síťové prostředky. Pro zajištění vysoké dostupnosti, ale musí mít dva řadiče. Dva řadiče jsou také maximální počet řadičů povolené. Můžete vytvořit druhý řadič webů s přímým přístupem v instalačním programu během nasazení.

## <a name="front-end-role"></a>Přední atributu role elementu End

**Doporučená minimální**: dvě instance A1 Standard

Front-endu směruje žádosti do webových pracovních procesů v závislosti na dostupnosti webového pracovního procesu. Pro zajištění vysoké dostupnosti měli byste mít více než jeden front-endu a může mít více než dvě. Pro účely plánování kapacity vezměte v úvahu, každé jádro může zpracovat přibližně 100 požadavků za sekundu.

## <a name="management-role"></a>Role pro správu

**Doporučená minimální**: dvě instance A3 Standard

Role správy Azure App Service je zodpovědná za aplikace služby Azure Resource Manageru a koncových bodů rozhraní API, rozšíření portálu (admin, tenanta, portálu Functions) a datové služby. Role serveru pro správu se obvykle vyžaduje jenom o 4 GB paměti RAM v produkčním prostředí. Však dojít vysokou úroveň využití procesoru, pokud se provádí řadu úloh správy (jako je například vytváření webu). Pro zajištění vysoké dostupnosti byste měli mít více než jeden server, které jsou přiřazené k této roli a minimálně dva jader na server.

## <a name="publisher-role"></a>Role vydavatele

**Doporučená minimální**: dvě instance A1 Standard

Pokud mnoho uživatelů současně publikujete, roli vydavatele může docházet k velkým využití procesoru. Pro zajištění vysoké dostupnosti zpřístupníte víc než jedné role vydavatele.  Vydavatel zpracovává jenom provoz FTP/FTPS.

## <a name="web-worker-role"></a>Webová role pracovního procesu

**Doporučená minimální**: dvě instance A1 Standard

Pro zajištění vysoké dostupnosti měli byste mít alespoň čtyři webových rolí pracovního procesu, dvě pro sdílený režim webu a dvě pro jednotlivé vrstvy vyhrazených pracovních serverů je v plánu nabídnout. Sdílené a vyhrazené výpočetní režimy poskytují různé úrovně služby klientům. Pokud mnoho vašich zákazníků, může být nutné další webových pracovních procesů:

- Pomocí vyhrazené výpočetní vrstvy pracovních procesů režimu, (které jsou náročné.)
- Používané sdílený výpočetní režim.

Jakmile uživatel vytvoří plán služby App Service pro vyhrazený výpočetní režim skladové položky, počet webových pracovníky uvedené v plánu služby App Service bude nadále již nebudou dostupné pro uživatele.

K poskytování Azure Functions pro uživatele v modelu plánu consumption, je nutné nasadit sdílených webových pracovních procesů.

Při rozhodování o počet sdílených rolí webového pracovního procesu k použití, přečtěte si tyto aspekty:

- **Paměť**: paměť je nejdůležitějším prostředkem pro roli webového pracovního procesu. Nedostatek paměti ovlivňuje výkonu webu, když se virtuální paměti Prohodí z disku. Každý server vyžaduje asi 1,2 GB paměti RAM pro operační systém. Paměť RAM nad touto prahovou hodnotou lze použít ke spuštění webové stránky.
- **Procento weby aktivní**: obvykle jsou aktivní o 5 procent aplikací v Azure App Service v nasazení Azure stacku. Procento aplikací, které jsou v kterémkoli daném okamžiku aktivní však může být vyšší nebo nižší. S objemem aktivní aplikace 5 procent maximální počet aplikací, umístíte do služby Azure App Service v nasazení Azure stacku by měl být menší než:
  - 20 opakováních číslo aktivní weby (5 × 20 = 100).
- **Průměrná paměť**: průměrná paměť pro aplikace v produkčním prostředí je asi 70 MB. Pomocí této nároky na místo, je paměť přidělená mezi všechny virtuální počítače nebo počítače pro roli webového pracovního procesu můžete vypočítat následujícím způsobem:

    *Počet aplikací zřízená * 70 MB * 5 % - (číslo z webových rolí pracovního procesu * 1044 MB)*

   Například pokud je 5 000 aplikací v prostředí, na kterém běží 10 rolemi webového pracovního procesu, každý webový pracovní proces role virtuálního počítače by měl mít 7060 MB paměti RAM:

   5 000 * 70 * 0,05 – (10 * 1044) = 7060 (= 7 GB)

   Informace o přidávání dalších instancí pracovního procesu najdete v tématu [přidávání dalších rolí pracovního procesu](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Role souborového serveru

Pro roli souborového serveru, můžete použít samostatný souborový server pro vývoj a testování, například při nasazování služby Azure App Service v Azure Stack Development Kit můžete použít tuto šablonu – <https://aka.ms/appsvconmasdkfstemplate>. Pro produkční účely byste použít předem nakonfigurovaný souborový Server Windows nebo předem nakonfigurovaný soubor bez Windows serveru.

V produkčním prostředí role souborového serveru narazí vstupně-výstupní operace náročné na disku. Protože to jsou uloženy všechny soubory obsahu a aplikace pro uživatelské web sites, musíte předem nakonfigurovat jednu z následujících akcí pro tuto roli:

- Souborový Server s Windows
- jeden Cluster souborových serverů Windows
- soubor není Windows server
- cluster bez Windows souborových serverů
- zařízení NAS (Network Attached Storage)

Další informace najdete v tématu [zřízení souborového serveru](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Další postup

[Před zahájením práce s App Service ve službě Azure Stack](azure-stack-app-service-before-you-get-started.md)
