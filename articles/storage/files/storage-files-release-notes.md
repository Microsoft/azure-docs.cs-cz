---
title: Poznámky k verzi agenta Azure File Sync | Microsoft Docs
description: Poznámky k verzi agenta Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 8/14/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 45f383691a52d841f35ed9b67d4658341de18afc
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036254"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Poznámky k verzi agenta Azure File Sync
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Vaše instalace Windows Serveru se transformují na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS). Můžete mít libovolný počet mezipamětí po celém světě.

Tento článek obsahuje poznámky k podporovaným verzím agenta Synchronizace souborů Azure.

## <a name="supported-versions"></a>Podporované verze
Agent Synchronizace souborů Azure podporuje následující verze:

| RTM | Číslo verze agenta | Datum vydání | Stav |
|----|----------------------|--------------|------------------|
| Kumulativní aktualizace z července 2019 – [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24. července 2019 | Podporováno |
| Kumulativní aktualizace z července 2019 – [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12. července 2019 | Podporováno |
| Verze v7 – [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19. června 2019 | Podporováno |
| Kumulativní aktualizace z června 2019 – [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27. června 2019 | Podporováno |
| Kumulativní aktualizace z června 2019 – [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13. června 2019 | Podporováno |
| Kumulativní aktualizace květen 2019 – [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7\. května 2019 | Podporováno |
| Verze V6 – [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21. dubna 2019 | Podporováno |
| Kumulativní aktualizace z dubna 2019 – [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4\. dubna 2019 | Podporováno |
| Kumulativní aktualizace z března 2019 – [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7\. března 2019 | Podporováno |
| Verze verze 5 – [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12. února 2019 | Podporováno |
| Kumulativní aktualizace z ledna 2019 – [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 14. ledna 2019 | Podporováno |
| Kumulativní aktualizace z prosince 2018 – [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10. prosince 2018 | Podporováno |
| Kumulativní aktualizace z prosince 2018 | 4.1.0.0 | 4\. prosince 2018 | Podporováno |
| Verze v4 | 4.0.1.0 | 13. listopadu 2018 | Podporováno |
| Kumulativní aktualizace z září 2018 | 3.3.0.0 | 24. září 2018 | Verze Supported agenta vyprší 19. srpna 2019 |
| Kumulativní aktualizace ze srpna 2018 | 3.2.0.0 | 15. srpna 2018 | Verze Supported agenta vyprší 19. srpna 2019 |
| Všeobecná dostupnost | 3.1.0.0 | 19. července 2018 | Verze Supported agenta vyprší 19. srpna 2019 |
| Agenti s vypršenou platností | 1.1.0.0 – 3.0.13.0 | Není k dispozici | Nepodporováno – platnost verzí agentů vypršela 1. října 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Synchronizace souborů Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-7200"></a>Verze agenta 7.2.0.0
Následující poznámky k verzi jsou pro verzi 7.2.0.0 agenta Azure File Sync vydané 24. července 2019. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 7.0.0.0.

Seznam problémů opravených v této verzi:  
- Agent synchronizace úložiště (FileSyncSvc) selže, pokud má konfigurace proxy hodnoty null.
- Koncový bod serveru se spustí BCDR (Error 0x80c80257-ECS_E_BCDR_IN_PROGRESS), pokud má více koncových bodů na serveru stejný název.
- Zvýšení spolehlivosti při vrstvení cloudu.

## <a name="agent-version-7100"></a>Verze agenta 7.1.0.0
Následující poznámky k verzi jsou pro verzi 7.1.0.0 agenta Azure File Sync vydané 12. července 2019. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 7.0.0.0.

Seznam problémů opravených v této verzi:  
- Přístup k umístění koncového bodu serveru přes SMB nebo jeho procházení v systému Windows Server 2012 R2 je pomalé. 
- Zvýšení využití procesoru po instalaci agenta Azure File Sync v6.
- Vylepšení telemetrie cloudových vrstev.
- Různá vylepšení spolehlivosti pro vrstvení a synchronizaci cloudu.

## <a name="agent-version-7000"></a>Verze agenta 7.0.0.0
Následující poznámky k verzi jsou pro 7.0.0.0 agenta Azure File Sync (vydané 19. června 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou vyřešeny

- Podpora větších velikostí sdílení souborů
    - S verzí Preview větších sdílených složek Azure zvyšujeme také naše limity podpory pro synchronizaci souborů. V tomto prvním kroku teď Azure File Sync podporuje až 25TB a 50million soubory v jednom, synchronizovaném oboru názvů. Chcete-li použít pro velkou verzi Preview sdílení souborů, vyplňte tento https://aka.ms/azurefilesatscalesurvey formulář. 
- Vylepšené obnovení Azure Backup na úrovni souborů
    - Jednotlivé soubory obnovené pomocí Azure Backup jsou nyní zjištěny a synchronizovány na koncový bod serveru rychleji.
- Vylepšená spolehlivost rutiny pro vracení cloudových vrstev 
    - Rutina revolání vrstvy cloudu (Invoke-StorageSyncFileRecall) teď podporuje počet opakování souborů a zpoždění opakování, podobně jako Robocopy.
- Podporuje se jenom TLS 1,2 (TLS 1,0 a 1,1 je zakázaná).
    - Azure File Sync teď podporuje použití TLS 1,2 jenom na serverech, které mají TLS 1,0 a 1,1 zakázané. Před tímto vylepšením selže registrace serveru, pokud na serveru dojde k zakázání TLS 1,0 a 1,1.
- Různá vylepšení výkonu a spolehlivosti pro synchronizaci a vrstvení cloudu
    - V této verzi je několik vylepšení spolehlivosti a výkonu. Některé z nich jsou zaměřené na zajištění efektivnějšího využívání cloudových vrstev a Azure File Sync jako celá práce v těchto situacích, když máte nastaven plán omezení šířky pásma.

### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje Azure File Sync Evaluation Tool. Tento nástroj je Azure PowerShell rutinou, která kontroluje potenciální problémy se systémem souborů a datovou sadou, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny k instalaci a používání najdete v části [Nástroj pro vyhodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) v příručce pro plánování. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync s Windows serverem, najdete v tématu [Plánování nasazení Azure File Sync](storage-sync-files-planning.md) a [postup nasazení Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný se zvýšenými oprávněními (správce).
- Možnost nasazení nano serveru nepodporuje agenta.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje aspoň 2 GiB paměti. Pokud server běží na virtuálním počítači s povolenou dynamickou pamětí, měl by být virtuální počítač nakonfigurovaný s minimální 2048 MiB paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nacházející se na svazku, který má komprimovaný adresář systému SVI (System Volume Information). Tato konfigurace bude mít za následek neočekávané výsledky.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu věnovaném [řešení potíží s Azure File Sync](storage-sync-files-troubleshoot.md).
- Blokování souborů (Správce prostředků souborového Správce prostředků serveru) může způsobit nekonečná selhání synchronizace, když jsou soubory blokované z důvodu blokování souborů.
- Spuštění nástroje Sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Agent Azure File Sync by měl být nainstalovaný po nasazení image serveru a dokončení zkrácené instalace nástroje Sysprep.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete v [Průvodci odstraňováním potíží](storage-sync-files-troubleshoot.md#handling-unsupported-characters) .
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
- Vrstvené soubory se stanou nepřístupné, pokud se soubory před odstraněním koncového bodu serveru nevrátí. Chcete-li obnovit přístup k souborům, vytvořte koncový bod serveru znovu. Pokud uplynula 30 dní od odstranění koncového bodu serveru nebo pokud byl odstraněn koncový bod cloudu, vrstvené soubory, které nebyly znovu volány, nebudou použitelné.
- Na systémovém svazku není podporováno vrstvení cloudu. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, zakažte při vytváření koncového bodu serveru vrstvení cloudu.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neukládejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu se neaktualizuje, pokud se server přejmenuje.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Azure File Sync podporuje přímé provádění změn ve sdílené složce Azure. Všechny změny provedené ve sdílené složce Azure se ale nejdřív musí zjistit pomocí úlohy zjišťování změn Azure File Sync. Úloha detekce změn se iniciuje pro koncový bod cloudu každých 24 hodin. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST neaktualizují čas poslední změny protokolu SMB a nebudou se zobrazovat jako změny v synchronizaci.
- Služba synchronizace úložiště nebo účet úložiště se dá přesunout do jiné skupiny prostředků nebo předplatného v rámci stávajícího tenanta Azure AD. Pokud je účet úložiště přesunutý, musíte mu udělit přístup ke službě hybridní Synchronizace souborů k účtu úložiště (podívejte se, [jestli má Azure File Sync přístup k účtu úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nepodporuje přesun předplatného na jiného tenanta Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí nástroje Robocopy použijte možnost/MIR, která zachová časová razítka souborů. Tím se zajistí, že se starší soubory budou vrstvit dřív než nedávno používané soubory.

## <a name="agent-version-6300"></a>Verze agenta 6.3.0.0
Následující poznámky k verzi jsou pro 6.3.0.0 verze agenta Azure File Sync vydané 27. června 2019. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 6.0.0.0.

Seznam problémů opravených v této verzi:  
- Přístup k umístění koncového bodu serveru přes SMB a jeho procházení v systému Windows Server 2012 R2 je pomalé. 
- Zvýšení využití procesoru po instalaci agenta Azure File Sync V6
- Vylepšení telemetrie cloudových vrstev

## <a name="agent-version-6200"></a>Verze agenta 6.2.0.0
Následující poznámky k verzi jsou pro 6.2.0.0 verze agenta Azure File Sync vydané 13. června 2019. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 6.0.0.0.

Seznam problémů opravených v této verzi:  
- Po vytvoření koncového bodu serveru se může vyskytnout vysoké využití procesoru, když stažení na pozadí stahuje soubory na server.
- Operace synchronizace a vrstvení cloudu můžou selhat s chybou ECS_E_SERVER_CREDENTIAL_NEEDED z důvodu vypršení platnosti tokenu.
- Pokud adresa URL ke stažení souboru obsahuje rezervované znaky, může se stát, že se vrácení souboru nezdaří. 

## <a name="agent-version-6100"></a>Verze agenta 6.1.0.0
Následující poznámky k verzi jsou pro verzi 6.1.0.0 agenta Azure File Sync vydané 6. května 2019. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 6.0.0.0.

Seznam problémů opravených v této verzi:  
- Centrum pro správu Windows nemůže zobrazit verzi agenta a konfiguraci koncového bodu serveru na serverech, na kterých je nainstalovaný agent Azure File Sync verze 6,0.

## <a name="agent-version-6000"></a>Verze agenta 6.0.0.0
Následující poznámky k verzi jsou pro 6.0.0.0 agenta Azure File Sync (vydané 22. dubna 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou vyřešeny

- Podpora automatických aktualizací agenta
  - Slyšeli jsme vaši zpětnou vazbu a přidali do agenta Azure File Sync serveru funkci Automatické aktualizace. Další informace najdete v tématu [zásady aktualizace agenta Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Podpora seznamů ACL pro Azure File Share
  - Azure File Sync vždycky podporovaly synchronizaci seznamů ACL mezi koncovými body serveru, ale seznamy ACL nebyly synchronizovány do koncového bodu cloudu (sdílená složka Azure). Tato verze přidává podporu pro synchronizaci seznamů ACL mezi koncovými body serveru a cloudu.
- Paralelní nahrávání a stahování synchronizačních relací pro koncový bod serveru 
  - Koncové body serveru nyní podporují nahrávání a stahování souborů současně. Nečeká se na dokončení stahování, takže soubory je možné odeslat do sdílené složky Azure. 
- Nové rutiny vrstvení cloudu pro získání stavu svazku a vrstvení
  - Pomocí dvou nových rutin prostředí PowerShell pro místní server se teď dají získat informace o vrstvách cloudu a o odvolání souborů. Vytvářejí informace o protokolování ze dvou kanálů událostí na serveru, který je k dispozici:
    - Get-StorageSyncFileTieringResult zobrazí seznam všech souborů a jejich cest, které nebyly vrstveny a z důvodu příčiny.
    - Get-StorageSyncFileRecallResult hlásí všechny události odvolání souborů. Obsahuje seznam všech odvolaného souboru a jeho cestu a také úspěch nebo chybu při odvolání.
  - Ve výchozím nastavení můžou oba kanály událostí ukládat až 1 MB. velikost souborů hlášených zvýšením velikosti kanálu událostí můžete zvýšit.
- Podpora režimu FIPS
  - Azure File Sync teď podporuje povolování režimu FIPS na serverech, na kterých je nainstalovaný agent Azure File Sync.
    - Před zapnutím režimu FIPS na serveru nainstalujte na server Azure File Sync agenta a [modul PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) . Pokud je na serveru už povolený FIPS, [ručně stáhněte](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download) [modul PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) na server.
- Různá vylepšení spolehlivosti pro vrstvení a synchronizaci cloudu

### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje Azure File Sync Evaluation Tool. Tento nástroj je Azure PowerShell rutinou, která kontroluje potenciální problémy se systémem souborů a datovou sadou, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny k instalaci a používání najdete v části [Nástroj pro vyhodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) v příručce pro plánování. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync s Windows serverem, najdete v tématu [Plánování nasazení Azure File Sync](storage-sync-files-planning.md) a [postup nasazení Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný se zvýšenými oprávněními (správce).
- Možnost nasazení nano serveru nepodporuje agenta.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje aspoň 2 GiB paměti. Pokud server běží na virtuálním počítači s povolenou dynamickou pamětí, měl by být virtuální počítač nakonfigurovaný s minimální 2048 MiB paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nacházející se na svazku, který má komprimovaný adresář systému SVI (System Volume Information). Tato konfigurace bude mít za následek neočekávané výsledky.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu věnovaném [řešení potíží s Azure File Sync](storage-sync-files-troubleshoot.md).
- Blokování souborů (Správce prostředků souborového Správce prostředků serveru) může způsobit nekonečná selhání synchronizace, když jsou soubory blokované z důvodu blokování souborů.
- Spuštění nástroje Sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Agent Azure File Sync by měl být nainstalovaný po nasazení image serveru a dokončení zkrácené instalace nástroje Sysprep.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete v [Průvodci odstraňováním potíží](storage-sync-files-troubleshoot.md#handling-unsupported-characters) .
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
- Vrstvené soubory se stanou nepřístupné, pokud se soubory před odstraněním koncového bodu serveru nevrátí. Chcete-li obnovit přístup k souborům, vytvořte koncový bod serveru znovu. Pokud uplynula 30 dní od odstranění koncového bodu serveru nebo pokud byl odstraněn koncový bod cloudu, vrstvené soubory, které nebyly znovu volány, nebudou použitelné.
- Na systémovém svazku není podporováno vrstvení cloudu. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, zakažte při vytváření koncového bodu serveru vrstvení cloudu.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neukládejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu se neaktualizuje, pokud se server přejmenuje.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Azure File Sync podporuje přímé provádění změn ve sdílené složce Azure. Všechny změny provedené ve sdílené složce Azure se ale nejdřív musí zjistit pomocí úlohy zjišťování změn Azure File Sync. Úloha detekce změn se iniciuje pro koncový bod cloudu každých 24 hodin. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST neaktualizují čas poslední změny protokolu SMB a nebudou se zobrazovat jako změny v synchronizaci.
- Služba synchronizace úložiště nebo účet úložiště se dá přesunout do jiné skupiny prostředků nebo předplatného v rámci stávajícího tenanta Azure AD. Pokud je účet úložiště přesunutý, musíte mu udělit přístup ke službě hybridní Synchronizace souborů k účtu úložiště (podívejte se, [jestli má Azure File Sync přístup k účtu úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nepodporuje přesun předplatného na jiného tenanta Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí nástroje Robocopy použijte možnost/MIR, která zachová časová razítka souborů. Tím se zajistí, že se starší soubory budou vrstvit dřív než nedávno používané soubory.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.

## <a name="agent-version-5200"></a>Verze agenta 5.2.0.0
Následující poznámky k verzi jsou pro verzi 5.2.0.0 agenta Azure File Sync vydané 4. dubna 2019. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 5.0.2.0.

Seznam problémů opravených v této verzi:  
- Vylepšení spolehlivosti pro offline přenos dat a funkce obnovení přenosu dat
- Vylepšení synchronizace telemetrie

## <a name="agent-version-5100"></a>Verze agenta 5.1.0.0
Následující poznámky k verzi jsou pro verzi 5.1.0.0 agenta Azure File Sync vydané 7. března 2019. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 5.0.2.0.

Seznam problémů opravených v této verzi:  
- Kvůli selhání výčtu změn na serveru se nemusí soubory synchronizovat s chybou 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED).
- Pokud relace synchronizace nebo soubor obdrží chybu 0x80072F78 (WININET_E_INVALID_SERVER_RESPONSE), synchronizace se teď pokusí operaci zopakovat.
- Soubory se nemusí podařit synchronizovat s chybou 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE).
- Při vracení souborů se může vyskytnout vysoké využití paměti.
- Vylepšení telemetrie cloudových vrstev 

## <a name="agent-version-5020"></a>Verze agenta 5.0.2.0
Následující poznámky k verzi jsou pro 5.0.2.0 agenta Azure File Sync (vydané 12. února 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Vylepšení a problémy, které jsou vyřešeny

- Podpora Azure Governmentho cloudu
  - Přidali jsme podporu Preview pro cloud Azure Government. To vyžaduje prázdné předplatné a speciálního agenta pro stažení od Microsoftu. Pokud chcete získat přístup k verzi Preview, pošlete nám prosím e-mail [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)přímo na.
- Podpora odstranění duplicitních dat
    - Odstranění duplicitních dat je teď plně podporované s povoleným vytvářením vrstev cloudu v systémech Windows Server 2016 a Windows Server 2019. Povolení odstranění duplicitních dat u svazku s povoleným vrstvou cloudu umožňuje ukládat do mezipaměti více souborů bez toho, aby bylo zajištěno více úložiště.
- Podpora offline přenosu dat (například prostřednictvím Data Box)
    - Jednoduše migrujte velké objemy dat do Azure File Sync prostřednictvím libovolného způsobu, jaký jste zvolili. Můžete zvolit Azure Data Box, AzCopy a dokonce i služby migrace třetích stran. Nemusíte používat obrovské množství šířky pásma k získání dat do Azure, v případě Data Box – stačí ho jednoduše odeslat. Další informace najdete v [dokumentaci k Offline přenos dat](https://aka.ms/AFS/OfflineDataTransfer).
- Vylepšený výkon synchronizace
    - Zákazníci s více koncovými body serveru na stejném svazku můžou před touto verzí narazit na pomalý výkon. Azure File Sync vytvoří dočasný snímek VSS jednou denně na serveru pro synchronizaci souborů s otevřenými popisovači. Synchronizace teď podporuje více než několik koncových bodů serveru, které se synchronizují na svazku, když je aktivní relace synchronizace VSS. Již nečekáte na dokončení relace synchronizace služby VSS, aby synchronizace mohla pokračovat na dalších koncových bodech serveru na svazku.
- Vylepšené monitorování na portálu
    - Na portálu služby synchronizace úložiště se přidaly grafy k zobrazení:
        - Počet synchronizovaných souborů
        - Velikost přenesených dat
        - Počet souborů, které se nesynchronizují
        - Velikost vrácených dat
        - Stav připojení serveru
    - Další informace najdete v tématu [monitorování Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Zlepšení škálovatelnosti a spolehlivosti
    - Maximální počet objektů systému souborů (adresářů a souborů) v adresáři se zvýšil na 1 000 000. Předchozí limit byl 200 000.
    - Synchronizace se pokusí obnovit přenos dat místo opakovaného přenosu při přerušení přenosu velkých souborů. 

### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje Azure File Sync Evaluation Tool. Tento nástroj je Azure PowerShell rutinou, která kontroluje potenciální problémy se systémem souborů a datovou sadou, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny k instalaci a používání najdete v části [Nástroj pro vyhodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) v příručce pro plánování. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync s Windows serverem, najdete v tématu [Plánování nasazení Azure File Sync](storage-sync-files-planning.md) a [postup nasazení Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný se zvýšenými oprávněními (správce).
- V možnostech nasazení systému Windows Server Core nebo nano Server není agent podporován.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje aspoň 2 GiB paměti. Pokud server běží na virtuálním počítači s povolenou dynamickou pamětí, měl by být virtuální počítač nakonfigurovaný s minimální 2048 MiB paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nacházející se na svazku, který má komprimovaný adresář systému SVI (System Volume Information). Tato konfigurace bude mít za následek neočekávané výsledky.
- Režim FIPS není podporován a musí být zakázán. 

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu věnovaném [řešení potíží s Azure File Sync](storage-sync-files-troubleshoot.md).
- Blokování souborů (Správce prostředků souborového Správce prostředků serveru) může způsobit nekonečná selhání synchronizace, když jsou soubory blokované z důvodu blokování souborů.
- Spuštění nástroje Sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Agent Azure File Sync by měl být nainstalovaný po nasazení image serveru a dokončení zkrácené instalace nástroje Sysprep.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete v [Průvodci odstraňováním potíží](storage-sync-files-troubleshoot.md#handling-unsupported-characters) .
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
- Vrstvené soubory se stanou nepřístupné, pokud se soubory před odstraněním koncového bodu serveru nevrátí. Chcete-li obnovit přístup k souborům, vytvořte koncový bod serveru znovu. Pokud uplynula 30 dní od odstranění koncového bodu serveru nebo pokud byl odstraněn koncový bod cloudu, vrstvené soubory, které nebyly znovu volány, nebudou použitelné.
- Na systémovém svazku není podporováno vrstvení cloudu. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, zakažte při vytváření koncového bodu serveru vrstvení cloudu.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neukládejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu se neaktualizuje, pokud se server přejmenuje.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Azure File Sync podporuje přímé provádění změn ve sdílené složce Azure. Všechny změny provedené ve sdílené složce Azure se ale nejdřív musí zjistit pomocí úlohy zjišťování změn Azure File Sync. Úloha detekce změn se iniciuje pro koncový bod cloudu každých 24 hodin. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST neaktualizují čas poslední změny protokolu SMB a nebudou se zobrazovat jako změny v synchronizaci.
- Služba synchronizace úložiště nebo účet úložiště se dá přesunout do jiné skupiny prostředků nebo předplatného v rámci stávajícího tenanta Azure AD. Pokud je účet úložiště přesunutý, musíte mu udělit přístup ke službě hybridní Synchronizace souborů k účtu úložiště (podívejte se, [jestli má Azure File Sync přístup k účtu úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nepodporuje přesun předplatného na jiného tenanta Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí nástroje Robocopy použijte možnost/MIR, která zachová časová razítka souborů. Tím se zajistí, že se starší soubory budou vrstvit dřív než nedávno používané soubory.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.

## <a name="agent-version-4300"></a>Verze agenta 4.3.0.0
Následující poznámky k verzi jsou pro 4.3.0.0 verze Azure File Sync agenta vydané 14. ledna 2019. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 4.0.1.0.

Seznam problémů opravených v této verzi:  
- Po upgradu agenta Azure File Sync na verzi 4. x nejsou soubory vrstveny.
- AfsUpdater. exe se teď podporuje v systému Windows Server 2019.
- Různá vylepšení spolehlivosti pro synchronizaci. 

## <a name="agent-version-4200"></a>Verze agenta 4.2.0.0
Následující poznámky k verzi jsou pro verzi 4.2.0.0 agenta Azure File Sync vydané 10. prosince 2018. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 4.0.1.0.

Seznam problémů opravených v této verzi:  
- Při vytvoření snímku služby VSS může dojít k chybě Stop 0x3B nebo stop Error 0x1E.  
- Pokud je povolená vrstva cloudu, může dojít k nevrácení paměti.  

## <a name="agent-version-4100"></a>Verze agenta 4.1.0.0
Následující poznámky k verzi jsou pro verzi 4.1.0.0 agenta Azure File Sync vydané 4. prosince 2018. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 4.0.1.0.

Seznam problémů opravených v této verzi:  
- Server může přestat reagovat z důvodu nevracení paměti na úrovni cloudu.  
- Instalace agenta se nezdařila s následující chybou: Chyba 1921. Službu agenta synchronizace úložiště (FileSyncSvc) se nepovedlo zastavit.  Ověřte, zda máte dostatečná oprávnění k zastavení systémových služeb.  
- Služba agenta synchronizace úložiště (FileSyncSvc) může selhat, pokud je využití paměti vysoké.  
- Různá vylepšení spolehlivosti pro vrstvení a synchronizaci cloudu.

## <a name="agent-version-4010"></a>Verze agenta 4.0.1.0
Následující poznámky k verzi jsou pro 4.0.1.0 agenta Azure File Sync (vydané 13. listopadu 2018).

### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje Azure File Sync Evaluation Tool. Tento nástroj je Azure PowerShell rutinou, která kontroluje potenciální problémy se systémem souborů a datovou sadou, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny k instalaci a používání najdete v části [Nástroj pro vyhodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) v příručce pro plánování. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync s Windows serverem, najdete v tématu [Plánování nasazení Azure File Sync](storage-sync-files-planning.md) a [postup nasazení Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný se zvýšenými oprávněními (správce).
- V možnostech nasazení systému Windows Server Core nebo nano Server není agent podporován.
- Agent je podporován pouze v systémech Windows Server 2019, Windows Server 2016 a Windows Server 2012 R2.
- Agent vyžaduje aspoň 2 GiB paměti. Pokud server běží na virtuálním počítači s povolenou dynamickou pamětí, měl by být virtuální počítač nakonfigurovaný s minimální 2048 MiB paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nacházející se na svazku, který má komprimovaný adresář systému SVI (System Volume Information). Tato konfigurace bude mít za následek neočekávané výsledky.
- Režim FIPS není podporován a musí být zakázán. 
- Při vytvoření snímku služby VSS může dojít k chybě Stop 0x3B nebo stop Error 0x1E.

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu věnovaném [řešení potíží s Azure File Sync](storage-sync-files-troubleshoot.md).
- Blokování souborů (Správce prostředků souborového Správce prostředků serveru) může způsobit nekonečná selhání synchronizace, když jsou soubory blokované z důvodu blokování souborů.
- Spuštění nástroje Sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Agent Azure File Sync by měl být nainstalovaný po nasazení image serveru a dokončení zkrácené instalace nástroje Sysprep.
- Odstranění duplicit dat a vrstvení cloudu se nepodporuje na stejném svazku.

### <a name="sync-limitations"></a>Omezení synchronizace
Následující položky se nesynchronizují, ale zbytek systému bude fungovat normálně dál:
- Soubory s nepodporovanými znaky. Seznam nepodporovaných znaků najdete v [Průvodci odstraňováním potíží](storage-sync-files-troubleshoot.md#handling-unsupported-characters) .
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
- Vrstvené soubory se stanou nepřístupné, pokud se soubory před odstraněním koncového bodu serveru nevrátí. Chcete-li obnovit přístup k souborům, vytvořte koncový bod serveru znovu. Pokud uplynula 30 dní od odstranění koncového bodu serveru nebo pokud byl odstraněn koncový bod cloudu, vrstvené soubory, které nebyly znovu volány, nebudou použitelné.
- Na systémovém svazku není podporováno vrstvení cloudu. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, zakažte při vytváření koncového bodu serveru vrstvení cloudu.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neukládejte stránkovací soubor operačního systému nebo aplikace do umístění koncového bodu serveru.
- Název serveru na portálu se neaktualizuje, pokud se server přejmenuje.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Azure File Sync podporuje přímé provádění změn ve sdílené složce Azure. Všechny změny provedené ve sdílené složce Azure se ale nejdřív musí zjistit pomocí úlohy zjišťování změn Azure File Sync. Úloha detekce změn se iniciuje pro koncový bod cloudu každých 24 hodin. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST neaktualizují čas poslední změny protokolu SMB a nebudou se zobrazovat jako změny v synchronizaci.
- Služba synchronizace úložiště nebo účet úložiště se dá přesunout do jiné skupiny prostředků nebo předplatného v rámci stávajícího tenanta Azure AD. Pokud je účet úložiště přesunutý, musíte mu udělit přístup ke službě hybridní Synchronizace souborů k účtu úložiště (podívejte se, [jestli má Azure File Sync přístup k účtu úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nepodporuje přesun předplatného na jiného tenanta Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Nastavení zásad cloudových vrstev na základě data slouží k zadání souborů, které se mají ukládat do mezipaměti, pokud se v zadaném počtu dnů přistupovalo. Další informace najdete v tématu [Přehled vrstvení cloudu](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při kopírování souborů pomocí nástroje Robocopy použijte možnost/MIR, která zachová časová razítka souborů. Tím se zajistí, že se starší soubory budou vrstvit dřív než nedávno používané soubory.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.

## <a name="agent-version-3300"></a>Verze agenta 3.3.0.0
Následující poznámky k verzi jsou pro verzi 3.3.0.0 agenta Azure File Sync vydané 24. září 2018. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 3.1.0.0.

Seznam problémů opravených v této verzi:
- Po upgradu agenta Azure File Sync na verzi 3,1 nebo 3,2 se zaregistrovaný stav serveru zobrazí jako offline.
- Služba agenta synchronizace úložiště (FileSyncSvc) selhává v důsledku souborů s dlouhými cestami.
- Registrace serveru se nezdařila s chybou: Nelze načíst soubor nebo sestavení Kailani. AFS. StorageSyncProtocol. v3.

## <a name="agent-version-3200"></a>Verze agenta 3.2.0.0
Následující poznámky k verzi jsou pro 3.2.0.0 verze agenta Azure File Sync vydané 15. srpna 2018. Tyto poznámky jsou kromě poznámky k verzi uvedené pro verzi 3.1.0.0.

Tato verze obsahuje následující opravu:
- Synchronizace se nezdařila s chybou nedostatku paměti (0x8007000E) kvůli nevrácené paměti

## <a name="agent-version-3100"></a>Verze agenta 3.1.0.0
Následující poznámky k verzi jsou pro 3.1.0.0 agenta Azure File Sync (vydané 19. července 2018).

### <a name="evaluation-tool"></a>Nástroj pro vyhodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí nástroje Azure File Sync Evaluation Tool. Tento nástroj je Azure PowerShell rutinou, která kontroluje potenciální problémy se systémem souborů a datovou sadou, jako jsou nepodporované znaky nebo Nepodporovaná verze operačního systému. Pokyny k instalaci a používání najdete v části [Nástroj pro vyhodnocení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) v příručce pro plánování. 

### <a name="agent-installation-and-server-configuration"></a>Instalace agenta a konfigurace serveru
Další informace o tom, jak nainstalovat a nakonfigurovat agenta Azure File Sync s Windows serverem, najdete v tématu [Plánování nasazení Azure File Sync](storage-sync-files-planning.md) a [postup nasazení Azure File Sync](storage-sync-files-deployment-guide.md).

- Instalační balíček agenta musí být nainstalovaný se zvýšenými oprávněními (správce).
- V možnostech nasazení systému Windows Server Core nebo nano Server není agent podporován.
- Agent se podporuje pouze ve Windows Serveru 2016 a Windows Serveru 2012 R2.
- Agent vyžaduje alespoň 2 GB fyzické paměti.
- Služba agenta synchronizace úložiště (FileSyncSvc) nepodporuje koncové body serveru nacházející se na svazku, který má komprimovaný adresář systému SVI (System Volume Information). Tato konfigurace bude mít za následek neočekávané výsledky.
- Režim FIPS není podporován a musí být zakázán. 

### <a name="interoperability"></a>Vzájemná funkční spolupráce
- Antivirové, zálohovací a další aplikace s přístupem k vrstveným souborům můžou způsobit nežádoucí odvolání, pokud nerespektují atribut offline a přeskočí čtení obsahu těchto souborů. Další informace najdete v tématu věnovaném [řešení potíží s Azure File Sync](storage-sync-files-troubleshoot.md).
- Nepoužívejte blokování souborů služby Správce prostředků souborového serveru ani jiné blokování souborů. Blokování souborů může způsobit chyby nekonečné synchronizace, pokud jsou kvůli němu soubory blokované.
- Spuštění nástroje Sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Instalace agenta a registrace serveru by se měly vyskytnout po nasazení image serveru a dokončení zkrácené instalace nástroje Sysprep.
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
- Vrstvené soubory se neprojeví, pokud se soubory před odstraněním koncového bodu serveru nevrátí.
- Na systémovém svazku není podporováno vrstvení cloudu. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, zakažte při vytváření koncového bodu serveru vrstvení cloudu.
- Clustering převzetí služeb při selhání se podporuje pouze s clusterovanými disky, nikoli však se sdílenými svazky clusteru (CSV).
- Koncový bod serveru nemůže být vnořený. Může existovat paralelně na stejném svazku spolu s jiným koncovým bodem.
- Neukládejte stránkovací soubor operačního systému nebo aplikace, který je na koncovém bodu serveru.
- Název serveru na portálu se neaktualizuje, pokud se server přejmenuje.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
- Azure File Sync podporuje přímé provádění změn ve sdílené složce Azure. Všechny změny provedené ve sdílené složce Azure se ale nejdřív musí zjistit pomocí úlohy zjišťování změn Azure File Sync. Úloha detekce změn se iniciuje pro koncový bod cloudu každých 24 hodin. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST neaktualizují čas poslední změny protokolu SMB a nebudou se zobrazovat jako změny v synchronizaci.
- Služba synchronizace úložiště nebo účet úložiště se dá přesunout do jiné skupiny prostředků nebo předplatného v rámci stávajícího tenanta Azure AD. Pokud je účet úložiště přesunutý, musíte mu udělit přístup ke službě hybridní Synchronizace souborů k účtu úložiště (podívejte se, [jestli má Azure File Sync přístup k účtu úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nepodporuje přesun předplatného na jiného tenanta Azure AD.

### <a name="cloud-tiering"></a>Vrstvení cloudu
- Pokud se vrstvený soubor zkopíruje do nového umístění pomocí příkazu Robocopy, výsledný soubor nebude vrstvený. Může však mít nastavený atribut offline, protože příkaz Robocopy nesprávně zahrnuje tento atribut do operací kopírování.
- Při zobrazení vlastností souboru v klientovi SMB se může zobrazit nesprávně nastavený atribut offline. Důvodem je ukládání metadat souboru do mezipaměti protokolem SMB.
