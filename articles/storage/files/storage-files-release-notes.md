---
title: Poznámky k verzi agenta Synchronizace souborů Azure | Microsoft Docs
description: Přečtěte si poznámky k verzi pro agenta Synchronizace souborů Azure, který vám umožní centralizovat sdílené složky ve vaší organizaci ve službě soubory Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/3/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 5549fc3b63b76c6158ae7399e6d94a43d2d4f28f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435184"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Poznámky k verzi pro agenta Synchronizace souborů Azure
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Vaše instalace Windows Serveru se transformují na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS). Můžete mít libovolný počet mezipamětí po celém světě.

Tento článek obsahuje poznámky k podporovaným verzím agenta Synchronizace souborů Azure.

## <a name="supported-versions"></a>Podporované verze
Podporovány jsou následující verze agenta Synchronizace souborů Azure:

| RTM | Číslo verze agenta | Datum vydání | Status |
|----|----------------------|--------------|------------------|
| V 11.2 release – [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2. února 2021 | Podporováno |
| V 11.1 release – [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4. listopadu 2020 | Podporováno |
| V 10.1 verze – [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5. června 2020 | Verze Supported agenta vyprší 7. června 2021 |
| Kumulativní aktualizace květen 2020 – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19. května 2020 | Verze Supported agenta vyprší 7. června 2021 |
| Verze V10 za účelem – [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9. dubna 2020 | Verze Supported agenta vyprší 7. června 2021 |

## <a name="unsupported-versions"></a>Nepodporované verze
Následující verze agenta Synchronizace souborů Azure prošly a již nejsou podporovány:

| RTM | Číslo verze agenta | Datum vydání | Status |
|----|----------------------|--------------|------------------|
| Verze v9 | 9.0.0.0 - 9.1.0.0 | – | Nepodporováno – platnost verze agenta vypršela 16. února 2021. |
| Verze V8 | 8.0.0.0 | – | Nepodporováno – platnost verze agenta vypršela 12. ledna 2021 |
| Verze v7 | 7.0.0.0 - 7.2.0.0 | – | Nepodporované – verze agentů vypršela od 1. září 2020 |
| Verze V6 | 6.0.0.0 - 6.3.0.0 | – | Nepodporováno – platnost verzí agentů vypršela 21. dubna 2020 |
| Verze verze 5 | 5.0.2.0 - 5.2.0.0 | – | Nepodporováno – platnost verzí agentů vypršela 18. března 2020 |
| Verze v4 | 4.0.1.0 - 4.3.0.0 | – | Nepodporované – verze agentů vypršela 6. listopadu 2019 |
| Verze v3 | 3.1.0.0 - 3.4.0.0 | – | Nepodporované – platnost verzí agenta vyprší 19. srpna 2019 |
| Agenti pre-GA | 1.1.0.0 – 3.0.13.0 | – | Nepodporováno – platnost verzí agentů vypršela 1. října 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Synchronizace souborů Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11200"></a>Verze agenta 11.2.0.0
Následující poznámky k verzi jsou pro verzi 11.2.0.0 agenta Synchronizace souborů Azure vydané 2. února 2021. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou vyřešeny 
- Pokud se relace synchronizace zruší kvůli vysokému počtu chyb na jednu položku, může synchronizace procházet pomocí odsouhlasení, když služba Synchronizace souborů Azure určí, jestli je pro opravu chyb jednotlivých položek nutná vlastní relace synchronizace.
- Registrace serveru pomocí rutiny Register-AzStorageSyncServer může selhat s chybou "Neošetřená výjimka".
- Nová rutina prostředí PowerShell (Add-StorageSyncAllowedServerEndpointPath) pro konfiguraci povolených cest koncových bodů serveru na serveru. Tato rutina je užitečná ve scénářích, ve kterých je nasazení Synchronizace souborů Azure spravované poskytovatelem Cloud Solution Provider (CSP) nebo poskytovatelem služeb a zákazníkem chce nakonfigurovat na serveru povolené cesty k koncovým bodům serveru. Pokud při vytváření koncového bodu serveru není zadaná cesta v seznamu povolených, vytvoření koncového bodu serveru se nezdařilo. Poznámka: Toto je volitelná funkce a při vytváření koncového bodu serveru jsou ve výchozím nastavení povolené všechny podporované cesty.  

    
    - Pokud chcete přidat cestu koncového bodu serveru, která je povolená, spusťte na serveru následující příkazy PowerShellu:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Chcete-li získat seznam podporovaných cest, spusťte následující příkaz prostředí PowerShell:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Chcete-li odebrat cestu, spusťte následující příkaz prostředí PowerShell:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Verze agenta 11.1.0.0
Následující poznámky k verzi jsou pro 11.1.0.0 agenta Synchronizace souborů Azure (vydané 4. listopadu 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou vyřešeny
- Nové režimy vrstvení cloudu pro řízení počátečního stahování a proaktivní odvolání
    - Režim počátečního stahování: teď můžete zvolit, jak se mají vaše soubory na nový koncový bod serveru stáhnout. Chcete, aby všechny vaše soubory byly vrstveny nebo kolik souborů bylo možné stáhnout na server podle poslední změny časového razítka? Můžete to udělat! Nemůžete použít vrstvení cloudu? Nyní se můžete rozhodnout, že nebudete mít v systému vrstvené soubory. Další informace najdete v části [Vytvoření koncového bodu serveru](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) v dokumentaci k nasazení synchronizace souborů Azure.
    - Režim proaktivní odvolání: pokaždé, když se soubor vytvoří nebo upraví, můžete ho aktivně odvolat na servery, které zadáte v rámci stejné skupiny synchronizace. Díky tomu bude soubor na každém serveru, který jste zadali, snadno dostupný pro spotřebu. Pracujete s týmy na celém světě a pracují se stejnými daty? Povolit proaktivní revolání, aby když tým dorazí na další ráno, stáhnou se všechny soubory aktualizované týmem v jiném časovém pásmu a budou připravené k použití! Další informace najdete v části [proaktivní odvolání nových a změněných souborů z oddílu sdílení souborů Azure](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) v dokumentaci k nasazení synchronizace souborů Azure.

- Vylučte aplikace z vrstev cloudu čas posledního přístupu, teď můžete vyloučit aplikace ze sledování času posledního přístupu. Když aplikace přistupuje k souboru, v databázi vrstev cloudu se aktualizuje čas posledního přístupu k souboru. Aplikace, které prohledávají systém souborů, jako je anti-virus, způsobí, že všechny soubory mají stejný čas posledního přístupu, který má vliv na vrstvení souborů.

    Pokud chcete vyloučit aplikace ze sledování času posledního přístupu, přidejte název procesu do nastavení registru HeatTrackingProcessNameExclusionList, které je umístěné v části HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Příklad: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

    > [!Note]  
    > Odstranění duplicitních dat a procesy Správce prostředků souborového Správce prostředků serveru jsou ve výchozím nastavení vyloučené (pevně zakódované) a seznam vyloučení procesů se aktualizuje každých 5 minut.

- Různá vylepšení výkonu a spolehlivosti
    - Vylepšený výkon detekce změn, aby se zjistily soubory, které se ve sdílené složce Azure změnily.
    - Vylepšený výkon nahrávání synchronizace.
    - Počáteční nahrávání se teď provádí ze snímku služby VSS, což snižuje chyby jednotlivých položek a selhání relace synchronizace.
    - Synchronizace vylepšení spolehlivosti pro určité vstupně-výstupní vzory.
    - Opravili jsme chybu, aby synchronizovaná databáze nemohla v případě převzetí služeb při selhání přejít do clusterů s podporou převzetí služeb při selhání v čase.
    - Lepší výkon při odvolání při přístupu k vrstvenému souboru.

### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Synchronizace souborů Azure byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje Synchronizace souborů Azure Evaluation Tool. Tento nástroj je Azure PowerShell rutinou, která kontroluje potenciální problémy se systémem souborů a datovou sadou, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny k instalaci a používání najdete v části [Nástroj pro vyhodnocení](./storage-sync-files-planning.md#evaluation-cmdlet) v příručce pro plánování. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Synchronizace souborů Azure s Windows serverem, najdete v tématu [Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md) a [postup nasazení synchronizace souborů Azure](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný se zvýšenými oprávněními (správce).
- Možnost nasazení nano serveru nepodporuje agenta.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje aspoň 2 GiB paměti. Pokud server běží na virtuálním počítači s povolenou dynamickou pamětí, měl by být virtuální počítač nakonfigurovaný s minimální 2048 MiB paměti. Další informace najdete v tématu [Doporučené systémové prostředky](./storage-sync-files-planning.md#recommended-system-resources) .
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nacházející se na svazku, který má komprimovaný adresář systému SVI (System Volume Information). Tato konfigurace bude mít za následek neočekávané výsledky.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu věnovaném [řešení potíží s synchronizace souborů Azure](storage-sync-files-troubleshoot.md).
- Blokování souborů (Správce prostředků souborového Správce prostředků serveru) může způsobit nekonečná selhání synchronizace, když jsou soubory blokované z důvodu blokování souborů.
- Spuštění nástroje Sysprep na serveru s nainstalovaným agentem Synchronizace souborů Azure není podporováno a může vést k neočekávaným výsledkům. Agent Synchronizace souborů Azure by měl být nainstalovaný po nasazení image serveru a dokončení zkrácené instalace nástroje Sysprep.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete v [Průvodci odstraňováním potíží](storage-sync-files-troubleshoot.md#handling-unsupported-characters) .
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
- Neukládejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu se neaktualizuje, pokud se server přejmenuje.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Synchronizace souborů Azure podporuje přímé provádění změn ve sdílené složce Azure. Všechny změny provedené ve sdílené složce Azure se ale nejdřív musí zjistit pomocí úlohy zjišťování změn Synchronizace souborů Azure. Úloha detekce změn se iniciuje pro koncový bod cloudu každých 24 hodin. K okamžité synchronizaci souborů, které se změnily ve sdílené složce Azure, se dá použít rutina PowerShellu [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) k ručnímu spuštění rozpoznávání změn ve sdílené složce Azure. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST neaktualizují čas poslední změny protokolu SMB a nebudou se zobrazovat jako změny v synchronizaci.
- Službu synchronizace úložiště nebo účet úložiště můžete přesunout do jiné skupiny prostředků, předplatného nebo tenanta Azure AD. Po přesunu služby synchronizace úložiště nebo účtu úložiště musíte aplikaci Microsoft. StorageSync udělit přístup k účtu úložiště ( [Zkontrolujte, jestli synchronizace souborů Azure má přístup k účtu úložiště](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Při vytváření koncového bodu cloudu musí být služba synchronizace úložiště a účet úložiště ve stejném tenantovi Azure AD. Po vytvoření koncového bodu cloudu se služba synchronizace úložiště a účet úložiště můžou přesunout do různých tenantů Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí nástroje Robocopy použijte možnost/MIR, která zachová časová razítka souborů. Tím se zajistí, že se starší soubory budou vrstvit dřív než nedávno používané soubory.
    > [!Warning]  
    > Příkaz Robocopy/B není u Synchronizace souborů Azure podporován. Použití přepínače Robocopy/B s koncovým bodem serveru Synchronizace souborů Azure jako zdroj může vést k poškození souboru.

## <a name="agent-version-10100"></a>Verze agenta 10.1.0.0
Následující poznámky k verzi jsou pro 10.1.0.0 verze agenta Synchronizace souborů Azure vydání 5. června 2020. Tyto poznámky se doplňují od poznámky k verzi uvedené pro verze 10.0.0.0 a 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou vyřešeny

- Podpora privátního koncového bodu Azure
    - Synchronizace provozu do služby synchronizace úložiště se teď dá poslat do privátního koncového bodu. To umožňuje tunelování přes ExpressRoute nebo připojení VPN. Další informace najdete v tématu [Konfigurace koncových bodů sítě synchronizace souborů Azure](./storage-sync-files-networking-endpoints.md).
- Když je spuštěná Velká synchronizace, bude se teď zobrazovat průběh souborů, na kterých se synchronizuje metrika, ale na konci.
- Různá vylepšení spolehlivosti pro instalaci agenta, vrstvení cloudu, synchronizaci a telemetrii

## <a name="agent-version-10020"></a>Verze agenta 10.0.2.0
Následující poznámky k verzi jsou pro 10.0.2.0 verze agenta Synchronizace souborů Azure vydání 19. května 2020. Tyto poznámky se doplňují od poznámky k verzi uvedené pro verzi 10.0.0.0.

Problém opravený v této verzi:  
- Agent synchronizace úložiště (FileSyncSvc) se často zhroutí po instalaci agenta Synchronizace souborů Azure v10 za účelem.

> [!Note]  
>Tato verze se nepoužila na servery, které jsou nakonfigurované tak, aby se automaticky aktualizovaly, když bude dostupná nová verze. K instalaci této aktualizace použijte Microsoft Update nebo katalog služby Microsoft Update (pokyny k instalaci najdete v tématu [KB4522412](https://support.microsoft.com/help/4522412) ).

## <a name="agent-version-10000"></a>Verze agenta 10.0.0.0
Následující poznámky k verzi jsou pro verzi 10.0.0.0 agenta Synchronizace souborů Azure (vydané 9. dubna 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou vyřešeny

- Vylepšený průběh synchronizace na portálu
    - S Azure Portal vydáním agenta v10 za účelem se brzy začne zobrazovat typ relace synchronizace, která je spuštěná. Například počáteční stažení, běžný stažení, odvolání na pozadí (rychlé případy zotavení po havárii) a podobné. 

- Vylepšené prostředí portálu pro vrstvení cloudu
    - Pokud máte soubory, které selhaly při úrovni nebo navracení, můžete nyní zobrazit chyby vrstvení ve vlastnostech koncového bodu serveru.
    - Další informace o vrstvení cloudu jsou k dispozici pro koncový bod serveru:
        - Velikost místní mezipaměti
        - Efektivita využití mezipaměti
        - Podrobnosti o vrstvách cloudu: velikost svazku, aktuální volné místo nebo čas posledního otevření nejstaršího souboru v místní mezipaměti.
    - Tyto změny se dostanou do Azure Portal krátce po úvodním vydání agenta v10 za účelem.

- Podpora přesunutí služby synchronizace úložiště nebo účtu úložiště do jiného Azure Active Directoryho tenanta
    - Synchronizace souborů Azure teď podporuje přesun služby synchronizace úložiště nebo účtu úložiště do jiné skupiny prostředků, předplatného nebo tenanta Azure AD.
    
- Různá vylepšení výkonu a spolehlivosti
    - Pokud jsou virtuální síť a pravidla brány firewall nakonfigurované v účtu úložiště, může zjišťování změn ve sdílené složce Azure selhat.
    - Snížená spotřeba paměti přidružená k odvolání. 
    - Vylepšený výkon při použití rutiny [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .
    - Další různá vylepšení spolehlivosti. 
    
### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Synchronizace souborů Azure byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje Synchronizace souborů Azure Evaluation Tool. Tento nástroj je Azure PowerShell rutinou, která kontroluje potenciální problémy se systémem souborů a datovou sadou, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny k instalaci a používání najdete v části [Nástroj pro vyhodnocení](./storage-sync-files-planning.md#evaluation-cmdlet) v příručce pro plánování. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Synchronizace souborů Azure s Windows serverem, najdete v tématu [Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md) a [postup nasazení synchronizace souborů Azure](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný se zvýšenými oprávněními (správce).
- Možnost nasazení nano serveru nepodporuje agenta.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje aspoň 2 GiB paměti. Pokud server běží na virtuálním počítači s povolenou dynamickou pamětí, měl by být virtuální počítač nakonfigurovaný s minimální 2048 MiB paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nacházející se na svazku, který má komprimovaný adresář systému SVI (System Volume Information). Tato konfigurace bude mít za následek neočekávané výsledky.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu věnovaném [řešení potíží s synchronizace souborů Azure](storage-sync-files-troubleshoot.md).
- Blokování souborů (Správce prostředků souborového Správce prostředků serveru) může způsobit nekonečná selhání synchronizace, když jsou soubory blokované z důvodu blokování souborů.
- Spuštění nástroje Sysprep na serveru s nainstalovaným agentem Synchronizace souborů Azure není podporováno a může vést k neočekávaným výsledkům. Agent Synchronizace souborů Azure by měl být nainstalovaný po nasazení image serveru a dokončení zkrácené instalace nástroje Sysprep.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete v [Průvodci odstraňováním potíží](storage-sync-files-troubleshoot.md#handling-unsupported-characters) .
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
- Neukládejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu se neaktualizuje, pokud se server přejmenuje.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Synchronizace souborů Azure podporuje přímé provádění změn ve sdílené složce Azure. Všechny změny provedené ve sdílené složce Azure se ale nejdřív musí zjistit pomocí úlohy zjišťování změn Synchronizace souborů Azure. Úloha detekce změn se iniciuje pro koncový bod cloudu každých 24 hodin. K okamžité synchronizaci souborů, které se změnily ve sdílené složce Azure, se dá použít rutina PowerShellu [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) k ručnímu spuštění rozpoznávání změn ve sdílené složce Azure. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST neaktualizují čas poslední změny protokolu SMB a nebudou se zobrazovat jako změny v synchronizaci.
- Službu synchronizace úložiště nebo účet úložiště můžete přesunout do jiné skupiny prostředků, předplatného nebo tenanta Azure AD. Po přesunu služby synchronizace úložiště nebo účtu úložiště musíte aplikaci Microsoft. StorageSync udělit přístup k účtu úložiště ( [Zkontrolujte, jestli synchronizace souborů Azure má přístup k účtu úložiště](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Při vytváření koncového bodu cloudu musí být služba synchronizace úložiště a účet úložiště ve stejném tenantovi Azure AD. Po vytvoření koncového bodu cloudu se služba synchronizace úložiště a účet úložiště můžou přesunout do různých tenantů Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí nástroje Robocopy použijte možnost/MIR, která zachová časová razítka souborů. Tím se zajistí, že se starší soubory budou vrstvit dřív než nedávno používané soubory.
