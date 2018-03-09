---
title: "Kolekce zařízení v Azure migrovat | Microsoft Docs"
description: "Poskytuje přehled kolekce zařízení a jeho konfiguraci."
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: 49f3d5ba55a9c1abfcd6dcb50058ed7a001a2eec
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="collector-appliance"></a>Kolekce zařízení

[Azure migrací](migrate-overview.md) vyhodnocuje místní úlohy pro migraci do Azure. Tento článek obsahuje informace o tom, jak používat kolekce zařízení.



## <a name="overview"></a>Přehled

Migrace kolekce služby Azure je lighweight zařízení, který slouží ke zjištění prostředí místní vCenter. Toto zařízení zjistí počítače VMware na místě a metadata o nich se odešle do služby Azure migrovat.

Kolekce zařízení je OVF, které si můžete stáhnout z projektu Azure migrovat. Vytvoření instance virtuálního počítače VMware s 4 jádra, 8 GB paměti RAM a jeden disk 80 GB. Operační systém zařízení je Windows Server 2012 R2 (64bitová verze).

Pomocí následujícího postupu můžete vytvořit kolekce zde - [postup vytvoření virtuálního počítače kolekce](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="collector-communication-diagram"></a>Diagram komunikace kolekce

![Diagram komunikace kolekce](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Komponenta      | Komunikace s   | Požadovaný port                            | Důvod                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Kolektor      | Služba Azure Migrate | TCP 443                                  | Kolekce musí být schopen komunikovat se službou v portu SSL 443 |
| Kolektor      | vCenter Server        | Výchozí 443                             | Kolekce by mohli ke komunikaci se serverem vCenter. Ve výchozím nastavení připojí k vCenter na 443. Pokud vCenter naslouchá na jiný port, že port by měl být k dispozici jako odchozí port v kolekci |
| Kolektor      | Protokol RDP|   | TCP 3389 | Abyste mohli pro připojení RDP do počítače kolekce |





## <a name="collector-pre-requisites"></a>Požadavky kolekce

Kolekce musí předat několik předběžné kontroly, aby se zajistilo může připojit ke službě Azure migrovat a nahrát zjištěná data. Tento článek vypadá na každé požadované součásti a pochopit, proč je vyžadována.

### <a name="internet-connectivity"></a>Připojení k internetu

Kolekce zařízení musí být připojen k Internetu k odeslání informací o zjištěných počítačů. Na počítači můžete připojit k Internetu v jednom z následujících způsobů.

1. Můžete nakonfigurovat kolekce tak, aby měl přímé připojení k Internetu.
2. Můžete nakonfigurovat kolekce pro připojení prostřednictvím serveru proxy.
    * Pokud proxy server vyžaduje ověření, můžete zadat uživatelské jméno a heslo v nastavení připojení.
    * Formulář http://IPaddress nebo http://FQDN by měla být IP adresa nebo plně kvalifikovaný název Proxy serveru. Je podporován pouze server proxy protokolu http.

> [!NOTE]
> Kolekce nepodporuje založený na protokolu HTTPS proxy servery.

#### <a name="whitelisting-urls-for-internet-connection"></a>Vytvoření seznamu povolených adres URL pro připojení k Internetu

Předběžné kontroly je úspěšné, pokud kolekce můžete připojit k Internetu prostřednictvím zadaného nastavení. Zkontrolujte připojení byl ověřen pomocí připojení k seznam adres URL, jak je uvedeno v následující tabulce. Pokud používáte žádné brány firewall založená na adresu URL proxy serveru k řízení odchozí připojení, nezapomeňte seznamu povolených IP adres, že následující požadované adresy URL:

**Adresa URL** | **Účel**  
--- | ---
*.portal.azure.com | Vyžaduje se zkontrolovat připojení ke službě Azure a ověřit synchronizaci času problémy.

Kromě toho kontroly také pokusí se ověřit připojení k následujícím adresám URL, ale neselže kontrolu, pokud není k dispozici. Konfigurace seznamu povolených IP adres pro následující adresy URL je volitelný, ale je potřeba provést ruční kroky ke zmírnění předběžné kontroly.

**Adresa URL** | **Účel**  | **Co když nemáte seznamu povolených IP adres**
--- | --- | ---
*.oneget.org:443 | Vyžaduje ke stažení powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Nainstalujte modul ručně.
*.windows.net:443 | Vyžaduje ke stažení powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Nainstalujte modul ručně.
*.windowsazure.com:443 | Vyžaduje ke stažení powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Nainstalujte modul ručně.
*.powershellgallery.com:443 | Vyžaduje ke stažení powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Nainstalujte modul ručně.
*.msecnd.net:443 | Vyžaduje ke stažení powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Nainstalujte modul ručně.
*.visualstudio.com:443 | Vyžaduje ke stažení powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Nainstalujte modul ručně.

### <a name="time-is-in-sync-with-the-internet-server"></a>Čas je synchronizována s internetového serveru

Kolekce musí být synchronizována s čas serveru internet k zajištění, že jsou požadavky na službu ověřen. Stránce portal.azure.com adresa url by měly být dostupné z kolekce, takže čas může být ověřen. Pokud tento počítač není synchronizován, budete muset změnit čas hodiny na virtuálním počítači kolekce tak, aby odpovídaly aktuálního času, následujícím způsobem:

1. Otevřete příkazový řádek správce ve virtuálním počítači.
1. Chcete-li zkontrolovat časové pásmo, spusťte w32tm /tz.
1. Chcete-li synchronizovat čas, spusťte w32tm/resync.

### <a name="collector-service-should-be-running"></a>Kolekce služby by měl být spuštěn.

Služba Azure migraci kolekce by měla být spuštěna v počítači. Tato služba je spuštěna automaticky, když se počítač spustí. Pokud tato služba neběží, můžete spustit *Azure migraci kolekce* služby v Ovládacích panelech. Kolekce služba je zodpovědná k připojení k serveru vCenter, shromažďování dat metadata a výkon počítače a jeho odeslání ke službě.

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5 

Modul prostředí powershell VMware PowerCLI musí být nainstalovaný, aby kolekce komunikovat se serverem vCenter a dotazy na podrobnosti o počítači a jejich data výkonu. Modul prostředí powershell je automaticky stažen a nainstalován jako součást předběžné kontroly. Automatické stahování vyžaduje několik adresy URL seznam povolených adres, které je nutné zadat buď nedaří získat přístup pomocí povolených je, nebo ruční instalace modulu.

Instalace modulu ručně pomocí následujících kroků:

1. K instalaci PowerCli v kolekci bez připojení k Internetu, postupujte podle kroků uvedených v [tento odkaz](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html) .
2. Po instalaci modulu prostředí PowerShell v jiném počítači, který má přístup k Internetu, zkopírujte soubory VMware.* z tohoto počítače do kolekce počítačů.
3. Restartujte kontrolu předpokladů a zkontrolujte, že je nainstalovaná PowerCLI.

## <a name="connecting-to-vcenter-server"></a>Připojení k systému vCenter Server

Kolekce musí připojit k systému vCenter Server a moct dotaz pro virtuální počítače, jejich metadat a jejich čítače výkonu. Tato data se v projektu používá k výpočtu posouzení.

1. Pro připojení k systému vCenter Server, účet jen pro čtení s oprávněními podle údaje v následující tabulce slouží ke spuštění zjišťování. 

    |Úkol  |Požadované role nebo účtu  |Oprávnění  |
    |---------|---------|---------|
    |Zjišťování zařízení na základě kolekce    | Je třeba alespoň uživatele jen pro čtení        |Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only         |

2. Pouze těchto datových center, které jsou přístupné pro zadaný účet vCenter je přístupný pro zjišťování.
3. Je třeba zadat plně kvalifikovaný název domény nebo IP adresu pro připojení k serveru vCenter server vCenter. Ve výchozím nastavení se připojí přes port 443. Pokud jste nakonfigurovali vCenter pro naslouchání na jiné číslo portu, můžete je zadat jako součást adresu serveru ve formě IPAddress:Port_Number nebo FQDN:Port_Number.
4. Nastavení statistiky pro vCenter Server je nastavit na úroveň 3, před zahájením nasazení. Pokud úroveň je nižší než 3, zjišťování bude dokončen, ale nebudou shromažďovány údaje o výkonu pro úložiště a sítě. Doporučení vyhodnocení velikosti v takovém případě budou založeny na údaje o výkonu pro procesor a paměť a pouze konfigurační data pro disk a síťové adaptéry. [Další informace](./concepts-collector.md) na jaká data se shromažďují a jak se má dopad na hodnocení.
5. Kolekce by měl mít sítě směrem pohledu k serveru vCenter.

> [!NOTE]
> Pouze vCenter Server 5.5, 6.0 a verze 6.5 oficiálně podporovaných verzích.

> [!IMPORTANT]
> Doporučujeme nastavit nejvyšší běžné úroveň (3) pro úroveň statistiky tak, aby všechny čítače jsou shromažďovány správně. Pokud máte vCenter nastavit na nižší úrovni, mohou být pouze několik čítače shromažďovány úplně, se zbytkem nastaven na hodnotu 0. Posouzení pak může zobrazovat neúplná data. 

### <a name="selecting-the-scope-for-discovery"></a>Výběr rozsahu zjišťování

Po připojení k serveru vCenter, můžete vybrat obor zjišťování. Výběr obor vyhledá všechny virtuální počítače z cesty zadané vCenter inventáře.

1. Datové centrum, složku nebo hostiteli ESXi, může být oboru. 
2. Můžete vybrat pouze jeden obor. Pokud chcete vybrat víc virtuálních počítačů, můžete dokončit jeden zjišťování a restartujte proces zjišťování nového oboru.
3. Můžete vybrat pouze v rozsahu, který má *méně než 1 000 virtuálních počítačů*. Pokud vyberete v rozsahu, který má více než 1 000 virtuálních počítačů, budete muset rozdělení oboru do menších jednotek tím, že vytvoříte složek. Dále musíte spustit nezávislé zjišťování menší složek.

## <a name="specify-migration-project"></a>Zadejte migrace projektu

Jakmile je připojen místní vCenter a je určena obor, nyní můžete určit podrobností projektu migrace, které je potřeba použít pro zjišťování a hodnocení. Zadejte ID projektu a klíč a připojení.

## <a name="start-discovery-and-view-collection-progress"></a>Spuštění zjišťování a zobrazení kolekce průběh

Jakmile se spustí zjišťování, jsou zjišťovány vCenter virtuální počítače a jejich data metadata a výkonu je odeslat na server. Stav průběhu také uvidíte následující ID:

1. ID kolekce: Jedinečné ID, který je přiřazen k vašemu počítači kolekce děti. Toto ID se nezmění napříč různá zjišťování pro daný počítač. Toto ID v případě selhání můžete použít při hlášení problému k Microsoft Support.
2. ID relace: Jedinečné ID pro spuštěné úlohy kolekce. Po dokončení úlohy zjišťování lze odkazovat na stejné ID relace na portálu. Toto ID změní pro všechny úlohy kolekce. V případě selhání můžete toto ID nahlásit společnosti Microsoft Support.

### <a name="what-data-is-collected"></a>Jaká data se shromažďují?

Úlohy kolekce zjistí následující statické metadata o vybraných virtuálních počítačů. 

1. Název zobrazení virtuálních počítačů (na vCenter)
2. Cesta inventáře Virtuálního počítače (hostitele nebo složku v systému vCenter)
3. IP adresa
4. Adresa MAC
5. Počet jader, disků, síťových adaptérů
6. Paměť RAM, velikosti disků
7. A čítače výkonu virtuálních počítačů, disku a sítě, jak je uvedeno v následující tabulce.

Následující tabulka uvádí čítače výkonu, které se shromažďují a také zobrazuje výsledky hodnocení, které jsou vliv, pokud nejsou zjištěny jednotlivých čítačů.

|Čítač                                  |Úroveň    |Úroveň za zařízení  |Dopad hodnocení                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |Není k dispozici                |Doporučená velikost virtuálního počítače a náklady                    |
|mem.usage.average                        | 1       |Není k dispozici                |Doporučená velikost virtuálního počítače a náklady                    |
|virtualDisk.read.average                 | 2       |2                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|virtualDisk.write.average                | 2       |2                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|net.received.average                     | 2       |3                 |Náklady na velikost a sítě virtuálních počítačů                        |
|net.transmitted.average                  | 2       |3                 |Náklady na velikost a sítě virtuálních počítačů                        |

> [!WARNING]
> Pokud jste právě nastavili na vyšší úrovni statistiky, bude trvat jeden den ke generování čítače výkonu. Ano doporučujeme spustit zjišťování po uplynutí jednoho dne.

### <a name="time-required-to-complete-the-collection"></a>Čas potřebný k dokončení kolekce

Kolekce pouze zjistí počítač data a odešle ji do projektu. Projektu může trvat déle než zjištěná data se zobrazí na portálu a můžete začít s vytvářením posouzení.

Podle počtu virtuálních počítačů v oboru vybrané, trvá až 15 minut odeslat statické metadata do projektu. Jakmile statické metadata jsou dostupné na portálu, můžete zobrazit seznam počítačů v portálu a začít vytvářet skupiny. Posouzení nelze vytvořit, až do dokončení úlohy kolekce a projekt má zpracovat data. Jednou kolekce úloha nebyla dokončena v kolekci, může trvat až jednu hodinu, než výkonu data, která mají být k dispozici na portálu, podle počtu virtuálních počítačů v oboru vybrané.

## <a name="how-to-upgrade-collector"></a>Postup upgradu kolekce

Kolekce můžete upgradovat na nejnovější verzi bez stahování vajíčka ještě jednou.

1. Stáhněte si nejnovější [balíček s upgradem](https://aka.ms/migrate/col/latestupgrade).
2. Pro zajištění zabezpečené stažené opravy hotfix, otevřete příkazové okno správce a spusťte následující příkaz pro vytvoření hodnotu hash pro souboru ZIP. Generované hodnoty hash shodovat se symbolem hash uvedených na konkrétní verzi:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    
    (příklad použití C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.5.zip SHA256)
3. Soubor zip zkopírujte do Azure migraci kolekce virtuální počítač (kolekce zařízení).
4. Klikněte pravým tlačítkem na soubor zip a vyberte možnost Extrahovat vše.
5. Klikněte pravým tlačítkem na Setup.ps1 a vyberte spustit v prostředí PowerShell a postupujte podle pokynů na obrazovce instalace aktualizace.

### <a name="list-of-updates"></a>Seznam aktualizací

#### <a name="upgrade-to-version-1095"></a>Upgrade na verzi 1.0.9.5

Pro Upgrade na verzi 1.0.9.5 stažení [balíčku](https://aka.ms/migrate/col/upgrade_9_5)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | d969ebf3bdacc3952df0310d8891ffdf
SHA1 | f96cc428eaa49d597eb77e51721dec600af19d53
SHA256 | 07c03abaac686faca1e82aef8b80e8ad8eca39067f1f80b4038967be1dc86fa1

## <a name="next-steps"></a>Další postup

[Nastavit hodnocení pro virtuální počítače VMware na místě](tutorial-assessment-vmware.md)
