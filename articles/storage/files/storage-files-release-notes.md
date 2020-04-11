---
title: Poznámky k verzi pro agenta synchronizace souborů Azure | Dokumenty společnosti Microsoft
description: Poznámky k vydání pro agenta Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: c6455930f88d02e11cb0c45d29594ae40eaad9e3
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113297"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Poznámky k verzi pro agenta Synchronizace souborů Azure
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Vaše instalace Windows Serveru se transformují na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS). Můžete mít libovolný počet mezipamětí po celém světě.

Tento článek obsahuje poznámky k podporovaným verzím agenta Synchronizace souborů Azure.

## <a name="supported-versions"></a>Podporované verze
Agent Synchronizace souborů Azure podporuje následující verze:

| Milník | Číslo verze agenta | Datum vydání | Status |
|----|----------------------|--------------|------------------|
| Verze V10 - [KB4522359](https://support.microsoft.com/en-us/help/4522409/azure-file-sync-agent-v10-release-march-2020)| 10.0.0.0 | 9. dubna 2020 | Za letu |
| Kumulativní aktualizace z prosince 2019 – [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12. prosince 2019December 12, 2019 | Podporuje se |
| Verze V9 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2. prosince 2019 | Podporuje se |
| Verze V8 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8. října 2019 | Podporuje se |
| Kumulativní aktualizace z července 2019 - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24. července 2019 | Podporuje se |
| Kumulativní aktualizace z července 2019 - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12. července 2019 | Podporuje se |
| Verze V7 - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19. června 2019 | Podporuje se |
| Kumulativní aktualizace z června 2019 - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27. června 2019 | Podporováno – platnost verze agenta vyprší 21. |
| Kumulativní aktualizace z června 2019 - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13. června 2019 | Podporováno – platnost verze agenta vyprší 21. |
| Kumulativní aktualizace z května 2019 - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7. května 2019 | Podporováno – platnost verze agenta vyprší 21. |
| Verze V6 - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21. dubna 2019April 21, 2019 | Podporováno – platnost verze agenta vyprší 21. |
| Verze V5 | 5.0.2.0 - 5.2.0.0 | – | Není podporováno – platnost verzí agenta vypršela 18. |
| Verze V4 | 4.0.1.0 - 4.3.0.0 | – | Není podporováno – platnost verzí agenta vypršela 6. |
| Verze V3 | 3.1.0.0 - 3.4.0.0 | – | Není podporováno – platnost verzí agenta vypršela 19. |
| Pre-GA agenti | 1.1.0.0 - 3.0.13.0 | – | Není podporováno – platnost verzí agenta vypršela 1. |

### <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Synchronizace souborů Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10000"></a>Agent verze 10.0.0.0
Následující poznámky k verzi jsou pro verzi 10.0.0.0 agenta Azure File Sync (vydané 9. dubna 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou opraveny

- Vylepšený průběh synchronizace na portálu
    - S vydáním agenta V10 se portál Azure brzy začne zobrazovat typ synchronizační relace, která běží. Například počáteční stahování, pravidelné stahování, stahování na pozadí (rychlé případy zotavení po havárii) a podobně. 

- Vylepšené prostředí portálu s vrstvením cloudu
    - Pokud máte soubory, které se nedaří vrstvení nebo odvolání, můžete nyní zobrazit chyby vrstvení ve vlastnostech koncového bodu serveru.
    - Další informace o vrstvení cloudu jsou k dispozici pro koncový bod serveru:
        - Velikost místní mezipaměti
        - Efektivita využití mezipaměti
        - Podrobnosti zásad vrstvení cloudu: velikost svazku, aktuální volné místo nebo poslední přístup nejstaršího souboru v místní mezipaměti.
    - Tyto změny budou dodávány na portálu Azure krátce po počáteční verzi agenta V10.

- Podpora přesunutí služby Synchronizace úložiště nebo účtu úložiště do jiného klienta Služby Azure Active Directory (AAD)
    - Azure File Sync teď podporuje přesunutí služby synchronizace úložiště nebo účtu úložiště do jiné skupiny prostředků, předplatného nebo klienta Azure AD.
    
- Nástroj pro vyhodnocení nyní identifikuje soubory nebo adresáře, které končí tečkou
    - [Nástroj hodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) byl aktualizován tak, aby identifikoval soubory nebo adresáře, které končí tečkou. Soubory nebo adresáře, které končí tečkou, nejsou aktuálně podporované Azure File Sync. Chcete-li použít aktualizovanou verzi [nástroje Vyhodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet), nainstalujte nejnovější verzi [modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
 
- Různá vylepšení výkonu a spolehlivosti
    - Zjišťování změn ve sdílené složce Azure může selhat, pokud virtuální síť (VNET) a firewall pravidla jsou nakonfigurované na účtu úložiště.
    - Snížená spotřeba paměti spojená s odvoláním. 
    - Vyšší výkon při použití rutiny [Invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
    - Další různá vylepšení spolehlivosti. 
    
### <a name="evaluation-tool"></a>Nástroj pro hodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje pro vyhodnocení synchronizace souborů Azure. Tento nástroj je rutina prostředí Azure PowerShell, která kontroluje potenciální problémy se souborovým systémem a datovou sadou, jako jsou nepodporované znaky nebo nepodporovaná verze operačního systému. Pokyny k instalaci a použití naleznete v části [Nástroj hodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) v průvodci plánováním. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync pomocí Windows Serveru, najdete v [tématu Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md) a [Jak nasadit Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalován se zvýšenými oprávněními (oprávněními správce).
- Agent není podporován u možnosti nasazení nano serveru.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje alespoň 2 GiB paměti. Pokud je server spuštěn ve virtuálním počítači s povolenou dynamickou pamětí, virtuální počítač by měl být nakonfigurován s minimálně 2048 MiB paměti.
- Služba Storage Sync Agent (FileSyncSvc) nepodporuje koncové body serveru umístěné na svazku, který má komprimovaný adresář s informacemi o systémovém svazku (SVI). Tato konfigurace povede k neočekávaným výsledkům.

### <a name="interoperability"></a>Interoperabilita
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete [v tématu Poradce při potížích se synchronizací souborů Azure](storage-sync-files-troubleshoot.md).
- Blokování souborů Správce prostředků souborového serveru (FSRM) může způsobit nekonečné selhání synchronizace při blokování souborů z důvodu blokování souborů.
- Spuštění programu sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Agent Azure File Sync by měl být nainstalován po nasazení image serveru a dokončení miniinstalace programu sysprep.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete [v příručce poradce při potížích.](storage-sync-files-troubleshoot.md#handling-unsupported-characters)
- Soubory nebo adresáře, které končí tečkou.
- Cesty delší než 2 048 znaků.
- Část seznamu řízení auditování přístupu (SACL) popisovače zabezpečení, která se používá se pro auditování.
- Rozšířené atributy.
- Alternativní datové proudy.
- Body rozboru.
- Pevné odkazy.
- Komprese (pokud je nastavená pro soubor na serveru) se nezachová při synchronizaci změn do tohoto souboru z ostatních koncových bodů.
- Všechny soubory šifrované pomocí systému souborů EFS (nebo jiného šifrování v uživatelském režimu), který brání službě ve čtení těchto dat.

    > [!Note]  
    > Synchronizace souborů Azure vždy šifruje přenášená data. Neaktivní uložená data se vždy šifrují v Azure.
 
### <a name="server-endpoint"></a>Koncový bod serveru
- Koncový bod serveru je možné vytvořit pouze na svazku NTFS. Synchronizace souborů Azure v současné době nepodporuje systémy souborů ReFS, FAT, FAT32 a jiné.
- Vrstvení cloudu se na systémovém svazku nepodporuje. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, při vytváření koncového bodu serveru zakažte vrstvení cloudu.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neuklápejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu není aktualizován, pokud je server přejmenován.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Azure File Sync podporuje provádění změn ve sdílené složce Azure přímo. Všechny změny provedené ve sdílené složce Azure však musí být nejprve zjištěny úlohou zjišťování změn synchronizace souborů Azure. Úloha zjišťování změn se inicializuje pro koncový bod cloudu jednou za 24 hodin. Chcete-li okamžitě synchronizovat soubory, které se změní ve sdílené složce Azure, můžete použít rutinu [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell k ručnímu spuštění zjišťování změn ve sdílené složce Azure. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST nebude aktualizovat čas smb naposledy změněné a nebude považovat za změnu synchronizace.
- Služba synchronizace úložiště nebo účet úložiště lze přesunout do jiné skupiny prostředků nebo předplatného v rámci existujícího klienta Azure AD. Pokud se účet úložiště přesune, musíte službě hybrid File Sync Service udělit přístup k účtu úložiště (viz [Zajištění azure file sync má přístup k účtu úložiště).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Při vytváření cloudového koncového bodu musí být služba synchronizace úložiště a účet úložiště ve stejném tenantovi Azure AD. Po vytvoření cloudového koncového bodu se služba synchronizace úložiště a účet úložiště dá přesunout do různých klientů Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí robokopie použijte možnost /MIR k zachování časových razítek souborů. Tím zajistíte, že starší soubory budou vrstvené dříve než nedávno přistupované soubory.

## <a name="agent-version-9100"></a>Agent verze 9.1.0.0
Následující poznámky k verzi jsou pro verzi 9.1.0.0 agenta Azure File Sync vydaného 12. Tyto poznámky jsou navíc k poznámkám k verzi pro verzi 9.0.0.0.

Problém opraven v této verzi:  
- Synchronizace se nezdaří s jednou z následujících chyb po upgradu na agenta Azure File Sync verze 9.0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Agent verze 9.0.0.0
Následující poznámky k verzi jsou pro verzi 9.0.0.0 agenta Azure File Sync (vydané 2. prosince 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou opraveny

- Samoobslužná podpora obnovení
    - Uživatelé nyní mohou obnovit své soubory pomocí funkce předchozí verze. Před vydáním verze v9 nebyla předchozí verze podporována na svazcích, které měly povoleno vrstvení cloudu. Tato funkce musí být povolena pro každý svazek zvlášť, na kterém existuje koncový bod s povolenou vrstvením cloudu. Další informace najdete v části  
[Samoobslužné obnovení prostřednictvím předchozích verzí a služby VSS (Služba stínové kopie svazku)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Podpora větších velikostí sdílené složky 
    - Azure File Sync teď podporuje až 64TiB a 100 milionů souborů v jednom synchronizačním oboru názvů.  
 
- Podpora odstranění duplicitních dat na Serveru 2019 
    - Odstranění duplicit dat je teď podporováno s povoleným vrstvením cloudu na Windows Serveru 2019. Pro podporu odstranění duplicit dat na svazcích s vrstvením cloudu musí být nainstalována aktualizace Systému Windows [KB4520062.](https://support.microsoft.com/help/4520062) 
 
- Vylepšená minimální velikost souboru pro soubor na úroveň 
    - Minimální velikost souboru pro soubor na úroveň je nyní založena na velikosti clusteru systému souborů (dvojnásobek velikosti clusteru systému souborů). Ve výchozím nastavení je například velikost clusteru systému souborů NTFS 4 KB, výsledná minimální velikost souboru pro soubor na úroveň je 8 KB. 
 
- Rutina testu připojení k síti 
    - Jako součást konfigurace Azure File Sync je nutné kontaktovat více koncových bodů služby. Každý z nich má svůj vlastní název DNS, který musí být přístupný serveru. Tyto adresy URL jsou také specifické pro oblast, do které je server registrován. Jakmile je server zaregistrován, lze k testování komunikace se všemi adresami URL specifickými pro tento server použít rutinu testu připojení (PowerShell a Server Registration Utility). Tato rutina může pomoci při řešení potíží, když neúplná komunikace zabrání serveru plně pracovat s Azure File Sync a lze ji použít k jemnému doladění konfigurací proxy a brány firewall.  
 
        Chcete-li spustit test připojení k síti, spusťte následující příkazy prostředí PowerShell: 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncPřipojení k síti
 
- Odebrání zlepšení koncového bodu serveru, pokud je povoleno vrstvení cloudu 
    - Stejně jako dříve, odebrání koncového bodu serveru nemá za následek odebrání souborů ve sdílené složce Azure. Chování bodů změny analýzy na místním serveru se však změnilo. Při odebírání koncového bodu serveru jsou nyní odstraněny body s třídou (ukazatele na soubory, které nejsou místní na serveru). Soubory uložené v mezipaměti zůstanou na serveru. Toto vylepšení bylo provedeno zabránit [osamocené vrstvené soubory](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) při odebírání koncového bodu serveru. Pokud je koncový bod serveru znovu vytvořen, budou na serveru znovu vytvořeny body náhrady pro vrstvené soubory.  
 
- Vylepšení výkonu a spolehlivosti 
    - Snížena selhání odvolání. Velikost odvolání je nyní automaticky upravena na základě šířky pásma sítě. 
    - Vyšší výkon stahování při přidávání nového serveru do skupiny synchronizace. 
    - Snížené soubory, které nejsou synchronizovány kvůli konfliktům omezení. 
    - Soubory se nezdaří vrstvy nebo jsou neočekávaně odvolány v určitých scénářích, pokud cesta koncového bodu serveru je přípojný bod svazku.
    
### <a name="evaluation-tool"></a>Nástroj pro hodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje pro vyhodnocení synchronizace souborů Azure. Tento nástroj je rutina prostředí Azure PowerShell, která kontroluje potenciální problémy se souborovým systémem a datovou sadou, jako jsou nepodporované znaky nebo nepodporovaná verze operačního systému. Pokyny k instalaci a použití naleznete v části [Nástroj hodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) v průvodci plánováním. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync pomocí Windows Serveru, najdete v [tématu Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md) a [Jak nasadit Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalován se zvýšenými oprávněními (oprávněními správce).
- Agent není podporován u možnosti nasazení nano serveru.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje alespoň 2 GiB paměti. Pokud je server spuštěn ve virtuálním počítači s povolenou dynamickou pamětí, virtuální počítač by měl být nakonfigurován s minimálně 2048 MiB paměti.
- Služba Storage Sync Agent (FileSyncSvc) nepodporuje koncové body serveru umístěné na svazku, který má komprimovaný adresář s informacemi o systémovém svazku (SVI). Tato konfigurace povede k neočekávaným výsledkům.

### <a name="interoperability"></a>Interoperabilita
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete [v tématu Poradce při potížích se synchronizací souborů Azure](storage-sync-files-troubleshoot.md).
- Blokování souborů Správce prostředků souborového serveru (FSRM) může způsobit nekonečné selhání synchronizace při blokování souborů z důvodu blokování souborů.
- Spuštění programu sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Agent Azure File Sync by měl být nainstalován po nasazení image serveru a dokončení miniinstalace programu sysprep.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete [v příručce poradce při potížích.](storage-sync-files-troubleshoot.md#handling-unsupported-characters)
- Soubory nebo adresáře, které končí tečkou.
- Cesty delší než 2 048 znaků.
- Část volitelného seznamu řízení přístupu (DACL) popisovače zabezpečení, pokud je větší než 2 kB. (To je problém pouze v případě, že pro jednu položku máte více než přibližně 40 položek řízení přístupu.)
- Část seznamu řízení auditování přístupu (SACL) popisovače zabezpečení, která se používá se pro auditování.
- Rozšířené atributy.
- Alternativní datové proudy.
- Body rozboru.
- Pevné odkazy.
- Komprese (pokud je nastavená pro soubor na serveru) se nezachová při synchronizaci změn do tohoto souboru z ostatních koncových bodů.
- Všechny soubory šifrované pomocí systému souborů EFS (nebo jiného šifrování v uživatelském režimu), který brání službě ve čtení těchto dat.

    > [!Note]  
    > Synchronizace souborů Azure vždy šifruje přenášená data. Neaktivní uložená data se vždy šifrují v Azure.
 
### <a name="server-endpoint"></a>Koncový bod serveru
- Koncový bod serveru je možné vytvořit pouze na svazku NTFS. Synchronizace souborů Azure v současné době nepodporuje systémy souborů ReFS, FAT, FAT32 a jiné.
- Vrstvené soubory se stanou nepřístupné, pokud soubory nejsou odvolány před odstraněním koncového bodu serveru. Chcete-li obnovit přístup k souborům, vytvořte koncový bod serveru znovu. Pokud uplynulo 30 dní od odstranění koncového bodu serveru nebo pokud byl odstraněn koncový bod cloudu, vrstvené soubory, které nebyly odvolány, budou nepoužitelné. Další informace naleznete v [tématu Vrstvené soubory nejsou přístupné na serveru po odstranění koncového bodu serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Vrstvení cloudu se na systémovém svazku nepodporuje. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, při vytváření koncového bodu serveru zakažte vrstvení cloudu.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neuklápejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu není aktualizován, pokud je server přejmenován.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Azure File Sync podporuje provádění změn ve sdílené složce Azure přímo. Všechny změny provedené ve sdílené složce Azure však musí být nejprve zjištěny úlohou zjišťování změn synchronizace souborů Azure. Úloha zjišťování změn se inicializuje pro koncový bod cloudu jednou za 24 hodin. Chcete-li okamžitě synchronizovat soubory, které se změní ve sdílené složce Azure, můžete použít rutinu [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell k ručnímu spuštění zjišťování změn ve sdílené složce Azure. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST nebude aktualizovat čas smb naposledy změněné a nebude považovat za změnu synchronizace.
- Služba synchronizace úložiště nebo účet úložiště lze přesunout do jiné skupiny prostředků nebo předplatného v rámci existujícího klienta Azure AD. Pokud se účet úložiště přesune, musíte službě hybrid File Sync Service udělit přístup k účtu úložiště (viz [Zajištění azure file sync má přístup k účtu úložiště).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync nepodporuje přesunutí předplatného do jiného klienta Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí robokopie použijte možnost /MIR k zachování časových razítek souborů. Tím zajistíte, že starší soubory budou vrstvené dříve než nedávno přistupované soubory.
- Soubory může selhat vrstvy, pokud pagefile.sys je umístěn na svazku, který má povoleno vrstvení v cloudu. Stránkovací soubor sys by měl být umístěn na svazku, který má zakázáno vrstvení v cloudu.

## <a name="agent-version-8000"></a>Agent verze 8.0.0.0
Následující poznámky k verzi jsou pro verzi 8.0.0.0 agenta Azure File Sync (vydané 8. října 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou opraveny

- Obnovení vylepšení výkonu
    - Rychlejší obnovení pro obnovení provedené prostřednictvím služby Azure Backup. Obnovené soubory se synchronizují zpět na servery Azure File Sync mnohem rychleji. 
- Vylepšené prostředí portálu s vrstvením cloudu  
    - Pokud máte vrstvené soubory, které se nedaří odvolat, můžete nyní zobrazit chyby odvolání ve vlastnostech koncového bodu serveru. Stav koncového bodu serveru se nyní zobrazí chybové a zmírnění kroky, pokud ovladač filtru vrstvení cloudu není načten na serveru.
- Jednodušší instalace agenta
    - Modul Az\AzureRM PowerShell již není nutný k registraci serveru, takže instalace je jednodušší a rychlá.
- Různá vylepšení výkonu a spolehlivosti

### <a name="evaluation-tool"></a>Nástroj pro hodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje pro vyhodnocení synchronizace souborů Azure. Tento nástroj je rutina prostředí Azure PowerShell, která kontroluje potenciální problémy se souborovým systémem a datovou sadou, jako jsou nepodporované znaky nebo nepodporovaná verze operačního systému. Pokyny k instalaci a použití naleznete v části [Nástroj hodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) v průvodci plánováním. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync pomocí Windows Serveru, najdete v [tématu Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md) a [Jak nasadit Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalován se zvýšenými oprávněními (oprávněními správce).
- Agent není podporován u možnosti nasazení nano serveru.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje alespoň 2 GiB paměti. Pokud je server spuštěn ve virtuálním počítači s povolenou dynamickou pamětí, virtuální počítač by měl být nakonfigurován s minimálně 2048 MiB paměti.
- Služba Storage Sync Agent (FileSyncSvc) nepodporuje koncové body serveru umístěné na svazku, který má komprimovaný adresář s informacemi o systémovém svazku (SVI). Tato konfigurace povede k neočekávaným výsledkům.

### <a name="interoperability"></a>Interoperabilita
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete [v tématu Poradce při potížích se synchronizací souborů Azure](storage-sync-files-troubleshoot.md).
- Blokování souborů Správce prostředků souborového serveru (FSRM) může způsobit nekonečné selhání synchronizace při blokování souborů z důvodu blokování souborů.
- Spuštění programu sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Agent Azure File Sync by měl být nainstalován po nasazení image serveru a dokončení miniinstalace programu sysprep.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete [v příručce poradce při potížích.](storage-sync-files-troubleshoot.md#handling-unsupported-characters)
- Soubory nebo adresáře, které končí tečkou.
- Cesty delší než 2 048 znaků.
- Část volitelného seznamu řízení přístupu (DACL) popisovače zabezpečení, pokud je větší než 2 kB. (To je problém pouze v případě, že pro jednu položku máte více než přibližně 40 položek řízení přístupu.)
- Část seznamu řízení auditování přístupu (SACL) popisovače zabezpečení, která se používá se pro auditování.
- Rozšířené atributy.
- Alternativní datové proudy.
- Body rozboru.
- Pevné odkazy.
- Komprese (pokud je nastavená pro soubor na serveru) se nezachová při synchronizaci změn do tohoto souboru z ostatních koncových bodů.
- Všechny soubory šifrované pomocí systému souborů EFS (nebo jiného šifrování v uživatelském režimu), který brání službě ve čtení těchto dat.

    > [!Note]  
    > Synchronizace souborů Azure vždy šifruje přenášená data. Neaktivní uložená data se vždy šifrují v Azure.
 
### <a name="server-endpoint"></a>Koncový bod serveru
- Koncový bod serveru je možné vytvořit pouze na svazku NTFS. Synchronizace souborů Azure v současné době nepodporuje systémy souborů ReFS, FAT, FAT32 a jiné.
- Vrstvené soubory se stanou nepřístupné, pokud soubory nejsou odvolány před odstraněním koncového bodu serveru. Chcete-li obnovit přístup k souborům, vytvořte koncový bod serveru znovu. Pokud uplynulo 30 dní od odstranění koncového bodu serveru nebo pokud byl odstraněn koncový bod cloudu, vrstvené soubory, které nebyly odvolány, budou nepoužitelné. Další informace naleznete v [tématu Vrstvené soubory nejsou přístupné na serveru po odstranění koncového bodu serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Vrstvení cloudu se na systémovém svazku nepodporuje. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, při vytváření koncového bodu serveru zakažte vrstvení cloudu.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neuklápejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu není aktualizován, pokud je server přejmenován.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Azure File Sync podporuje provádění změn ve sdílené složce Azure přímo. Všechny změny provedené ve sdílené složce Azure však musí být nejprve zjištěny úlohou zjišťování změn synchronizace souborů Azure. Úloha zjišťování změn se inicializuje pro koncový bod cloudu jednou za 24 hodin. Chcete-li okamžitě synchronizovat soubory, které se změní ve sdílené složce Azure, můžete použít rutinu [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell k ručnímu spuštění zjišťování změn ve sdílené složce Azure. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST nebude aktualizovat čas smb naposledy změněné a nebude považovat za změnu synchronizace.
- Služba synchronizace úložiště nebo účet úložiště lze přesunout do jiné skupiny prostředků nebo předplatného v rámci existujícího klienta Azure AD. Pokud se účet úložiště přesune, musíte službě hybrid File Sync Service udělit přístup k účtu úložiště (viz [Zajištění azure file sync má přístup k účtu úložiště).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync nepodporuje přesunutí předplatného do jiného klienta Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí robokopie použijte možnost /MIR k zachování časových razítek souborů. Tím zajistíte, že starší soubory budou vrstvené dříve než nedávno přistupované soubory.

## <a name="agent-version-7200"></a>Agent verze 7.2.0.0
Následující poznámky k verzi jsou pro verzi 7.2.0.0 agenta Azure File Sync vydaného z 24. Tyto poznámky jsou navíc k poznámkám k verzi pro verzi 7.0.0.0.

Seznam problémů opravených v této verzi:  
- Agent synchronizace úložiště (FileSyncSvc) dojde k chybě, pokud je konfigurace proxy je null.
- Koncový bod serveru spustí BCDR (chyba 0x80c80257 - ECS_E_BCDR_IN_PROGRESS), pokud více koncových bodů na serveru mají stejný název.
- Vylepšení spolehlivosti vrstvení cloudu.

## <a name="agent-version-7100"></a>Agent verze 7.1.0.0
Následující poznámky k verzi jsou pro verzi 7.1.0.0 agenta Azure File Sync vydaného 12. Tyto poznámky jsou navíc k poznámkám k verzi pro verzi 7.0.0.0.

Seznam problémů opravených v této verzi:  
- Přístup k umístění koncového bodu serveru nebo jeho procházení přes smb je v systému Windows Server 2012 R2 pomalé. 
- Zvýšené využití procesoru po instalaci agenta Azure File Sync v6.
- Vylepšení telemetrie vrstvení cloudu.
- Různá vylepšení spolehlivosti pro vrstvení a synchronizaci v cloudu.

## <a name="agent-version-7000"></a>Agent verze 7.0.0.0
Následující poznámky k verzi jsou pro verzi 7.0.0.0 agenta Azure File Sync (vydané 19. června 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou opraveny

- Podpora větších velikostí sdílené složky
    - S náhledem větších sdílených složek Azure zvyšujeme také limity podpory pro synchronizaci souborů. V tomto prvním kroku Azure File Sync teď podporuje až 25 TB a 50 milionů souborů v jednom synchronizačním oboru názvů. Chcete-li požádat o náhled velké https://aka.ms/azurefilesatscalesurveysdílené složky, vyplňte tento formulář . 
- Podpora nastavení brány firewall a virtuální sítě u účtů úložiště
    - Azure File Sync teď podporuje nastavení brány firewall a virtuální sítě na účtech úložiště. Informace o konfiguraci nasazení tak, aby fungovalo s bránou firewall a nastavením virtuální sítě, naleznete [v tématu Konfigurace nastavení brány firewall a virtuální sítě](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- Rutina prostředí PowerShell pro okamžitou synchronizaci souborů změněných ve sdílené složce Azure
    - Chcete-li okamžitě synchronizovat soubory, které se změní ve sdílené složce Azure, můžete použít rutinu Invoke-AzStorageSyncChangeDetection PowerShell k ručnímu spuštění zjišťování změn ve sdílené složce Azure. Tato rutina je určena pro scénáře, kde nějaký typ automatizovaného procesu provádí změny ve sdílené složce Azure nebo změny provádí správce (jako je přesunutí souborů a adresářů do sdílené složky). Pro změny koncových uživatelů doporučujeme nainstalovat agenta Azure File Sync do virtuálního počítače IaaS a mít koncovým uživatelům přístup ke sdílené složce prostřednictvím virtuálního počítače IaaS. Tímto způsobem budou všechny změny rychle synchronizovány s jinými agenty bez nutnosti použití rutiny Invoke-AzStorageSyncChangeDetection. Další informace naleznete v dokumentaci [invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
- Vylepšené prostředí portálu, pokud narazíte na soubory, které nejsou synchronizovány
    - Pokud máte soubory, které se nedaří synchronizovat, nyní rozlišujeme mezi přechodovými a trvalými chybami na portálu. Přechodné chyby se obvykle vyřeší samy bez nutnosti akce správce. Například soubor, který je aktuálně používán, se nesynchronizuje, dokud nebude popisovač souboru uzavřen. U trvalých chyb nyní zobrazujeme počet souborů ovlivněných každou chybou. Počet trvalých chyb se také zobrazí v souborech, které nesynchronizují všechny koncové body serveru ve skupině synchronizace.
- Vylepšené obnovení na úrovni souborů azure backup
    - Jednotlivé soubory obnovené pomocí služby Azure Backup se teď rychleji detekují a synchronizují s koncovým bodem serveru.
- Vylepšená spolehlivost rutiny cmdlet s odstupňováním cloudu 
    - Rutina Invoke-StorageSyncFileRecall nyní umožňuje zákazníkům zadat počet opakování na soubor a zpoždění opakování na soubor podobné robocopy. Dříve by tato rutina vyvolat všechny vrstvené soubory pod danou cestu v náhodném pořadí. S parametrem new -Order tato rutina nejprve vyvolá nejžhavější data a bude respektovat zásady vrstvení cloudu (přestaňte si připomínat, zda je splněna zásada data nebo je splněno volné místo svazku; podle toho, co se stane dříve).
- Podpora pouze tls 1.2 (TLS 1.0 a 1.1 je zakázáno)
    - Azure File Sync teď podporuje použití TLS 1.2 jenom na serverech, které mají tls 1.0 a 1.1 zakázáno. Před tímto vylepšením by registrace serveru selhala, pokud by byly na serveru zakázány požadavky TLS 1.0 a 1.1.
- Různá vylepšení výkonu a spolehlivosti pro synchronizaci a vrstvení v cloudu
    - V této verzi je několik vylepšení spolehlivosti a výkonu. Některé z nich jsou cílené, aby se zefektivnila vrstvení cloudu a Azure File Sync jako celek lépe fungovat v těchto situacích, když máte nastaven plán omezení šířky pásma.

### <a name="evaluation-tool"></a>Nástroj pro hodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje pro vyhodnocení synchronizace souborů Azure. Tento nástroj je rutina prostředí Azure PowerShell, která kontroluje potenciální problémy se souborovým systémem a datovou sadou, jako jsou nepodporované znaky nebo nepodporovaná verze operačního systému. Pokyny k instalaci a použití naleznete v části [Nástroj hodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) v průvodci plánováním. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync pomocí Windows Serveru, najdete v [tématu Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md) a [Jak nasadit Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalován se zvýšenými oprávněními (oprávněními správce).
- Agent není podporován u možnosti nasazení nano serveru.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje alespoň 2 GiB paměti. Pokud je server spuštěn ve virtuálním počítači s povolenou dynamickou pamětí, virtuální počítač by měl být nakonfigurován s minimálně 2048 MiB paměti.
- Služba Storage Sync Agent (FileSyncSvc) nepodporuje koncové body serveru umístěné na svazku, který má komprimovaný adresář s informacemi o systémovém svazku (SVI). Tato konfigurace povede k neočekávaným výsledkům.

### <a name="interoperability"></a>Interoperabilita
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete [v tématu Poradce při potížích se synchronizací souborů Azure](storage-sync-files-troubleshoot.md).
- Blokování souborů Správce prostředků souborového serveru (FSRM) může způsobit nekonečné selhání synchronizace při blokování souborů z důvodu blokování souborů.
- Spuštění programu sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Agent Azure File Sync by měl být nainstalován po nasazení image serveru a dokončení miniinstalace programu sysprep.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete [v příručce poradce při potížích.](storage-sync-files-troubleshoot.md#handling-unsupported-characters)
- Soubory nebo adresáře, které končí tečkou.
- Cesty delší než 2 048 znaků.
- Část volitelného seznamu řízení přístupu (DACL) popisovače zabezpečení, pokud je větší než 2 kB. (To je problém pouze v případě, že pro jednu položku máte více než přibližně 40 položek řízení přístupu.)
- Část seznamu řízení auditování přístupu (SACL) popisovače zabezpečení, která se používá se pro auditování.
- Rozšířené atributy.
- Alternativní datové proudy.
- Body rozboru.
- Pevné odkazy.
- Komprese (pokud je nastavená pro soubor na serveru) se nezachová při synchronizaci změn do tohoto souboru z ostatních koncových bodů.
- Všechny soubory šifrované pomocí systému souborů EFS (nebo jiného šifrování v uživatelském režimu), který brání službě ve čtení těchto dat.

    > [!Note]  
    > Synchronizace souborů Azure vždy šifruje přenášená data. Neaktivní uložená data se vždy šifrují v Azure.
 
### <a name="server-endpoint"></a>Koncový bod serveru
- Koncový bod serveru je možné vytvořit pouze na svazku NTFS. Synchronizace souborů Azure v současné době nepodporuje systémy souborů ReFS, FAT, FAT32 a jiné.
- Vrstvené soubory se stanou nepřístupné, pokud soubory nejsou odvolány před odstraněním koncového bodu serveru. Chcete-li obnovit přístup k souborům, vytvořte koncový bod serveru znovu. Pokud uplynulo 30 dní od odstranění koncového bodu serveru nebo pokud byl odstraněn koncový bod cloudu, vrstvené soubory, které nebyly odvolány, budou nepoužitelné.
- Vrstvení cloudu se na systémovém svazku nepodporuje. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, při vytváření koncového bodu serveru zakažte vrstvení cloudu.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neuklápejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu není aktualizován, pokud je server přejmenován.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Azure File Sync podporuje provádění změn ve sdílené složce Azure přímo. Všechny změny provedené ve sdílené složce Azure však musí být nejprve zjištěny úlohou zjišťování změn synchronizace souborů Azure. Úloha zjišťování změn se inicializuje pro koncový bod cloudu jednou za 24 hodin. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST nebude aktualizovat čas smb naposledy změněné a nebude považovat za změnu synchronizace.
- Služba synchronizace úložiště nebo účet úložiště lze přesunout do jiné skupiny prostředků nebo předplatného v rámci existujícího klienta Azure AD. Pokud se účet úložiště přesune, musíte službě hybrid File Sync Service udělit přístup k účtu úložiště (viz [Zajištění azure file sync má přístup k účtu úložiště).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync nepodporuje přesunutí předplatného do jiného klienta Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí robokopie použijte možnost /MIR k zachování časových razítek souborů. Tím zajistíte, že starší soubory budou vrstvené dříve než nedávno přistupované soubory.

## <a name="agent-version-6300"></a>Agent verze 6.3.0.0
Následující poznámky k verzi jsou pro verzi 6.3.0.0 agenta Azure File Sync vydaného 27. Tyto poznámky jsou navíc k poznámkám k verzi uvedené pro verzi 6.0.0.0.

Seznam problémů opravených v této verzi:  
- Přístup k umístění koncového bodu serveru nebo jeho procházení přes smb je v systému Windows Server 2012 R2 pomalé 
- Zvýšené využití procesoru po instalaci agenta Azure File Sync v6
- Vylepšení telemetrie úrovně cloudu

## <a name="agent-version-6200"></a>Agent verze 6.2.0.0
Následující poznámky k verzi jsou pro verzi 6.2.0.0 agenta Azure File Sync vydaného 13. Tyto poznámky jsou navíc k poznámkám k verzi uvedené pro verzi 6.0.0.0.

Seznam problémů opravených v této verzi:  
- Po vytvoření koncového bodu serveru může dojít k vysokému využití procesoru při stahování souborů na server na pozadí
- Operace synchronizace a vrstvení cloudu mohou selhat s chybou ECS_E_SERVER_CREDENTIAL_NEEDED z důvodu vypršení platnosti tokenu
- Vyvolání souboru může selhat, pokud adresa URL pro stažení souboru obsahuje vyhrazené znaky 

## <a name="agent-version-6100"></a>Agent verze 6.1.0.0
Následující poznámky k verzi jsou pro verzi 6.1.0.0 agenta Azure File Sync vydaného 6. Tyto poznámky jsou navíc k poznámkám k verzi uvedené pro verzi 6.0.0.0.

Seznam problémů opravených v této verzi:  
- Windows Admin Center se nezdaří zobrazit verzi agenta a konfigurace koncového bodu serveru na serverech, které mají nainstalovaný agent Azure File Sync verze 6.0.

## <a name="agent-version-6000"></a>Agent verze 6.0.0.0
Následující poznámky k verzi jsou pro verzi 6.0.0.0 agenta Azure File Sync (vydané 22. dubna 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou opraveny

- Podpora automatických aktualizací agenta
  - Vyslechli jsme vaši zpětnou vazbu a přidali jsme funkci automatické aktualizace do agenta serveru Azure File Sync. Další informace naleznete v [tématu Zásady aktualizace agenta synchronizace souborů Azure](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Podpora pro akly sdílené složky azure
  - Azure File Sync vždy podporovala synchronizaci aklů mezi koncovými body serveru, ale počet aklů nebyl synchronizován do cloudového koncového bodu (sdílení souborů Azure). Tato verze přidává podporu pro synchronizaci aloků aloků mezi koncovými body serveru a cloudu.
- Paralelní relace synchronizace odesílání a stahování pro koncový bod serveru 
  - Koncové body serveru nyní podporují nahrávání a stahování souborů současně. Už žádné čekání na dokončení stahování, aby se soubory mohly nahrát do sdílené složky Azure. 
- Nové rutiny úrovně cloudu pro získání stavu objemu a vrstvení
  - Dvě nové rutiny prostředí PowerShell místní na serveru lze nyní použít k získání informací o vrstvení cloudu a navrácení souborů. Zpřístupniují informace o protokolování ze dvou kanálů událostí na serveru:
    - Get-StorageSyncFileTieringResult zobrazí seznam všech souborů a jejich cest, které nebyly vrstvené a sestavy o důvod, proč.
    - Get-StorageSyncFileRecallResult hlásí všechny události vyvolání souboru. Obsahuje seznam všech stažené souboru a jeho cestu, stejně jako úspěch nebo chybu pro toto odvolání.
  - Ve výchozím nastavení mohou oba kanály událostí uložit až 1 MB – můžete zvýšit množství souborů hlášených zvětšením velikosti kanálu událostí.
- Podpora režimu FIPS
  - Azure File Sync teď podporuje povolení režimu FIPS na serverech, na kterých je nainstalovaný agent Azure File Sync.
    - Před povolením režimu FIPS na serveru nainstalujte na server agenta Synchronizace souborů Azure a [modul Správa balíčků.](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) Pokud fips je již povolena na serveru, [ručně stáhnout](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) [PackageManagement modul](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) na server.
- Různá vylepšení spolehlivosti pro vrstvení a synchronizaci v cloudu

### <a name="evaluation-tool"></a>Nástroj pro hodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje pro vyhodnocení synchronizace souborů Azure. Tento nástroj je rutina prostředí Azure PowerShell, která kontroluje potenciální problémy se souborovým systémem a datovou sadou, jako jsou nepodporované znaky nebo nepodporovaná verze operačního systému. Pokyny k instalaci a použití naleznete v části [Nástroj hodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) v průvodci plánováním. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync pomocí Windows Serveru, najdete v [tématu Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md) a [Jak nasadit Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalován se zvýšenými oprávněními (oprávněními správce).
- Agent není podporován u možnosti nasazení nano serveru.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje alespoň 2 GiB paměti. Pokud je server spuštěn ve virtuálním počítači s povolenou dynamickou pamětí, virtuální počítač by měl být nakonfigurován s minimálně 2048 MiB paměti.
- Služba Storage Sync Agent (FileSyncSvc) nepodporuje koncové body serveru umístěné na svazku, který má komprimovaný adresář s informacemi o systémovém svazku (SVI). Tato konfigurace povede k neočekávaným výsledkům.

### <a name="interoperability"></a>Interoperabilita
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete [v tématu Poradce při potížích se synchronizací souborů Azure](storage-sync-files-troubleshoot.md).
- Blokování souborů Správce prostředků souborového serveru (FSRM) může způsobit nekonečné selhání synchronizace při blokování souborů z důvodu blokování souborů.
- Spuštění programu sysprep na serveru, na kterém je nainstalován agent Azure File Sync, není podporováno a může vést k neočekávaným výsledkům. Agent Azure File Sync by měl být nainstalován po nasazení image serveru a dokončení miniinstalace programu sysprep.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete [v příručce poradce při potížích.](storage-sync-files-troubleshoot.md#handling-unsupported-characters)
- Soubory nebo adresáře, které končí tečkou.
- Cesty delší než 2 048 znaků.
- Část volitelného seznamu řízení přístupu (DACL) popisovače zabezpečení, pokud je větší než 2 kB. (To je problém pouze v případě, že pro jednu položku máte více než přibližně 40 položek řízení přístupu.)
- Část seznamu řízení auditování přístupu (SACL) popisovače zabezpečení, která se používá se pro auditování.
- Rozšířené atributy.
- Alternativní datové proudy.
- Body rozboru.
- Pevné odkazy.
- Komprese (pokud je nastavená pro soubor na serveru) se nezachová při synchronizaci změn do tohoto souboru z ostatních koncových bodů.
- Všechny soubory šifrované pomocí systému souborů EFS (nebo jiného šifrování v uživatelském režimu), který brání službě ve čtení těchto dat.

    > [!Note]  
    > Synchronizace souborů Azure vždy šifruje přenášená data. Neaktivní uložená data se vždy šifrují v Azure.
 
### <a name="server-endpoint"></a>Koncový bod serveru
- Koncový bod serveru je možné vytvořit pouze na svazku NTFS. Synchronizace souborů Azure v současné době nepodporuje systémy souborů ReFS, FAT, FAT32 a jiné.
- Vrstvené soubory se stanou nepřístupné, pokud soubory nejsou odvolány před odstraněním koncového bodu serveru. Chcete-li obnovit přístup k souborům, vytvořte koncový bod serveru znovu. Pokud uplynulo 30 dní od odstranění koncového bodu serveru nebo pokud byl odstraněn koncový bod cloudu, vrstvené soubory, které nebyly odvolány, budou nepoužitelné.
- Vrstvení cloudu se na systémovém svazku nepodporuje. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, při vytváření koncového bodu serveru zakažte vrstvení cloudu.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neuklápejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu není aktualizován, pokud je server přejmenován.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Azure File Sync podporuje provádění změn ve sdílené složce Azure přímo. Všechny změny provedené ve sdílené složce Azure však musí být nejprve zjištěny úlohou zjišťování změn synchronizace souborů Azure. Úloha zjišťování změn se inicializuje pro koncový bod cloudu jednou za 24 hodin. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST nebude aktualizovat čas smb naposledy změněné a nebude považovat za změnu synchronizace.
- Služba synchronizace úložiště nebo účet úložiště lze přesunout do jiné skupiny prostředků nebo předplatného v rámci existujícího klienta Azure AD. Pokud se účet úložiště přesune, musíte službě hybrid File Sync Service udělit přístup k účtu úložiště (viz [Zajištění azure file sync má přístup k účtu úložiště).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync nepodporuje přesunutí předplatného do jiného klienta Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí robokopie použijte možnost /MIR k zachování časových razítek souborů. Tím zajistíte, že starší soubory budou vrstvené dříve než nedávno přistupované soubory.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.

## <a name="agent-version-5200"></a>Agent verze 5.2.0.0
Následující poznámky k verzi jsou pro verzi 5.2.0.0 agenta Azure File Sync vydaného 4. Tyto poznámky jsou navíc k poznámkám k verzi uvedeným pro verzi 5.0.2.0.

Seznam problémů opravených v této verzi:  
- Vylepšení spolehlivosti pro funkce přenosu dat offline a obnovení přenosu dat
- Synchronizace vylepšení telemetrie

## <a name="agent-version-5100"></a>Agent verze 5.1.0.0
Následující poznámky k verzi jsou pro verzi 5.1.0.0 agenta Azure File Sync vydaného 7. Tyto poznámky jsou navíc k poznámkám k verzi uvedeným pro verzi 5.0.2.0.

Seznam problémů opravených v této verzi:  
- Pokud se nezdaří počet změn na serveru, může dojít k synchronizaci souborů s chybou 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED).
- Pokud se v relaci synchronizace nebo souboru zobrazí chyba 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), synchronizace nyní operaci zopakují.
- Soubory se nemusí selhat při synchronizaci s chybou 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Při vyvolání souborů může dojít k vysokému využití paměti.
- Vylepšení telemetrie úrovně cloudu 

## <a name="agent-version-5020"></a>Agent verze 5.0.2.0
Následující poznámky k verzi jsou pro verzi 5.0.2.0 agenta Azure File Sync (vydáno 12. února 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou opraveny

- Podpora cloudu Azure Government
  - Přidali jsme podporu předběžné verze pro cloud Azure Government. To vyžaduje předplatné uvedené v seznamu white a stažení zvláštního agenta od společnosti Microsoft. Chcete-li získat přístup k náhledu, napište nám přímo na . [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)
- Podpora odstranění duplicitních dat
    - Odstranění duplicit dat je teď plně podporováno díky povolenému vrstvení cloudu v systémech Windows Server 2016 a Windows Server 2019. Povolení deduplikace na svazku s povoleným vrstvením v cloudu vám umožní ukládat do mezipaměti více souborů v místním prostředí, aniž byste museli zřazovat další úložiště.
- Podpora offline přenosu dat (např. přes datovou schránku)
    - Snadno migrujte velké množství dat do Azure File Sync pomocí všech dostupných prostředků. Můžete si vybrat Azure Data Box, AzCopy a dokonce i migrace třetích stran. Není třeba používat obrovské množství šířky pásma, abyste dostali svá data do Azure, v případě Data Boxu - jednoduše je pošlete tam! Další informace najdete [v tématu Offline Dokumenty pro přenos dat](https://aka.ms/AFS/OfflineDataTransfer).
- Zlepšený výkon synchronizace
    - Zákazníci s více koncovými body serveru na stejném svazku pravděpodobně zaznamenali před touto verzí pomalý výkon synchronizace. Azure File Sync vytvoří dočasný snímek VSS jednou denně na serveru pro synchronizaci souborů, které mají otevřené popisovače. Synchronizace nyní podporuje synchronizaci více koncových bodů serveru na svazku, když je aktivní synchronizační relace VSS. Žádné další čekání na dokončení relace synchronizace VSS, takže synchronizace může pokračovat na jiných koncových bodech serveru na svazku.
- Vylepšené monitorování na portálu
    - Na portál služby synchronizace úložiště byly přidány grafy, které chcete zobrazit:
        - Počet synchronizovaných souborů
        - Velikost přenesených dat
        - Počet nesynchronizovaných souborů
        - Velikost stažených dat
        - Stav připojení serveru
    - Další informace najdete [v tématu Sledování synchronizace souborů Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Lepší škálovatelnost a spolehlivost
    - Maximální počet objektů systému souborů (adresářů a souborů) v adresáři se zvýšil na 1 000 000. Předchozí limit byl 200 000.
    - Synchronizace se pokusí obnovit přenos dat, nikoli znovu přenášet, když je přenos přerušen pro velké soubory 

### <a name="evaluation-tool"></a>Nástroj pro hodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje pro vyhodnocení synchronizace souborů Azure. Tento nástroj je rutina prostředí Azure PowerShell, která kontroluje potenciální problémy se souborovým systémem a datovou sadou, jako jsou nepodporované znaky nebo nepodporovaná verze operačního systému. Pokyny k instalaci a použití naleznete v části [Nástroj hodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) v průvodci plánováním. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync pomocí Windows Serveru, najdete v [tématu Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md) a [Jak nasadit Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalován se zvýšenými oprávněními (oprávněními správce).
- Agent není podporován v možnostech nasazení Windows Server Core nebo Nano Server.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje alespoň 2 GiB paměti. Pokud je server spuštěn ve virtuálním počítači s povolenou dynamickou pamětí, virtuální počítač by měl být nakonfigurován s minimálně 2048 MiB paměti.
- Služba Storage Sync Agent (FileSyncSvc) nepodporuje koncové body serveru umístěné na svazku, který má komprimovaný adresář s informacemi o systémovém svazku (SVI). Tato konfigurace povede k neočekávaným výsledkům.
- Režim FIPS není podporován a musí být zakázán. 

### <a name="interoperability"></a>Interoperabilita
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete [v tématu Poradce při potížích se synchronizací souborů Azure](storage-sync-files-troubleshoot.md).
- Blokování souborů Správce prostředků souborového serveru (FSRM) může způsobit nekonečné selhání synchronizace při blokování souborů z důvodu blokování souborů.
- Spuštění programu sysprep na serveru, na kterém je nainstalován agent Azure File Sync, není podporováno a může vést k neočekávaným výsledkům. Agent Azure File Sync by měl být nainstalován po nasazení image serveru a dokončení miniinstalace programu sysprep.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete [v příručce poradce při potížích.](storage-sync-files-troubleshoot.md#handling-unsupported-characters)
- Soubory nebo adresáře, které končí tečkou.
- Cesty delší než 2 048 znaků.
- Část volitelného seznamu řízení přístupu (DACL) popisovače zabezpečení, pokud je větší než 2 kB. (To je problém pouze v případě, že pro jednu položku máte více než přibližně 40 položek řízení přístupu.)
- Část seznamu řízení auditování přístupu (SACL) popisovače zabezpečení, která se používá se pro auditování.
- Rozšířené atributy.
- Alternativní datové proudy.
- Body rozboru.
- Pevné odkazy.
- Komprese (pokud je nastavená pro soubor na serveru) se nezachová při synchronizaci změn do tohoto souboru z ostatních koncových bodů.
- Všechny soubory šifrované pomocí systému souborů EFS (nebo jiného šifrování v uživatelském režimu), který brání službě ve čtení těchto dat.

    > [!Note]  
    > Synchronizace souborů Azure vždy šifruje přenášená data. Neaktivní uložená data se vždy šifrují v Azure.
 
### <a name="server-endpoint"></a>Koncový bod serveru
- Koncový bod serveru je možné vytvořit pouze na svazku NTFS. Synchronizace souborů Azure v současné době nepodporuje systémy souborů ReFS, FAT, FAT32 a jiné.
- Vrstvené soubory se stanou nepřístupné, pokud soubory nejsou odvolány před odstraněním koncového bodu serveru. Chcete-li obnovit přístup k souborům, vytvořte koncový bod serveru znovu. Pokud uplynulo 30 dní od odstranění koncového bodu serveru nebo pokud byl odstraněn koncový bod cloudu, vrstvené soubory, které nebyly odvolány, budou nepoužitelné.
- Vrstvení cloudu se na systémovém svazku nepodporuje. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, při vytváření koncového bodu serveru zakažte vrstvení cloudu.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neuklápejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu není aktualizován, pokud je server přejmenován.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Azure File Sync podporuje provádění změn ve sdílené složce Azure přímo. Všechny změny provedené ve sdílené složce Azure však musí být nejprve zjištěny úlohou zjišťování změn synchronizace souborů Azure. Úloha zjišťování změn se inicializuje pro koncový bod cloudu jednou za 24 hodin. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST nebude aktualizovat čas smb naposledy změněné a nebude považovat za změnu synchronizace.
- Služba synchronizace úložiště nebo účet úložiště lze přesunout do jiné skupiny prostředků nebo předplatného v rámci existujícího klienta Azure AD. Pokud se účet úložiště přesune, musíte službě hybrid File Sync Service udělit přístup k účtu úložiště (viz [Zajištění azure file sync má přístup k účtu úložiště).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync nepodporuje přesunutí předplatného do jiného klienta Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí robokopie použijte možnost /MIR k zachování časových razítek souborů. Tím zajistíte, že starší soubory budou vrstvené dříve než nedávno přistupované soubory.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.
