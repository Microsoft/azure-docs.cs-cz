---
title: Poznámky k verzi pro agenta Azure File Sync | Dokumentace Microsoftu
description: Poznámky k verzi pro agenta Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 6/13/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: d9bbc76fe60a5d363cd05b75df33f6fce00d7e9a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303408"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Poznámky k verzi pro agenta Azure File Sync
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Vaše instalace Windows Serveru se transformují na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS). Můžete mít libovolný počet mezipamětí po celém světě.

Tento článek obsahuje poznámky k podporovaným verzím agenta Synchronizace souborů Azure.

## <a name="supported-versions"></a>Podporované verze
Agent Synchronizace souborů Azure podporuje následující verze:

| Milník | Číslo verze agenta | Datum vydání | Status |
|----|----------------------|--------------|------------------|
| V7 Release - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19. červnu 2019 | [Publikování testovacích verzí](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#automatic-agent-lifecycle-management) |
| Červen 2019 kumulativní - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13. června 2019 | Podporované (doporučená verze) |
| Kumulativní – aktualizace. května 2019 [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7\. května 2019 | Podporováno |
| V6 Release - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21. dubna 2019 | Podporováno |
| 2019 dubna kumulativní - [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4\. dubna 2019 | Podporováno |
| Kumulativní – aktualizace březen 2019 [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7\. března 2019 | Podporováno |
| Verze 5 Release - [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12. února 2019 | Podporováno |
| 2019 ledna kumulativní - [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | Od 14. května 2019 | Podporováno |
| Kumulativní – aktualizace z prosince 2018 [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10. prosince 2018 | Podporováno |
| Kumulativní aktualizace z prosince 2018 | 4.1.0.0 | 4\. prosince 2018 | Podporováno |
| Verzi V4 | 4.0.1.0 | 13. listopadu 2018 | Podporováno |
| Kumulativní aktualizace. září 2018 | 3.3.0.0 | 24. září 2018 | Podporuje se – agenta verze vyprší dne 19. května 2019 |
| Kumulativní aktualizace. srpna 2018 | 3.2.0.0 | 15. srpna 2018 | Podporuje se – agenta verze vyprší dne 19. května 2019 |
| Všeobecná dostupnost | 3.1.0.0 | 19. července 2018 | Podporuje se – agenta verze vyprší dne 19. května 2019 |
| Vypršela platnost agentů | 1.1.0.0 - 3.0.13.0 | neuvedeno | Nepodporuje se – verze agentů platnost 1. října 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Synchronizace souborů Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-7000"></a>Verze agenta 7.0.0.0
Následující poznámky k verzi platí pro verze 7.0.0.0 agenta Azure File Sync (vydané 19. červnu 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a vyřešené problémy

- Podpora pro větší velikosti sdílené složky souborů
    - Ve verzi Preview větší sdílených složek Azure zvyšujeme naše omezeních podpory pro synchronizaci souboru také. V tento první krok Azure File Sync teď podporuje až 25TB a 50million souborů do jediného, synchronizace oboru názvů. Pokud chcete použít pro sdílenou složku velkých souborů, ve verzi preview, vyplňte tento formulář https://aka.ms/azurefilesatscalesurvey. 
- Vylepšené obnovení na úrovni souboru Azure Backup
    - Jednotlivé soubory obnovit pomocí služby Azure Backup jsou nyní zjištěn a rychleji se synchronizují se koncový bod serveru.
- Vylepšené cloudu ovládání datových vrstev na vyvolání rutiny spolehlivosti 
    - Cloud ovládání datových vrstev na vyvolání rutiny (Invoke-StorageSyncFileRecall) teď podporuje na soubor počet opakování a zpoždění, podobně jako robocopy při opakování.
- Podpora protokolu TLS 1.2 pouze (protokol TLS 1.0 a 1.1 zakázána)
    - Azure File Sync teď podporuje používání protokolu TLS 1.2 pouze na serverech, které mají protokol TLS 1.0 a 1.1 zakázáno. Před toto vylepšení registrace serveru selže, pokud byl zakázán protokol TLS 1.0 a 1.1 na serveru.
- Různé výkon a vylepšení spolehlivosti pro synchronizace a vrstvení cloudu
    - Existuje několik vylepšení spolehlivosti a výkonu v této verzi. Některé z nich jsou cíleny na vylepšit efektivnější vrstvení cloudu a Azure File Sync jako celé práce v těchto situacích, až budete mít omezení plánu nastavení šířky pásma.

### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Azure File Sync, byste měli předem zvážit, jestli je kompatibilní s nástrojem pro vyhodnocení Azure File Sync systémem. Tento nástroj je rutiny Azure Powershellu, který kontroluje potenciální problémy s systému souborů a datové sady, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny pro instalaci a použití, naleznete v tématu [nástroj pro vyhodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) části v Průvodci plánem. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync se systémem Windows Server najdete v tématu [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) a [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný oprávnění se zvýšenými oprávněními (správce).
- Agent se nepodporuje na možnosti nasazení Nano serveru.
- Agent se podporuje jenom u 2019 serveru systému Windows, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje alespoň 2 GB paměti. Pokud na serveru běží na virtuálním počítači s povolenou dynamickou paměť, musí být virtuální počítač nakonfigurovaný s minimální MiB 2048 paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nachází na svazku, který má systémový svazek informace (SVI) adresář komprimované. Tato konfigurace bude vést k neočekávaným výsledkům.

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

## <a name="agent-version-6200"></a>Verze agenta 6.2.0.0
Následující poznámky k verzi platí pro verze 6.2.0.0 agenta Azure File Sync vydáno 13. června 2019. Tyto poznámky doplňují uvedené pro verzi 6.0.0.0 poznámky.

Seznam opravených chybách v této verzi:  
- Po vytvoření koncový bod serveru se vysoké využití procesoru může dojít při vyvolání na pozadí je stahování souborů na server
- Synchronizace a operace vrstvení cloudu může selhat s chybou ECS_E_SERVER_CREDENTIAL_NEEDED z důvodu vypršení platnosti tokenu
- Vracení souboru může selhat, pokud adresa URL pro stažení souboru obsahuje vyhrazené znaky 

## <a name="agent-version-6100"></a>Verze agenta 6.1.0.0
Následující poznámky k verzi platí pro verze 6.1.0.0 agenta Azure File Sync vydáno 6. května 2019. Tyto poznámky doplňují uvedené pro verzi 6.0.0.0 poznámky.

Seznam opravených chybách v této verzi:  
- Zobrazení konfigurace agenta verze a server koncových bodů na serverech, které mají verzi agenta Azure File Sync 6.0 nainstalovaný Windows Admin Center se nezdaří.

## <a name="agent-version-6000"></a>Verze agenta 6.0.0.0
Následující poznámky k verzi platí pro verzi 6.0.0.0 agenta Azure File Sync (vydané 22. dubna 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a vyřešené problémy

- Podporuje automatické aktualizace agenta
  - Jsme slyšeli váš názor a rozrostl o funkci Automatické aktualizace na server agenta Azure File Sync. Další informace najdete v tématu [zásady aktualizace agenta Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Podpora pro Azure file sdílet seznamy ACL
  - Azure File Sync vždy podporuje synchronizaci mezi koncové body serveru seznamy ACL, ale nebyly synchronizovány seznamy ACL ke koncovým bodům cloudu (Azure file share). Tato verze přidává podporu pro synchronizaci seznamy řízení přístupu mezi serverem a koncové body cloudu.
- Paralelní nahrávání a stahování relace synchronizace pro koncový bod serveru 
  - Koncové body serveru teď podporují nahrávání a stahování souborů ve stejnou dobu. Žádné další čeká na stažení dokončete tak soubory můžete nahrát do sdílené složky Azure. 
- Nové rutiny zobrazíte svazku a vrstvení stav vrstvení cloudu
  - Dvě nové, místní server rutiny Powershellu teď umožňuje získat informace o odvolání cloudu ovládání datových vrstev a soubor. Protokolování informací z dvou kanálů událostí na serveru využívají k dispozici:
    - Get-StorageSyncFileTieringResult zobrazí seznam všech souborů a jejich cesty, které nebyly vrstvené a zprávy o důvod proč.
    - Get-StorageSyncFileRecallResult hlásí všechny události spojené s vracením souboru. Uvádí seznam všech souborů připomenout a jeho cesty a také úspěch nebo chyba pro tohoto odvolání.
  - Ve výchozím nastavení jak události kanálů můžete uložit až 1MB – může se zvýšit množství souborů hlášené zvětšením velikosti kanálu událostí.
- Podpora režimu FIPS
  - Azure File Sync teď podporuje povolení režimu FIPS na serverech, které mají nainstalovaného agenta Azure File Sync.
    - Před povolením režimu FIPS na serveru, nainstalujte agenta Azure File Sync a [modulu PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) na vašem serveru. Pokud už je povolený standard FIPS na serveru, [ručně stáhnout](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download) [modulu PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) k vašemu serveru.
- Různé vyšší spolehlivost pro ovládání datových vrstev a synchronizace cloudu

### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Azure File Sync, byste měli předem zvážit, jestli je kompatibilní s nástrojem pro vyhodnocení Azure File Sync systémem. Tento nástroj je rutiny Azure Powershellu, který kontroluje potenciální problémy s systému souborů a datové sady, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny pro instalaci a použití, naleznete v tématu [nástroj pro vyhodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) části v Průvodci plánem. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync se systémem Windows Server najdete v tématu [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) a [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný oprávnění se zvýšenými oprávněními (správce).
- Agent se nepodporuje na možnosti nasazení Nano serveru.
- Agent se podporuje jenom u 2019 serveru systému Windows, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje alespoň 2 GB paměti. Pokud na serveru běží na virtuálním počítači s povolenou dynamickou paměť, musí být virtuální počítač nakonfigurovaný s minimální MiB 2048 paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nachází na svazku, který má systémový svazek informace (SVI) adresář komprimované. Tato konfigurace bude vést k neočekávaným výsledkům.

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

## <a name="agent-version-5200"></a>Verze agenta 5.2.0.0
Následující poznámky k verzi platí pro verze 5.2.0.0 agenta Azure File Sync vydané 4. dubna 2019. Tyto poznámky doplňují uvedené verze 5.0.2.0 poznámky.

Seznam opravených chybách v této verzi:  
- Funkce obnovení vylepšení spolehlivosti pro přenos dat v režimu offline a přenosy dat.
- Vylepšení telemetrie synchronizace

## <a name="agent-version-5100"></a>Verze agenta 5.1.0.0
Následující poznámky k verzi platí pro verze 5.1.0.0 agenta Azure File Sync vydáno 7. března 2019. Tyto poznámky doplňují uvedené verze 5.0.2.0 poznámky.

Seznam opravených chybách v této verzi:  
- Soubory se nemusí podařit synchronizace s chybou 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED), pokud se nedaří výčtu změn na serveru
- Pokud relace synchronizace nebo souboru se zobrazí chyba 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), synchronizovat nyní opakujte operaci
- Soubory se nemusí podařit synchronizovat s chybou 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Využití velkého množství paměti může dojít při vracení souborů
- Vylepšení vrstvení telemetrických dat v cloudu 

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
