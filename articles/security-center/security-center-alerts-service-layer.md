---
title: Detekce hrozeb pro vrstvu služeb Azure v Azure Security Center | Microsoft Docs
description: V tomto tématu jsou uvedeny výstrahy ve vrstvě služeb Azure, které jsou k dispozici v Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/18/2019
ms.author: v-mohabe
ms.openlocfilehash: aae2270417e0bb3bdf0dd847e2e09e7cb1999d39
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972471"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Detekce hrozeb pro vrstvu služeb Azure v Azure Security Center

V tomto tématu jsou uvedeny výstrahy Security Center, které jsou k dispozici při monitorování následujících vrstev služeb Azure.

* [Síťová vrstva Azure](#network-layer)
* [Vrstva správy Azure (Azure Resource Manager) (Preview)](#management-layer)

>[!NOTE]
>Pomocí telemetrie, kterou Security Center využívá při klepnutí na interní informační kanály Azure, se níže uvedená analýza vztahuje na všechny typy prostředků.

## Síťová vrstva Azure<a name="network-layer"></a>

Analýza Security Centerch síťových vrstev vychází z ukázkových [dat IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), což jsou hlavičky paketů shromážděné směrovači Azure Core. Na základě tohoto datového kanálu Security Center modely strojového učení identifikují a označí škodlivé činnosti provozu. K rozšíření IP adres Security Center využívá databázi Microsoft Threat Intelligence.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Podezřelá odchozí síťová aktivita protokolu RDP**|Byla zjištěna analýza síťového provozu neobvyklé odchozího přenosu protokol RDP (Remote Desktop Protocol) (RDP) pocházející z prostředku ve vašem nasazení. Tato aktivita se považuje za neobvyklou pro toto prostředí a může znamenat, že došlo k ohrožení zabezpečení prostředku a že se teď používá pro externí koncový bod protokolu RDP hrubou silou. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|
|**Podezřelá odchozí síťová aktivita protokolu RDP do více cílů**|Byla zjištěna analýza síťového provozu neobvyklé odchozího přenosu protokol RDP (Remote Desktop Protocol) (RDP) pocházející z prostředku v nasazení do více cílů. Tato aktivita se považuje za neobvyklou pro toto prostředí a může znamenat, že došlo k ohrožení zabezpečení prostředku a že se teď používá pro externí koncové body protokolu RDP hrubou silou. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|
|**Podezřelá odchozí síťová aktivita protokolu SSH**|Ukázková Analýza provozu v síti zjistila komunikaci neobvyklé odchozí Secure Shell (SSH) pocházející z prostředku ve vašem nasazení. Tato aktivita se považuje za neobvyklou pro toto prostředí a může znamenat, že došlo k ohrožení zabezpečení prostředku a že se teď používá pro externí koncový bod SSH s hrubou silou. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|
|**Podezřelá odchozí síťová aktivita protokolu SSH do více cílů**|Ukázková analýza síťového provozu zjistila, že komunikace neobvyklé odchozího Secure Shell (SSH) pocházející z prostředku v nasazení do více cílů. Tato aktivita se považuje za neobvyklou pro toto prostředí a může znamenat, že došlo k ohrožení zabezpečení prostředku a že se teď používá k externím koncovým bodům SSH pro hrubou silou. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|
|**Podezřelá příchozí síťová aktivita protokolu SSH z více zdrojů**|Analýza analýzy síťových přenosů zjistila neobvyklé příchozí komunikaci SSH z více zdrojů do prostředku ve vašem nasazení. Různé jedinečné IP adresy, které se připojují k vašemu prostředku, se považují za neobvyklé pro toto prostředí. Tato aktivita může znamenat pokus o útok hrubou silou vašeho rozhraní SSH z více hostitelů (botnetu).|
|**Podezřelá příchozí síťová aktivita protokolu SSH**|Při analýze ukázkových síťových přenosů se zjistila příchozí komunikace SSH neobvyklé prostředku ve vašem nasazení. Poměrně velký počet příchozích připojení k vašemu prostředku se pro toto prostředí považuje za neobvyklé. Tato aktivita může znamenat pokus o útok hrubou silou vašeho rozhraní SSH.
|**Podezřelá příchozí síťová aktivita protokolu RDP z více zdrojů**|Při analýze ukázkových síťových přenosů se zjistilo, že příchozí komunikace protokolu RDP z více zdrojů do prostředku v nasazení byla neobvyklé. Různé jedinečné IP adresy, které se připojují k vašemu prostředku, se považují za neobvyklé pro toto prostředí. Tato aktivita může naznačovat pokus o útok hrubou silou vašeho rozhraní RDP z více hostitelů (botnetu).|
|**Podezřelá příchozí síťová aktivita protokolu RDP**|Při analýze ukázkových síťových přenosů bylo zjištěno, že příchozí komunikace protokolu RDP neobvyklé do prostředku v nasazení. Poměrně velký počet příchozích připojení k vašemu prostředku se pro toto prostředí považuje za neobvyklé. Tato aktivita může znamenat pokus o útok hrubou silou vašeho rozhraní SSH.|
|**Byla zjištěna síťová komunikace se škodlivou adresou.**|Analýza analýzy síťových přenosů zjistila komunikaci pocházející z prostředku ve vašem nasazení s možným příkazem a řízením (C & C) Server. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|

Informace o tom, jak Security Center můžou použít signály související se sítí pro použití ochrany před hrozbami, najdete [v tématu heuristické detekce služby DNS v Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).

>[!NOTE]
>Výstrahy detekce hrozby síťové vrstvy Azure se v Azure Security Center negenerují během první hodiny po vytvoření virtuálního počítače.

## Vrstva správy Azure (Azure Resource Manager) (Preview)<a name ="management-layer"></a>

>[!NOTE]
>Vrstva Security Center Protection založená na Azure Resource Manager je aktuálně ve verzi Preview.

Security Center nabízí další úroveň ochrany tím, že využívá Azure Resource Manageré události, které se považují za řídicí plochu pro Azure. Díky analýze záznamů Azure Resource Manager Security Center detekuje neobvyklé nebo potenciálně škodlivé operace v prostředí předplatného Azure.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Běh sady nástrojů pro mikroshluky**|Ve vašem prostředí se zjistila známá spuštění rekognoskace sady nástrojů cloudového prostředí. Nástroj "mikroshlukování" (viz https://github.com/NetSPI/MicroBurst) může použít útočník (nebo tester) k mapování vašich prostředků předplatného, identifikace nezabezpečených konfigurací a důvěrných informací o netěsnosti.|
|**Spuštění sady nástrojů Azurite**|Ve vašem prostředí se zjistila známá spuštění rekognoskace sady nástrojů cloudového prostředí. Nástroj "Azurite" (viz https://github.com/mwrlabs/Azurite) může být použit útočníkem (nebo testerem průniku) k mapování prostředků vašich předplatných a identifikaci nezabezpečených konfigurací.|
|**Podezřelá relace správy s použitím neaktivního účtu**|Analýza protokolů aktivit předplatného zjistila podezřelé chování. Objekt zabezpečení, který se nepoužívá po dlouhou dobu, nyní provádí akce, které mohou zabezpečit trvalost pro útočníka.|
|**Podezřelá relace správy s využitím PowerShellu**|Analýza protokolů aktivit předplatného zjistila podezřelé chování. Objekt zabezpečení, který ke správě prostředí předplatného pravidelně nepoužívá PowerShell, teď používá PowerShell a provádí akce, které můžou zabezpečit trvalost pro útočníka.|
|**Použití pokročilých technik trvalosti Azure**|Analýza protokolů aktivit předplatného zjistila podezřelé chování. Přizpůsobené role byly předány entitám identity legitimized. To může vést k tomu, že útočník získá průniku v prostředí Azure Customer.|
|**Aktivita z málo časté země**|Došlo k aktivitě z umístění, které se v poslední době nebo nikdy nenavštívilo žádným uživatelem v organizaci.<br/>Tato detekce ohledem na minulou umístění aktivit k určení nové a úlohy s řídkým umístění. Modul detekce anomálií ukládá informace o předchozí umístění, které používají uživatelé v organizaci. 
|**Aktivita z anonymních IP adres**|Byla zjištěna aktivita uživatelů z IP adresy, která byla identifikována jako IP adresa anonymního proxy serveru. <br/>Tato proxy používají lidé, kteří mají skrýt IP adresu svého zařízení a může sloužit ke škodlivým činnostem. Tato detekce využívá algoritmus strojového učení, který omezuje "falešně pozitivní", například IP adresy nesprávného označení, které uživatelé v organizaci často používají.|
|**Zjistila se nemožná cesta.**|Existují dvě aktivity uživatelů (Jedná se o jednu nebo více relací), které pocházejí z geograficky vzdálených umístění v rámci časového období kratšího než čas, kdy by uživatel musel cestovat z prvního umístění do druhé. To znamená, že stejný přihlašovací údaj používá jiný uživatel. <br/>Tato detekce využívá algoritmus strojového učení, který ignoruje zjevné "falešně pozitivní", které přispívají k nemožným podmínkám cestování, jako jsou sítě VPN a lokality pravidelně používané ostatními uživateli v organizaci. Detekce má období učení sedm dní, během kterých se naučí vzor aktivit nového uživatele.|

>[!NOTE]
> Některé z výše uvedených analýz jsou napájené z Microsoft Cloud App Security (MCAS). Pro výhod těchto analýz se vyžaduje aktivovaná licence MCAS. Pokud máte licenci MCAS, jsou tyto výstrahy ve výchozím nastavení povolené. Chcete-li je zakázat:
>
> 1. V okně Security Center vyberte **zásady zabezpečení**. U předplatného, které chcete změnit, klikněte na **Upravit nastavení**.
> 2. Klikněte na **detekce hrozeb**.
> 3. V části **Povolit integrace**zrušte zaškrtnuté políčko **Povolit Microsoft Cloud App Security přístup k datům**a klikněte na **Uložit**.

>[!NOTE]
>Azure Security Center ukládá zákaznická data týkající se zabezpečení ve stejné geografické podobě jako její prostředek. Pokud společnost Microsoft ještě není nasazená Azure Security Center v geografickém prostředku, uloží data do USA. Pokud je povolená Microsoft Cloud App Security (MCAS), ukládají se tyto informace v souladu s pravidly geografického umístění MCAS. Další informace najdete v tématu [úložiště dat pro jiné než regionální služby](https://azuredatacentermap.azurewebsites.net/).
