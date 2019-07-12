---
title: Analýzy hrozeb, zjišťování virtuálních počítačů a serverů v Azure Security Center | Dokumentace Microsoftu
description: Toto téma představuje virtuální počítač a server výstrahy k dispozici ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 5487b4f49f5dbf7b968cd45d40555c69b54c329a
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571579"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Detekce hrozeb pro virtuální počítače a servery ve službě Azure Security Center

Toto téma představuje různé druhy metod detekce a výstrahy, které jsou k dispozici pro virtuální počítače a servery s následující operační systémy. Seznam podporovaných verzí najdete v tématu [platformy a funkcí podporovaných službou Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Security Center umožňuje integrací se službami Azure a monitorovat a chránit vaše počítače se systémem Windows.  Security Center nabízí výstrahy a nápravné návrhy ze všech těchto služeb ve formátu snadným ovládáním.

### Microsoft Server Defender ATP <a nanme="windows-atp"></a>

Azure Security Center rozšiřuje své cloudové platformy ochranu úloh díky integraci s Windows Defender Advanced Threat Protection (ATP). To nabízí rozsáhlé možnosti koncový bod zjišťování a odpovědi (EDR).

> [!NOTE]
> Senzor ochrany ATP v programu Defender programu systému Windows Server se automaticky zapnulo Windows serverů, které jsou zapojený do služby Azure Security Center.

Když Server programu Windows Defender ATP zjistí hrozbu, aktivuje výstrahu. Upozornění se zobrazí na řídicím panelu Security Center. Na řídicím panelu můžete vytvořit kontingenční ke konzole ochrany ATP v programu Windows Defender provádět podrobné zkoumání odhalit další obor útoku. Další informace o serveru programu Windows Defender ATP najdete v tématu [servery připojit ke službě ochrana ATP v programu Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Při analýze výpisu stavu systému <a nanme="windows-dump"></a>

Pokud dojde k chybě softwaru, ve výpisu stavu systému se zaznamená část paměti v době selhání.

Selhání může být způsobena malwaru nebo obsahují malware. Aby se zabránilo zjištění produkty zabezpečení, různé formy malwaru použít fileless útoku, který předchází zápisu na disku nebo šifrování softwarové komponenty na disk. Tento typ útoku je obtížné rozpoznat pomocí tradičních detekce založené na disku.

Tento druh útoku lze ale zjistit pomocí analýzy paměti. Díky analýze paměti ve výpisu, může Security Center zjišťovat techniky, které útoku používá zranitelností softwaru, přístup k důvěrným datům a nenápadně přetrvávají v napadeném počítači. To se provádí Security Center s back-endem minimální dopad na hostitelích výkon.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Zjištěna injektáž kódu**|Injektáž kódu představuje vložení spustitelných modulů do spuštěných procesů nebo vláken. Tato technika je používána malwarem pro přístup k datům, když úspěšně skrytí, aby se najít a odebrat. <br/>Tato výstraha znamená, že ve výpisu je injektovaný modul. K rozlišení mezi- škodlivé injektované moduly, Security Center kontroluje, jestli injektovaný modul odpovídá profilu podezřelého chování.|
|**Podezřelý segment kódu zjištění**|Označuje, že segment kódu byla přidělena nestandardní metody, jako je například reflektivní injektáží a hloubením procesů. Výstraha poskytuje další charakteristiky segmentu kódu, které byly zpracovány poskytnout kontext pro funkce a chování segmentu ohlášené kódu.|
|**Zjištěn skrytý spustitelný kód**|Skrytý spustitelný kód je datová část, která se spouští potom, co malware zneužije chybu zabezpečení softwaru.<br/>Tato výstraha indikuje, že při analýze výpisu stavu systému byl nalezen spustitelný kód, který vykazuje chování škodlivý software. I když software bez zlých mohou také provádět toto chování, není typický pro normální praktik vývoje.|

### Detekce útoku fileless <a nanme="windows-fileless"></a>

V Azure vidíme pravidelně fileless útoky cílení na koncové body našich zákazníků.

Aby se zabránilo detekce, vložit fileless útoky škodlivý software do paměti. Útočník datových částí přetrvávají v paměti ohrožených procesy a provádění široké škály škodlivých aktivit.

S detekcí fileless útoku identifikaci forenzní metody automatizované paměti sady nástrojů fileless útoku, techniky a chování. Toto řešení pravidelně kontroluje váš počítač za běhu a extrahuje přehledy přímo z paměti procesy kritické pro zabezpečení.

Najde doklad o využívání, vkládání kódu a provádění škodlivý software. Fileless detekce útoku generuje výstrahy podrobné zabezpečení ke zrychlení třídění výstrah, korelace a podřízené odezvu. Tento přístup doplňuje řešení založeného na událostech EDR poskytuje větší rozsahu zjišťování.

> [!NOTE]
> Upozornění Windows můžete simulovat stažením [Azure Security Center Playbook](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef): Výstrahy zabezpečení a postupujte podle pokynů na zadaný

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Zjištěna techniku fileless útoku**|Paměť procesu níže uvedených obsahuje sadu nástrojů fileless útoku: Meterpreter. Sady nástrojů fileless útoku obvykle nemají přítomnost v systému souborů, ztěžuje detekce pomocí tradiční antivirový program.|

### <a name="further-reading"></a>Další čtení

Příklady a další informace o zjišťování Security Center:

* [Jak Azure Security Center automatizuje detekce kybernetických útoků](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Jak Azure Security Center detekuje ohrožení zabezpečení pomocí nástroje pro správu](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

Security Center shromažďuje záznamy z počítače s Linuxem pomocí auditu **démona auditd**, jeden z nejběžnějších Linux auditování architektury. démona auditd nabízí výhodu v podobě byla po dlouhou dobu a žijící v hlavní linii jádra. 

### Linux démona auditd výstrahy a integrace služby Microsoft Monitoring Agent (MMA) <a name="linux-auditd"></a>

Systém démona auditd tvoří podsystému úrovni jádra, který je zodpovědný za monitorování systémových volání, filtrování podle dané sady pravidel a psaní zpráv pro ně k soketu. Security Center umožňuje integrací funkce z démona auditd balíčku v rámci Microsoft Monitoring Agent (MMA). Tato integrace umožňuje shromažďování událostí auditu ve všech podporovaných Linuxových distribucích bez všechny požadované součásti.  

záznamy auditu jsou shromažďovány, rozšiřují a agregují do události pomocí agenta MMA pro Linux. Security Center soustavně pracujeme na přidání nového analytics, že využívají Linux signál k detekci škodlivého chování v cloudu a místních počítačů s Linuxem. Podobně jako funkce Windows tyto analýzy pracovat nad více podezřelých procesů, pokusy o přihlášení. podezřelých, načtení modulu jádra a jiné činnosti, které označují počítače je buď terčem útoku nebo po porušení zabezpečení.  

Níže je několik příkladů analýzy, které ukazují, jak budeme pracovat nad více různých fázích životního cyklu útoku.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Proces viděli neobvyklým způsobem přístupu k souboru autorizovaných klíčů SSH**|Soubor autorizovaných klíčů SSH někdo zobrazil během metody podobné známé malwarové kampaně. Tento přístup může znamenat, že se útočník pokouší získat trvalý přístup k počítači|
|**Pokus o zjištěném trvalosti**|Analýza dat hostitele zjistil nainstaloval spouštěcí skript pro jednouživatelský režim. <br/>Protože je vzácné, že jakýkoli legitimní proces by bylo zapotřebí k provedení v tomto režimu, může to znamenat, že útočník přidal do všech úrovních spustit zaručit trvalost škodlivý proces.|
|**Manipulace s naplánovaných úloh, zjištěn**|Analýza dat hostitele zjistil možné manipulaci s naplánovaných úloh. Útočníci často přidat do počítače, které jste k ohrožení k získání trvalého naplánované úlohy.|
|**Změna časového razítka podezřelý soubor**|Analýza dat hostitele zjistila podezřelá časové razítko úpravy. Útočníci často zkopírujte časové razítko z existujících legitimních souborů do nové nástroje, aby se zabránilo detekce tyto nově přetažené soubory|
|**Přidání nového uživatele do skupiny sudoers**|Analýza dat hostitele zjistil, že uživatel přidal do skupiny sudoers, která svým členům umožňuje spouštět příkazy s vysokou úrovní oprávnění.|
|**Pravděpodobně zneužít ohrožení zabezpečení DynoRoot v klientovi dhcp**|Analýza dat hostitele zjistila provádění příkazu k neobvyklé pomocí nadřazeného procesu dhclient skriptu.|
|**Zjištěno podezřelé jádra modul**|Analýza dat hostitele zjistila sdíleného objektu souboru načítání jako modulu jádra. Může to být legitimní aktivity nebo došlo k napadení jako ukazatel toho, že jedna z vašich počítačů.|
|**Proces přidružený digitální měny dolování zjistil**|Analýza dat hostitele zjistila spuštění procesu, obvykle spojené s dolování digitální měny|
|**Potenciální přesměrování portu na externí IP adresu**|Analýza dat hostitele zjistila zahájení port přesměrování na externí IP adresu.|

> [!NOTE]
> Upozornění Windows můžete simulovat stažením [Azure Security Center Playbook: Výstrahy zabezpečení](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) a postupujte podle pokynů na zadaný.


Další informace najdete v těchto článcích:  

* [Využijte Azure Security Center, který zjistí ohrožení zabezpečení Linux stroje útoku](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure Security Center můžete detekovat vznikající ohrožení zabezpečení v systému Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 