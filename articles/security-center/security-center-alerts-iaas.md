---
title: Detekce hrozeb pro virtuální počítače a servery v Azure Security Center | Microsoft Docs
description: V tomto tématu se zobrazí výstrahy virtuálního počítače a serveru, které jsou dostupné v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 0070ab94196dfe752e048e97dd40a8f69cf98373
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202606"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Detekce hrozeb pro virtuální počítače a servery v Azure Security Center

V tomto tématu jsou uvedeny různé typy metod detekce a upozornění, která jsou k dispozici pro virtuální počítače a servery s následujícími operačními systémy. Seznam podporovaných verzí najdete v tématu [platformy a funkce podporované nástrojem Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Azure Security Center se integruje se službami Azure za účelem monitorování a ochrany počítačů se systémem Windows. Security Center prezentuje výstrahy a návrhy oprav ze všech těchto služeb ve snadno použitelném formátu.

### ATP Windows serveru Defender<a nanme="windows-atp"></a>

Security Center rozšiřuje svou cloudovou platformu ochrany díky integraci s pokročilou ochranou před internetovými útoky v programu Windows Server Defender (ATP). Poskytuje komplexní možnosti detekce a odezvy koncových bodů (EDR).

> [!NOTE]
> Senzor ATP v programu Windows Server Defender je automaticky povolen na serverech se systémem Windows, které používají Security Center.

Když ochrana ATP v programu Windows Server Defender detekuje hrozbu, aktivuje výstrahu. Výstraha se zobrazí na řídicím panelu Security Center. Z řídicího panelu se můžete překlopit do konzoly ATP v programu Windows Defender a provést podrobné šetření, které odhalí rozsah útoku. Další informace o ochraně ATP v programu Windows Server Defender najdete v tématu připojení [serverů ke službě ochrany ATP v programu Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Analýza výpisu stavu systému<a nanme="windows-dump"></a>

Pokud dojde k chybě softwaru, ve výpisu stavu systému se zaznamená část paměti v době selhání.

Selhání mohlo být způsobeno malwarem nebo obsahem malwaru. Aby nedocházelo k detekci v produktech zabezpečení, různé formy malwaru používají útok bez souborů, což zabraňuje psaní na disk nebo šifrování softwarových součástí zapsaných na disk. Tento typ útoku se obtížně detekuje pomocí tradičních přístupů založených na disku.

Pomocí analýzy paměti ale můžete zjistit tento druh útoku. Díky analýze paměti ve výpisu stavu systému může Security Center detekovat techniky, které útok používá. Útok se například může pokusit zneužít ohrožení zabezpečení softwaru, přistupovat k důvěrným datům a nenápadně uchovat v napadeném počítači. Security Center to funguje s minimálním dopadem na výkon hostitelů.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Bylo zjištěno vkládání kódu**|Injektáž kódu představuje vložení spustitelných modulů do spuštěných procesů nebo vláken. Tento postup využije malware pro přístup k datům, přičemž se ale úspěšně skrývá, aby nedocházelo k jejich nalezení a odebrání. <br/>Tato výstraha znamená, že ve výpisu je injektovaný modul. Aby bylo možné odlišit škodlivé a neškodlivé vložené moduly, Security Center kontroluje, zda vložený modul odpovídá profilu podezřelého chování.|
|**Zjištěn podezřelý segment kódu**|Označuje, že segment kódu byl přidělen pomocí nestandardních metod, jako je reflektování vkládání a zpracování prázdných procesů. Výstraha poskytuje další charakteristiky segmentu kódu, který byl zpracován, aby poskytoval kontext pro schopnosti a chování oznámeného segmentu kódu.|
|**Zjištěné skrytý spustitelný kód**|Skrytý spustitelný kód je datová část, která se spouští potom, co malware zneužije chybu zabezpečení softwaru.<br/>Tato výstraha znamená, že při analýze výpisu stavu systému byl nalezen spustitelný kód, který vykazuje chování obvykle prováděné škodlivými datovými částmi. I když neškodlivý software může toto chování také provést, není typický pro běžné postupy při vývoji softwaru.|

### Detekce útoků typu soubor<a nanme="windows-fileless"></a>

Časté útoky souborů, které cílí na vaše koncové body, jsou běžné. Aby nedocházelo k detekci, útoky bez souborů vkládají do paměti škodlivá datová data. V paměti napadených procesů a provádění široké škály škodlivých aktivit může dojít k trvalému uložení datových částí.

Díky detekci útoků bez souborů můžou automatizované techniky forenzní paměti identifikovat sady nástrojů, techniky a chování pro útoky bez souborů. Toto řešení pravidelně kontroluje počítač za běhu a extrahuje přehledy přímo z paměti důležitých procesů, které jsou kritické pro zabezpečení.

Najde důkazy o zneužití, vkládání kódu a spuštění škodlivých datových částí. Detekce útoků bez souborů generuje podrobné výstrahy zabezpečení, které urychlují jejich třídění, korelace a dobu odezvy za provozu. Tento přístup doplňuje řešení EDR založené na událostech a zajišťuje větší pokrytí detekce.

> [!NOTE]
> Výstrahy Windows můžete simulovat stažením [Azure Security Center PlayBook: Výstrahy](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)zabezpečení.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Zjištěna technika útoku s neoprávněnými soubory**|Paměť zadaného procesu obsahuje sadu nástrojů pro útoky s neoprávněnými soubory: Meterpreter. Sady nástrojů pro útoky bez souborů většinou nemají přítomnost v systému souborů, což ztěžuje jejich detekci pomocí tradičního antivirového softwaru.|

### <a name="further-reading"></a>Další čtení

Příklady a další informace o detekci Security Center najdete v tématech:

* [Jak Azure Security Center automatizuje detekci internetoví útoků](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Jak Azure Security Center detekuje chyby zabezpečení pomocí nástrojů pro správu](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

Security Center shromažďuje záznamy auditu z počítačů se systémem Linux pomocí **auditu**, jeden z nejběžnějších rozhraní pro auditování Linux. auditované života v jádru hlavní. 

### Integrace se systémem Linux audited Alerts a Microsoft Monitoring Agent (MMA)<a name="linux-auditd"></a>

Auditované systémy se skládají z podsystému na úrovni jádra, který zodpovídá za monitorování volání systému. Filtruje je podle zadané sady pravidel a zapisuje zprávy pro ně do soketu. Security Center integruje funkce z auditovaného balíčku v rámci Microsoft Monitoring Agent (MMA). Tato integrace umožňuje shromažďování auditovaných událostí ve všech podporovaných distribucích systému Linux bez jakýchkoli požadavků.  

auditované záznamy jsou shromažďovány, obohaceny a agregovány do událostí pomocí agenta MMA pro Linux. Security Center průběžně přidává nové analýzy, které používají signály Linux k detekci škodlivého chování na cloudových a místních počítačích se systémem Linux. Podobně jako funkce Windows tyto analýzy přesahují podezřelé procesy, pokusy o přihlášení k podezřelých, načítání modulů jádra a další činnosti. Tyto aktivity mohou znamenat, že počítač je buď napadený, nebo byl napadený.  

Níže jsou uvedeny některé příklady analýz, které jsou rozloženy v různých fázích životního cyklu útoku.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Proces, při kterém se zobrazil přístup k souboru klíčů autorizovaným SSH, neobvyklým způsobem**|V metodě, která se podobá známým postupům malwaru, je přístup k souboru klíčů autorizovaných pro SSH. Tento přístup může znamenat, že se útočník pokouší získat trvalý přístup k počítači.|
|**Zjištěn pokus o trvalosti**|Analýza dat hostitele zjistila, že byl nainstalován spouštěcí skript pro režim jednoho uživatele. <br/>Vzhledem k tomu, že je zřídka, že by se v tomto režimu musely spustit jakýkoli legitimní proces, může to znamenat, že útočník přidal škodlivý proces do každé úrovně spuštění, aby zajistil trvalost.|
|**Zjištěna manipulace s naplánovanými úlohami**|Analýza dat hostitele zjistila možnou manipulaci s naplánovanými úlohami. Útočníci často přidávají naplánované úlohy do počítačů, které byly ohroženy, aby získali trvalost.|
|**Podezřelá Změna časového razítka souboru**|Analýza dat hostitele zjistila podezřelou změnu časového razítka. Útočníci často kopírují časová razítka z existujících, legitimních souborů do nových nástrojů, aby nedocházelo k detekci těchto nově vynechaných souborů.|
|**Do skupiny sudoers se přidal nový uživatel.**|Analýza dat hostitele zjistila, že byl uživatel přidán do skupiny sudoers, která umožňuje jeho členům spouštět příkazy s vysokou úrovní oprávnění.|
|**Pravděpodobná zneužití ohrožení zabezpečení DynoRoot v klientovi DHCP**|Analýza dat hostitele zjistila spuštění neobvyklého příkazu s nadřazeným procesem skriptu dhclient.|
|**Zjistil se podezřelý modul jádra.**|Analýza dat hostitele zjistila, že se soubor sdíleného objektu načítá jako modul jádra. Může se jednat o legitimní aktivitu nebo indikaci, že jeden z vašich počítačů byl ohrožen.|
|**Zjistil se proces přidružený k dolování v digitální měně.**|Analýza dat hostitele zjistila spuštění procesu, který je normálně přidružen k dolování v digitální měně.|
|**Potenciální přeposílání portů na externí IP adresu**|Analýza dat hostitele zjistila zahájení předávání portů externí IP adrese.|

> [!NOTE]
> Výstrahy Windows můžete simulovat stažením [Azure Security Center PlayBook: Detekce](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)Linux.


Další informace naleznete v tématu:  

* [Využití Azure Security Center k detekci napadení napadených počítačů se systémem Linux](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure Security Center dokáže detekovat nově vznikající chyby zabezpečení v systému Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 