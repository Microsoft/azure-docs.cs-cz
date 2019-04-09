---
title: Spravovat infrastrukturu úložiště pro službu Azure Stack | Dokumentace Microsoftu
description: Spravovat infrastrukturu úložiště pro službu Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: ''
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 03/11/2019
ms.reviewer: jiahan
ms.openlocfilehash: 416d75b254d0fbe14a0b39e5ae77d09a48e548f6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271284"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>Spravovat infrastrukturu úložiště pro službu Azure Stack

*Týká se Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek popisuje kondice a provozního stavu prostředků infrastruktury úložiště služby Azure Stack. Tyto prostředky zahrnují úložné jednotky a svazky. Informace v tomto tématu mohou být neocenitelnou pomocí při pokusu o řešení potíží s různými problémy, jako jednotku nelze přidat do fondu.

## <a name="understand-drives-and-volumes"></a>Vysvětlení jednotky a svazky

### <a name="drives"></a>Jednotky

Používá technologii Windows serverový software, Azure Stack definuje možnosti úložiště s kombinací úložiště prostorů s přímým přístupem (S2D) a Windows Server Failover Clustering zajistit výkonné, škálovatelné a odolné úložiště služby.

Azure Stack integrované systému partneři nabízejí množství variant řešení, včetně širokou škálu flexibilitu úložiště. Nyní můžete vybrat kombinaci tří typů disků: NVMe (stálé paměti Express), SATA a SAS SSD (jednotky SSD), pevný disk (jednotku pevného disku).

Prostory úložiště – přímé funkce mezipaměť pro zajištění maximálního výkonu úložiště. V zařízení služby Azure Stack s jedním nebo více typů jednotek, prostory úložiště – přímé automaticky používat všechny jednotky "nejrychlejší" (NVMe &gt; SSD &gt; HDD) typu pro ukládání do mezipaměti. Zbývající jednotky se použijí k ukládání. Jednotky by mohly být seskupené do nasazení "typu flash" nebo "hybridní":

![Infrastruktura Azure stacku úložiště](media/azure-stack-storage-infrastructure-overview/image1.png)

Typu flash cílem nasazení pro zajištění maximálního výkonu úložiště a nezahrnují rotačních pevných disků (HDD).

![Infrastruktura Azure stacku úložiště](media/azure-stack-storage-infrastructure-overview/image2.png)

Hybridní nasazení, zaměřuje vyvážit výkon a kapacitu nebo chcete maximalizovat kapacitu a zahrnují rotačních pevných disků (HDD).

Chování mezipaměti je určen automaticky v závislosti na typy jednotek, které jsou do mezipaměti pro. Při ukládání do mezipaměti pro jednotky SSD (jako je například NVMe ukládání do mezipaměti pro disky SSD), jsou uložené v mezipaměti jenom zápisy. To snižuje opotřebení jednotky kapacity, snížení kumulativní přenosy pro jednotky kapacity a rozšíření jejich životního cyklu. Do té doby protože čtení neovlivňují výrazně životnost flash a jednotky SSD univerzálně nabízejí s nízkou latencí pro čtení, čtení nejsou uložené v mezipaměti. Při ukládání do mezipaměti pro jednotky pevného disku (například jednotky SSD ukládání do mezipaměti pro pevné disky), jak operace čtení a zápisy jsou uložené v mezipaměti, a poskytnout latenci, flash jako (často ~ 10 x vyšší) pro obojí.

![Infrastruktura Azure stacku úložiště](media/azure-stack-storage-infrastructure-overview/image3.png)

Pro konfiguraci dostupnosti úložiště, můžete zkontrolovat partnera Azure Stack OEM (https://azure.microsoft.com/overview/azure-stack/partners/) pro podrobné specifikace.

> [!Note]  
> Zařízení Azure Stack může doručit v hybridním nasazení s jednotkami jak HDD a SSD (nebo NVMe). Ale jednotkách rychlejší typu se použije jako jednotky mezipaměti a všechny zbývající jednotky se použije jako jednotky kapacity jako fond. Data tenanta (objekty BLOB, tabulky, fronty a disky) umístěné na jednotky kapacity. Proto zřizování prémiové disky nebo jeho výběru účtu služby premium storage, že typ neznamená objekty je zaručeno, přidělené na SSD nebo NVMe jednotky a získat vyšší výkon.

### <a name="volumes"></a>Svazky

*Službu storage* rozděluje úložiště k dispozici do samostatných svazcích, které jsou přiděleny pro uchovávání dat systému a tenanta. Svazky kombinovat jednotky ve fondu úložiště k odolnosti proti chybám, škálovatelnosti a přinese zlepšení výkonu prostorů úložiště s přímým.

![Infrastruktura Azure stacku úložiště](media/azure-stack-storage-infrastructure-overview/image4.png)

Existují tři typy svazků vytvořených ve fondu úložiště služby Azure Stack:

-   Infrastruktury: používat virtuální počítače infrastruktury Azure stacku a základní služby hostitelské soubory.

-   Virtuální počítač Temp: hostitele dočasné disky připojené k klientské virtuální počítače a tato data uložená v těchto disků.

-   Objekt Store: hostovat data tenanta údržby objekty BLOB, tabulky, fronty a disky virtuálních počítačů.

V nasazení s více uzly uvidíte tři svazky infrastruktury, zatímco počet Temp virtuálního počítače a objekt Store svazky se rovná počtu uzlů v nasazení Azure Stack:

-   U nasazení čtyřmi uzly jsou čtyři stejné Temp virtuálního počítače a čtyři rovná objektu Store svazky.

-   Pokud chcete přidat nový uzel do clusteru, bude nový svazek pro oba typy se dají vytvářet.

-   Počet svazků zůstává stejný, i když uzel nefunguje správně nebo se odebere.

-   Pokud používáte sady pro vývojáře Azure Stack, je na jednom svazku s více sdílených složek.

Svazky do prostorů úložiště s přímým poskytují odolnost proti chybám a ochranu před problémy s hardwarem, jako je například selhání disku nebo serveru povolit nepřetržitou dostupnost v rámci údržby serverů, jako je například aktualizace softwaru. Nasazení Azure Stack používá třícestné zrcadlení zajistit odolnost dat. Tři kopie dat tenantů se zapisují různým serverům, kde se objeví v mezipaměti:

![Infrastruktura Azure stacku úložiště](media/azure-stack-storage-infrastructure-overview/image5.png)

Zrcadlení poskytuje odolnost proti chybám díky uchovávání několika kopií všechna data. Jak tato data je rozdělená a umístit je netriviální (viz další informace tomto blogu), ale je naprosto true Představme, že všechna data uložená pomocí zrcadlení je napsána v celém rozsahu, více než jednou. Každá kopie jsou zapsána do různých fyzických hardwaru (různé jednotky na různých serverech), který předpokládá, že jsou selhání nezávisle na sobě. Trojcestné zrcadlení bezpečně tolerovat aspoň dva problémy s hardwarem (jednotky nebo serveru) v čase. Například pokud se restartování jednoho serveru při náhle jinou jednotku nebo server se nezdaří, všechna data zůstanou nepřetržitě dostupné a bezpečné.

## <a name="volume-states"></a>Stavy svazku

Pokud chcete zjistit stav volumess jsou v, použijte následující příkazy Powershellu:

\$scaleunit\_název = (Get-AzsScaleUnit)\[0\].name

\$subsystém\_název = (Get-AzsStorageSubSystem - ScaleUnit \$scaleunit\_název)\[0\].name

Get-AzsVolume - ScaleUnit \$scaleunit\_název - StorageSubSystem \$subsystému\_název | Select-Object VolumeLabel, bude, provozní stav, RepairStatus, popis, akce, TotalCapacityGB, RemainingCapacityGB

Tady je příklad výstupu zobrazující svazek odpojit a snížení neúplné svazku:

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Neznámé | Odpojit |
| ObjStore_2 | Upozornění | {Snížený výkon, neúplný} |

Následující části uvádějí stav a provozní stavy.

### <a name="volume-health-state-healthy"></a>Stav svazku: V pořádku

| Provozní stav | Popis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Svazek je v pořádku. |
| Neoptimální | Data se zapisují rovnoměrně na jednotkách.<br> <br>**Akce:** Kontaktujte prosím podporu k optimalizaci využití disku ve fondu úložiště. Než začnete, proces shromažďování souborů protokolů pomocí pokynů z https://aka.ms/azurestacklogfiles. Bude pravděpodobně nutné obnovení ze zálohy po obnovení připojení se nezdařilo. |


### <a name="volume-health-state-warning"></a>Stav svazku: Upozornění

Když svazek je ve stavu upozornění, znamená to, že nejsou k dispozici jeden nebo více kopií vašich dat, ale Azure Stack i nadále můžete načítat alespoň jedna kopie vašich dat.

| Provozní stav | Popis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ve službě | Azure Stack je oprava svazku, například po přidání nebo odebrání jednotky. Po dokončení opravy svazku by měl vrátit do stavu OK.<br> <br>**Akce:** Počkejte pro službu Azure Stack k dokončení opravy svazek a potom zkontrolujte stav. |
| Neúplný | Odolnosti svazku se sníží, protože jeden nebo víc jednotek chybí nebo se nezdařilo. Chybějící disky ale obsahovat aktuální kopie vašich dat.<br> <br>**Akce:** Znovu připojit všechny chybějící jednotky, nahraďte všechny neúspěšné jednotky a převedení do online režimu všechny servery, které jsou v režimu offline. |
| Sníženo | Odolnosti svazku se sníží, protože jeden nebo víc jednotek se nezdařilo nebo chybí, a jsou zastaralé kopií vašich dat na těchto jednotkách.<br> <br>**Akce:** Znovu připojit všechny chybějící jednotky, nahraďte všechny neúspěšné jednotky a převedení do online režimu všechny servery, které jsou v režimu offline. |

 

### <a name="volume-health-state-unhealthy"></a>Stav svazku: Není v pořádku

Když svazek je ve stavu není v pořádku, stavu, některá nebo všechna data na svazek je momentálně nedostupný.

| Provozní stav | Popis |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Žádné redundance | Svazku došlo ke ztrátě dat, protože příliš mnoho jednotek selhala.<br> <br>**Akce:** Obraťte se prosím na podporu. Než začnete, proces shromažďování souborů protokolů pomocí pokynů z https://aka.ms/azurestacklogfiles. |


### <a name="volume-health-state-unknown"></a>Stav svazku: Neznámé

Svazku můžete také být v neznámý stav v pořádku, pokud se odpojit virtuální disk.

| Provozní stav | Popis |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Odpojit | Došlo k selhání zařízení úložiště, což může způsobit svazku, který má být nedostupné. Některá data mohou být ztraceny.<br> <br>**Akce:** <br>1. Zkontrolujte fyzický a síťové připojení všech zařízení úložiště.<br>2. Pokud jsou všechna zařízení správně připojeny, kontaktujte prosím podporu. Než začnete, proces shromažďování souborů protokolů pomocí pokynů z https://aka.ms/azurestacklogfiles. Bude pravděpodobně nutné obnovení ze zálohy po obnovení připojení se nezdařilo. |

## <a name="drive-states"></a>Stavy disku

Ke sledování stavu jednotky použijte následující příkazy Powershellu:

 

\$scaleunit\_název = (Get-AzsScaleUnit)\[0\].name

\$subsystém\_název = (Get-AzsStorageSubSystem - ScaleUnit \$scaleunit\_název)\[0\].name

, Sériové číslo

 

Get-AzsDrive - ScaleUnit \$scaleunit\_název - StorageSubSystem \$subsystému\_název | Select-Object StorageNode, PhysicalLocation, bude, provozní stav, popis, akce, využití, CanPool, CannotPoolReason, sériové číslo, Model, MediaType, CapacityGB

Stav jednotky může být v následujících částech.

### <a name="drive-health-state-healthy"></a>Stav disku: V pořádku

| Provozní stav | Popis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Svazek je v pořádku. |
| Ve službě | Na jednotce je provádění některých operací vnitřní údržbu. Po dokončení akce by měla vrátit na jednotce se OK stavu. |

### <a name="drive-health-state-healthy"></a>Stav disku: V pořádku

Jednotka v může stav upozornění číst a zapisovat data úspěšně, ale má problém.

| Provozní stav | Popis |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ztráta komunikace | Připojení bylo ztraceno na jednotku.<br> <br>**Akce:** Přeneste zpět do online režimu všechny servery. Pokud, který se neodstraní, znovu připojte jednotku. Pokud to udržuje děje, nahraďte jednotky zajistit plnou odolnost. |
| Prediktivní selhání | Selhání disku, očekává se, že brzy dojde k.<br> <br>**Akce:** Výměna disku co nejdřív zajistit plnou odolnost. |
| Vstupně-výstupní chybě | Došlo k dočasné chybě přístup k jednotce.<br> <br>**Akce:** Pokud to udržuje děje, nahraďte jednotky zajistit plnou odolnost. |
| Přechodná chyba | Došlo k dočasné chybě jednotce. To obvykle znamená, že nereagoval na jednotce, ale to může také znamenají, že prostory úložiště – přímé Ochranný oddíl neoprávněně odebraly z jednotky. <br> <br>**Akce:** Pokud to udržuje děje, nahraďte jednotky zajistit plnou odolnost. |
| Neobvyklé latence | Jednotka je někdy reagovat a zobrazuje známky selhání.<br> <br>**Akce:** Pokud to udržuje děje, nahraďte jednotky zajistit plnou odolnost. |
| Odebírá se z fondu | Azure Stack je právě odebrání jednotky z fondu úložiště.<br> <br>**Akce:** Počkejte pro službu Azure Stack na dokončení odebrání jednotky a potom zkontrolujte stav.<br>Pokud tento stav, kontaktujte prosím podporu. Než začnete, proces shromažďování souborů protokolů pomocí pokynů z https://aka.ms/azurestacklogfiles. |
|  |  |
| Výchozí režim údržby | Azure Stack je umístění na jednotce v režimu údržby. Jedná se o dočasný stav – jednotka by už brzy bude ve stavu v režimu údržby.<br> <br>**Akce:** Počkejte pro službu Azure Stack pro dokončení procesu a zkontrolovat stav později. |
| V režimu údržby | Jednotka je v režimu údržby, zastavení operace čtení a zápisy z jednotky. To obvykle znamená, že správa služby Azure Stack úkoly, jako je PNU nebo FRU pracuje na jednotce. Ale správce může také uvést jednotky do režimu údržby.<br> <br>**Akce:** Počkejte na dokončení úlohy správy Azure Stack a potom zkontrolujte stav.<br>Pokud tento stav, kontaktujte prosím podporu. Než začnete, proces shromažďování souborů protokolů pomocí pokynů z https://aka.ms/azurestacklogfiles. |
|  |  |
| Zastavení režimu údržby | Azure Stack je právě přináší jednotky zpět do online režimu. Jedná se o dočasný stav - jednotky by měla brzy v jiném stavu – v ideálním případě jsou v pořádku.<br> <br>**Akce:** Počkejte pro službu Azure Stack pro dokončení procesu a zkontrolovat stav později. |

 

### <a name="drive-health-state-unhealthy"></a>Stav disku: Není v pořádku

Jednotky ve stavu není v pořádku nelze nyní zapsána do nebo získat přístup.

| Provozní stav | Popis |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Rozdělit | Jednotka má oddělit od fondu.<br> <br>**Akce:** Výměna disku se nový disk. Pokud je nutné použít tento disk, odebrat disk ze systému, ujistěte se, že neexistuje žádná užitečná data na disku, vymazat disk a pak znovu upevněte disku. |
| Nejde použít | Fyzický disk je umístěn do karantény, protože ho nepodporuje dodavatele vašeho řešení. Jsou podporovány pouze disky, které jsou schváleny pro řešení a mají správný disk firmwaru.<br> <br>**Akce:** Výměna disku s diskem, který má schválené výrobce a model číslo pro řešení. |
| Zastaralá metadata | Náhradní disk byl dříve používán a může obsahovat data z neznámého úložný systém. Disk se umístí do karantény.        <br> <br>**Akce:** Výměna disku se nový disk. Pokud je nutné použít tento disk, odebrat disk ze systému, ujistěte se, že neexistuje žádná užitečná data na disku, vymazat disk a pak znovu upevněte disku. |
| Nerozpoznaná metadata | Nerozpoznaná metadata na jednotce, která obvykle znamená, že má jednotka metadat z jiný fond na něm nenašel.<br> <br>**Akce:** Výměna disku se nový disk. Pokud je nutné použít tento disk, odebrat disk ze systému, ujistěte se, že neexistuje žádná užitečná data na disku, vymazat disk a pak znovu upevněte disku. |
| Chyba média | Na jednotce se nezdařilo a nepoužije prostory úložiště už.<br> <br>**Akce:** Výměna disku co nejdřív zajistit plnou odolnost. |
| Selhání hardwaru zařízení | Na této jednotce došlo k selhání hardwaru. <br> <br>**Akce:** Výměna disku co nejdřív zajistit plnou odolnost. |
| Aktualizace firmwaru | Azure Stack je aktualizace firmwaru na jednotce. Jedná se o dočasný stav, který obvykle trvá méně než minutu a naruší čas disků ve fondu se postaráme čte a zapisuje.<br> <br>**Akce:** Počkejte na dokončení aktualizace Azure Stack a potom zkontrolujte stav. |
| Spouštění | Na jednotce se připravuje pro operaci. Musí se jednat o dočasný stav – Jakmile budete hotovi, že jednotky by měl přejít na různé provozní stav.<br> <br>**Akce:** Počkejte na dokončení operace Azure Stack a potom zkontrolujte stav. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>Důvodům, proč jednotku nejde ve fondu

Některé jednotky jednoduše nejsou připravené k odeslání do fondu úložiště služby Azure Stack. Můžete zjistit, proč není zobrazením vlastností CannotPoolReason jednotky pro jednotku. Následující tabulka uvádí trochu podrobněji na každém z důvodů.

| Důvod | Popis |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hardware nedodržující předpisy | Na jednotce není v seznamu schválených modelů zadat pomocí služby Health Service.<br> <br>**Akce:** Výměna disku se nový disk. |
| Firmware neodpovídá | Firmware na fyzický disk není v seznamu schválených firmware revize pomocí služby Health Service.<br> <br>**Akce:** Výměna disku se nový disk. |
| Používá v clusteru | Na jednotce se aktuálně používá clusterem pro převzetí služeb při selhání.<br> <br>**Akce:** Výměna disku se nový disk. |
| Vyměnitelné médium | Na jednotce je klasifikován jako vyměnitelnou jednotku. <br> <br>**Akce:** Výměna disku se nový disk. |
| Nejsou v dobrém stavu | Na jednotce není v pořádku a může být potřeba nahradit.<br> <br>**Akce:** Výměna disku se nový disk. |
| Nedostatečné kapacity | Existují oddíly zabíraly volného místa na disku.<br> <br>**Akce:** Výměna disku se nový disk. Pokud je nutné použít tento disk, odebrat disk ze systému, ujistěte se, že neexistuje žádná užitečná data na disku, vymazat disk a pak znovu upevněte disku. |
| Probíhá ověření. | Služba Health Service je kontroluje se, pokud je na jednotku nebo firmware jednotky schválená k používání.<br> <br>**Akce:** Počkejte pro službu Azure Stack pro dokončení procesu a zkontrolovat stav později. |
| Chyba ověřování | Služba Health Service nelze zkontrolovat Pokud disku nebo firmware jednotky je schválená k používání.<br> <br>**Akce:** Obraťte se prosím na podporu. Než začnete, proces shromažďování souborů protokolů pomocí pokynů z https://aka.ms/azurestacklogfiles. |
| Offline | Jednotka je v režimu offline. <br> <br>**Akce:** Obraťte se prosím na podporu. Než začnete, proces shromažďování souborů protokolů pomocí pokynů z https://aka.ms/azurestacklogfiles. |

## <a name="next-step"></a>Další krok

[Správa kapacity úložiště](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-storage-shares) 