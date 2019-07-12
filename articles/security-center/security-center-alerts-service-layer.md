---
title: Pro vrstvu služby Azure ve službě Azure Security Center detekce hrozeb | Dokumentace Microsoftu
description: Toto téma představuje službu Azure vrstvu výstrahy k dispozici ve službě Azure Security Center.
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
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 8c1733877834f82d9ee2524cf8bf54f532e7d9c4
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571722"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Detekce hrozeb pro vrstvu služby Azure ve službě Azure Security Center

Toto téma představuje výstrahy Security Center k dispozici při monitorování následujícími vrstvami služeb Azure.

* [Azure síťové vrstvě](#network-layer)
* [Vrstva sady Azure Management (Azure Resource Manager) (Preview)](#management-layer)

>[!NOTE]
>Na základě telemetrických údajů, které využívá připojení z proniknutí do interní informačních kanálech Azure Security Center, analytics, níže uvedené platí pro všechny typy prostředků.

## Azure síťové vrstvě<a name="network-layer"></a>

Security Center síťové vrstvy analytics jsou založené na vzorku [IPFIX data](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), které jsou shromážděné směrovačů Azure core hlavičky paketů. Podle tento datový kanál, modely machine learning Security Center identifikovat a označit aktivity škodlivý provoz. Rozšiřuje IP adresy, Security Center bude využívat databází hrozeb společnosti Microsoft.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Podezřelá odchozí síťová aktivita protokolu RDP**|Analýza provozu vzorky sítě zjistila neobvyklou odchozí komunikaci protokolu RDP (Remote Desktop) pocházející z prostředků ve vašem nasazení. Tato aktivita se považuje za neobvyklé pro toto prostředí a může znamenat, že došlo k napadení vašich prostředků a se teď používá k útoku hrubou silou externí koncový bod protokolu RDP. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|
|**Podezřelá odchozí síťová aktivita protokolu RDP do více cílů**|Analýza provozu vzorky sítě zjistila neobvyklou odchozí komunikaci protokolu RDP (Remote Desktop) pocházející z prostředků ve vašem nasazení do více cílů. Tato aktivita se považuje za neobvyklé pro toto prostředí a může naznačovat, že došlo k napadení vašeho prostředku, který se teď používá k útoku hrubou silou externí koncové body protokolu RDP. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|
|**Podezřelá odchozí síťová aktivita protokolu SSH**|Analýza provozu vzorky sítě zjistila neobvyklou odchozí komunikaci Secure Shell (SSH) pocházející z prostředků ve vašem nasazení. Tato aktivita se považuje za neobvyklé pro toto prostředí a může naznačovat, že došlo k napadení vašeho prostředku, který se teď používá k útoku hrubou silou externí koncový bod SSH. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|
|**Podezřelá odchozí síťová aktivita protokolu SSH do více cílů**|Analýza provozu vzorky sítě zjistila neobvyklou odchozí komunikaci Secure Shell (SSH) pocházející z prostředků ve vašem nasazení do více cílů. Tato aktivita se považuje za neobvyklé pro toto prostředí a může naznačovat, že došlo k napadení vašeho prostředku, který se teď používá k útoku hrubou silou externí koncové body SSH. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|
|**Podezřelá příchozí síťová aktivita protokolu SSH z více zdrojů**|Analýza provozu vzorky sítě zjistila neobvyklou příchozí SSH komunikace z různých zdrojů na prostředek ve vašem nasazení. Připojení k prostředku různých jedinečné IP adresy se považuje za neobvyklé pro toto prostředí. Tato aktivita může naznačovat pokus o útok hrubou silou rozhraní SSH z více hostitelů (Botnet).|
|**Podezřelá příchozí síťová aktivita protokolu SSH**|Analýza provozu vzorky sítě zjistila neobvyklou příchozí komunikaci protokolu SSH na prostředek ve vašem nasazení. Relativně velký počet příchozích připojení na váš prostředek se považuje za neobvyklé pro toto prostředí. Tato aktivita může naznačovat pokus o útok hrubou silou rozhraní SSH.
|**Podezřelá příchozí síťová aktivita protokolu RDP z více zdrojů**|Analýza provozu vzorky sítě zjistila neobvyklou příchozí komunikace RDP z více zdrojů k prostředku ve vašem nasazení. Připojení k prostředku různých jedinečné IP adresy se považuje za neobvyklé pro toto prostředí. Tato aktivita může naznačovat pokus o útok hrubou silou rozhraní protokolu RDP z více hostitelů (Botnet).|
|**Podezřelá příchozí síťová aktivita protokolu RDP**|Analýza provozu vzorky sítě zjistila neobvyklou příchozí komunikaci protokolu RDP na prostředek ve vašem nasazení. Relativně velký počet příchozích připojení na váš prostředek se považuje za neobvyklé pro toto prostředí. Tato aktivita může naznačovat pokus o útok hrubou silou rozhraní SSH.|

Abyste pochopili, jak Security Center může pomocí sítě související s signály, platí ochrana před internetovými útoky, naleznete v tématu [DNS Heuristická detekce ve službě Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).
## Vrstva sady Azure Management (Azure Resource Manager) (Preview)<a name ="management-layer"></a>

>[!NOTE]
>Security Center vrstvy ochrany založené na Azure Resource Manageru je aktuálně ve verzi preview.

Security Center nabízí další vrstvu ochrany s využitím Azure Resource Manageru události, která je považována za rovinu řízení Azure. Díky analýze záznamy Azure Resource Manageru, Security Center detekuje neobvyklé nebo potenciálně škodlivých operací v prostředí předplatného Azure.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Sada nástrojů microBurst spuštění**|Sada nástrojů rekognoskace známé cloudovém prostředí pro spuštění se zjistil ve vašem prostředí. Nástroj "MicroBurst" (Zobrazit https://github.com/NetSPI/MicroBurst) mohou využívat útočník (nebo testování průniku) ke zmapování prostředky předplatného, identifikovat nezabezpečené konfigurace a úniku důvěrným informacím.|
|**Sada nástrojů azurite spuštění**|Sada nástrojů rekognoskace známé cloudovém prostředí pro spuštění se zjistil ve vašem prostředí. Nástroj "Azurite" (Zobrazit https://github.com/mwrlabs/Azurite) mohou využívat útočník (nebo testování průniku) k prostředkům předplatného namapování a identifikování nezabezpečené konfigurace.|
|**Relace podezřelé správy pomocí neaktivní účty**|Analýza protokolů aktivit předplatného zjistila podezřelé chování. Instanční objekt, který se používá pro dlouhou dobu, je nyní provádí akce, které se dají zabezpečit trvalosti pro útočníka.|
|**Relace podezřelé správy pomocí Powershellu**|Analýza protokolů aktivit předplatného zjistila podezřelé chování. Instanční objekt, který nepoužívá pravidelně Powershellu ke správě předplatného prostředí, je nyní pomocí prostředí PowerShell a provádí akce, které se dají zabezpečit trvalosti pro útočníka.|
|**Použití metod pokročilé trvalosti pro Azure**|Analýza protokolů aktivit předplatného zjistila podezřelé chování. Vlastní role mají legitimized identity entity. To může způsobit útočník získat průniku do sítě v prostředí Azure zákazníka.|
|**Aktivita z málo časté země**|Aktivita z umístění, ke kterému nedávno nebo nikdy nenavštívil žádný uživatel v organizaci došlo k chybě.<br/>Tato detekce ohledem na minulou umístění aktivit k určení nové a úlohy s řídkým umístění. Modul detekce anomálií ukládá informace o předchozí umístění, které používají uživatelé v organizaci. 
|**Aktivita z anonymních IP adres**|Aktivita uživatele z IP adresy, která se identifikovala jako anonymní proxy IP adresa byla zjištěna. <br/>Tato proxy používají lidé, kteří mají skrýt IP adresu svého zařízení a může sloužit ke škodlivým činnostem. Toto zjišťování využívá strojové učení algoritmu, který snižuje "falešně pozitivní výsledky", jako jsou správně označené IP adresy, které se běžně používají uživatelé v organizaci.|
|**Neuskutečnitelná cesta zjistil**|Dvě aktivity uživatele (je jeden nebo více relací) došlo, pocházející z geograficky vzdáleném umístění v časovém období kratší než čas ho by trvalo uživateli cestují z první umístění do druhého. To znamená, že jiný uživatel používá stejné přihlašovací údaje. <br/>Toto zjišťování využívá strojové učení algoritmu, který ignoruje zřejmé "počet falešně pozitivních výsledků" přispívání do podmínky neuskutečnitelná cesta, například sítě VPN a umístění pravidelně používat další uživatelé v organizaci. Detekce má období učení sedm dní, během kterých se naučí vzor aktivit nového uživatele.|

>[!NOTE]
> Některé z výše uvedených analýzy s využitím pomocí Cloud App Security (MCAS). Chcete-li využívat tyto analýzy, je potřeba licence k aktivované MCAS. Pokud máte licenci MCAS tyto výstrahy jsou povolené ve výchozím nastavení. Chcete-li je zakázat:
>
> 1. V okně Security Center vyberte **zásady zabezpečení**. Předplatné, které chcete změnit, klikněte na tlačítko **upravit nastavení**.
> 2. Klikněte na tlačítko **detekce hrozeb**.
> 3. V části **povolovat integrace**, zrušte zaškrtnutí políčka **povolit Microsoft Cloud App Security přístup k data**a klikněte na tlačítko **Uložit**.

>[!NOTE]
>Azure Security Center ukládá data zákazníků týkající se zabezpečení v rámci stejné Geografie jako jeho prostředků. Pokud Microsoft nebyla ještě nasazena Azure Security Center v geograficky prostředku, uloží ve Spojených státech amerických data. Pokud je povoleno Microsoft Cloud App Security (MCAS), tyto informace jsou uloženy v souladu s pravidly geografické umístění MCAS. Zobrazit [úložiště dat pro – neregionální služby více informace](http://azuredatacentermap.azurewebsites.net/).
