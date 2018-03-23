---
title: Poznámky k verzi pro agenta Azure File Sync (Preview) | Microsoft Docs
description: Poznámky k verzi pro agenta synchronizace souboru Azure (preview).
services: storage
author: wmgries
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/12/2018
ms.author: wgries
ms.openlocfilehash: b42287580078b4391ddbc5b8ff2835131c64236d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="release-notes-for-the-azure-file-sync-agent-preview"></a>Poznámky k verzi pro agenta Azure File Sync (Preview)
Azure File Sync umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Vaše instalace Windows Serveru se transformují na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS). Můžete mít libovolný počet mezipamětí po celém světě.

Tento článek obsahuje poznámky k podporovaným verzím agenta Azure File Sync.

## <a name="supported-versions"></a>Podporované verze
Agent Azure File Sync podporuje následující verze:

| Milník | Číslo verze agenta | Datum vydání | Status |
|----|----------------------|--------------|------------------|
| Kumulativní aktualizace března | 2.2.0.0 | 12. březen 2018 | Podporovaná (doporučená verze) |
| Kumulativní aktualizace února | 2.1.0.0 | 28. února 2018 | Podporováno |
| Aktualizujte 1 | 2.0.11.0 | 8. února 2018 | Podporováno |
| Leden kumulativní aktualizace | 1.4.0.0 | 8 leden 2018 | Podporovaná do 8 může 2018<sup>1</sup> |
| Kumulativní aktualizace listopadu | 1.3.0.0 | 30. listopadu 2017 | Podporovaná do 8 může 2018<sup>1</sup> |
| Říjen kumulativní aktualizace | 1.2.0.0 | 31. října 2017 | Podporovaná do 8 může 2018<sup>1</sup> |
| Počáteční verze Preview | 1.1.0.0 | 26. září 2017 | Podporovaná do 8 může 2018<sup>1</sup> |

\[1\]: verze agenta synchronizace souboru Azure během preview záměrně nesplňují zásady aktualizace. Aktualizace zásad se vynutí od první verze agenta po synchronizaci souboru Azure je deklarovaná všeobecně dostupná.

### <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-2200"></a>Verze agenta 2.2.0.0
Následující poznámky k verzi jsou pro verzi agenta Azure souboru Sync vydané 12. březen 2018 2.2.0.0.  Tyto poznámky doplňují uvedené pro verzi 2.1.0.0 a 2.0.11.0 poznámky k verzi

Instalace v2.1.0.0 někteří zákazníci by selhat z důvodu FileSyncSvc není zastavení. Tato aktualizace řeší tento problém.

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
Další informace o instalaci a konfiguraci agenta Azure File Sync s použitím Windows Serveru najdete v tématech [Plánování nasazení služby Azure File Sync (Preview)](storage-sync-files-planning.md) a [Nasazení služby Azure File Sync (Preview)](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta (MSI) se musí nainstalovat se zvýšenými oprávněními (správce).
- Agent se nepodporuje v možnostech nasazení Windows Server Core nebo Nano Server.
- Agent se podporuje pouze ve Windows Serveru 2016 a Windows Serveru 2012 R2.
- Agent vyžaduje alespoň 2 GB fyzické paměti.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu [Řešení potíží se službou Azure File Sync (Preview)](storage-sync-files-troubleshoot.md).
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
- Odstranění velkého počtu (více než 10 000) adresářů ze serveru najednou může způsobit chyby synchronizace. Odstraňujte adresáře v dávkách po méně než 10 000. Před odstraněním další dávky se ujistěte, že se operace odstranění úspěšně synchronizovaly.
- Tato verze přidává podporu kořenového adresáře synchronizace v kořenovém adresáři svazku.
- Neukládejte stránkovací soubor operačního systému nebo aplikace, který je na koncovém bodu serveru.
- Změna v této vydané verzi: Přidání nových událostí pro sledování celkové doby vrstvení cloudu (ID události 9016), průběhu nahrávání synchronizace (ID události 9302) a souborů, které se nesynchronizovaly (ID události 9900).
- Změna v této vydané verzi: Výrazné zvýšení výkonu rychlé synchronizace oboru názvů DR.
 
### <a name="cloud-tiering"></a>Vrstvení cloudu
- Změna oproti předchozí verzi: Vrstvení nových souborů proběhne do 1 hodiny (dříve to bylo 32 hodin) v souladu s nastavením zásad vrstvení. Pro vrstvení na vyžádání poskytujeme rutinu PowerShellu. Pomocí této rutiny můžete vrstvení vyhodnotit efektivněji a bez čekání na procesy na pozadí.
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.
- Změna oproti předchozí verzi: Soubory se teď na jiných serverech stahují jako vrstvené soubory za předpokladu, že je soubor nový nebo už je vrstvený.

## <a name="agent-version-1100"></a>Agent verze 1.1.0.0
Následující poznámky k verzi platí pro agenta Azure File Sync verze 1.1.0.0 (vydaného 9. září 2017 v počáteční verzi Preview). 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o instalaci a konfiguraci agenta Azure File Sync s použitím Windows Serveru najdete v tématech [Plánování nasazení služby Azure File Sync (Preview)](storage-sync-files-planning.md) a [Nasazení služby Azure File Sync (Preview)](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta (MSI) se musí nainstalovat se zvýšenými oprávněními (správce).
- Agent se nepodporuje v možnostech nasazení Windows Server Core nebo Nano Server.
- Agent se podporuje pouze ve Windows Serveru 2016 a 2012 R2.
- Agent vyžaduje alespoň 2 GB fyzické paměti.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu [Řešení potíží se službou Azure File Sync (Preview)](storage-sync-files-troubleshoot.md).
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