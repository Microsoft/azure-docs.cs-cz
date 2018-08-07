---
title: Poznámky k verzi pro agenta Azure File Sync | Dokumentace Microsoftu
description: Poznámky k verzi pro agenta Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 69cd7774c92cf1c213f8522dffeb02be6c024acb
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525133"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Poznámky k verzi pro agenta Azure File Sync
Azure File Sync umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Vaše instalace Windows Serveru se transformují na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS). Můžete mít libovolný počet mezipamětí po celém světě.

Tento článek obsahuje poznámky k podporovaným verzím agenta Azure File Sync.

## <a name="supported-versions"></a>Podporované verze
Agent Azure File Sync podporuje následující verze:

| Milník | Číslo verze agenta | Datum vydání | Status |
|----|----------------------|--------------|------------------|
| Obecná dostupnost | 3.1 | 19. července 2018 | Podporované (doporučená verze) |
| S kumulativní aktualizací z června | 3.0.13.0 | 29. června 2018 | Agent verze vyprší 4. září 2018 |
| Aktualizace 2 | 3.0.12.0 | 22. května 2018 | Agent verze vyprší 4. září 2018 |
| Kumulativní aktualizace dne | 2.3.0.0 | 8. května 2018 | Agent verze vyprší 4. září 2018 |
| Kumulativní aktualizace. března | 2.2.0.0 | 12. března 2018 | Agent verze vyprší 4. září 2018 |
| Kumulativní aktualizace. února | 2.1.0.0 | 28. února 2018 | Agent verze vyprší 4. září 2018 |
| Aktualizace 1 | 2.0.11.0 | 8. února 2018 | Agent verze vyprší 4. září 2018 |
| Od kumulativní aktualizace | 1.4.0.0 | 8. ledna 2018 | Agent verze vyprší 4. září 2018 |
| S kumulativní aktualizací z listopadu | 1.3.0.0 | 30. listopadu 2017 | Agent verze vyprší 4. září 2018 |
| Říjen kumulativní aktualizace | 1.2.0.0 | 31. října 2017 | Agent verze vyprší 4. září 2018 |
| Počáteční verze Preview | 1.1.0.0 | 26. září 2017 | Agent verze vyprší 4. září 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-3100"></a>Verze agenta 3.1.0.0
Následující poznámky k verzi platí pro verze 3.1.0.0 agenta Azure File Sync (vydané 19. července 2018).

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync se systémem Windows Server najdete v tématu [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) a [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný oprávnění se zvýšenými oprávněními (správce).
- Agent se nepodporuje v možnostech nasazení Windows Server Core nebo Nano Server.
- Agent se podporuje pouze ve Windows Serveru 2016 a Windows Serveru 2012 R2.
- Agent vyžaduje alespoň 2 GB fyzické paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nachází na svazku, který má systémový svazek informace (SVI) adresář komprimované. Tato konfigurace bude vést k neočekávaným výsledkům.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu [Poradce při potížích s Azure File Sync](storage-sync-files-troubleshoot.md).
- Nepoužívejte blokování souborů služby Správce prostředků souborového serveru ani jiné blokování souborů. Blokování souborů může způsobit chyby nekonečné synchronizace, pokud jsou kvůli němu soubory blokované.
- Spuštění příkazu sysprep na serveru, který má nainstalovaného agenta Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Instalace a server registrace agenta se budou objevovat po nasazení bitové kopie serveru a dokončení zkrácené instalace nástroje sysprep.
- Odstranění duplicit dat a vrstvení cloudu se nepodporuje na stejném svazku.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
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
    > Azure File Sync vždy šifruje přenášená data. Neaktivní uložená data se vždy šifrují v Azure.
 
### <a name="server-endpoint"></a>Koncový bod serveru
- Koncový bod serveru je možné vytvořit pouze na svazku NTFS. Azure File Sync v současné době nepodporuje systémy souborů ReFS, FAT, FAT32 a jiné.
- Vrstvené soubory už nepůjdou použít, pokud nejsou soubory připomenout, před odstraněním koncový bod serveru.
- Cloud nepodporuje ovládání datových vrstev na systémovém svazku. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, zakažte vrstvení cloudu po vytvoření koncového bodu serveru.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neukládejte stránkovací soubor operačního systému nebo aplikace, který je na koncovém bodu serveru.
- Název serveru na portálu není aktualizován, pokud bylo přejmenováno na serveru. Chcete-li aktualizovat název serveru na portálu, zrušte registraci a server znovu zaregistrovat.

### <a name="cloud-endpoint"></a>Koncový bod cloudu.
- Azure File Sync podporuje provádění změn přímo do sdílené složky Azure. Však všechny změny provedené na sdílenou složku Azure nejprve mají být zjišťované úlohou detekce změn v Azure File Sync. Pro koncový bod cloudu jednou za 24 hodin se spustí úloha zjišťování změn. Kromě toho změny provedené do sdílené složky Azure přes protokol REST neaktualizuje SMB čas poslední změny a se projeví jako změnu synchronizace.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.

## <a name="agent-version-30130"></a>Verze agenta 3.0.13.0
Následující poznámky k verzi platí pro verze 3.0.13.0 agenta Azure File Sync vydaného 29. června 2018. Tyto poznámky doplňují uvedené verze 3.0.12.0 poznámky.

Tato verze zahrnuje následující opravy:
- Body rozboru synchronizace nezdaří, pokud je server se přidá k existující skupině synchronizace, pokud existují v umístění koncového bodu serveru na serveru.

## <a name="agent-version-30120"></a>Verze agenta 3.0.12.0
Následující poznámky k verzi platí pro verze 3.0.12.0 agenta Azure File Sync (vydané 22. května 2018).

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync se systémem Windows Server najdete v tématu [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) a [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný oprávnění se zvýšenými oprávněními (správce).
- Agent se nepodporuje v možnostech nasazení Windows Server Core nebo Nano Server.
- Agent se podporuje pouze ve Windows Serveru 2016 a Windows Serveru 2012 R2.
- Agent vyžaduje alespoň 2 GB fyzické paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nachází na svazku, který má systémový svazek informace (SVI) adresář komprimované. Tato konfigurace bude vést k neočekávaným výsledkům.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu [Poradce při potížích s Azure File Sync](storage-sync-files-troubleshoot.md).
- Nepoužívejte blokování souborů služby Správce prostředků souborového serveru ani jiné blokování souborů. Blokování souborů může způsobit chyby nekonečné synchronizace, pokud jsou kvůli němu soubory blokované.
- Spuštění příkazu sysprep na serveru, který má nainstalovaného agenta Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Instalace a server registrace agenta se budou objevovat po nasazení bitové kopie serveru a dokončení zkrácené instalace nástroje sysprep.
- Odstranění duplicit dat a vrstvení cloudu se nepodporuje na stejném svazku.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
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
    > Azure File Sync vždy šifruje přenášená data. Neaktivní uložená data se vždy šifrují v Azure.
 
### <a name="server-endpoints"></a>Koncové body serveru
- Koncový bod serveru je možné vytvořit pouze na svazku NTFS. Azure File Sync v současné době nepodporuje systémy souborů ReFS, FAT, FAT32 a jiné.
- Cloud nepodporuje ovládání datových vrstev na systémovém svazku. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, zakažte vrstvení cloudu po vytvoření koncového bodu serveru.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neukládejte stránkovací soubor operačního systému nebo aplikace, který je na koncovém bodu serveru.
- Vrstvené soubory už nepůjdou použít, pokud nejsou soubory připomenout, před odstraněním koncový bod serveru.
 
### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.

## <a name="agent-version-2300"></a>Agent verze 2.3.0.0
Následující poznámky k verzi platí pro verze 2.3.0.0 agenta Azure File Sync vydáno 8. května 2018. Tyto poznámky doplňují uvedené poznámky k verzi 2.0.11.0.

Tato verze zahrnuje následující opravy:
- Aktualizace agenta může přestat reagovat, pokud ovladač filtru vrstvení cloudu se neuvolní.
- Při synchronizaci velké množství souborů, může se snížit výkon synchronizace.

## <a name="agent-version-2200"></a>Verze agenta 2.2.0.0
Následující poznámky k verzi platí pro verze 2.2.0.0 agenta Azure File Sync vydáno 12. března 2018.  Tyto poznámky doplňují uvedené verze 2.1.0.0 a 2.0.11.0 poznámky

Instalace v2.1.0.0 pro některé zákazníky selže z důvodu FileSyncSvc není se zastavuje. Tato aktualizace řeší tento problém.

## <a name="agent-version-2100"></a>Agent verze 2.1.0.0
Následující poznámky k verzi platí pro agenta Azure File Sync verze 2.1.0.0 vydaného 28. února 2018. Tyto poznámky doplňují uvedené poznámky k verzi 2.0.11.0.

Tato vydaná verze zahrnuje následující změny:
- Zlepšení zpracování převzetí služeb při selhání clusteru.
- Zlepšení spolehlivého zpracování vrstvených souborů.
- Podpora instalace agenta na počítačích řadiče domény přidaných do doménového prostředí Windows Serveru 2008 R2.
- Oprava v této vydané verzi: Nadměrné generování diagnostiky na serverech s velkým množstvím souborů.
- Zlepšení zpracování chyb v případě selhání relace.
- Zlepšení zpracování chyb v případě problémů s přenosem souborů.
- Změna v této vydané verzi: Výchozí interval spouštění vrstvení cloudu po povolení na koncovém bodu služby je nyní 1 hodina. 
- Dočasný blokující problém: Přesun prostředků Azure File Sync (služba synchronizace úložiště) do nového předplatného Azure.

## <a name="agent-version-20110"></a>Agent verze 2.0.11.0
Následující poznámky k verzi platí pro agenta Azure File Sync verze 2.0.11.0 (vydaného 9. února 2018). 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync se systémem Windows Server najdete v tématu [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) a [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta (MSI) se musí nainstalovat se zvýšenými oprávněními (správce).
- Agent se nepodporuje v možnostech nasazení Windows Server Core nebo Nano Server.
- Agent se podporuje pouze ve Windows Serveru 2016 a Windows Serveru 2012 R2.
- Agent vyžaduje alespoň 2 GB fyzické paměti.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu [Poradce při potížích s Azure File Sync](storage-sync-files-troubleshoot.md).
- Tato verze přidává podporu DFS-R. Další informace najdete v [průvodci plánováním](storage-sync-files-planning.md#distributed-file-system-dfs).
- Nepoužívejte blokování souborů služby Správce prostředků souborového serveru ani jiné blokování souborů. Blokování souborů může způsobit chyby nekonečné synchronizace, pokud jsou kvůli němu soubory blokované.
- Duplikace registrovaných serverů (včetně klonování virtuálních počítačů) může vést k neočekávaným výsledkům. Konkrétně se synchronizace nemusí nikdy sloučit.
- Odstranění duplicit dat a vrstvení cloudu se nepodporuje na stejném svazku.
 
### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
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
    > Azure File Sync vždy šifruje přenášená data. Neaktivní uložená data se vždy šifrují v Azure.
 
### <a name="server-endpoints"></a>Koncové body serveru
- Koncový bod serveru je možné vytvořit pouze na svazku NTFS. Azure File Sync v současné době nepodporuje systémy souborů ReFS, FAT, FAT32 a jiné.
- Koncový bod serveru nemůže být na systémovém svazku. Například C:\moje_složka není přijatelná cesta, pokud C:\moje_složka není bod připojení.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Tato verze přidává podporu kořenového adresáře synchronizace v kořenovém adresáři svazku.
- Neukládejte stránkovací soubor operačního systému nebo aplikace, který je na koncovém bodu serveru.
- Změna v této vydané verzi: Přidání nových událostí pro sledování celkové doby vrstvení cloudu (ID události 9016), průběhu nahrávání synchronizace (ID události 9302) a souborů, které se nesynchronizovaly (ID události 9900).
- Vylepšení v této verzi: 
- Výrazné zvýšení výkonu synchronizace oboru názvů rychlé zotavení po Havárii.
- Odstranění velkého počtu (více než 10 000) adresářů není nutné provádět v dávkách s v2 *.
 
### <a name="cloud-tiering"></a>Vrstvení cloudu
- Změna oproti předchozí verzi: Vrstvení nových souborů proběhne do 1 hodiny (dříve to bylo 32 hodin) v souladu s nastavením zásad vrstvení. Pro vrstvení na vyžádání poskytujeme rutinu PowerShellu. Pomocí této rutiny můžete vrstvení vyhodnotit efektivněji a bez čekání na procesy na pozadí.
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.
- Změna oproti předchozí verzi: Soubory se teď na jiných serverech stahují jako vrstvené soubory za předpokladu, že je soubor nový nebo už je vrstvený.

## <a name="agent-version-1100"></a>Agent verze 1.1.0.0
Následující poznámky k verzi platí pro agenta Azure File Sync verze 1.1.0.0 (vydaného 9. září 2017 v počáteční verzi Preview). 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync se systémem Windows Server najdete v tématu [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) a [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta (MSI) se musí nainstalovat se zvýšenými oprávněními (správce).
- Agent se nepodporuje v možnostech nasazení Windows Server Core nebo Nano Server.
- Agent se podporuje pouze ve Windows Serveru 2016 a 2012 R2.
- Agent vyžaduje alespoň 2 GB fyzické paměti.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu [Poradce při potížích s Azure File Sync](storage-sync-files-troubleshoot.md).
- Nepoužívejte blokování souborů služby Správce prostředků souborového serveru ani jiné blokování souborů. Blokování souborů může způsobit chyby nekonečné synchronizace, pokud jsou kvůli němu soubory blokované.
- Duplikace registrovaných serverů (včetně klonování virtuálních počítačů) může vést k neočekávaným výsledkům. Konkrétně se synchronizace nemusí nikdy sloučit.
- Odstranění duplicit dat a vrstvení cloudu se nepodporuje na stejném svazku.
 
### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Cesty delší než 2 048 znaků.
- Část DACL popisovače zabezpečení, pokud je větší než 2 kB. (To je problém pouze v případě, že pro jednu položku máte více než přibližně 40 položek řízení přístupu.)
- Část SACL popisovače zabezpečení, která se používá pro auditování.
- Rozšířené atributy.
- Alternativní datové proudy.
- Body rozboru.
- Pevné odkazy.
- Komprese (pokud je nastavená pro soubor na serveru) se nezachová při synchronizaci změn do tohoto souboru z ostatních koncových bodů.
- Všechny soubory šifrované pomocí systému souborů EFS (nebo jiného šifrování v uživatelském režimu), který brání službě ve čtení těchto dat. 
    
    > [!Note]  
    > Azure File Sync vždy šifruje přenášená data. Neaktivní uložená data se vždy šifrují v Azure.
 
### <a name="server-endpoints"></a>Koncové body serveru
- Koncový bod serveru je možné vytvořit pouze na svazku NTFS. Azure File Sync v současné době nepodporuje systémy souborů ReFS, FAT, FAT32 a jiné.
- Koncový bod serveru nemůže být na systémovém svazku. Například C:\moje_složka není přijatelná cesta, pokud C:\moje_složka není bod připojení.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, a ne se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Odstranění velkého počtu (více než 10 000) adresářů ze serveru najednou může způsobit chyby synchronizace. Odstraňujte adresáře v dávkách po méně než 10 000. Před odstraněním další dávky se ujistěte, že se operace odstranění úspěšně synchronizovaly.
- Koncový bod serveru v kořenovém adresáři svazku se v současné době nepodporuje.
- Neukládejte stránkovací soubor operačního systému nebo aplikace, který je na koncovém bodu serveru.
 
### <a name="cloud-tiering"></a>Vrstvení cloudu
- Za účelem zajištění možnosti správného odvolání souborů systém nemusí provést automatické vrstvení nových nebo změněných souborů po dobu až 32 hodin. Tento proces zahrnuje i první vrstvení po konfiguraci nového koncového bodu serveru. Pro vrstvení na vyžádání poskytujeme rutinu PowerShellu. Pomocí této rutiny můžete vrstvení vyhodnotit efektivněji a bez čekání na procesy na pozadí.
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.
