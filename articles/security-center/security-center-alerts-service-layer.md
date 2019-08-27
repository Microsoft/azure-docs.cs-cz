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
ms.date: 8/25/2019
ms.author: v-mohabe
ms.openlocfilehash: bc1b2a07f15ca98da7750c85cd25f2c5766c9bb5
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018168"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Detekce hrozeb pro vrstvu služby Azure v Azure Security Center

V tomto tématu jsou uvedeny výstrahy Azure Security Center, které jsou k dispozici při monitorování následujících vrstev služeb Azure:

* [Síťová vrstva Azure](#network-layer)
* [Vrstva správy Azure (Azure Resource Manager) (Preview)](#management-layer)

>[!NOTE]
>Následující analýzy se vztahují na všechny typy prostředků. Využívají telemetrii, kterou Security Center poskytuje klepnutím do interních kanálů Azure.

## Síťová vrstva Azure<a name="network-layer"></a>

Analýza Security Centerch síťových vrstev vychází z ukázkových [dat IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), což jsou hlavičky paketů shromážděné směrovači Azure Core. Na základě tohoto datového kanálu Security Center modely strojového učení identifikují a označí škodlivé činnosti provozu. K rozšíření IP adres Security Center využívá databázi Microsoft Threat Intelligence.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Podezřelá odchozí síťová aktivita protokolu RDP**|Ukázková Analýza provozu v síti zjistila komunikaci neobvyklé odchozího protokol RDP (Remote Desktop Protocol) (RDP), která pochází z prostředku ve vašem nasazení. Tato aktivita se pro toto prostředí považuje za neobvyklou. Může to znamenat, že došlo k ohrožení zabezpečení prostředku a teď se používá k útoku hrubou silou na externí koncový bod RDP. Upozorňujeme, že tento typ aktivity může způsobit, že vaše IP adresa bude označená jako škodlivá externími entitami.|
|**Podezřelá odchozí síťová aktivita protokolu RDP do více cílů**|Byla zjištěna analýza síťového provozu neobvyklé odchozí komunikace protokolu RDP, která pochází z prostředku v nasazení do více cílů. Tato aktivita se pro toto prostředí považuje za neobvyklou. Může to znamenat, že došlo k ohrožení zabezpečení prostředku a teď se používá k útoku hrubou silou na externí koncové body protokolu RDP. Upozorňujeme, že tento typ aktivity může způsobit, že vaše IP adresa bude označená jako škodlivá externími entitami.|
|**Podezřelá odchozí síťová aktivita protokolu SSH**|Ukázka analýzy síťových přenosů zjistila komunikaci neobvyklé odchozího Secure Shell (SSH), která pochází z prostředku ve vašem nasazení. Tato aktivita se pro toto prostředí považuje za neobvyklou. Může to znamenat, že došlo k ohrožení zabezpečení prostředku a teď se používá k útoku hrubou silou na externí koncový bod SSH. Upozorňujeme, že tento typ aktivity může způsobit, že vaše IP adresa bude označená jako škodlivá externími entitami.|
|**Podezřelá odchozí síťová aktivita protokolu SSH do více cílů**|Analýza síťového provozu zjistila neobvyklé odchozí komunikaci SSH, která pochází z prostředku v nasazení do více cílů. Tato aktivita se pro toto prostředí považuje za neobvyklou. Může to znamenat, že došlo k ohrožení zabezpečení prostředku a teď se používá k útoku hrubou silou na externí koncové body SSH. Upozorňujeme, že tento typ aktivity může způsobit, že vaše IP adresa bude označená jako škodlivá externími entitami.|
|**Podezřelá příchozí síťová aktivita protokolu SSH z více zdrojů**|Analýza analýzy síťových přenosů zjistila neobvyklé příchozí komunikaci SSH z více zdrojů do prostředku ve vašem nasazení. Různé jedinečné IP adresy, které se připojují k vašemu prostředku, se považují za neobvyklé pro toto prostředí. Tato aktivita může znamenat pokus o útok hrubou silou na vaše rozhraní SSH z více hostitelů (botnetu).|
|**Podezřelá příchozí síťová aktivita protokolu SSH**|Při analýze ukázkových síťových přenosů se zjistila příchozí komunikace SSH neobvyklé prostředku ve vašem nasazení. Poměrně velký počet příchozích připojení k vašemu prostředku se pro toto prostředí považuje za neobvyklé. Tato aktivita může znamenat pokus o útok hrubou silou na vaše rozhraní SSH.
|**Podezřelá příchozí síťová aktivita protokolu RDP z více zdrojů**|Při analýze ukázkových síťových přenosů se zjistilo, že příchozí komunikace protokolu RDP z více zdrojů do prostředku v nasazení byla neobvyklé. Různé jedinečné IP adresy, které se připojují k vašemu prostředku, se považují za neobvyklé pro toto prostředí. Tato aktivita může naznačovat pokus o útok hrubou silou na vaše rozhraní RDP z více hostitelů (botnetu).|
|**Podezřelá příchozí síťová aktivita protokolu RDP**|Při analýze ukázkových síťových přenosů bylo zjištěno, že příchozí komunikace protokolu RDP neobvyklé do prostředku v nasazení. Poměrně velký počet příchozích připojení k vašemu prostředku se pro toto prostředí považuje za neobvyklé. Tato aktivita může znamenat pokus o útok hrubou silou na vaše rozhraní SSH.|
|**Byla zjištěna síťová komunikace se škodlivou adresou.**|Analýza analýzy síťových přenosů zjistila komunikaci pocházející z prostředku ve vašem nasazení s možným příkazem a řízením (C & C) Server. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|

Informace o tom, jak Security Center můžou použít signály související se sítí pro použití ochrany před hrozbami, najdete [v tématu heuristické detekce služby DNS v Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).

>[!NOTE]
>Výstrahy detekce hrozby síťové vrstvy Azure v Azure Security Center se generují jenom pro virtuální počítače, kterým se přiřadila stejná IP adresa pro celou hodinu, během které se nastavila podezřelá komunikace. To platí pro virtuální počítače i pro virtuální počítače, které jsou vytvořené v rámci předplatného zákazníka, jako součást spravované služby (např. AKS, datacihly).

## Vrstva správy Azure (Azure Resource Manager) (Preview)<a name ="management-layer"></a>

>[!NOTE]
>Vrstva Security Center Protection založená na Azure Resource Manager je aktuálně ve verzi Preview.

Security Center nabízí další vrstvu ochrany pomocí Azure Resource Managerch událostí, které se považují za řídicí plochu pro Azure. Díky analýze záznamů Azure Resource Manager Security Center detekuje neobvyklé nebo potenciálně škodlivé operace v prostředí předplatného Azure.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Běh sady nástrojů pro mikroshluky**|Ve vašem prostředí se zjistila známá spuštění rekognoskace sady nástrojů cloudového prostředí. Nástroj mikroshlukování lze použít útočníkem (nebo testerem průniku) k mapování prostředků předplatných, identifikaci nezabezpečených konfigurací a nevracení důvěrných informací. [](https://github.com/NetSPI/MicroBurst)|
|**Spuštění sady nástrojů Azurite**|Ve vašem prostředí se zjistila známá spuštění rekognoskace sady nástrojů cloudového prostředí. Nástroj [Azurite](https://github.com/mwrlabs/Azurite) může použít útočník (nebo Tester pro průnik) k mapování prostředků předplatných a k identifikaci nezabezpečených konfigurací.|
|**Podezřelá relace správy s použitím neaktivního účtu**|Analýza protokolů aktivit předplatného zjistila podezřelé chování. Objekt zabezpečení, který se nepoužívá po dlouhou dobu, nyní provádí akce, které mohou zabezpečit trvalost pro útočníka.|
|**Podezřelá relace správy s využitím PowerShellu**|Analýza protokolů aktivit předplatného zjistila podezřelé chování. Objekt zabezpečení, který pravidelně nepoužívá PowerShell ke správě prostředí předplatného, teď používá PowerShell a provádí akce, které můžou zabezpečit trvalost pro útočníka.|
|**Použití pokročilých technik trvalosti Azure**|Analýza protokolů aktivit předplatného zjistila podezřelé chování. Přizpůsobené role byly předány entitám identity legitimized. To může vést k tomu, že útočník získá průniku v prostředí Azure Customer.|
|**Aktivita z málo časté země**|Došlo k aktivitě z umístění, které nedávno nebo dříve navštívil žádný uživatel v organizaci.<br/>Tato detekce ohledem na minulou umístění aktivit k určení nové a úlohy s řídkým umístění. Modul detekce anomálií ukládá informace o předchozí umístění, které používají uživatelé v organizaci. 
|**Aktivita z anonymních IP adres**|Byla zjištěna aktivita uživatelů z IP adresy, která byla identifikována jako IP adresa anonymního proxy serveru. <br/>Tyto proxy servery používají lidé, kteří chtějí skrýt IP adresu svého zařízení a můžou se používat pro škodlivý záměr. Tato detekce používá algoritmus strojového učení, který zkracuje falešně pozitivní informace, například IP adresy nesprávného označení, které uživatelé v organizaci používají často.|
|**Zjistila se nemožná cesta.**|Nastaly dvě uživatelské aktivity (v jedné nebo několika relacích), které pocházejí z geograficky vzdálených umístění. K tomu dojde v časovém období kratším, než je čas, kdy by uživatel musel cestovat z prvního umístění do druhé. To znamená, že stejný přihlašovací údaj používá jiný uživatel. <br/>Tato detekce používá algoritmus strojového učení, který ignoruje zjevné falešně pozitivní informace, které přispívají k nemožným podmínkám cestování, jako jsou sítě VPN a lokality pravidelně používané ostatními uživateli v organizaci. Tato detekce má počáteční období učení sedmi dnů, během kterého se učí vzor aktivity nového uživatele.|

>[!NOTE]
> Některé z předchozích analýz jsou napájené z Microsoft Cloud App Security. Pokud chcete tyto analýzy využít, musíte aktivovat licenci Cloud App Security. Pokud máte licenci Cloud App Security, jsou tyto výstrahy ve výchozím nastavení povolené. Chcete-li je zakázat:
>
> 1. V okně **Security Center** vyberte **zásady zabezpečení**. U předplatného, které chcete změnit, vyberte **Upravit nastavení**.
> 2. Vyberte **detekci hrozeb**.
> 3. V části **Povolit integrace**zrušte zaškrtnutí políčka **Povolit Microsoft Cloud App Security k přístupu k datům**a vyberte **Uložit**.

>[!NOTE]
>Security Center ukládá zákaznická data týkající se zabezpečení ve stejné geografické podobě jako její prostředek. Pokud společnost Microsoft ještě nebyla nasazena Security Center v geografickém prostředku, uloží data do USA. Pokud je povolená Cloud App Security, ukládají se tyto informace v souladu s pravidly geografického umístění Cloud App Security. Další informace najdete v tématu [úložiště dat pro jiné než regionální služby](https://azuredatacentermap.azurewebsites.net/).
