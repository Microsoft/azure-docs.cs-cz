---
title: Poznámky k verzi pro agenta Azure File Sync | Dokumentace Microsoftu
description: Poznámky k verzi pro agenta Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 2/12/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 2bbac21b9ac3e07cbb41ea8aa4cf93dcbd636d15
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181775"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Poznámky k verzi pro agenta Azure File Sync
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Vaše instalace Windows Serveru se transformují na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS). Můžete mít libovolný počet mezipamětí po celém světě.

Tento článek obsahuje poznámky k podporovaným verzím agenta Synchronizace souborů Azure.

## <a name="supported-versions"></a>Podporované verze
Agent Synchronizace souborů Azure podporuje následující verze:

| Milník | Číslo verze agenta | Datum vydání | Status |
|----|----------------------|--------------|------------------|
| Verze 5 Release - [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12. února 2019 | Podporované (doporučená verze) |
| 2019 ledna kumulativní - [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | Od 14. května 2019 | Podporováno |
| Kumulativní – aktualizace z prosince 2018 [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10. prosince 2018 | Podporováno |
| Kumulativní aktualizace z prosince 2018 | 4.1.0.0 | 4. prosince 2018 | Podporováno |
| Verzi V4 | 4.0.1.0 | 13. listopadu 2018 | Podporováno |
| Kumulativní aktualizace. září 2018 | 3.3.0.0 | 24. září 2018 | Podporuje se – agenta verze vyprší dne 19. května 2019 |
| Kumulativní aktualizace. srpna 2018 | 3.2.0.0 | 15. srpna 2018 | Podporuje se – agenta verze vyprší dne 19. května 2019 |
| Všeobecná dostupnost | 3.1.0.0 | 19. července 2018 | Podporuje se – agenta verze vyprší dne 19. května 2019 |
| Vypršela platnost agentů | 1.1.0.0 - 3.0.13.0 | neuvedeno | Nepodporuje se – verze agentů platnost 1. října 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Synchronizace souborů Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-5020"></a>Verze agenta 5.0.2.0
Následující poznámky k verzi platí pro verze 5.0.2.0 agenta Azure File Sync (vydané 12. února 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a vyřešené problémy

- Podpora pro cloud Azure Government
    - Přidali jsme podporu cloudu Azure Government ve verzi preview. To vyžaduje předplatné uvedené prázdné a stažení speciální agenta od Microsoftu. Chcete-li získat přístup k verzi preview, prosím pošlete nám e-mail přímo na [ AzureFiles@microsoft.com ](mailto:AzureFiles@microsoft.com).
- Podpora pro odstranění duplicitních dat
    - Odstranění duplicitních dat jsou nyní plně podporovány s cloudem ovládání datových vrstev na Windows serveru 2016 a Windows Server 2019 zapnout. Povolení odstranění duplicitních dat na svazku s povoleno vrstvení cloudu umožňuje ukládat do mezipaměti další soubory lokálně bez zřizování úložiště.
- Podpora pro offline datové přenosy (např. zařízení Data Box)
    - Snadno migrujte velkých objemů dat do Azure File Sync žádným způsobem, který zvolíte. Můžete zvolit Azure Data Box, AzCopy a dokonce i služby třetích stran migrace. Není nutné používat obrovské množství šířky pásma jak dostat data do Azure, v případě zařízení Data Box – e-mailů jednoduše ji existuje! Další informace najdete v tématu [Offline Data Transfer dokumentace](https://aka.ms/AFS/OfflineDataTransfer).
- Vylepšené synchronizace výkonu
    - Zákazníci s několika koncovými body server na stejném svazku pravděpodobně došlo k výkonu pomalá synchronizace před touto verzí. Azure File Sync vytvoří dočasné snímek služby VSS jednou za den na serveru, aby synchronizovat soubory, které mají otevřených popisovačů. Synchronizovat nyní podporuje několik koncových bodů serveru synchronizace na svazku, když je aktivní relace synchronizace stínové kopie svazku. Žádné další čeká VSS synchronizovat relace vyplnění synchronizace lze obnovit na jiné koncové body serveru na svazku.
- Vylepšené monitorování na portálu
    - Grafy se přidaly na portálu služby synchronizace úložiště pro zobrazení:
        - Počet souborů, které jsou synchronizovány
        - Velikost přenesených dat.
        - Počet souborů nesynchronizuje
        - Velikost dat připomenout
        - Stav připojení k serveru
    - Další informace najdete v tématu [monitorování Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Lepší škálovatelnost a spolehlivost
    - Maximální počet objektů systému souborů (adresářů a souborů) v adresáři zvýšil na 1 000 000. Byl překročen limit předchozí 200 000.
    - Synchronizace se pokusí pokračovat v přenosu dat spíše než oboustranné při převodu dojde k přerušení pro velké soubory 

### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Azure File Sync, byste měli předem zvážit, jestli je kompatibilní s nástrojem pro vyhodnocení Azure File Sync systémem. Tento nástroj je rutiny Azure Powershellu, který kontroluje potenciální problémy s systému souborů a datové sady, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny pro instalaci a použití, naleznete v tématu [nástroj pro vyhodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) části v Průvodci plánem. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync se systémem Windows Server najdete v tématu [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) a [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný oprávnění se zvýšenými oprávněními (správce).
- Agent se nepodporuje v možnostech nasazení Windows Server Core nebo Nano Server.
- Agent se podporuje jenom u 2019 serveru systému Windows, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje alespoň 2 GB paměti. Pokud na serveru běží na virtuálním počítači s povolenou dynamickou paměť, musí být virtuální počítač nakonfigurovaný s minimální MiB 2048 paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nachází na svazku, který má systémový svazek informace (SVI) adresář komprimované. Tato konfigurace bude vést k neočekávaným výsledkům.
- Režim FIPS není podporován a musí se zakázat. 

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu [Poradce při potížích s Azure File Sync](storage-sync-files-troubleshoot.md).
- Blokování souborů správce prostředků souborového serveru (FSRM) může způsobit chyby nekonečné synchronizace, pokud soubory jsou blokovány z důvodu blokování souborů.
- Spuštění příkazu sysprep na serveru, který má nainstalovaného agenta Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Po nasazení bitové kopie serveru a dokončení zkrácené instalace nástroje sysprep, je třeba nainstalovat agenta Azure File Sync.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporované znaky. Zobrazit [Průvodce odstraňováním potíží](storage-sync-files-troubleshoot.md#handling-unsupported-characters) seznam nepodporovaných znaků.
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
- Vrstvené soubory bude nedostupná, pokud nejsou soubory připomenout, před odstraněním koncový bod serveru. Chcete-li obnovit přístup k souborům, znovu vytvořte koncový bod serveru. Pokud 30 dnů jste předali, protože se odstranil koncový bod serveru nebo koncového bodu cloudu byl odstraněn, nepoužitelné vrstvené soubory, které nebyly připomenout.
- Cloud nepodporuje ovládání datových vrstev na systémovém svazku. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, zakažte vrstvení cloudu po vytvoření koncového bodu serveru.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neukládejte operačního systému nebo aplikace stránkovacího souboru v rámci umístění koncového bodu serveru.
- Název serveru na portálu není aktualizován, pokud bylo přejmenováno na serveru.

### <a name="cloud-endpoint"></a>Koncový bod cloudu.
- Azure File Sync podporuje provádění změn přímo do sdílené složky Azure. Však všechny změny provedené na sdílenou složku Azure nejprve mají být zjišťované úlohou detekce změn v Azure File Sync. Pro koncový bod cloudu jednou za 24 hodin se spustí úloha zjišťování změn. Kromě toho změny provedené do sdílené složky Azure přes protokol REST neaktualizuje SMB čas poslední změny a se projeví jako změnu synchronizace.
- Služba synchronizace úložiště a/nebo účtu úložiště lze přesunout do jiné skupiny prostředků nebo předplatného v rámci stávajícího tenanta Azure AD. Pokud účet úložiště se přesune, budete muset poskytnout přístup hybridní služby File Sync k účtu úložiště (viz [zajistit Azure File Sync má přístup k účtu úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nepodporuje přesun předplatného na jinou v Azure AD tenanta.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí příkazu robocopy, použijte možnost/MIR zachovat časová razítka souborů. Tím se zajistí, že starší souborů proběhne rychleji než naposledy použitých souborů.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.

## <a name="agent-version-4300"></a>Verze agenta 4.3.0.0
Následující poznámky k verzi platí pro verze 4.3.0.0 agenta Azure File Sync vydaného od 14. května 2019. Tyto poznámky doplňují uvedené verze 4.0.1.0 poznámky.

Seznam opravených chybách v této verzi:  
- Po upgradu na verzi agenta Azure File Sync nejsou vrstvené soubory 4.x.
- AfsUpdater.exe se teď podporuje Windows Server 2019.
- Různé vyšší spolehlivost pro synchronizaci. 

## <a name="agent-version-4200"></a>Verze agenta 4.2.0.0
Následující poznámky k verzi platí pro verze 4.2.0.0 agenta Azure File Sync vydáno 10. prosince 2018. Tyto poznámky doplňují uvedené verze 4.0.1.0 poznámky.

Seznam opravených chybách v této verzi:  
- Chyba zastavení 0x3B nebo chyba Stop 0x1E může dojít, když se vytvoří snímek služby VSS.  
- Nevracení paměti může dojít, když cloud ovládání datových vrstev je povoleno  

## <a name="agent-version-4100"></a>Verze agenta 4.1.0.0
Následující poznámky k verzi platí pro verze 4.1.0.0 agenta Azure File Sync vydáno 4. prosince 2018. Tyto poznámky doplňují uvedené verze 4.0.1.0 poznámky.

Seznam opravených chybách v této verzi:  
- Server může přestat reagovat z důvodu nevrácené paměti vrstvení cloudu.  
- Instalace agenta se nezdaří s následující chybou: Došlo k chybě. 1921. Službu 'agenta synchronizace úložiště"(FileSyncSvc) se nepovedlo zastavit.  Ověřte, že máte dostatečná oprávnění k zastavení systémových služeb.  
- Služba agenta synchronizace úložiště (FileSyncSvc) dojít k chybě při vysoké využití paměti.  
- Vylepšení spolehlivosti různé cloudové ovládání datových vrstev a synchronizace.

## <a name="agent-version-4010"></a>Verze agenta 4.0.1.0
Následující poznámky k verzi platí pro verze 4.0.1.0 agenta Azure File Sync (vydáno 13. listopadu 2018).

### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Azure File Sync, byste měli předem zvážit, jestli je kompatibilní s nástrojem pro vyhodnocení Azure File Sync systémem. Tento nástroj je rutiny Azure Powershellu, který kontroluje potenciální problémy s systému souborů a datové sady, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny pro instalaci a použití, naleznete v tématu [nástroj pro vyhodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) části v Průvodci plánem. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync se systémem Windows Server najdete v tématu [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) a [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný oprávnění se zvýšenými oprávněními (správce).
- Agent se nepodporuje v možnostech nasazení Windows Server Core nebo Nano Server.
- Agent se podporuje jenom u 2019 serveru systému Windows, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje alespoň 2 GB paměti. Pokud na serveru běží na virtuálním počítači s povolenou dynamickou paměť, musí být virtuální počítač nakonfigurovaný s minimální MiB 2048 paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nachází na svazku, který má systémový svazek informace (SVI) adresář komprimované. Tato konfigurace bude vést k neočekávaným výsledkům.
- Režim FIPS není podporován a musí se zakázat. 
- Chyba zastavení 0x3B nebo Chyba stop 0x1E může dojít, když se vytvoří snímek služby VSS.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu [Poradce při potížích s Azure File Sync](storage-sync-files-troubleshoot.md).
- Blokování souborů správce prostředků souborového serveru (FSRM) může způsobit chyby nekonečné synchronizace, pokud soubory jsou blokovány z důvodu blokování souborů.
- Spuštění příkazu sysprep na serveru, který má nainstalovaného agenta Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Po nasazení bitové kopie serveru a dokončení zkrácené instalace nástroje sysprep, je třeba nainstalovat agenta Azure File Sync.
- Odstranění duplicit dat a vrstvení cloudu se nepodporuje na stejném svazku.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporované znaky. Zobrazit [Průvodce odstraňováním potíží](storage-sync-files-troubleshoot.md#handling-unsupported-characters) seznam nepodporovaných znaků.
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
- Vrstvené soubory bude nedostupná, pokud nejsou soubory připomenout, před odstraněním koncový bod serveru. Chcete-li obnovit přístup k souborům, znovu vytvořte koncový bod serveru. Pokud 30 dnů jste předali, protože se odstranil koncový bod serveru nebo koncového bodu cloudu byl odstraněn, nepoužitelné vrstvené soubory, které nebyly připomenout.
- Cloud nepodporuje ovládání datových vrstev na systémovém svazku. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, zakažte vrstvení cloudu po vytvoření koncového bodu serveru.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neukládejte operačního systému nebo aplikace stránkovacího souboru v rámci umístění koncového bodu serveru.
- Název serveru na portálu není aktualizován, pokud bylo přejmenováno na serveru.

### <a name="cloud-endpoint"></a>Koncový bod cloudu.
- Azure File Sync podporuje provádění změn přímo do sdílené složky Azure. Však všechny změny provedené na sdílenou složku Azure nejprve mají být zjišťované úlohou detekce změn v Azure File Sync. Pro koncový bod cloudu jednou za 24 hodin se spustí úloha zjišťování změn. Kromě toho změny provedené do sdílené složky Azure přes protokol REST neaktualizuje SMB čas poslední změny a se projeví jako změnu synchronizace.
- Služba synchronizace úložiště a/nebo účtu úložiště lze přesunout do jiné skupiny prostředků nebo předplatného v rámci stávajícího tenanta Azure AD. Pokud účet úložiště se přesune, budete muset poskytnout přístup hybridní služby File Sync k účtu úložiště (viz [zajistit Azure File Sync má přístup k účtu úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nepodporuje přesun předplatného na jinou v Azure AD tenanta.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Cloudu založený na datu ovládání datových vrstev na nastavení zásad se používá k určení souborů, které by ukládat do mezipaměti, pokud zadaný počet dnů. Další informace najdete v tématu [přehled vrstvení cloudu](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí příkazu robocopy, použijte možnost/MIR zachovat časová razítka souborů. Tím se zajistí, že starší souborů proběhne rychleji než naposledy použitých souborů.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.

## <a name="agent-version-3300"></a>Verze agenta 3.3.0.0
Následující poznámky k verzi platí pro verze 3.3.0.0 agenta Azure File Sync vydáno 24. září 2018. Tyto poznámky doplňují uvedené verze 3.1.0.0 poznámky.

Seznam opravených chybách v této verzi:
- Registrovaný server stav je "Se zobrazí v režimu offline" za Azure File Sync, agent se upgraduje na verzi 3.1 nebo 3.2.
- Úložiště služby agenta synchronizace (FileSyncSvc) dojde k chybě z důvodu soubory, které mají dlouhé cesty.
- Registrace serveru nezdaří s chybou: Nepovedlo se načíst soubor nebo sestavení Kailani.Afs.StorageSyncProtocol.V3.

## <a name="agent-version-3200"></a>Agent verze 3.2.0.0
Následující poznámky k verzi platí pro verze 3.2.0.0 agenta Azure File Sync vydaného 15. srpna 2018. Tyto poznámky doplňují uvedené verze 3.1.0.0 poznámky.

Tato verze zahrnuje následující opravy:
- Synchronizace se nezdaří s nedostatek paměti (není dostatečná 0x8007000e) z důvodu nevracení paměti

## <a name="agent-version-3100"></a>Verze agenta 3.1.0.0
Následující poznámky k verzi platí pro verze 3.1.0.0 agenta Azure File Sync (vydané 19. července 2018).

### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Azure File Sync, byste měli předem zvážit, jestli je kompatibilní s nástrojem pro vyhodnocení Azure File Sync systémem. Tento nástroj je rutiny Azure Powershellu, který kontroluje potenciální problémy s systému souborů a datové sady, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny pro instalaci a použití, naleznete v tématu [nástroj pro vyhodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) části v Průvodci plánem. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync se systémem Windows Server najdete v tématu [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) a [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný oprávnění se zvýšenými oprávněními (správce).
- Agent se nepodporuje v možnostech nasazení Windows Server Core nebo Nano Server.
- Agent se podporuje pouze ve Windows Serveru 2016 a Windows Serveru 2012 R2.
- Agent vyžaduje alespoň 2 GB fyzické paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nachází na svazku, který má systémový svazek informace (SVI) adresář komprimované. Tato konfigurace bude vést k neočekávaným výsledkům.
- Režim FIPS není podporován a musí se zakázat. 

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
    > Synchronizace souborů Azure vždy šifruje přenášená data. Neaktivní uložená data se vždy šifrují v Azure.
 
### <a name="server-endpoint"></a>Koncový bod serveru
- Koncový bod serveru je možné vytvořit pouze na svazku NTFS. Synchronizace souborů Azure v současné době nepodporuje systémy souborů ReFS, FAT, FAT32 a jiné.
- Vrstvené soubory už nepůjdou použít, pokud nejsou soubory připomenout, před odstraněním koncový bod serveru.
- Cloud nepodporuje ovládání datových vrstev na systémovém svazku. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, zakažte vrstvení cloudu po vytvoření koncového bodu serveru.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neukládejte stránkovací soubor operačního systému nebo aplikace, který je na koncovém bodu serveru.
- Název serveru na portálu není aktualizován, pokud bylo přejmenováno na serveru.

### <a name="cloud-endpoint"></a>Koncový bod cloudu.
- Azure File Sync podporuje provádění změn přímo do sdílené složky Azure. Však všechny změny provedené na sdílenou složku Azure nejprve mají být zjišťované úlohou detekce změn v Azure File Sync. Pro koncový bod cloudu jednou za 24 hodin se spustí úloha zjišťování změn. Kromě toho změny provedené do sdílené složky Azure přes protokol REST neaktualizuje SMB čas poslední změny a se projeví jako změnu synchronizace.
- Služba synchronizace úložiště a/nebo účtu úložiště lze přesunout do jiné skupiny prostředků nebo předplatného v rámci stávajícího tenanta Azure AD. Pokud účet úložiště se přesune, budete muset poskytnout přístup hybridní služby File Sync k účtu úložiště (viz [zajistit Azure File Sync má přístup k účtu úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nepodporuje přesun předplatného na jinou v Azure AD tenanta.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.
